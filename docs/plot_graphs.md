How to plot graphs comparing runs ?
===

Plotting graphs is used only for multi-run experiments.
```
cd /home/binsec-ase/artefact/plotting_graphs
```

# Plotting experiment results to get paper graphs

Since the full experiment considered in this paper takes more than a week with 40 CPU cores to run, we simply provide the .csv file aggregating the raw results of said experiment to showcase its transformation into the graphs in the paper. You will find it in `plotting_graphs/records_paper`.

Simply launch
```
python3 plot_graphs.py ./records_paper/raw_paper_stats.csv paper
```
to get the graphs in `plotting_graphs/figures`, prefixed by `paper`. The analysis time graphs are labelled `AT`, the explored paths are labelled `EP`, the average solving time per query graphs are labelled `ST` and the number of query sent graph is prefixed `QS`. Graphs without the Forking technique which highlight the differences between our variants are labelled `zoom`.

# Plotting your own results

To plot your own results instead, use the aggregating script. For instance, if you want to get the results from a multi-run called `check_7.3`, use the following command.
```
bash concat_and_move_csv.sh check_7.3
```
It will aggregate each individual .csv files into a big one and place it in `plotting_graphs/records_artefact/` with the name `raw_check_7.3.csv`.

Then launch the plotting script
```
python3 plot_graphs.py ./records_artefact/raw_check_7.3.csv check_7.3
```