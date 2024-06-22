# Running SLM Lab in Google Colab

This repository contains instructions and code for running SLM Lab in Google Colab, along with experiments on the Atari Freeway Game.

## Files

- `FreewayRL.ipynb`: Notebook with step-by-step instructions to set up and run SLM Lab in Google Colab
- `AtariFreewayRLExperiments.pdf`: PDF document presenting the Atari Freeway Game and the experiments conducted [3]
- `RL folder`: Folder that should be copied to your Google Drive account if you want to make your life easier when running SLM Lab in Google Colab
- `RL\configs folder`: Folder that contains all my configuration files for the algorithms I trained on Atary Freeway
- `RL\*.py`: Modified files from SLM Lab Repository [1] in order to make it work in Google Colab
- `RL\Roms.rar`: Atari Mania ROM Collection [4]


## Prerequisites

- A Google account to use Google Colab and Google Drive
- Basic knowledge of Python and Jupyter Notebooks


## Instructions

> [!NOTE]
> These instructions can also found in the [Jupyter Notebook](FreewayRL.ipynb) file.

### Step 1: Basic configuration

* Connect to Google Drive
* Clone the repository from [SLMLab](https://github.com/kengz/SLM-Lab.git)
* Change directory to SLM-Lab
* Run the setup from .bin/setup. (the interactive console is right under the dotted line. You have to click there in order to press enter)

### Step 2: Solving problems

* Some versions are deprecated. Run the following code in order to install everything we need:
```
!pip install pybullet
!pip install roboschool
!pip install atari-py
!pip install pydash
!pip install ujson
!pip install colorlog
!pip install ray
!pip install gym==0.15.4
!pip install roboschool==1.0.48
!pip install PyOpenGL==3.1.* PyOpenGL-accelerate==3.1.*
!sudo apt-get install freeglut3-dev
!sudo apt-get install mesa-utils
!apt-get install -y xvfb x11-utils
!pip install swig
!pip install pyvirtualdisplay
!pip install -U kaleido
```
* We need to modify some files from SLM Lab

  *SLM-Lab/slm_lab/spec/random_baseline.py: comment `import roboschool` (line 7)*
  
  *SLM-Lab/slm_lab/env/vec_env.py: `np.bool_` instead of `np.bool` (line 22)*
  
  *SLM-Lab/slm_lab/env/openai.py: comment `import roboschool` (line 10)*

> [!TIP]
> You can save them to Google Drive and just copy them to the right location.
  
* We need to import the ROM for the game. Download the archive from [AtariMania](https://www.atarimania.com/rom_collection_archive_atari_2600_roms.html).

  Extract it's content and run: `!python -m atari_py.import_roms /content/SLM-Lab/HCROMS`

> [!TIP]
> You can upload it to Google Drive as Roms.rar and extract it by running: `!unrar x /content/drive/MyDrive/RL/Roms.rar`.

EXTRA:
* If you want to run DQN PER: *SLM-Lab/slm_lab/agent/memory/prioritized.py: `int` instead of `np.int` (line 144)*
* If you want to run A2C, A3C or PPO: *SLM-Lab/slm_lab/agent/__init__.py: update line 158-173*:
```
row = pd.Series({
          'epi': self.env.clock.epi,
          't': env.clock.t,
          'wall_t': wall_t,
          'opt_step': self.env.clock.opt_step,
          'frame': frame,
          'fps': fps,
          'total_reward': total_reward,
          'total_reward_ma': np.nan,  # update outside
          'loss': self.loss.cpu().item() if torch.is_tensor(self.loss) else self.loss,
          'lr': self.get_mean_lr(),
          'explore_var': self.explore_var,
          'entropy_coef': self.entropy_coef.cpu().item() if hasattr(self, 'entropy_coef') and torch.is_tensor(self.entropy_coef) else (self.entropy_coef if hasattr(self, 'entropy_coef') else np.nan),
          'entropy': self.mean_entropy.cpu().item() if torch.is_tensor(self.mean_entropy) else self.mean_entropy,
          'grad_norm': self.mean_grad_norm.cpu().item() if torch.is_tensor(self.mean_grad_norm) else self.mean_grad_norm,
      }, dtype=np.float32)
```
  
### Step 3: Google Colab workaround

* SLM Lab needs a display to connect to. Run the following code in order to create a virtual display [5]:
```
import pyvirtualdisplay

_display = pyvirtualdisplay.Display(visible=False, size=(1400, 900))
_ = _display.start()
```

### Step 4: Running and Saving Results

* Upload your config files to slm_lab/spec folder (demo.json file is also there).

> [!TIP]
> I saved them on Google Drive and copied them to the specified folder.

* Start using SLM Lab by running this command [2]: 
  `!python run_lab.py {spec file} {spec name} {lab mode}`

* The outputs are saved to *SLM-Lab/data* folder. I recommend saving the files to Google Drive, since all the data is lost from Google Colab when the session ends

> [!IMPORTANT]
> Some algorithms might not run till finish on the free version of Google Colab. The problem is not the GPU/CPU, but the RAM memory. If you switch to Colab Pro, change runtime type to *High-RAM*.


## Reference list

1. SLM Lab Repository. https://github.com/kengz/SLM-Lab
2. SLM Lab. https://slm-lab.gitbook.io/slm-lab
3. Reinforcement Learning Course, Prof. Dr. Habil. Eng. Călin-Adrian POPA, 2025
4. Atari Mania ROM Collection: https://www.atarimania.com/rom_collection_archive_atari_2600_roms.html
5. Colab virtual screen. https://discuss.huggingface.co/t/colab-virtual-screen-in-reinforcement-learning-course/33427

