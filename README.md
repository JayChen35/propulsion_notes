# Propulsion Notes
Fluid mechanics is notoriously frustrating to master, and I've found myself at numerous points learning 
and re-learning the same concepts. This is my attempt at compiling everything I've learned at SpaceX and 
[MASA](https://masa.engin.umich.edu/), and (hopefully) explaining it in a way that is practical to the 
discipline of stage propulsion, and most importantly, intuitive. I hope you find it useful!

## Working with Jupyter Books
- I've installed `jupyter-book` via `pip3` per the [overview instructions](https://jupyterbook.org/en/stable/start/overview.html)
- Then, build the book via 
```
jb build [path_to_book]
```
- I've set up a Github Action to automate updates to my website at https://jaychen35.github.io/propulsion_notes once a new notebook build is git pushed to this repository
