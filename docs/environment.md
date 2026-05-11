# Окружение эксперимента

## Хост-система

- **OS (host):** Windows 11
- **Гипервизор:** WSL2

## Linux-окружение (WSL2)

- **Дистрибутив:** Ubuntu 24.04.1 LTS (noble)
- **Ядро:** Linux 6.6.87.2-microsoft-standard-WSL2 (x86_64)

## Аппаратные ресурсы (видимые из WSL)

- **CPU:** 11th Gen Intel(R) Core(TM) i5-11400H @ 2.70GHz
- **Логических ядер:** 12
- **RAM:** 7.6 GiB total / 7.1 GiB available

### Замечание о бюджете масштабируемости

- При `nproc = 12` рабочие конфигурации эксперимента: **N ∈ {1, 2, 4, 8}**.
- N = 16 невозможно без oversubscribe; не используем.
- Из-за всего 7.6 GiB RAM граф для основного прогона должен помещаться целиком при N=1 (≤ ~3 GiB в `.gr` с запасом на
  репликацию). Это влияет на выбор датасета в Task 04 — Twitter/ClueWeb отпадают, **LiveJournal или Orkut** подходят, но
  при N=1 могут быть на грани; возможно стоит рассмотреть **rmat-22** или меньший SNAP-граф (например, `com-Orkut` ~1
  GiB `.gr`).

## Зависимости

| Инструмент     | Версия                                  |
|----------------|-----------------------------------------|
| gcc            | 13.3.0 (Ubuntu 13.3.0-6ubuntu2~24.04.1) |
| CMake          | 3.28.3                                  |
| Boost          | 1.83.0                                  |
| LLVM / libllvm | 14.0.6                                  |
| libfmt         | 9.1.0                                   |
| MPICH          | 4.2.0 (HYDRA launcher)                  |
| libnuma        | 2.0.18                                  |
| Python         | 3.12.3                                  |
| pandas         | 2.1.4                                   |
| matplotlib     | 3.6.3                                   |
| numpy          | 1.26.4                                  |


## Galois

- **Репозиторий:** https://github.com/IntelligentSoftwareSystems/Galois
- **Ветка/тег:** `master` (переход с release-5.0 ради distributed Triangle Counting + совпадения с исходной ссылкой в
  задании)
- **Ключевые флаги CMake:** `-DCMAKE_BUILD_TYPE=Release -DGALOIS_ENABLE_DIST=1`.
- **Структура:** distributed-приложения лежат в **`lonestar/analytics/distributed/`**.
- **Параметры сборки:** `make -j2` (не -j$(nproc)!) — при 7.6 GiB RAM параллелизм -j12 вызывает OOM и зависание WSL.


## Где лежат графы

Скрипт `03-prepare-data.sh` кладёт `.gr`/`.tgr`/`.sgr` в `~/graphs-data/` **внутри WSL**, а не в директорию проекта.
