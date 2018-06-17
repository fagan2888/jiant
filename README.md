# JSALT: *J*iant (or *J*SALT) *S*entence *A*ggregating *L*earning *T*hing
This repo contains the code for jiant sentence representation learning model for the 2018 JSALT Workshop.

## Dependencies

Make sure you have installed the packages listed in environment.yml.
When listed, specific particular package versions are required.
If you use conda, you can create an environment from this package with the following command:

```
conda env create -f environment.yml
```

Note: The version of AllenNLP available on pip may not be compatible with PyTorch 0.4, in which we recommend installing from [source](https://github.com/allenai/allennlp).

## Downloading data

The repo contains a convenience python script for downloading all GLUE data and standard splits.

```
python download_glue_data.py --data_dir glue_data --tasks all
```

After downloading GLUE, point ``PATH_PREFIX`` in  ``src/preprocess.py`` to the directory containing the data.

For other pretraining task data, contact the person in charge.

## Running

To run things, use ``src/main.py`` or ``run_stuff.sh``..
Because preprocessing is expensive (particularly for ELMo) and we often want to run multiple experiments using the same preprocessing, we use an argument ``--exp_dir`` for sharing preprocessing between experiments. We use argument ``--run_dir`` to save information specific to a particular run, with ``run_dir`` usually nested within ``exp_dir``.

To force rereading and reloading of the tasks, perhaps because you changed the format, use the flag ``--reload_tasks 1``.
To force rebuilding of the vocabulary, perhaps because you want to include vocabulary for more tasks, use the flag ``--reload_vocab 1``.

```
python main.py --exp_dir EXP_DIR --run_dir RUN_DIR --train_tasks all --word_embs_file PATH_TO_GLOVE
```

## Adding New Tasks

To add new tasks, you should:
- Create a class in ``src/tasks.py``, making sure that:
    - Your task inherits from existing classes as necessary (e.g. ``PairClassificationTask``, ``SequenceGenerationTask``, etc.).
    - The task definition should also include the data loader, as a method called ``load\_data()``.
    - Your task should include an attributes ``task.sentences`` that is a list of all text to index.
- In ``src/model.py``, make sure that:
    - The correct task-specific module is being created for your task in ``build_module()``.
    - Your task is correctly being handled in ``forward()`` of ``MultiTaskModel``. Create additional methods or add branches to existing methods as necessary. If you do add additional methods, make sure to make use of the ``sent_encoder`` attribute of the model, which is shared amongst all tasks.
- In ``src/preprocess.py``, make sure that:
    - The correct task-specific preprocessing is being used for your task in ``process_task()``

## Pretrained Embeddings

### GloVe

Many of our models make use of [GloVe pretrained word embeddings](https://nlp.stanford.edu/projects/glove/), in particular the 300-dimensional, 840B version.
To use GloVe vectors, download and extract the relevant files and set ``word_embs_file`` to the GloVe file.
To learn embeddings from scratch, set ``--glove`` to 0.

### CoVe

We use the CoVe implementation provided [here](https://github.com/salesforce/cove).
To use CoVe, clone the repo and fill in ``PATH_TO_COVE`` in ``src/models.py`` and set ``--cove`` to 1.

### ELMo

We use the ELMo implementation provided by [AllenNLP](https://github.com/allenai/allennlp/blob/master/tutorials/how_to/elmo.md).
To use ELMo, set ``--elmo`` to 1. To use ELMo without GloVe, additionally set ``--elmo_no_glove`` to 1.

### FastText

TODO.

## Getting Help

Feel free to contact alexwang _at_ nyu.edu with any questions or comments.
