## OPAMP_Generator的说明

此目录下包含了OPAMP-Generator中的拓扑优化，以及晶体管级电路自动映射方法的相关代码，其中：

### topo_opt（拓扑优化）

1：运行代码前请确保Python（推荐3.6.13）环境中已安装“torch”(推荐1.10.2)和“pygraphviz”（推荐1.6）库，以及其他常用科学计算库；

2：VGAE模型的训练主程序为train.py，可以通过'source run_train.sh'命令执行训练，或者直接执行命令：'python -W ignore train.py --model DVAE_test2 --data_file dataset_withoutY_1w --batch_size 16 --save-appendix _1w --lr 1e-5 --gpu 1';

3: 进行拓扑优化的主程序为optimization_bfgs.py，可以通过'source run_opt.sh'命令执行优化，或者直接执行命令：'python -W ignore optimization_bfgs.py --iteration 30 --save-appendix _Bfgs_exp1_bound15 --nz 10 --which_gp sklearn --load_model_path _nz10_1w_demo --load_model_name 400 --emb_bound 15 --bfgs_time 200 --samping_ratio 0.0001 --gpu 3';

4：VGAE模型的训练结果将会被保存在results文件夹下，其下已有一个训练好的demo；拓扑优化的结果将会被保存在opt_results及tmp_circuits文件夹下，电路demo可以参阅../schematic_mapping/behv_circuits。

### schematic_mapping（晶体管级电路自动映射）

1：运行代码前请确保Python（推荐3.6.13）环境中已安装“hebo”和“pymoo”库，以及其他常用科学计算库；

2：本程序针对tsmc18工艺适用，tsmc18rf_1p6m_lut下给出了gmid方法需要用到的查找表，但是运行前还需将相应的晶体管模型文件（rf018.scs）拷至本目录下；

3：主程序为main.py，运行命令为：'python -W ignore main.py --TITLE --MAP --SIM --OUT --REOPT1 --intrinsic_gain_times 1'；

4：需要映射的行为级电路模型需要放置在behv_circuits目录下，映射完成的晶体管级电路会生成在mapped_circuits下，论文中展示的三个电路已经放置在相应目录下。
    注意：在行为级模型以及晶体管级电路的网表中，器件尺寸皆为BO程序最后一轮迭代的尝试，所以对其直接进行仿真可能会和论文中展示的最优结果无法对应。

## sdm_topo_dse的说明

此目录下包含了提出的针对delta-sigma调制器的高层次拓扑综合方法的相关代码，其中：

1：运行代码前请确保Python（推荐3.6.13）环境中已安装“hebo”和“matlab.engine”库，以及其他常用科学计算库；

2：运行前请将kill_matlab.sh脚本中的'user'替换为自己的用户名；

3：主程序为optimization.py，运行命令为：'python -W ignore optimization.py'；

4：所有的调制器拓扑及性能结果可在database下的相应html格式文件内查看；

5：满足约束的调制器simulink文件会被存储在results文件夹下；

6：case_study路径下给出了一个示例调制器的高层次模型（adc_test.slx），可以用过simulink打开查看及仿真。

## testbench的说明

此目录下的behv_circuits文件夹中包含了32个由自动拓扑优化程序搜索得到的行为级三级运算放大器电路，经由schematic_mapping程序可以自动转化为晶体管级的电路网表，并进行sizing优化。

经过测试，在.18工艺下，所有的32个行为级电路在进行晶体管级映射后，均可达到一下电路规格：

       prb_constr0     gain      >= 85  dB    

       prb_constr1     pm        >= 55        

       prb_constr2     gbw       >= 0.7 MHz   

       prb_constr3     power     <= 250 uW    

       goal            fom_s     maximize     

## 注意事项：

1：拓扑优化及晶体管级电路映射都需要调用Cadence Spectre仿真器仿真，请确保环境变量里配置了相应软件，推荐ic615版本；

2：VGAE神经网络的训练需要GPU及cuda环境，推荐使用CUDA 11.0；

3：delta-sigma调制器的高层次拓扑综合需要MATLAB及Simulink，MATLAB推荐2017.b版本，此外还需要安装SDToolbox2工具箱用于调制器的建模及仿真，
    工具箱可在https://www.mathworks.com/matlabcentral/fileexchange/25811-sdtoolbox-2/下找到；

4：testbench中的晶体管映射代码与schematic_mapping路径下的相同，但是对电路中的R及C的尺寸自动计算部分尚未支持，如若需要对接电路版图的自动生成流程，需要根据工艺手动确定R和C的尺寸。