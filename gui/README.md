# Intake: Discovering and exploring data in a graphical interface

![Intake GUI with plotting panel open](assets/gui_plot.gif)

## Motivation
Do you have data that you'd like people to be able to explore on their own?
Are you always passing around snippets of code to load specific data files?
These are problems that people encounter all the time when working in groups
and using the same datasources or when trying to distribute data to the public.
Some users are comfortable interacting with data entirely programatically, but
often it is helpful to use a GUI (Graphical User Interface) instead. With that
in mind we have reimplemented the Intake GUI so that in addition to working in a
[jupyter notebook](https://jupyter.org/), it can be served as a web application
next to your data, or at any endpoint.

## What is Intake?
[Intake](https://intake.readthedocs.io) is a lightweight set of tools for
loading and sharing data. You might have seen earlier [blog posts](https://www.anaconda.com/?s=intake).
Intake separates the concepts of the *data engineer* - the person curating,
managing, and disseminating data - from the *data user* - the person analyzing
and visualizing the data. The data engineer sets up catalog files describing
data sources and the data user loads data without needing to know how it is
stored.

## How to use it
To start using the GUI pip/conda install intake and panel. intake.GUI:

```python
import intake
intake.gui
```

## How it works (hint: Panel!)


## What it does


## What's next
We have ambitious plans for the future of this GUI. The vision is that one
day we will have a
