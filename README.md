# VizTracer

[![build](https://github.com/gaogaotiantian/viztracer/workflows/build/badge.svg)](https://github.com/gaogaotiantian/viztracer/actions?query=workflow%3Abuild)  [![flake8](https://github.com/gaogaotiantian/viztracer/workflows/lint/badge.svg)](https://github.com/gaogaotiantian/viztracer/actions?query=workflow%3ALint)  [![readthedocs](https://img.shields.io/readthedocs/viztracer)](https://viztracer.readthedocs.io/en/stable/)  [![coverage](https://img.shields.io/codecov/c/github/gaogaotiantian/viztracer)](https://codecov.io/gh/gaogaotiantian/viztracer)  [![pypi](https://img.shields.io/pypi/v/viztracer.svg)](https://pypi.org/project/viztracer/)  [![support-version](https://img.shields.io/pypi/pyversions/viztracer)](https://img.shields.io/pypi/pyversions/viztracer)  [![license](https://img.shields.io/github/license/gaogaotiantian/viztracer)](https://github.com/gaogaotiantian/viztracer/blob/master/LICENSE)  [![commit](https://img.shields.io/github/last-commit/gaogaotiantian/viztracer)](https://github.com/gaogaotiantian/viztracer/commits/master)  [![twitter](https://img.shields.io/twitter/follow/viztracer?label=viztracer&style=flat&logo=twitter)](https://twitter.com/viztracer)

VizTracer is a low-overhead logging/debugging/profiling tool that can trace and visualize your python code execution.

You can take a look at the [demo](http://www.minkoder.com/viztracer/result.html) result of multiple example programs.
The UI is powered by [Chrome Trace Viewer](https://chromium.googlesource.com/catapult). **Use "AWSD" to zoom/navigate**.
More help can be found by clicking "?" on the top right corner.

[![example_img](https://github.com/gaogaotiantian/viztracer/blob/master/img/example.png)](https://github.com/gaogaotiantian/viztracer/blob/master/img/example.png)


## Highlights

* Detailed function entry/exit information on timeline with source code
* Super easy to use, no source code change for most features, no package dependency
* Optional function filter to ignore functions you are not interested
* Custom events to log and track arbitrary data through time
* Log arbitrary function/variable using RegEx without code change
* Stand alone HTML report with powerful front-end, or chrome-compatible json
* Works on Linux/MacOS/Windows

## Install

The prefered way to install VizTracer is via pip

```
pip install viztracer
```

## Basic Usage

### Command Line

Assume you have a python script to run:

```
python3 my_script.py arg1 arg2
```

You can simply use VizTracer by

```
viztracer my_script.py arg1 arg2
# Or use --open to open the report after execution
viztracer --open my_script.py arg1 arg2
```

which will generate a ```result.html``` file in the directory you run this command, which you can open with Chrome.

You can also generate ```json``` file or ```gz``` file and load it with [perfetto](https://ui.perfetto.dev/) or [chrome://tracing/](chrome://tracing/) or. ```gz``` file is especially helpful when your trace file is large

```
viztracer -o result.json my_script.py arg1 arg2
viztracer -o result.json.gz my_script.py arg1 arg2
# --open still works
viztracer -o result.json --open my_script.py arg1 arg2
viztracer -o result.json.gz --open my_script.py arg1 arg2
```

### Display Result

By default, VizTracer will generate a stand alone HTML file which you can simply open with Chrome.

Or, you can use ``vizviewer`` to open generated HTML/json/gz file.

```
# Open with chrome trace viewer that shows source code
vizviewer result.html
# Open with perfetto
vizviewer result.json
```

You can also pass ``--open`` to ``viztracer`` so it will automatically open the report after tracing

```
# Open with chrome trace viewer that shows source code
viztracer -o result.html --open my_script.py
# Open with perfetto
viztracer -o result.json --open my_script.py
```

As Chrome Trace Viewer is already deprecated, we will gradually lean towards [perfetto](https://ui.perfetto.dev/).

If you prefer Chrome Trace Viewer, you can use html output, or use chrome://tracing to load the json/gz file.

**When you are dealing with big traces, a stand alone HTML file might be very large and hard to load. You should try to dump a compressed ```filename.json.gz``` file**

### Inline

You can also manually start/stop VizTracer in your script as well.

```python
from viztracer import VizTracer

tracer = VizTracer()
tracer.start()
# Something happens here
tracer.stop()
tracer.save() # also takes output_file as an optional argument
```

Or, you can do it with ```with``` statement

```python
with VizTracer(output_file="optional.html") as tracer:
    # Something happens here
```

## Advanced Usage

### Trace Filter

VizTracer can filter out the data you don't want to reduce overhead and keep info of a longer time period before you dump the log.

* [Max Stack Depth](https://viztracer.readthedocs.io/en/stable/filter.html#max-stack-depth)
* [Include Files](https://viztracer.readthedocs.io/en/stable/filter.html#include-files)
* [Exclude Files](https://viztracer.readthedocs.io/en/stable/filter.html#exclude-files)
* [Ignore C Function](https://viztracer.readthedocs.io/en/stable/filter.html#ignore-c-function)
* [Sparse Log](https://viztracer.readthedocs.io/en/stable/filter.html#log-sparse)

### Extra Logs without Code Change

VizTracer can log extra information without changing your source code

* [Any Variable/Attribute with RegEx](https://viztracer.readthedocs.io/en/stable/extra_log.html#log-variable)
* [Function Entry](https://viztracer.readthedocs.io/en/stable/extra_log.html#log-function-entry)
* [Variables in Specified Function](https://viztracer.readthedocs.io/en/stable/extra_log.html#log-function-execution)
* [Garbage Collector Operation](https://viztracer.readthedocs.io/en/stable/extra_log.html#log-garbage-collector)
* [Function Input Arguments](https://viztracer.readthedocs.io/en/stable/extra_log.html#log-function-arguments)
* [Function Return Value](https://viztracer.readthedocs.io/en/stable/extra_log.html#log-function-return-value)
* [Raised Exceptions](https://viztracer.readthedocs.io/en/stable/extra_log.html#log-exception)

### Add Custom Event

VizTracer supports inserting custom events while the program is running. This works like a print debug, but you can know when this print happens while looking at trace data.

* [Instant Event](https://viztracer.readthedocs.io/en/stable/viztracer.html#VizTracer.add_instant)
* [Counter Event](https://viztracer.readthedocs.io/en/stable/custom_event.html#VizCounter)
* [Object Event](https://viztracer.readthedocs.io/en/stable/custom_event.html#VizObject)
* [Duration Event](https://viztracer.readthedocs.io/en/stable/custom_event_intro.html#duration-event)

## Misc

### Multi Thread Support

VizTracer supports python native ```threading``` module without the need to do any modification to your code. Just start ```VizTracer``` before you create threads and it will just work.

[![example_img](https://github.com/gaogaotiantian/viztracer/blob/master/img/multithread_example.png)](https://github.com/gaogaotiantian/viztracer/blob/master/img/multithread_example.png)


### Multi Process Support

VizTracer supports ```subprocess``` with ```--log_subprocess``` and ```multiprocessing``` or ```os.fork()``` with ```--log_multiprocess```.
For more general multi-process cases, VizTracer can support with some extra steps.

Refer to [multi process docs](https://viztracer.readthedocs.io/en/stable/concurrency.html) for details

### Async Support

VizTracer supports ```asyncio``` natively, but could enhance the report by using ```--log_async```. 

Refer to [async docs](https://viztracer.readthedocs.io/en/stable/concurrency.html) for details

### Remote attach

VizTracer supports remote attach to a process as long as you installed VizTracer on that process.

Refer to [remote attach docs](https://viztracer.readthedocs.io/en/stable/remote_attach.html)

### JSON alternative

VizTracer needs to dump the internal data to json format. It is recommended for the users to install ```orjson```, which is much faster than the builtin ```json``` library. VizTracer will try to import ```orjson``` and fall back to the builtin ```json``` library if ```orjson``` does not exist.

## Virtual Debug

You can virtually debug your program with you saved json report. The interface is very similar to ```pdb```. Even better, you can **go back in time**
because VizTracer has all the info recorded for you.

```
vdb <your_json_report>
```

Refer to the [docs](https://viztracer.readthedocs.io/en/stable/virtual_debug.html) for detailed commands

## Performance

VizTracer will introduce 2x to 3x overhead in the worst case. The overhead is much better if there are less function calls or if filters are applied correctly.

An example run for test_performance with Python 3.8 / Ubuntu 18.04.4 on Github VM

```
fib:
0.000678067(1.00)[origin]
0.019880272(29.32)[py] 0.011103901(16.38)[parse] 0.021165599(31.21)[json]
0.001344933(1.98)[c] 0.008181911(12.07)[parse] 0.015789866(23.29)[json]
0.001472846(2.17)[cProfile]

hanoi     (6148, 4100):
0.000550255(1.00)[origin]
0.016343521(29.70)[py] 0.007299123(13.26)[parse] 0.016779364(30.49)[json]
0.001062505(1.93)[c] 0.006416136(11.66)[parse] 0.011463236(20.83)[json]
0.001144914(2.08)[cProfile]

qsort     (8289, 5377):
0.002817679(1.00)[origin]
0.052747431(18.72)[py] 0.011339725(4.02)[parse] 0.023644345(8.39)[json]
0.004767673(1.69)[c] 0.008735166(3.10)[parse] 0.017173703(6.09)[json]
0.007248019(2.57)[cProfile]

slow_fib  (1135, 758):
0.028759652(1.00)[origin]
0.033994071(1.18)[py] 0.001630461(0.06)[parse] 0.003386635(0.12)[json]
0.029481623(1.03)[c] 0.001152415(0.04)[parse] 0.002191417(0.08)[json]
0.028289305(0.98)[cProfile]
```

## Documentation

For full documentation, please see [https://viztracer.readthedocs.io/en/stable](https://viztracer.readthedocs.io/en/stable)

## Bugs/Requests

Please send bug reports and feature requests through [github issue tracker](https://github.com/gaogaotiantian/viztracer/issues). VizTracer is currently under development now and it's open to any constructive suggestions.

## License

Copyright Tian Gao, 2020.

Distributed under the terms of the  [Apache 2.0 license](https://github.com/gaogaotiantian/viztracer/blob/master/LICENSE).
