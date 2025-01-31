# SYCL Academy

### Exercise 2: Configuring a Queue

---

In this first exercise you will learn:
* How to construct a queue using a device selector.
* How to define and use your own device selector.
* How to query information about a device.

---

The first thing you must do in a SYCL application is to construct a queue that
will enqueue work, and a queue is associated with a single device to which it
enqueues work. The simplest way to construct a queue in SYCL is to pass it a
device selector, that is then used to choose a device from all the devices
available in your system.

1.) Create a queue using the default selector

Construct a SYCL queue using the default constructor and try printing the name
of the device.

Remember the device associated with a queue can be retrieved using the
`get_device` member function and information about a device can be queried
using the `get_info` member function template.

2.) Try other device selectors

Replace the default selector with one of the other standard device selectors
that are provided by SYCL (see [SYCL 1.2.1 specification][sycl-specification],
sec. 4.6.1.2) and see which device those choose.

3.) Create your own device selector

Create a device selector using the template below, implementing the function
call operator, using various device and platform info queries like the one we
used earlier to query the device name (see
[SYCL 1.2.1 specification][sycl-specification], sec. 4.6.4.2) and then use that
device selector in the queue constructor:

```
class my_device_selector : public device_selector {
 public:
  my_device_selector() {}

  virtual int operator()(const device &device) const { }
};
```

Remember the platform associated with a device can be retrieved using the
`get_platform` member function.

Remember that the value returned from the device selector's function call
operator will represent the score for each device, and a device with a negative
score will never be chosen. 

#### Build And Execution Hints

For For DPC++ (using the Intel DevCloud):
```
dpcpp -o sycl-ex-2 -I../External/Catch2/single_include ../Code_Exercises/Exercise_2_Configuring_a_Queue/source.cpp
```
In Intel DevCloud, to run computational applications, you will submit jobs to a queue for execution on compute nodes,
especially some features like longer walltime and multi-node computation is only abvailable through the job queue.
Please refer to the [example][devcloud-job-submission].

So wrap the binary into a script `job_submission` and run:
```
qsub job_submission
```

For ComputeCpp:
```
cmake -DSYCL_ACADEMY_USE_COMPUTECPP=ON -DSYCL_ACADEMY_INSTALL_ROOT=/insert/path/to/computecpp ..
make Exercise_2_source
./Code_Exercises/Exercise_2_Configuring_a_Queue/Exercise_2_source
```


For hipSYCL:
```
# Add -DHIPSYCL_GPU_ARCH=<arch> to the cmake arguments when compiling for GPUs.
# <arch> is e.g. sm_60 for NVIDIA Pascal GPUs, gfx900 for AMD Vega 56/64, and gfx906 for Radeon VII.
cmake -DSYCL_ACADEMY_USE_HIPSYCL=ON -DSYCL_ACADEMY_INSTALL_ROOT=/insert/path/to/hipsycl -DHIPSYCL_PLATFORM=<cpu|cuda|rocm> ..
make Exercise_2_source
./Code_Exercises/Exercise_2_Configuring_a_Queue/Exercise_2_source
```
alternatively, without cmake:
```
cd Code_Exercises/Exercise_2_Configuring_a_Queue
HIPSYCL_PLATFORM=<cpu|cuda|rocm> HIPSYCL_GPU_ARCH=<arch-when-compiling-for-gpu> /path/to/hipsycl/bin/syclcc -o sycl-ex-2 -I../../External/Catch2/single_include source.cpp
./sycl-ex-2
```



[sycl-specification]: https://www.khronos.org/registry/SYCL/specs/sycl-1.2.1.pdf
[devcloud-job-submission]: https://devcloud.intel.com/oneapi/learn/advanced-queue/basic-job-submission#command-file-job-script-