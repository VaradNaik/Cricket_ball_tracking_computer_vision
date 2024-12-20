# Cricket_ball_tracking_computer_vision

## Installation

SAM 2 needs to be installed first before use. The code requires python>=3.10, as well as torch>=2.5.1 and torchvision>=0.20.1. Please follow the instructions here to install both PyTorch and TorchVision dependencies. You can install SAM 2 on a GPU machine using:

`git clone https://github.com/facebookresearch/sam2.git && cd sam2`

`pip install -e .`

If you are installing on Windows, it's strongly recommended to use Windows Subsystem for Linux (WSL) with Ubuntu.

To use the SAM 2 predictor and run the example notebooks, jupyter and matplotlib are required and can be installed by:

`pip install -e ".[notebooks]"`
Note:

1. It's recommended to create a new Python environment via Anaconda for this installation and install PyTorch 2.5.1 (or higher) via pip following https://pytorch.org/. If you have a PyTorch version lower than 2.5.1 in your current environment, the installation command above will try to upgrade it to the latest PyTorch version using pip.
2. The step above requires compiling a custom CUDA kernel with the nvcc compiler. If it isn't already available on your machine, please install the CUDA toolkits with a version that matches your PyTorch CUDA version.
3. If you see a message like Failed to build the SAM 2 CUDA extension during installation, you can ignore it and still use SAM 2 (some post-processing functionality may be limited, but it doesn't affect the results in most cases).

## Getting Started

Download Checkpoints
First, we need to download a model checkpoint. All the model checkpoints can be downloaded by running:

`cd checkpoints && \
./download_ckpts.sh && \
cd ..`

or individually from:

- sam2.1_hiera_tiny.pt
- sam2.1_hiera_small.pt
- sam2.1_hiera_base_plus.pt
- sam2.1_hiera_large.pt

(note that these are the improved checkpoints denoted as SAM 2.1; see Model Description for details.)

## Using FFMPEG

We assume that the video is stored as a list of JPEG frames with filenames like <frame_index>.jpg.

For your custom videos, you can extract their JPEG frames using ffmpeg (https://ffmpeg.org/) as follows:

`ffmpeg -i <your_video>.mp4 -q:v 2 -start_number 0 <output_dir>/'%05d.jpg'`

where -q:v generates high-quality JPEG frames and -start_number 0 asks ffmpeg to start the JPEG file from 00000.jpg.

## Alternative to ffmpeg

If you are having trouble with ffmpeg, you can use the following code to convert your video into a list of JPEG frames -

`import cv2`

`import os`

`video_path = "c2.mp4"  # your input video file`


`output_dir = "cricket2_dir"`

`os.makedirs(output_dir, exist_ok=True)`

`# Open the video file`
`cap = cv2.VideoCapture(video_path)`

`frame_count = 0`
`while True:`
   `ret, frame = cap.read()`
    `if not ret:`
        `break`
    `# Save the current frame as a jpg image`
    `frame_path = os.path.join(output_dir, f"frame_{frame_count:06d}.jpg")`
    `cv2.imwrite(frame_path, frame)`
    `frame_count += 1`

along with the code above, the code snippet below converts changes the file name to be conistent with the ffmpeg command above (5 zeros)
`directory = output_dir`
`for filename in os.listdir(directory):`
    `if filename.startswith('frame_') and filename.endswith('.jpg'):`
        `new_name = filename.replace('frame_', '', 1)`
        `os.rename(os.path.join(directory, filename),`
                 `os.path.join(directory, new_name))`