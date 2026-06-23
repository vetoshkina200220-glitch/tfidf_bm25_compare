# TF-IDF vs BM25 — покомпонентное исследование

**Языки:** [English](README.md) | Русский

Один Jupyter-ноутбук, который разбирает механику **TF-IDF** и **BM25** и сравнивает
их на реальном датасете для поиска — [NFCorpus](https://www.cl.uni-heidelberg.de/statnlpgroup/nfcorpus/)
из бенчмарка [BEIR](https://github.com/beir-cellar/beir).

Цель — **не** общий ML-бенчмарк, а аккуратный разбор *каждого* компонента двух
формул ранжирования: с графиками, интуицией и выводами:

- **Насыщение TF** — почему «сырая» частота термина переоценивает «TF-спам»
- **Нормализация длины документа** — справедливый штраф длинным документам
- **Сглаживание IDF** — работа с редкими и очень частыми терминами
- **Гиперпараметры BM25** `k1` и `b` — сетка на синтетике и на реальных данных

## Что внутри

Ноутбук `tfidf_vs_bm25.ipynb` состоит из 11 секций. Каждая секция — это один
H2-заголовок → код → финальная ячейка **«Вывод:»**.

1. Введение и постановка задачи
2. Игрушечный корпус и базовые векторизации
3. TF: насыщение частоты термина (TF-спам)
4. Нормализация длины документа
5. IDF: сглаживание и редкие/частые термины
6. Гиперпараметры BM25: сетка по `k1` и `b` (синтетика)
7. Загрузка NFCorpus
8. Индексация и ранжирование на NFCorpus
9. Оценка качества: Recall@k, MRR, nDCG@k
10. Влияние гиперпараметров на NFCorpus
11. Итоговое сравнение и общий вывод

## Установка

Проект использует **локальный виртуальный venv**. Глобальный Python не трогаем.

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
python -m ipykernel install --user --name tfidf_bm25_venv
```

Затем запустите Jupyter и откройте ноутбук:

```bash
jupyter notebook tfidf_vs_bm25.ipynb
```

Выберите ядро `tfidf_bm25_venv`.

## Данные

NFCorpus **загружается прямо из ноутбука** (Секция 7) — вручную скачивать ничего
не нужно:

```python
from beir import util
from beir.retrieval.evaluation import GenericDataLoader

url = "https://public.ukp.informatik.tu-darmstadt.de/thakur/BEIR/datasets/nfcorpus.zip"
data_path = util.download_and_unzip(url, "datasets")
corpus, queries, qrels = GenericDataLoader(data_path).load(split="test")
```

Датасет (~3.6k документов) и папка `datasets/` **не коммитятся** в репозиторий —
они воспроизводятся во время выполнения.

> **Про `pytrec_eval`:** если `from beir.retrieval.evaluation import EvaluateRetrieval`
> не импортируется (частая проблема сборки `pytrec_eval`), ноутбук переключается на
> ручные реализации Recall@k, MRR и nDCG@k на NumPy.

## Воспроизведение

Для чистого прогона от начала до конца используйте **«Restart Kernel and Run All»**.
Весь ноутбук должен выполняться без ошибок за несколько минут на ~3.6k документах.

## Структура проекта

```
tfidf_bm25/
├── tfidf_vs_bm25.ipynb   # само исследование (главный артефакт)
├── requirements.txt      # зафиксированные зависимости
├── CLAUDE.md             # заметки по проекту / договорённости о работе
├── LICENSE               # MIT
└── README.md / README.ru.md
```

## Лицензия

Распространяется под [лицензией MIT](LICENSE).
