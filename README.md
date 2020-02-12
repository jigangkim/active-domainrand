# Active Domain Randomization

[Active Domain Randomization](https://arxiv.org/abs/1904.04762) (ADR) is a new method for improved, zero-shot transfer of robotic reinforcement learning policies. Building upon traditional domain randomization, which uniformly samples the _randomization space_, we show that replacing this with an active search for difficult MDP instances improves generalization and robustness in the resulting policies.

<p align="center">
  <img src="adr.gif"><br>
</p>

Below is our code to reproduce the experiments in the paper. Please check out our [Domain Randomizer](https://github.com/montrealrobotics/domain-randomizer) repository if you're interested in a easy way to do domain randomization in parallel.

## Installation guide (by jgkim)

1. Option 1. Manually install the dependencies with setup.py (custom github repo packages must be installed manually!)

2. Option 2. Use conda virtual environment (anaconda3 or miniconda3 recommended)
    1. Move to cloned repo
    ```
    cd path/to/cloned/repo
    ```
    2. Create conda environment 'adr-lg' (may take up to 10 minutes)
    ```
    conda env create -f env.yml
    ```
    3. Activate environment and run code
    ```
    conda activate adr-lg
    ```

## Experiments 

### Baseline Experiments

#### Pure Baseline

The most important flag here is `--initial-svpg-steps=1e6`, which will make sure that only randomized environments are proposed until that step limit is reached (it never will be). The environment names (`randomized-env-id`) handle the range of randomization - `LunarLanderDefault-v0` has a single-valued range, so a `randomize()` call will always result in the same, default environment. Likewise, `LunarLanderRandomized-v0` has the full randomization range (in one dimension).

On the command line, specify an experiment type from `[lunar|pusher|ergo]` to get defaults for that experiment. You can find a detailed list of command line arguments in `experiments/args.py`. 

```
python -m experiments.domainrand.experiment_driver [lunar|pusher|ergo] \
    --experiment-name=unfreeze-policy  --freeze-discriminator \
    --experiment-prefix="true-baseline" --agent-name=baseline --initial-svpg-steps=1e6 \
    --continuous-svpg --freeze-svpg --seed={SEED}
```

#### Uniform Domain Randomization 

```
python -m experiments.domainrand.experiment_driver [lunar|pusher|ergo] \
    --experiment-name=unfreeze-policy --randomized-eval-env-id="[corresponding env ID]" \
    --experiment-prefix="fulldr" --agent-name=fulldr --initial-svpg-steps=1e6 \
    --continuous-svpg --freeze-svpg --seed={SEED}
```

### Active Domain Randomization (bootstraping?)

```
python -m experiments.domainrand.experiment_driver [lunar|pusher|ergo] \
    --experiment-name=unfreeze-policy --load-discriminator --randomized-eval-env-id="[corresponding env ID]" \
    --freeze-discriminator --experiment-prefix="ours-agent-scratch" --seed={SEED}
```

### baseline - lunar, pusher (by jgkim)

```
python -m experiments.domainrand.experiment_driver lunar --experiment-name=unfreeze-policy --freeze-discriminator \
    --experiment-prefix="200113render-true-baseline" --agent-name=baseline --initial-svpg-steps=1e6 --continuous-svpg \
    --freeze-svpg --record-video

python -m experiments.domainrand.experiment_driver pusher --experiment-name=unfreeze-policy --freeze-discriminator \
    --experiment-prefix="200113render-true-baseline" --agent-name=baseline --initial-svpg-steps=1e6 --continuous-svpg \
    --freeze-svpg --record-video
```

### DR - lunar, pusher (by jgkim)

```
python -m experiments.domainrand.experiment_driver lunar --experiment-name=unfreeze-policy \
    --randomized-env-id=LunarLanderRandomized-v0 --experiment-prefix="200113render-fulldr" --agent-name=fulldr \
    --initial-svpg-steps=1e6 --continuous-svpg --freeze-svpg --record-video

python -m experiments.domainrand.experiment_driver pusher --experiment-name=unfreeze-policy \
    --randomized-env-id=Pusher3DOFRandomized-v0 --experiment-prefix="200113render-fulldr" --agent-name=fulldr \
    --initial-svpg-steps=1e6 --continuous-svpg --freeze-svpg --record-video
```

### ADR - lunar, pusher (by jgkim)

```
python -m experiments.domainrand.experiment_driver lunar --experiment-name=unfreeze-policy \
    --randomized-env-id=LunarLanderRandomized-v0 --experiment-prefix="200113render-ours-agent-scratch" --agent-name=activedr \
    --continuous-svpg --record-video

python -m experiments.domainrand.experiment_driver pusher --experiment-name=unfreeze-policy \
    --randomized-env-id=Pusher3DOFRandomized-v0 --experiment-prefix="200113render-ours-agent-scratch" --agent-name=activedr \
    --continuous-svpg --record-video
```

## Reference

```
@article{mehta2019adr,
  title={Active Domain Randomization},
  author={Mehta, Bhairav and Diaz, Manfred and Golemo, Florian and Pal, Christopher and Paull, Liam},
  url={https://arxiv.org/abs/1904.04762},
  year={2019}
}
```

Built by [@bhairavmehta95](https://bhairavmehta95.github.io), [@takeitallsource](https://github.com/takeitallsource), and [@fgolemo](https://github.com/fgolemo).
