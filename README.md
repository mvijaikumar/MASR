# MASR
This repo contains source code for our paper: "Adversarial Mahalanobis Distance-based Attentive Song Recommender for Automatic Playlist Continuation" published in SIGIR 2019. 

## Data Format:
- *.rating data file: [user_id]:[playlist_id] \t track_id \t [random-position-number] \t [1]
- *.negative data file: ([user_id]:[playlist_id],[track_id]) \t [negative_track_id1] \t [negative_track_id2] ...

## Hyper-parameters:
- <code>saved_path</code>: path to save the output checkpoint, default is  [chk_points] folder.
- <code>load_best_chkpoint</code>: whether or not loading the best saved checkpoint [1 = Yes, 0 = No, Default is 0].
- <code>path</code>: based path to the data directory. Default is [data] folder.
- <code>dataset</code>: dataset name. Default is [demo]
- <code>epochs</code>: Number of training epochs. Default is 50.
- <code>batch_size</code>: Batch size. Default is 256.
- <code>num_factors</code>: Number of hidden factors. Default is 64.
- <code>reg_mdr</code>: regularization term of MDR model.
- <code>reg_mass</code>: regularization term of MASS model.
- <code>num_neg</code>: number of negative items to be sampled to compare with each positive item. Default is 4.
- <code>max_seq_len</code>: maximum number of tracks in a playlist to consider. Default is -1, meaning considering all tracks in a target playlist.
- <code>dropout</code>: Dropout. Default is 0.2
- <code>act_func</code>: Activation function for the MASS model. 3 options [none, relu, tanh]. "none" means identity activation function here.
- <code>act_func_mdr</code>: Activation function for MDR model. Default is "none".
- <code>model</code>: model to train. 3 options ["mdr", "mass", "masr"].
- <code>beta</code>: contribution of MASS in MASR. Default is 0.5
- <code>out</code>: whether or not saving the output checkpoint. Default is 1, meaning saving output checkpoint for each epoch.
- <code>cuda</code>: Using GPU or not. 1 = Using GPU, 0 = using CPU. Default is 0.
- <code>data_type</code>: whether or not using both user + playlist + track info ["upt"], or only user + track info ["ut"], or playlist + track info ["pt"]. For MDR, choose ["upt"]. For MASS, choose ["ut"]. This parameter is used when training MDR, or MASS. (3 options ["upt", "ut", "pt"].
- <code>data_type_mdr</code>: Data type of MDR. This parameter is useful when training MASR, so we need to know which MDR model with which input data type we want to use.
- <code>data_type_mass</code>: Data type of MASS. This parameter is useful when training MASR, so we need to know which MASS model with which input data type we want to use.
- <code>adv</code>: training with flexible adversarial training? [1 = Yes, 0 = No]. Please training with normal MDR, or MASS to get best initial checkpoint and then train with adversarial training to get best results. Training with adv=1 from scratch cat lead to a lower result.
- <code>reg_noise</code>: regularization term of noise. Default is 1.0. [or \lambda_\delta in Equation (23)]
- <code>eps</code>: noise magnitude. Default is 1.0 [refers to \eps in Equation (24)]. For smaller hidden factors (i.e. 8 hidden factors), this noise magnitude can be set to 0.5 if you observe non-boosting results. 


## Demo example:
### Training MDR and AMDR:
#### Training with MDR:

```
python -u main.py --cuda 1 --dropout 0.2 --dataset demo --epochs 50 --load_best_chkpoint 0 --model mdr --num_factors 64 --reg_mdr 0.0 --adv 0 --act_func_mdr none --data_type upt
```

#### Training with AMDR:
After training MDR, we will have best checkpoint saved at **chk_points**. The model will then automatically load the best chekpoint w.r.t the validation dataset, and use it as an initial start for adversarial learning. Without the initial learning of MDR, if you learn with adversarial learning from the sractch, we can get lower results.

```
python main.py --dataset demo --data_type upt --model mdr --num_factors  64 --reg_mdr 0.0 --load_best_chkpoint 1 --cuda 1 --epochs 50 --adv 1 --reg_noise 1.0 --eval 0 --lr 1e-3 
```
#### Training with MASS:

```
python -u main.py --act_func relu --cuda 1 --dropout 0.2 --dataset demo --epochs 50 --load_best_chkpoint 0 --model mass --num_factors 64 --reg_mass 1e-6 --adv 0 --data_type ut
```

#### Training with AMASS:
```
python main.py --act_func relu --dataset demo --data_type ut --model mass --num_factors  64 --reg_mass 1e-6 --load_best_chkpoint 1 --cuda 1 --epochs 50 --adv 1 --reg_noise 1.0 --eval 0 --lr 1e-3 
```

#### Training with MASR:
```
python main.py --act_func relu --dataset demo --model masr --num_factors  64 --reg_mass 1e-6 --reg_mdr 0.0 --load_best_chkpoint 1 --cuda 1 --epochs 50 --adv 0 --reg_noise 1.0 --eval 0 --lr 1e-3 --act_func_mdr none --data_type_mdr upt --data_type_mass ut --beta 0.5
```

#### Training with AMASR:
```
python main.py --act_func relu --dataset demo --model masr --num_factors  64 --reg_mass 1e-6 --reg_mdr 0.0 --load_best_chkpoint 1 --cuda 1 --epochs 50 --adv 1 --reg_noise 1.0 --eval 0 --lr 1e-3 --act_func_mdr none --data_type_mdr upt --data_type_mass ut --beta 0.5
```

If you dont have GPU, then set ```--cuda 0```. Please enjoy the **boosted performance** from the adversarial training with our flexible noise magnitude.

Please cite our paper if you see it is helpful at:
```
@inproceedings{tran2019adversarial,
  title={Adversarial Mahalanobis Distance-based Attentive Song Recommender for Automatic Playlist Continuation},
  author={Tran, Thanh and Sweeney, Renee and Lee, Kyumin},
  booktitle={Proceedings of the 42nd International ACM SIGIR Conference on Research and Development in Information Retrieval},
  pages={245-254},
  year={2019},
  organization={ACM}
}
```

