# System Architecture

## High-Level Diagram

[Userspace] [Kernel Space] [Hardware]
+--------+ +------------+ +-----------+
| injectctl | <--ioctl--> | v4l2_inject | <--kprobe--> | V4L2 |
+--------+ | (module) | | Driver |
| +------------+ +-----------+
| | |
+-----------------------|--------------------------+
| | |
[Video File] [DMA Buffer] [Camera Sensor]
(source) (injected) (real)


## Module Initialization

1. The kernel module registers a set of kprobes on the functions `v4l2_fh_open`, `v4l2_ioctl`, and specifically on the ioctl handlers for `VIDIOC_DQBUF` and `VIDIOC_QBUF`.
2. Upon loading, the module allocates a DMA‑coherent buffer of size equal to the maximum supported frame size (configurable via module parameter).
3. It then creates a character device `/dev/v4l2_inject` with file operations for `open`, `close`, `ioctl`, and `mmap`.

## Frame Injection Flow

1. The userspace `injectctl` loads a frame (or video stream) into the DMA buffer via `mmap` and `write`.
2. When a camera application calls `VIDIOC_DQBUF` to dequeue a filled buffer, the kprobe handler is triggered.
3. The handler checks if injection is enabled and if the current device matches the target (by minor number).
4. If enabled, the handler:
   - Copies the original buffer's timestamp and sequence numbers.
   - Replaces the buffer's data pointer with the injected frame's DMA address.
   - Marks the buffer as ready with `vb2_buffer_done`.
5. The application receives the injected frame as if it came from the sensor.

## Evasion Techniques

- **SELinux**: The module temporarily disables SELinux enforcement for the current process using `setenforce` (or by patching the SELinux hooks). Alternatively, a custom policy can be loaded.
- **Root Detection**: The module hides itself from `/proc/modules` by hooking the `seq_show` function of the modules list.
- **Audit Logs**: The kprobes are installed without triggering the audit subsystem (using `kprobe` flags that avoid `KPROBE_FLAG_TRACE`).

## Limitations

- Requires kernel symbols to be accessible (not stripped).
- Only works on Android kernels that have kprobes compiled in (most custom kernels).
- DMA buffer size must be sufficient for the chosen resolution; exceeding it causes OOM.
- The module may crash the kernel if the target V4L2 device is not streaming or if injection is attempted at the wrong time.