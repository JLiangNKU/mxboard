# Logging MXNet Data for Visualization in TensorBoard


## Overview

MXBoard provides a set of APIs for logging
[MXNet](http://mxnet.incubator.apache.org/) data for visualization in
[TensorBoard](https://www.tensorflow.org/programmers_guide/summaries_and_tensorboard).
The idea of this project comes from discussions with [Zihao Zheng](https://github.com/zihaolucky),
the author of
[dmlc/tensorboard](https://github.com/dmlc/tensorboard),
on delivering a visualization solution for MXNet users.
We aim at providing the logging APIs that can process MXNet data efficiently
and supporting most of the data types for visualization in the TensorBoard GUI.
We adapted the following low-level logging components from their Python and C++
implementations in [TensorFlow](https://github.com/tensorflow/tensorflow): `FileWriter`, `EventFileWriter`,
`EventsWriter`, `RecordWriter`,
and `_EventLoggerThread`. We also adapted the user-level logging APIs defined in `SummaryWriter` from
[tensorboard-pytorch](https://github.com/lanpa/tensorboard-pytorch).
The encoding algorithm used in writing protobuf objects into event files
is directly borrowed from
[TeamHG-Memex/tensorboard_logger](https://github.com/TeamHG-Memex/tensorboard_logger).

MXBoard supports a set of Python APIs for logging the following data types
for TensorBoard to render. Logging APIs for other languages may be added in the future.

![mxboard_cover](https://github.com/reminisce/web-data/blob/add_mxboard_cover/mxnet/tensorboard/mxboard_cover.png)

The corresponding Python APIs are accessible through a class called `SummaryWriter` as follows:

```python
    mxboard.SummaryWriter.add_graph
    mxboard.SummaryWriter.add_scalar
    mxboard.SummaryWriter.add_histogram
    mxboard.SummaryWriter.add_embedding
    mxboard.SummaryWriter.add_image
    mxboard.SummaryWriter.add_text
    mxboard.SummaryWriter.add_pr_curve
    mxboard.SummaryWriter.add_audio
```


## Installation

### Install MXBoard from PyPI

```bash
pip install mxboard
```

### Install MXBoard Python package from source

```bash
git clone https://github.com/awslabs/mxboard.git
cd mxboard/python
python setup.py install
```


### Install TensorBoard from PyPI
MXBoard is a logger for writing MXNet data to event files.
To visualize those data in browsers, users still have to install
[TensorBoard](https://www.tensorflow.org/versions/r1.1/get_started/summaries_and_tensorboard)
separately.

```bash
pip install tensorflow tensorboard
```

Use the following to verify that the TensorBoard binary has been installed correctly.

```bash
tensorboard --help
```


### Other required packages
MXBoard relies on the following packages for data logging.
- [MXNet](https://pypi.python.org/pypi/mxnet)
- [protobuf3](https://pypi.python.org/pypi/protobuf)
- [six](https://pypi.python.org/pypi/six)
- [Pillow](https://pypi.python.org/pypi/Pillow)


## Visualizing MXNet data in 30 seconds
Now that you have installed all of the required packages, let's walk through a simple visualization example. You will see how
MXBoard enables visualizing MXNet `NDArray`s with histograms.

**Step 1.** Logging event data to a file.

Prepare a Python script for writing data generated by the `normal` operator to an event file.
The data is generated ten times with decreasing standard deviation and written to the event
file each time. It's expected to see the data distribution gradually become more centered around
the mean value. Note that here we specify creating the event file in the folder `logs`
under the current directory. We will need to pass this folder path to the TensorBoard binary.

```python
import mxnet as mx
from mxboard import SummaryWriter


with SummaryWriter(logdir='./logs') as sw:
    for i in range(10):
        # create a normal distribution with fixed mean and decreasing std
        data = mx.nd.normal(loc=0, scale=10.0/(i+1), shape=(10, 3, 8, 8))
        sw.add_histogram(tag='norml_dist', values=data, bins=200, global_step=i)
```

**Step 2.** Run TensorBoard on the event logs.
Use the following command to start the TensorBoard server. It will use the logs that were generated in the current directory's `logs` folder.

```bash
tensorboard --logdir=./logs --host=127.0.0.1 --port=8888
```
Note that in some situations,
the port number `8888` may be occupied by other applications and launching TensorBoard
may fail. You may choose a different available port number.

**Step 3.** Open TensorBoard in your browser.

In the browser, enter the address `127.0.0.1:8888`, and click the tab **HISTOGRAMS**
in the TensorBoard GUI. You will see data distribution changing as time progresses.

![summary_histogram_norm](https://github.com/dmlc/web-data/blob/master/mxnet/tensorboard/doc/summary_histogram_norm.png)

## More tutorials
- [Quick start for logging data of various types](https://github.com/awslabs/mxboard/blob/master/demo.md)
- [Monitoring training an MNIST model with MXBoard](https://github.com/awslabs/mxboard/blob/master/examples/mnist/train_mnist_mxboard.py)
- [Visualizing filters of ConvNets](https://github.com/reminisce/mxboard-demo#visualizing-filters-of-convnets)
- [Visualizing ConvNet codes as embeddings](https://github.com/reminisce/mxboard-demo#visualizing-convnet-codes-as-embeddings)

## References
1. https://github.com/TeamHG-Memex/tensorboard_logger
2. https://github.com/lanpa/tensorboard-pytorch
3. https://github.com/dmlc/tensorboard
4. https://github.com/tensorflow/tensorflow
5. https://github.com/tensorflow/tensorboard

## License
This library is licensed under the Apache 2.0 License.
