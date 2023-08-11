# Code verschieben

1. In der Datei `params.yaml` folgende Änderung durchführen:
    ```diff
    +base:
    +    log_level: INFO

    data:
        dataset_joblib: data/raw/data_digits.joblib
        features_path: data/processed/digits.csv
        test_size: 0.5
        train_path: data/train/train.csv
        test_path: data/test/test.csv
    train:
        svc_params:
            kernel: linear
            C: 0.025
        model_path: models/model.joblib

    reports:
        metrics_path: reports/metrics.json
        cm_plot_path: reports/confusion_matrix.png
    ```
1. Die Datei `src/utils.py` mit folgendem Inhalt erstellen:
    ```python
    import logging
    from typing import Text, Union
    import sys
    from pathlib import Path


    def use_file(path):
        _path = Path(path)
        _path.parent.mkdir(parents=True, exist_ok=True)
        return _path


    def get_console_handler() -> logging.StreamHandler:
        console_handler = logging.StreamHandler(sys.stdout)
        formatter = logging.Formatter(
            "%(asctime)s — %(name)s — %(levelname)s — %(message)s")
        console_handler.setFormatter(formatter)

        return console_handler


    def get_logger(name: Text = __name__, log_level: Union[Text, int] = logging.DEBUG) -> logging.Logger:
        logger = logging.getLogger(name)
        logger.setLevel(log_level)

        if logger.hasHandlers():
            logger.handlers.clear()

        logger.addHandler(get_console_handler())
        logger.propagate = False

        return logger
    ```

## Daten laden

1. Die Datei `src/data_load.py` mit folgendem Inhalt erstellen:
    ```python
    import argparse
    from sklearn import datasets, utils
    import yaml
    from typing import Text
    import joblib

    from src.utils import get_logger
    from src.utils import use_file


    def data_load(config_path: Text) -> utils.Bunch:
        """Load raw data.
        Args:
            config_path {Text}: path to config
        """

        with open(config_path) as conf_file:
            config = yaml.safe_load(conf_file)

        logger = get_logger('data_load', log_level=config['base']['log_level'])

        logger.info('Get dataset')
        digits = datasets.load_digits()

        logger.info('Save raw data')
        joblib.dump(digits, use_file(config["data"]["dataset_joblib"]))

        return digits


    if __name__ == '__main__':

        args_parser = argparse.ArgumentParser()
        args_parser.add_argument('--config', dest='config', required=True)
        args = args_parser.parse_args()

        data_load(config_path=args.config)
    ```
1. Skript testen mit folgendem Befehl:
    ```shell
    python3 src/data_load.py --config params.yaml
    ```

### In einem Jupyter Notebook testen

1. Ein neues Jupyter Notebook `notebooks/pipline.ipynb` erstellen
1. Folgender Code in eine Zelle einfügen:
    ```python
    %load_ext autoreload
    %autoreload 2

    import os
    import sys
    from pathlib import Path

    # change directory one level up if current working directory is 'notebooks'
    if (Path.cwd().name == 'notebooks'):
        %cd ..
    ```
1. Folgender Code in eine weitere Zelle einfügen:
    ```python
    from src.data_load import data_load
    digits = data_load('params.yaml')
    ```
1. Oder direkt aus dem Notebook heraus wie auf der Kommandozeile:
    ```python
    !python3 src/data_load.py --config=params.yaml
    ```
