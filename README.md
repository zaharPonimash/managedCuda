Donate a beer to help me to keep managedCuda up to date :)



[![Flattr this git repo](http://api.flattr.com/button/flattr-badge-large.png)](https://flattr.com/submit/auto?user_id=kunzmi&url=https://github.com/kunzmi/managedCuda&title=managedCuda&language=&tags=github&category=software)
or
[![Support via PayPal](https://www.paypalobjects.com/en_GB/i/btn/btn_donate_SM.gif)](https://www.paypal.me/kunzmi/)


### Plans:
* Replace the console value of type "BadParam: An incorrect value or parameter was passed to the function." for exceptions


# managedCuda
ManagedCUDA aims an easy integration of NVidia's CUDA in .net applications written in C#, Visual Basic or any other .net language.

For this it includes:
- A complete wrapper for the  CUDA Driver API, version 10.0 (a 1:1 representation of cuda.h in C#) 
- Based on this, wrapper classes for CUDA context, kernel, device variable, etc. 
- Wrapper for graphics interop with DirectX and OpenGL, respectively SlimDX and OpenTK 
- CUDA vector types like int2, float3 etc. with ToString() methods and operators (+, –, *, /) 
- Define your own types: CudaDeviceVariable accepts any user defined type if it is a value type, i.e. a struct in C# 
- Includes most CUDA libraries: CUFFT, CURAND, CUSPARSE, CUBLAS, NPP and NVRTC
- Access device memory directly per element using [] operator:
```
CudaDeviceVariable<float> devVar = new CudaDeviceVariable<float>(64);
devVar[0] = 1.0f;
devVar[1] = 2.0f;
float hostVar1 = devVar[0];
float hostVar2 = devVar[1];
```
- Implicit converter operators: Allocate and initialize device or host arrays in only one line of code: 
```
float3[] array_host = new float3[100];
for (int i = 0; i < 100; i++)
{
	array_host[i] = new float3(i, i+1, i+2);
}
//alloc device memory and copy data:
CudaDeviceVariable<float3> array_device = array_host;
//alloc host array and copy data: 
float3[] array_host2 = array_device; 
```
- NPPs extension methods for CudaDeviceVariable. Add a reference to the NPP library and include the ManagedCuda.NPP.NPPsExtensions namespace: 
```
Random rand = new Random();
int length = 256;

//init some ramdom values
double[] randoms = new double[length];
for (int i = 0; i < length; i++)
{
	randoms[i] = rand.NextDouble();
}

//Alloc device memory
CudaDeviceVariable<double> a = randoms;
CudaDeviceVariable<double> b = new CudaDeviceVariable<double>(length);
b.Set(10.0); //NPPs method
int size = a.MeanGetBufferSize(); //NPPs method
//Alloc temporary memory for NPPs mean method
CudaDeviceVariable<byte> buffer = new CudaDeviceVariable<byte>(size);
CudaDeviceVariable<double> mean = new CudaDeviceVariable<double>(1);

a.Mul(b); //NPPs method
a.DivC(10.0); //NPPs method
a.Mean(mean, buffer); //NPPs method

//Copy data back to host
double m = mean;
double[] res = a;

//Clean up
mean.Dispose();
buffer.Dispose();
b.Dispose();
a.Dispose();
```
- Compiles for .net 2.0 and .net 4.0 (default), runs also on mono and Linux. 
- The new feature 'per thread default stream' is available as a compiler directive of the managedCuda main library: Compile the library with the option "_PerThreadDefaultStream" to enable it.
