A docker image for SSD object detection training. <br/>
The original source: https://github.com/naisy/train_ssd_mobilenet <br/>
It is recommended to read the orginal instruction. But the installation of files and setup are already done in the docker image.

# Set up
* git clone && cd github
* docker pull registry.gitlab.com/haiandaidi/docker_ssd:2020_07_11
* docker run --gpus all -it -v $PWD/github:/github:rw -w /github --network=host registry.gitlab.com/haiandaidi/docker_ssd:2020_07_11
* cd train_ssd_mobileneet

# Preparing of the training data
The training needs data of voc format.
windex_data_voc_format<br/>
    Annotations<br/>
    JPEGImages<br/>
    xxx_label.pbtxt # label information <br/>
* change `config.yml`
* change `ssd_mobilenet_v1_xxx.config`
* python build1_trainval.py
* python build2_tf_record.py <br/>
  If erros of existing folder occurs, just delete those folders by `rm -rf xxx/xxx`

# Training
Choose the corresponding config file and run
`python /github/models/research/object_detection/model_main.py --alsologtostderr -model_dir=./train --pipeline_config_path=./ssd_mobilenet_v1_tello.config` <br/>
monitoring: `tensorboard --logdir=train`

# Evaluation
Choose the corresponding config file and run
`python /github/models/research/object_detection/eval.py --logtostderr --checkpoint_dir=./train --eval_dir=./eval --pipeline_config_path=./ssd_mobilenet_v1_tello.config`

# Freeze Graph
Choose the corresponding config file and run
`python /github/models/research/object_detection/export_inference_graph.py --input_type image_tensor --pipeline_config_path=./ssd_mobilenet_v1_tello.config --trained_checkpoint_prefix ./train/model.ckpt-6513 --output_directory ./output \
       --config_override " \
            model{ \
              ssd { \
                post_processing { \
                  batch_non_max_suppression { \
                    score_threshold: 0.5 \
                  } \
                } \
              } \
            }"`