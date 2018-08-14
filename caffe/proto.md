参考链接：http://alanse7en.github.io/caffedai-ma-jie-xi-1/

# Caffe主要包含了4个大类:  
    Solver: An interface for classes that perform optimization on Nets
    Net: Connects Layers together into a directed acyclic graph (DAG) specified by a NetParameter
    Layer: An interface for the units of computation which can be composed into a Net
    Blob: A wrapper around SyncedMemory holders serving as the basic computational unit through which Layers, Nets, and Solvers interact
    Blob是一个包装类，作为基础的计算单元，提供层，网络，求解器间之间的内存同步。
 
 1. 其中Solver这个类实现了优化函数的封装，其中有一个protected的成员:shared_ptr<Net > net_;，
 这个成员是一个指向Net类型的智能指针（shared_ptr），Solver正是通过这个指针来和网络Net来交互并完成模型的优化。
 不同的子类分别实现了不同的优化方法：SGDSolver, NesterovSolver, AdaGradSolver, RMSPropSolver, AdaDeltaSolver和AdamSolver。
 2. 类似地Layer这个类派生出了很多子类，这些子类实现了Data的读取和Convolution, Pooling, InnerProduct等各种功能的layer。
 3. Net则是对整个网络的一个封装，其中有一个成员为:vector<shared_ptr<Layer > > layers_;，
 这个vector中包含了整个网络中每一层layer的智能指针，Net通过调用这些layer各自的forward()和backward()接口实现了网络整体的ForwardBackward()。
 4. Blob则是Caffe对数据的封装，在整个网络的计算中，不管是数据还是网络的参数和梯度都是这个类的对象，均为num\*channel\*width\*height形式的数据。  
-----
# .proto文件
> 除了清晰的代码结构，让Caffe变得易用更应该归功于Google Protocol Buffer的使用。
Google Protocol Buffer是Google开发的一个用于serializing结构化数据的开源工具  
      
> http://alanse7en.github.io/caffedai-ma-jie-xi-2/    
    
Caffe使用这个工具来定义Solver和Net，以及Net中每一个layer的参数。
这使得只是想使用Caffe目前支持的Layer(已经非常丰富了)来做一些实验或者demo的用户可以不去和代码打交道，
只需要在*.prototxt文件中描述自己的Solver和Net即可，再通过Caffe提供的command line interfaces就可以完成模型的train/finetune/test等功能。
   
-----------------
# 命令行接口  

    Google Flags的使用
    Register Brew Function的宏的定义和使用
    train()函数的具体实现
    SolverParameter的具体解析过程

## Google Flags的使用    
caffe的Command Line Interfaces一共提供了四个功能：train/test/time/device_query，而Interfaces的输入除了这四种功能还可以输入诸如-solver/-weights/-snapshot/-gpu等参数。这些参数的解析是通过Google Flags这个工具来完成的。  
```
DEFINE_string(gpu, "",
    "Optional; run in GPU mode on given device IDs separated by ','."
    "Use '-gpu all' to run on all available GPUs. The effective training "
    "batch size is multiplied by the number of devices.");
    这个宏的使用方式为DEFINE_xxx(name, default_value, instruction);，这样就定义了一个xxx类型名为FLAGS_name的标志，如果用户没有在Command Line中提供其值，那么会默认为default_value，instruction是这个标志含义的说明。上面的代码定义了一个string类型的名为FLAGS_gpu的标志。
    
```
解析这些标志的代码在caffe.cpp中的main()中调用了/CAFFE_ROOT/src/common.cpp中的GlobalInit(&argc, &argv)函数： 
```
void GlobalInit(int* pargc, char*** pargv) {
  // Google flags.
  ::gflags::ParseCommandLineFlags(pargc, pargv, true);
  // Google logging.
  ::google::InitGoogleLogging(*(pargv)[0]);
  // Provide a backtrace on segfault.
  ::google::InstallFailureSignalHandler();
}
```
## Register Brew Function的宏的定义和使用  
Caffe在Command Line Interfaces中一共提供了4种功能:train/test/time/device_query，分别对应着四个函数，这四个函数的调用是通过一个叫做g_brew_map的全局变量来完成的：  
```
typedef int (*BrewFunction)();
typedef std::map<caffe::string, BrewFunction> BrewMap;
BrewMap g_brew_map;
```
g_brew_map是一个key为string类型，value为BrewFunction类型的一个map类型的全局变量，BrewFunction是一个函数指针类型，指向的是参数为空，返回值为int的函数，也就是train/test/time/device_query这四个函数的类型。  

RegisterBrewFunction这个宏在每一个实现主要功能的函数之后将这个函数的名字和其对应的函数指针添加到了g_brew_map中，然后在main函数中，通过GetBrewFunction得到了我们需要调用的那个函数的函数指针，并完成了调用。

## train()函数的具体实现  
```c
// Train / Finetune a model.
int train() {
  //glog的CHECK_GT宏（含义为check greater than）
  // 如果小于或等于0则输出提示：”Need a solver definition to train”
  // 检查FLAGS_solver的size是否大于0确保提供对应的solver定义文件的路径
  CHECK_GT(FLAGS_solver.size(), 0) << "Need a solver definition to train.";
  //如果sanpshot与weight都有，那么提示只需要输入一个
  CHECK(!FLAGS_snapshot.size() || !FLAGS_weights.size())
      << "Give a snapshot to resume training or weights to finetune "
      "but not both.";
  vector<string> stages = get_stages_from_flags();

  //SolverParameter是通过Google Protocol Buffer自动生成的一个类
  caffe::SolverParameter solver_param;
  caffe::ReadSolverParamsFromTextFileOrDie(FLAGS_solver, &solver_param);

  solver_param.mutable_train_state()->set_level(FLAGS_level);
  for (int i = 0; i < stages.size(); i++) {
    solver_param.mutable_train_state()->add_stage(stages[i]);
  }

  // If the gpus flag is not provided, allow the mode and device to be set
  // in the solver prototxt.
  if (FLAGS_gpu.size() == 0
      && solver_param.has_solver_mode()
      && solver_param.solver_mode() == caffe::SolverParameter_SolverMode_GPU) {
      if (solver_param.has_device_id()) {
          FLAGS_gpu = "" +
              boost::lexical_cast<string>(solver_param.device_id());
      } else {  // Set default GPU if unspecified
          FLAGS_gpu = "" + boost::lexical_cast<string>(0);
      }
  }

  vector<int> gpus;
  //将存放在FLAGS_gpu中的string转成了一个vector，并完成了具体的设置。
  get_gpus(&gpus);
  if (gpus.size() == 0) {
    LOG(INFO) << "Use CPU.";
    Caffe::set_mode(Caffe::CPU);
  } else {
    ostringstream s;
    for (int i = 0; i < gpus.size(); ++i) {
      s << (i ? ", " : "") << gpus[i];
    }
    LOG(INFO) << "Using GPUs " << s.str();
#ifndef CPU_ONLY
    cudaDeviceProp device_prop;
    for (int i = 0; i < gpus.size(); ++i) {
      cudaGetDeviceProperties(&device_prop, gpus[i]);
      LOG(INFO) << "GPU " << gpus[i] << ": " << device_prop.name;
    }
#endif
    solver_param.set_device_id(gpus[0]);
    Caffe::SetDevice(gpus[0]);
    Caffe::set_mode(Caffe::GPU);
    Caffe::set_solver_count(gpus.size());
  }

  caffe::SignalHandler signal_handler(
        GetRequestedAction(FLAGS_sigint_effect),
        GetRequestedAction(FLAGS_sighup_effect));

  if (FLAGS_snapshot.size()) {
    solver_param.clear_weights();
  } else if (FLAGS_weights.size()) {
    solver_param.clear_weights();
    solver_param.add_weights(FLAGS_weights);
  }

  shared_ptr<caffe::Solver<float> >
      solver(caffe::SolverRegistry<float>::CreateSolver(solver_param));
//下面的代码声明并通过SolverRegistry初始化了一个指向Solver类型的shared_ptr。
// 并通过这个shared_ptr指明了在遇到系统信号(用户按了ctrl+c或者关闭了当前的terminal)时的处理方式。
  solver->SetActionFunction(signal_handler.GetActionFunction());
//判断是否有snapshot，如果有就从中读取已经训练好的参数，前面已经进行了权重的清空。如果有weight的执行策略在上方
  if (FLAGS_snapshot.size()) {
    LOG(INFO) << "Resuming from " << FLAGS_snapshot;
    solver->Restore(FLAGS_snapshot.c_str());
  }

  LOG(INFO) << "Starting Optimization";
//如果用户设置了要使用多个gpu，那么要声明一个P2PSync类型的对象，并通过这个对象来完成多gpu的计算
  if (gpus.size() > 1) {
#ifdef USE_NCCL
    caffe::NCCL<float> nccl(solver);
    nccl.Run(gpus, FLAGS_snapshot.size() > 0 ? FLAGS_snapshot.c_str() : NULL);
#else
    LOG(FATAL) << "Multi-GPU execution not available - rebuild with USE_NCCL";
#endif
  } else {
    //而如果是只使用单个gpu，那么就通过Solver的Solve()开始具体的优化过程
    solver->Solve();    // 开始训练网络
  }
  LOG(INFO) << "Optimization Done.";
  return 0;
}
RegisterBrewFunction(train);
```

## SolverParameter的具体解析过程  
SolverParameter是通过ReadSolverParamsFromTextFileOrDie来完成解析的，这个函数的实现在/CAFFE_ROOT/src/caffe/util/upgrade_proto.cpp里。
```c
// Read parameters from a file into a SolverParameter proto message.
void ReadSolverParamsFromTextFileOrDie(const string& param_file,
                                       SolverParameter* param) {
  CHECK(ReadProtoFromTextFile(param_file, param))
      << "Failed to parse SolverParameter file: " << param_file;
  UpgradeSolverAsNeeded(param_file, param);
}
这里调用了先后调用了两个函数，首先是ReadProtoFromTextFile，这个函数的作用是从param_file这个路径去读取solver的定义，并将文件中的内容解析存到param这个指针指向的对象，具体的实现在/CAFFE_ROOT/src/caffe/util/io.cpp。  
然后UpgradeSolverAsNeeded完成了新老版本caffe.proto的兼容处理
```
-------------
# Solver相关的代码   
    Solver的初始化（Register宏和构造函数）
    SIGINT和SIGHUP信号的处理
    Solver::Solve()具体实现
    SGDSolver::ApplyUpdate具体实现
## Solver的初始化（Register宏和构造函数）  





