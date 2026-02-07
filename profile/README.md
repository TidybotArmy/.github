# Tidybot

Hey humans and agents! Welcome.

The vision here is simple: **a shared space where we all build on the Tidybot together, as a community.** Humans write code, agents write code, the robot moves things around, and we all figure it out as we go.

So here's everything you need to know to get started.

---

## There's a Server

The robot runs an API server. If you can reach it, you're in:

```
http://<ROBOT_IP>:8080
```

Replace `<ROBOT_IP>` with the robot's address on your network.

A quick sanity check:

```bash
curl http://<ROBOT_IP>:8080/health
```

If that responds, you're good.

## There's a Doc

The robot serves its own documentation. Want to know every endpoint, every SDK module, every parameter? Just ask it:

```bash
# Human-friendly markdown
curl http://<ROBOT_IP>:8080/code/sdk/markdown

# Machine-friendly JSON (for you, agents)
curl http://<ROBOT_IP>:8080/code/sdk
```

That's the source of truth — always up to date, always on the robot itself.

## You Move the Robot by Submitting Code

Nothing moves until you send Python code to execute on the robot. The flow is:

1. **Grab a lease** (one controller at a time):
   ```bash
   curl -X POST http://<ROBOT_IP>:8080/lease/acquire \
     -H "Content-Type: application/json" \
     -d '{"holder": "your-name-here"}'
   ```

2. **Submit code** — it runs in an isolated process on the robot with the full SDK available (`arm`, `base`, `gripper`, `sensors`, `rewind`):
   ```bash
   curl -X POST http://<ROBOT_IP>:8080/code/execute \
     -H "X-Lease-Id: <your-lease-id>" \
     -H "Content-Type: application/json" \
     -d '{"code": "from robot_sdk import arm\narm.move_joints([0, -0.785, 0, -2.356, 0, 1.571, 0.785])\nprint(\"Hello from the robot!\")"}'
   ```

3. **Check on it:**
   ```bash
   curl http://<ROBOT_IP>:8080/code/status
   curl http://<ROBOT_IP>:8080/code/result
   ```

4. **Release the lease** when you're done (so others can play too).

## Don't Be Afraid to Make Mistakes

There's a **rewind system** built in. The robot records its trajectory as it moves, and you can reverse it — like an undo button for the physical world.

Went somewhere weird? Just rewind:

```bash
curl -X POST http://<ROBOT_IP>:8080/rewind/percentage \
  -H "X-Lease-Id: <your-lease-id>" \
  -H "Content-Type: application/json" \
  -d '{"percentage": 100.0}'
```

That rolls back the whole trajectory. You can also rewind partially, step by step, or reset to a safe home position. It's all in the docs (you know where to find them).

So experiment. Try things. Break stuff. Rewind. Try again.

---

**For the full, serious documentation — every endpoint, every parameter, every module — it's all on the robot:**

```bash
curl http://<ROBOT_IP>:8080/code/sdk/markdown
```

Happy building.
