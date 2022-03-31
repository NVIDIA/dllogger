# DLLogger for Python
# DLLogger - minimal logging tool

This project emerged from the need for a unified logging schema for Deep Learning Example modules. It provides a simple, extensible, and intuitive logging capabilities with API trimmed to an absolute minimum.

## Table Of Contents

- [Installation](#installation)
- [Quick Start Guide](#quick-start-guide)
- [Available backends overview](#available-backends-overview)
  * [StdOutBackend](#stdoutbackend)
  * [JSONStreamBackend](#jsonstreambackend)
- [Advanced usage](#advanced-usage)
  * [Multiple loggers](#multiple-loggers)


## Installation

To install DLLogger, run (separate commands):

```bash
pip install git+https://github.com/NVIDIA/dllogger#egg=dllogger
```

## Quick Start Guide

To start using DLLogger, add the following two lines of code to your DL training script:

```python
from dllogger import StdOutBackend, JSONStreamBackend, Verbosity
import dllogger as DLLogger

DLLogger.init(backends=[
    StdOutBackend(Verbosity.DEFAULT),
    JSONStreamBackend(Verbosity.VERBOSE, "log.json"),
])
```

To log data, call `DLLogger.log(step=<TRAINING_STEP>, data=<DATA>, verbosity=<VERBOSITY>)`

Where:

- `<TRAINING_STEP>` can be any number/string/tuple which would indicate where are we in the training process.

- Use `step="PARAMETER"` for script parameters (everything that is needed to reproduce the result).

- Use a tuple of numbers to indicate the training progress, for example:

  - `step=tuple(epoch_number, iteration_number, validation_iteration_number)` for a `validation_iteration_number` in a validation that happens after iteration `iteration_number` of epoch `epoch_number`

  - `step=tuple(epoch_number,)` for a summary of epoch `epoch_number`

  - `step=tuple()` for a summary of the entire training

- `<DATA>` should be a dictionary with metric names as keys and metric values as values.

To log metric metadata, for example, unit, description, ordering, and format, call `DLLogger.metadata(metric_name, metric_metadata)` where `metric_metadata` is a dictionary.

Backends can use the metadata information for logging purposes, for example, `StdOutBackend` uses the `format` and `unit` fields to format its output.

The log is automatically saved on the exit of the Python process (with exception of processes killed with `SIGKILL`). To flush the log file before training ends, run:

```
DLLogger.flush()
```

For usage examples, refer to the `examples/dllogger_example.py` and `examples/dllogger_singleton_example.py` files.

## Available backends overview

DLLogger can use multiple backends for logging, for example, with one logging call, the logged data can be saved or printed in multiple formats.

### StdOutBackend

A vanilla backend that holds no buffers; and that prints the provided values to `stdout`.

```python
StdOutBackend(verbosity, step_format=..., metric_format=...)
```

Where:

- `step_format` is a function that formats step in the `DLLogger.log` call.

- `metric_format` is a function that formats a metric name and value given its metadata

For more information, see the `default_*_format` functions in the `dllogger/logger.py` script.

For example output, refer to the `examples/stdout.txt` file.


### JSONStreamBackend

This backend saves JSON formatted lines into a file, adding time stamps for each line.

```python
JsonBackend(verbosity, file_name)
```

For example output, refer to the `examples/dummy_resnet_log.json` file.


## Advanced usage

By default, DLLogger takes advantage of the singleton pattern.
It is possible to obtain a logger instance without referencing the DLLogger global instance,
for example, to create multiple and different logs from one script.

```python
from dllogger import Logger
logger = Logger(backends=BACKEND_LIST)
```
