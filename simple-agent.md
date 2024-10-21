# Evaluations on the Effectiveness of LM Agents with BugSwarm 

We will test the efficacy of CIFailureFix-Agent using `tananaev-traccar-64783123` as an example artifact. It is a Java artifact and from github, we come to know its diff size is 2.

# Setting up the CIFailureFix-Agent

1. Clone the repository
```bash
git clone -b debug https://github.com/BugSwarm/CIFailureFix-Agent.git
```

2. Run
```bash
python -m pip install --upgrade pip && pip install --editable .
```
at the repository root (as with any python setup, it's recommended to use conda or virtual environments to manage dependencies).

3. Create a `keys.cfg` file in `swe-agent`. Contents will be like
```bash
GITHUB_TOKEN=""
OPENAI_API_KEY=""
BUGSWARM_TOKEN=""
```

4. Go into `swe-agent` folder and run 
```bash
./setup.sh
```

5. For generating patch
```bash
python run.py --model_name <llm_model_name> --data_path <bugswarm_image_name> --config_file config/default_from_url.yaml  --per_instance_cost_limit 2.0
```