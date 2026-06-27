# greet_rbnx — passerby-greeting skill (Robonix)

A Robonix **skill**: watch the robot's front RGB camera and greet people in
its path so they make way. Two-stage to keep it cheap — a local **YOLO**
person detector runs every frame (free, on-GPU); only when it sees nearby
people does it call a **VLM** for one short, friendly, customised line, then
speaks it via `speech/speak`. The robot base is never driven.

```
camera/rgb → YOLO (filter far/small) → (people) → VLM line → speech/speak
```

## Capabilities (async triplet)

- `robonix/skill/greet/greet` — start the greeting watch (returns a run_id)
- `robonix/skill/greet/greet/status` — poll (SUCCEEDED once the loop is up)
- `robonix/skill/greet/greet/cancel` — stop it

## Config (Driver CMD_INIT)

| key | meaning |
|-----|---------|
| `vlm.upstream` / `vlm.api_key` / `vlm.model` | OpenAI-compatible vision endpoint |
| `yolo_conf` | YOLO person confidence (default 0.4) |
| `yolo_min_box_frac` | ignore people whose box is shorter than this fraction of frame height (default 0.30) |
| `period_s` / `cooldown_s` | detect period / min seconds between greetings |
| `speak_target` | speaker provider id (empty = first non-bridge) |

See `CAPABILITY.md` for the full manual. Native deploy: `scripts/build.sh`
(codegen + venv, symlinks host CUDA torch on Jetson) then `scripts/start.sh`.
`weights/yolov8n.pt` is bundled for offline robots (no internet on the car);
`build.sh` would otherwise fetch it via ultralytics.
