# Tacotron

An implementation of Tacotron speech synthesis in TensorFlow (Modified to use for Nepali Dataset.)

## Demo
https://www.youtube.com/watch?v=jTdbOnkWSg4

## Quick Start

### Installing dependencies

1. Install Python 3.

2. Install the latest version of [TensorFlow](https://www.tensorflow.org/install/) for your platform. For better
   performance, install with GPU support if it's available. This code works with TensorFlow 1.3 and later.

3. Install requirements:
   ```
   pip install -r requirements.txt
   ```


### Using a pre-trained model

1. **Download and unpack a model**:
   FOR NEPALI:
   Download [NEPALI PRETRAINED MODEL](https://drive.google.com/open?id=1P6tyIYZiTG2_6wPim4IRGZrNEYadJrU6) on 30, 50 and 75k iterations. For the better results use the model checkpoint trained on 75k iteration.

2. **Run the demo server**:
   FOR NEPALI DEMO:
   ```
   python3 demo_server.py --checkpoint <full_path_to_pretrained_model>/model.ckpt-30000
    ```

3. **Point your browser at localhost:9000**
   * Type what you want to synthesize
   * FOR NEPALI USE NEPALI UNICODE SENTENCES.

### Training
1. **Download a speech dataset.**

    * [ne_np_female](https://research.google/tools/datasets/nepali-tts/) (Creative Commons Attribution Share-Alike)

2. **Unpack the dataset into `~/PycharmProjects/tacotron`**
   For nepali TTS the folder should look like(the extracted folder is renamed to `nepali` for simplicity)
      ```
   tacotron
     |- nepali
         |- line_index.tsv
         |- wavs
   ```

3. **Preprocess the data**
   ```
   python3 preprocess.py --dataset nepali
   ```
     * For nepali dataset [hparams.py](hparams.py) is set to `cleaners='transliteration_cleaners'`. 
      If you are using other dataset, change it to default. 

4. **Train a model**
   ```
   python3 train.py
   ```

   Tunable hyperparameters are found in [hparams.py](hparams.py). You can adjust these at the command
   line using the `--hparams` flag, for example `--hparams="batch_size=16,outputs_per_step=2"`.
   Hyperparameters should generally be set to the same values at both training and eval time.
   
   * For nepali dataset use `python3 train.py --hparams="max_iters=300"`. See `Notes and Common Issues` for details.


5. **Monitor with Tensorboard** (optional)
   ```
   tensorboard --logdir ~/PycharmProjects/tacotron/logs-tacotron
   ```

   The trainer dumps audio and alignments every 1000 steps. You can find these in
   `~/PycharmProjects/tacotron/logs-tacotron`.

6. **Synthesize from a checkpoint**
   ```
   python3 demo_server.py --checkpoint ~/PycharmProjects/tacotron/logs-tacotron/model.ckpt-50000
   ```
   Replace "50000" with the checkpoint number that you want to use, then open a browser
   to `localhost:9000` and type what you want to speak. Alternately, you can
   run [eval.py](eval.py) at the command line:
   ```
   python3 eval.py --checkpoint ~/PycharmProjects/tacotron/logs-tacotron/model.ckpt-50000
   ```
   If you set the `--hparams` flag when training, set the same value here.


## Notes and Common Issues
    
  * During eval and training, audio length is limited to `max_iters * outputs_per_step * frame_shift_ms`
    milliseconds. With the defaults (max_iters=200, outputs_per_step=5, frame_shift_ms=12.5), this is
    12.5 seconds.
    
    If your training examples are longer, you will see an error like this:
    `Incompatible shapes: [32,1340,80] vs. [32,1000,80]`
    
    To fix this, you can set a larger value of `max_iters` by passing `--hparams="max_iters=300"` to
    train.py (replace "300" with a value based on how long your audio is and the formula above).

  * In this fork the basedir should be noted as `~/PycharmProjects/tacotron`, please modify path location according to your cloning dir.

### For more information please refer to [original implementation](https://github.com/keithito/tacotron)
  
