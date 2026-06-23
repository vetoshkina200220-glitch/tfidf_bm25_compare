# TF-IDF vs BM25 — исследование

Один Jupyter notebook, разбирающий механизмы TF-IDF и BM25 (TF saturation,
нормализация длины, сглаживание IDF, гиперпараметры k1/b) и сравнивающий их
на реальном датасете NFCorpus (BEIR). Цель — не общий ML-бенчмарк, а разбор
КАЖДОГО компонента формул отдельно (плюсы/минусы) с графиками и выводами.

## Окружение

Работаем строго в локальном venv проекта. Глобальный Python не трогать.

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
python -m ipykernel install --user --name tfidf_bm25_venv
```

## Данные

NFCorpus (BEIR) — загружается прямо из notebook (Section 7), не вручную:

```python
from beir import util
from beir.retrieval.evaluation import GenericDataLoader
url = "https://public.ukp.informatik.tu-darmstadt.de/thakur/BEIR/datasets/nfcorpus.zip"
data_path = util.download_and_unzip(url, "datasets")
corpus, queries, qrels = GenericDataLoader(data_path).load(split="test")
```

Если зеркало BEIR недоступно — не подменять тихо на другой источник, сообщить.

## Структура notebook (`tfidf_vs_bm25.ipynb`)

Каждая секция — один H2 markdown-заголовок + код + финальная ячейка "Вывод:".

1. Введение и постановка задачи
2. Игрушечный корпус и базовые векторизации
3. TF: насыщение частоты термина (TF-спам)
4. Нормализация длины документа
5. IDF: сглаживание и редкие/частые термины
6. Гиперпараметры BM25: сетка по k1 и b (синтетика)
7. Загрузка NFCorpus
8. Индексация и ранжирование на NFCorpus
9. Оценка качества: Recall@k, MRR, nDCG@k
10. Влияние гиперпараметров на NFCorpus
11. Итоговое сравнение и общий вывод

## Валидация

1. Тест-импорт `from beir.retrieval.evaluation import EvaluateRetrieval` — если
   падает на `pytrec_eval`, реализовать Recall@k/MRR/nDCG@k вручную на numpy.
2. Проверить, что `BM25Okapi` принимает `k1`, `b`, `epsilon`, даёт `.idf`.
3. На игрушечном корпусе — оба метода должны ранжировать документ A выше D.
4. После Section 9 — вручную проверить топ-5 заголовков по 1-2 запросам.
5. Финал — "Restart Kernel and Run All", весь notebook должен выполняться без
   ошибок за разумное время (несколько минут на 3.6k документах).
