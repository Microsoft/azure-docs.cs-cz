---
title: 알고리즘 및 모듈 참조
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 시각적 인터페이스에서 사용할 수 있는 모듈에 대 한 자세한 정보
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 2d81c407c17c50c34afd15a99d2f8ac2f8c5361e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692842"
---
# <a name="algorithm--module-reference-overview"></a>알고리즘 & 모듈 참조 개요

이 참조 콘텐츠는 Azure Machine Learning 서비스의 시각적 인터페이스 (미리 보기)에서 사용할 수 있는 각 기계 학습 알고리즘 및 모듈에 대 한 기술 배경을 제공 합니다.

각 모듈은 필요한 입력을 고려 하 여 독립적으로 실행 하 고 machine learning 작업을 수행할 수 있는 코드 집합을 나타냅니다. 모듈은 특정 알고리즘을 포함 하거나 기계 학습에서 중요 한 작업 (예: 누락 된 값 대체 또는 통계 분석)을 수행할 수 있습니다.

> [!TIP]
> 시각적 인터페이스의 모든 파이프라인에서 특정 모듈에 대 한 정보를 가져올 수 있습니다. 모듈을 선택한 다음 **빠른 도움말** 창에서 **추가 도움말** 링크를 선택 합니다.

## <a name="modules"></a>모듈

모듈은 기능별로 구성 됩니다.

| 기능 | 설명 | 모듈 |
| --- |--- | ---- |
| 데이터 형식 변환 | 기계 학습에 사용 되는 다양 한 파일 형식 간에 데이터 변환 | [CSV로 변환](convert-to-csv.md) |
| 데이터 입력 및 출력 | 클라우드 원본에서 실험으로 데이터를 이동 합니다. 실험을 실행 하거나 클라우드 저장소를 사용 하 여 실험 간에 데이터를 교환 하는 동안 결과 또는 중간 데이터를 Azure Storage, SQL database 또는 Hive에 씁니다.  | [데이터 가져오기](import-data.md)<br/>[데이터 내보내기](export-data.md)<br/>[수동으로 데이터 입력](enter-data-manually.md) |
| 데이터 변환 | 데이터 표준화 또는 범주화, 기능 선택, 차원 감소 등 기계 학습에 고유한 데이터에 대 한 작업입니다.| [데이터 집합에서 열 선택](select-columns-in-dataset.md) <br/> [메타 데이터 편집](edit-metadata.md) <br/> [누락 데이터 정리](clean-missing-data.md) <br/>  [기능 해시](feature-hashing.md) <br/>  [텍스트에서 N 개의 문법 기능 추출](extract-n-gram-features-from-text.md) <br/> [열 추가](add-columns.md) <br/> [행 추가](add-rows.md) <br/> [중복 행 제거](remove-duplicate-rows.md) <br/> [텍스트 전처리](preprocess-text.md) <br/> [데이터 조인](join-data.md) <br/> [데이터 분할](split-data.md) <br/> [데이터 정규화](normalize-data.md) <br/> [파티션 및 샘플](partition-and-sample.md) <br/> [값 자르기](clip-values.md) <br/> [SQL 변환 적용](apply-sql-transformation.md)|
| Python 및 R 모듈 | 코드를 작성 하 고이를 모듈에 포함 하 여 Python 및 R을 실험과 통합 합니다. | [Python 스크립트 실행](execute-python-script.md)   <br/> [Python 모델 만들기](create-python-model.md) <br/> [R 스크립트 실행](execute-r-script.md)
|  | **기계 학습 알고리즘**: | |
| 분류 | 클래스를 예측 합니다.  이진 파일 (2 클래스) 또는 다중 클래스 알고리즘에서 선택 합니다.| [다중 클래스 의사 결정 포리스트](multiclass-decision-forest.md) <br/> [다중 클래스 승격 된 의사 결정 트리](multiclass-boosted-decision-tree.md) <br/> [다중 클래스 로지스틱 회귀](multiclass-logistic-regression.md)  <br/> [다중 클래스 신경망](multiclass-neural-network.md)  <br/>  [2 클래스 로지스틱 회귀](two-class-logistic-regression.md)  <br/>[2 클래스 평균 퍼셉트론](two-class-averaged-perceptron.md) <br/> [2 클래스 &nbsp;Boosted &nbsp;Decision &nbsp;Tree](two-class-boosted-decision-tree.md)  <br/> [2 클래스 의사 결정 포리스트](two-class-decision-forest.md)  <br/> [2 클래스 신경망](two-class-neural-network.md)  <br/> [@No__t_3Vector&#8209;&nbsp;Support 두 개의 클래스 &nbsp;Machine](two-class-support-vector-machine.md) 
| 클러스터링 | 데이터를 그룹화 합니다.| [K-클러스터링을 의미 합니다.](k-means-clustering.md)
| 회귀 | 값을 예측 합니다. | [선형 회귀](linear-regression.md)  <br/> [신경망 회귀](neural-network-regression.md)  <br/> [의사 결정 포리스트 회귀](decision-forest-regression.md)  <br/> [승격 된 &nbsp;Decision &nbsp;Tree &nbsp;Regression](boosted-decision-tree-regression.md)
|  | **모델 작성 및 평가**: | |
| 학습   | 알고리즘을 통해 데이터를 실행 합니다. | [모델 학습](train-model.md)  <br/> [클러스터링 모델 학습](train-clustering-model.md)    |
| 모델 평가 | 학습 된 모델의 정확도를 측정 합니다. |  [모델 평가](evaluate-model.md)
| 점수 | 방금 학습 한 모델에서 예측을 가져옵니다. | [변환 적용](apply-transformation.md)<br/>[@No__t_1Data &nbsp;to 할당 &nbsp;Clusters](assign-data-to-clusters.md) <br/>[모델 점수 매기기](score-model.md)
| 통계 함수 | 데이터 과학과 관련 된 다양 한 숫자 메서드를 제공 합니다. | [수학 연산 적용](apply-math-operation.md) <br/> [데이터 요약](summarize-data.md)|

## <a name="error-messages"></a>오류 메시지

Azure Machine Learning 서비스의 시각적 인터페이스에서 모듈을 사용 하 여 발생할 수 있는 [오류 메시지 및 예외 코드](machine-learning-module-error-codes.md) 에 대해 알아봅니다.
