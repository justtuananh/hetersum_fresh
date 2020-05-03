# HeterSumGraph

Code for ACL 2020 paper: [Heterogeneous Graph Neural Networks for Extractive Document Summarization](<https://arxiv.org/abs/2004.12393>)

Some code are borrowed from [Transformer](https://github.com/jadore801120/attention-is-all-you-need-pytorch). Thanks for their work.

## Dependencies 

- python 3.5+
- [PyTorch](https://pytorch.org/) 1.0
- [DGL](http://dgl.ai) 0.4
- [rouge](https://github.com/pltrdy/rouge) 1.0.0
  - A full Python Implementation of the ROUGE Metric which is used in validation phrase
- [pyrouge](https://github.com/bheinzerling/pyrouge) 0.1.3

- others
  - nltk
  - numpy
  - sklearn



## Data

We have preprocessed **CNN/DailyMail**, **NYT50** and **Multi-News** datasets for the TF-IDF features used in the graph creation, which you can find [here](https://drive.google.com/open?id=1oIYBwmrB9_alzvNDBtsMENKHthE9SW9z).

For **CNN/DailyMail** and **Multi-News**, we also provide the json-format datasets in [this link](https://drive.google.com/open?id=1JW033KefyyoYUKUFj6GqeBFZSHjksTfr).  However, due to the license, NYT(The New York Times Annotated Corpus) can only be available from [LDC](https://catalog.ldc.upenn.edu/LDC2008T19). And we follow the [preprocessing code](http://nlp.cs.berkeley.edu/projects/summarizer.shtml) of [Durrett et al. (2016)](http://nlp.cs.berkeley.edu/pubs/Durrett-BergKirkpatrick-Klein_2016_LearningSumm_paper.pdf) to get the **NYT50** datasets. 

The example looks like this:

```json
{
  "text":["deborah fuller has been banned from keeping animals ... 30mph",...,"a dog breeder and exhibitor... her dogs confiscated"],
  "summary":["warning : ... at a speed of around 30mph",... ,"she was banned from ... and given a curfew "],
  "label":[1,3,6]
}
```

and each line in the file is an example.  For the *text* key, the value can be list of string (single-document) or list of list of string (multi-document). The example in training set can ignore the *summary* key since we only use *label* during the training phrase. All strings need be lowercase and tokenized by [Stanford Tokenizer](https://nlp.stanford.edu/software/tokenizer.shtml).

After getting the standard json format, you can prepare the dataset for the graph by ***PrepareDataset.sh*** in the project directory. The processed files will be put under the ***cache*** directory.

The default file names for training, validation and test are: *train.label.jsonl*, *val.label.jsonl* and *test.label.jsonl*. If you would like to use other names, please change the according names in  ***PrepareDataset.sh***,  Line 322-323 in ***train.py*** and Line 188 in ***evaluation.py***. (We recommend default names)



## Train

For training, you can run commands like this:

```shell
python train.py --cuda --gpu 0 --data_dir <data dir of your json-format dataset> --cache_dir <cache directory of graph features> --model [HSG|HDSG] --save_root <model path> --log_root <log path> --lr_descent --grad_clip -m 3
```



We also provide our checkpoints on **CNN/DailyMail**, **NYT50** and **Multi-News** in [this link](https://drive.google.com/open?id=16wA_JZRm3PrDJgbBiezUDExYmHZobgsB).



## Test

For evaluation, the command may like this:

```shell
python evaluation.py --cuda --gpu 0 --data_dir <data dir of your json-format dataset> --cache_dir <cache directory of graph features> --model [HSG|HDSG] --save_root <model path> --log_root <log path> -m 3 --test_model multi --use_pyrouge
```

Some options:

- *use_pyrouge*: whether to use pyrouge for evaluation. Default is False (which means using rouge).

- *limit*: whether to limit the output to the length of gold summaries. This option is only set for evaluation on NYT50 (which uses ROUGE-recall instead of ROUGE-f). Default is False.
- *blocking*: whether to use Trigram blocking. Default is False.