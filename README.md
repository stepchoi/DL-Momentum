## Dependencies
- Python 3.6+, Postgres, Unix (tested on Ubuntu)
- `pip install -r requirements.txt`
- Install hyperopt [from source](http://hyperopt.github.io/hyperopt/#installation)

## Setup
1. `createdb dl_price_mom`
1. `cp config.example.json config.json` then modify `config.json`
1. Sign into [Kaggle](https://www.kaggle.com), download [this dataset](https://www.kaggle.com/borismarjanovic/price-volume-data-for-all-us-stocks-etfs/home)
1. Unzip
    ```
    unzip price-volume-data-for-all-us-stocks-etfs.zip -d tmp
    pushd tmp
    unzip Data.zip
    popd
    ```

The important additions to your dir structure should be:

```
- config.json (modified)
- tmp
  - Stocks 
```

#### Import
`python import.py`

This will populate your database from the Kaggle dataset, it will take a few hours.

#### Note re: dataset
We have arrange the code to work with the Kaggle dataset but there are few important provisions:
1. Kaggle data was NOT used for our analysis. Our research is based on proprietary licensed data from FTSE Russell which we cannot provide by the terms of our agreement.
2. Kaggle does not specify the investment universe, which was sourced from FTSE Rusell database for the Russell 1000-based investment universe.

## Run
You'll train three separate components to completion (they depend on each other sequentially):
1. Autoencoder
2. Embedded Clustering
3. Recurrent Neural Network (GRU/FFN)

Each component can take 6h or more to run; EmbedClust, in particular, takes multiple days. Run each step in a tmux session and check back in 24h. Between each step (after completion), you'll choose optimal autoencoder configurations or embedded clusterings.

1. `python ae.py` - runs hyperopt for autoencoding of the data 
2. `python select_winners.py` - selects optimal hyperparameter configurations for autoencoder.
3. `python embed_clust.py` - runs embedded clustering on the data for origins and k clusters based on the selected autoencoder.  
4. Manually select embed_clust clustering outputs for each origin (set `use` to `TRUE`), based on normalized X_B or S_Dbw scores.
5. `python rnn.py` - runs GRU/FFN based on selected optimal embedded clusterings.

## Credit
- Deep Clustering with Convolutional Autoencoders (DCEC)
  - Code: [XifengGuo/DCEC](https://github.com/XifengGuo/DCEC)
  - Paper: [Unsupervised Deep Embedding for Clustering Analysis](https://xifengguo.github.io/papers/ICONIP17-DCEC.pdf)
- S_Dbw
  - Code: [iphysresearch/S_Dbw_validity_index](https://github.com/iphysresearch/S_Dbw_validity_index)
  - Paper: [Clustering Validity Assessment: Finding the optimal partitioning of a data set ](https://ieeexplore.ieee.org/document/989517/)
- Xie-Beni index (XB)
  -  Paper: [A validity measure for fuzzy clustering](https://ieeexplore.ieee.org/document/85677/)

