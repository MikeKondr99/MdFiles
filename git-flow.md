# Git Flow: release/dev/feature/bug/hotfix + QA

Этот документ описывает предлагаемый git flow, зачем он нужен и как в нем
появляются точки QA. Диаграммы сделаны отдельно для каждого сценария.

## Зачем такая схема

- `release` хранит текущий стабильный релиз и покрывается тегами.
- `dev` — основная ветка разработки следующего минорного релиза.
- Из `dev` ведутся фичи и баги, чтобы изолировать работу и упростить ревью.
- `hotfix` позволяет быстро исправлять критичные проблемы в уже выкачанном
  релизе.

## Базовые ветки и теги

```mermaid
---
config:
  gitGraph:
    mainBranchName: release
---
gitGraph
  commit
  commit tag: "v1.33.4"
  branch dev
  commit id: "баги"
  commit id: "фичи"
  checkout release
  merge dev
  commit tag: "v1.33.5"
```

## Фича: работа в ветке + QA до слияния

QA для фичи проводится в самой фиче перед ее слиянием в `dev`.

```mermaid
---
config:
  gitGraph:
    mainBranchName: release
---
gitGraph
  commit
  commit tag: "v1.33.4"
  branch dev
  commit
  branch feature/analytics
  branch feature/analytics-fe
  commit id: "feature: FE part"
  checkout feature/analytics
  merge feature/analytics-fe
  commit tag: "QA тестирование фичи"
  checkout dev
  merge feature/analytics
  commit tag: "Здесь можно сделать уборку?"
  checkout release
  merge dev
  commit tag: "v1.33.5"
```

## Баг: ветка от dev + QA после слияния

QA для бага проводится после слияния в `dev`.

```mermaid
---
config:
  gitGraph:
    mainBranchName: release
---
gitGraph
  commit
  commit tag: "v1.33.4"
  branch dev
  commit
  branch bug/bug1
  commit id: "feature: FE part"
  checkout dev
  merge bug/bug1
  commit tag: "QA тестирование бага"
  checkout release
  merge dev
  commit tag: "v1.33.5"
```

## Hotfix: быстрый ремонт релиза (TODO для QA)

Hotfix делается, когда текущий минорный релиз критично сломан.

```mermaid
---
config:
  gitGraph:
    mainBranchName: release
---
gitGraph
commit
commit tag: "v1.33.4"

branch hotfix
commit
checkout release

branch dev
commit
commit


checkout release
merge hotfix tag: "1.33.4-hotfix1"
checkout dev
merge hotfix


checkout dev
commit

checkout release
merge dev
commit tag: "v1.33.5"
```

**TODO: Определить, где и как проходит QA hotfix**

## Минорный релиз: freeze dev и merge в release

Перед слиянием `dev` в `release` нужен фриз `dev` и полное тестирование
фич и багов, вошедших в минорную версию.

```mermaid
---
config:
  gitGraph:
    mainBranchName: release
---
gitGraph
  commit
  commit tag: "v1.33.4"
  branch dev
  commit id: "баги"
  commit id: "фичи"
  checkout release
  merge dev
  commit tag: "QA минор релиз"
  commit
  commit tag: "v1.33.5"
```
