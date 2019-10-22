---
title: 첫 번째 자동화된 ML 분류 실험 만들기
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 작업 영역 방문 페이지(미리 보기)에서 자동화된 기계 학습을 사용하여 분류 모델을 학습하고 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/26/2019
ms.openlocfilehash: dcd6f2ea6f5c79664af0c2431da07549e71c26bc
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72035681"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>자습서: 자동화 된 machine learning을 사용 하 여 첫 번째 분류 모델 만들기

이 자습서에서는 코드 줄을 하나도 작성하지 않고 작업 영역 방문 페이지(미리 보기)를 통해 첫 번째 자동화된 기계 학습 실험을 만드는 방법을 알아봅니다. 이 예제에서는 클라이언트가 금융 기관의 정기 예금을 신청할 것인지 예측하는 분류 모델을 만듭니다.

자동화된 기계 학습을 사용하면 시간이 많이 걸리는 작업을 자동화할 수 있습니다. 자동화된 기계 학습은 사용자가 선택한 성공 메트릭을 기반으로 최상의 모델을 발견할 수 있도록 알고리즘과 하이퍼 매개 변수의 여러 조합을 빠르게 반복합니다.

이 자습서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Machine Learning 작업 영역을 만듭니다.
> * 자동화된 기계 학습 실험을 실행합니다.
> * 실험 세부 정보를 봅니다.
> * 모델을 배포합니다.

## <a name="prerequisites"></a>전제 조건

* Azure 구독. Azure 구독이 아직 없는 경우 [무료 계정](https://aka.ms/AMLFree)을 만듭니다.

* [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) 데이터 파일을 다운로드합니다. **y** 열은 고객이 정기 예금에 가입했는지 여부를 나타내며, 뒷부분에서 이 자습서의 예측에 대한 대상 열로 식별됩니다. 

## <a name="create-a-workspace"></a>작업 영역 생성

Azure Machine Learning 작업 영역은 기계 학습 모델을 실험하고, 학습시키고, 배포하는 데 사용하는 클라우드의 기본 리소스입니다. Azure 구독 및 리소스 그룹을 서비스에서 사용하기 쉬운 개체에 연결합니다. 

Azure 리소스를 관리하기 위한 웹 기반 콘솔인 Azure Portal을 통해 작업 영역을 만듭니다. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> **작업 영역** 및 **구독**을 적어 둡니다. 올바른 작업 영역에 실험을 만들려면 이 정보가 필요합니다. 

## <a name="create-and-run-the-experiment"></a>실험 만들기 및 실행

모든 기술 수준의 데이터 과학 전문가용 데이터 과학 시나리오를 수행하기 위한 기계 학습 도구를 포함하는 통합 인터페이스인 작업 영역 방문 페이지에서 다음 실험 설정 및 실행 단계를 완료합니다. 작업 영역 방문 페이지는 Internet Explorer 브라우저에서 지원되지 않습니다.

1. [작업 영역 방문 페이지](https://ml.azure.com/workspaceportal/)에 로그인합니다.

1. 해당 구독과 직접 만든 작업 영역을 선택합니다.

1. **시작**을 선택합니다.

1. 왼쪽 창의 **작성** 섹션에서 **자동화된 ML**을 선택합니다.

   이는 자동화된 ML의 첫 번째 실험이므로 시작 화면이 표시됩니다.

   ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. **실험 만들기**를 선택합니다. 

1. 이 실험 이름 `my-1st-automl-experiment`를 입력합니다.

1. **새 컴퓨팅 만들기**를 선택하고 컴퓨팅 대상을 구성합니다. 컴퓨팅 대상은 학습 스크립트를 실행하거나 서비스 배포를 호스팅하는 데 사용되는 로컬 또는 클라우드 기반 리소스 환경입니다. 이 실험에서는 클라우드 기반 컴퓨팅을 사용합니다. 

   필드 | 설명 | 자습서에서 사용하는 값
   ----|---|---
   컴퓨팅 이름 |컴퓨팅 컨텍스트를 식별하는 고유한 이름입니다.|automl-compute
   Virtual&nbsp;machine&nbsp;size| 컴퓨팅에 사용할 가상 머신 크기를 선택합니다.|Standard_DS12_V2
   최소/최대 노드(고급 설정)| 데이터를 프로파일링하려면 하나 이상의 노드를 지정해야 합니다.|최소 노드: 1<br>최대 노드: 6

   >[!NOTE]
   >이 자습서에서는 기본 스토리지 계정과 새 컴퓨팅으로 만든 컨테이너를 사용합니다. 이 계정과 컨테이너가 양식에 자동으로 채워집니다.
    
1. **만들기**를 선택하여 컴퓨팅 대상을 가져옵니다. 

   **이 작업은 완료하는 데 몇 분이 소요됩니다.** 

1. 만들기가 완료되면 드롭다운 목록에서 새 컴퓨팅 대상을 선택하고 **다음**을 선택합니다.

1. **로컬 파일에서 업로드**를 선택하여 새 데이터 세트 만들기를 시작합니다. 

    1. **찾아보기**를 선택합니다.
    
    1. 로컬 컴퓨터에 있는 **bankmarketing_train.csv** 파일을 선택합니다. 이는 [구성 요소](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)로 다운로드한 파일입니다.

    1. 데이터 세트에 고유 이름을 지정하고 선택적 설명을 입력합니다. 

    1. 왼쪽 아래에서 **다음**을 선택하여 작업 영역을 만드는 동안 자동으로 설정된 기본 컨테이너에 데이터 세트를 업로드합니다. 공개 미리 보기에서는 로컬 파일 업로드만 지원합니다. 
    
       업로드가 완료되면, 설정 및 미리 보기 양식이 파일 형식에 따라 미리 채워집니다. 
       
    1. **설정 및 미리 보기** 양식이 다음과 같이 채워졌는지 확인하고 **다음**을 선택합니다.
        
        필드|설명| 자습서에서 사용하는 값
        ---|---|---
        파일 형식|파일에 저장된 데이터의 레이아웃 및 유형을 정의합니다.| 구분됨
        구분 기호|일반 텍스트 또는 기타 데이터 스트림에서 개별의 독립된 지역 간의&nbsp; 경계를 지정하는 데 사용하는 하나 이상의 문자입니다. |쉼표
        Encoding|데이터 세트를 읽는 데 사용할 문자 스키마 테이블을 식별합니다.| UTF-8
        열 머리글| 데이터 세트의 헤더(있는 경우)가 처리되는 방법을 나타냅니다.| 모든 파일의 머리글이 동일함
        행 건너뛰기 | 데이터 세트에서 건너뛴 행(있는 경우)의 수를 나타냅니다.| 없음
    
        ![미리 보기 탭 구성](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

1. 예측 작업으로 **분류**를 선택합니다.

1. 예측하려는 항목인 대상 열로 **y**를 선택합니다. 이 열은 클라이언트가 정기 예금에 가입했는지 여부를 나타냅니다.

1. **고급 설정**을 확장하고 다음과 같이 필드를 채웁니다. 이러한 설정은 학습 작업을 더 효율적으로 제어하기 위한 것입니다. 그렇지 않으면 실험 선택 및 데이터를 기반으로 기본값이 적용됩니다.

   >[!NOTE]
   > 이 자습서에서는 메트릭 점수 또는 반복 임계값당 최대 코어를 설정하지 않습니다. 또한 알고리즘 테스트를 차단하지 않습니다.
   
   Advanced&nbsp;settings|설명|Value&nbsp;for&nbsp;tutorial
   ------|---------|---
   기본 메트릭| 기계 학습 알고리즘을 측정할 평가 메트릭입니다.|AUC_weighted
   종료 기준| 조건을 충족하는 경우 학습 작업이 중지됩니다. |@No__t_1time &nbsp;job 교육: 5 <br> <br> Max&nbsp;#&nbsp;of&nbsp;iterations&#58;10
   전처리| 자동화된 기계 학습을 통해 전처리를 수행할 수 있도록 설정합니다. 여기에는 자동 데이터 정리, 준비 및 가상 기능 생성을 위한 변환이 포함됩니다.| 사용
   유효성 검사 유형 | 교차 유효성 검사 유형을 선택합니다.|K-폴드 교차 유효성 검사
   유효성 검사 수 | 테스트의 횟수입니다. | 2 교차 유효성 검사 
   동시성| 최대 동시 반복 횟수입니다.|5
   
1. **시작**을 선택하여 실험을 실행합니다. 실험 준비가 시작됨에 따라 상태 메시지와 함께 화면이 나타납니다.

>[!IMPORTANT]
> 실험 실행을 준비하기 위한 준비는 **10-15분**이 걸립니다. 실행이 시작되면 **각 반복에 대해 2-3분 더** 소요됩니다.  
>
> 프로덕션 환경에서는 시간이 더 걸릴 수 있습니다. 그러나 이 자습서에서는 다른 사용자가 실행하는 동안 실행이 완료되면 반복 결과 탐색을 시작하는 것이 좋습니다. 

##  <a name="explore-iteration-results"></a>반복 결과 탐색

실험이 진행됨에 따라 화면의 **반복 차트**와 **반복 목록**이 완료되면서 생성되는 다른 반복(모델)으로 업데이트됩니다. 기본적으로 반복은 메트릭 점수를 기준으로 정렬됩니다. 이 자습서의 경우 선택한 **AUC_weighted** 메트릭에 따라 가장 높은 점수를 획득한 모델이 목록 맨 위에 표시됩니다.

모든 실험 반복이 완료될 때까지 기다리는 동안 완료된 반복의 **이름**을 선택하여 성능 세부 정보를 살펴봅니다. 

다음은 정밀도-리콜 곡선, 혼동 행렬, 가중치가 적용된 정확도 점수 등과 같은 각 반복에 대해 생성된 차트 및 실행 메트릭을 보여줍니다. 

![반복 실행 세부 정보](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>모델 배포

작업 영역 방문 페이지에서 자동화된 기계 학습을 사용하면 몇 단계 안에 최적 모델을 웹 서비스로 배포할 수 있습니다. 배포는 모델 통합이므로 새 데이터를 예측하고 잠재적인 기회 영역을 식별할 수 있습니다. 이 실험에서 웹 서비스에 배포한다는 것은 이제 금융 기관이 잠재적 정기 예금 고객을 식별할 수 있는 반복적이고 확장 가능한 솔루션을 확보했다는 것을 의미합니다. 

실행이 완료되면 **반복 차트** 및 **반복 목록** 세부 정보 페이지로 다시 이동합니다. 

**AUC_weighted** 메트릭에 따라 이 실험에서 가장 적합한 모델은 **VotingEnsemble**입니다.  이 모델을 배포할 것이며, 배포 시간이 약 20분 정도 걸립니다. 배포 프로세스에는 모델 등록, 리소스 생성, 웹 서비스에 대한 구성을 포함한 몇 가지 단계가 수반됩니다.

1. 오른쪽 위 모서리에 있는 **최적 모델 배포** 단추를 선택합니다.

1. **최적 모델 배포** 창을 다음과 같이 채웁니다.

    필드| Value
    ----|----
    배포 이름| my-automl-deploy
    배포 설명| 첫 번째 자동화된 기계 학습 실험 배포
    채점 스크립트| 자동 생성
    환경 스크립트| 자동 생성
    
1. **배포**를 선택합니다.  

    배포가 성공적으로 완료되면 배포 완료 메시지가 표시됩니다.
    
이제 예측을 생성하는 운영 웹 서비스가 생겼습니다.

## <a name="clean-up-resources"></a>리소스 정리

배포 파일은 데이터 및 실험 파일보다 크기 때문에 더 많은 저장 비용이 발생합니다. 작업 영역 및 실험 파일을 유지하려는 경우에는 배포 파일만 삭제하여 계정 비용을 최소화할 수 있습니다. 또는 어떤 파일도 사용할 계획이 없으면 전체 리소스 그룹을 삭제합니다.  

### <a name="delete-the-deployment-instance"></a>배포 인스턴스 삭제

다른 자습서에서 사용하거나 탐색할 수 있도록 리소스 그룹과 작업 영역을 유지하려면 Azure Portal에서 배포 인스턴스만 삭제합니다. 

1. [Azure 포털](https://portal.azure.com//)로 이동합니다. 작업 영역으로 이동한 다음, 왼쪽 **자산** 창 아래에서 **배포**를 선택합니다. 

1. 삭제하려는 배포를 선택하고 **삭제**를 선택합니다. 

1. **계속**을 선택합니다.

### <a name="delete-the-resource-group"></a>리소스 그룹 삭제

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 자동화된 기계 학습 자습서에서는 작업 영역 방문 페이지를 사용하여 분류 모델을 만들고 배포했습니다. 자세한 내용과 다음 단계는 아래 문서를 참조하세요.

> [!div class="nextstepaction"]
> [웹 서비스 사용](how-to-consume-web-service.md)

+ [전처리](how-to-create-portal-experiments.md#preprocess)에 대한 자세한 정보
+ [데이터 프로파일링](how-to-create-portal-experiments.md#profile)에 대한 자세한 정보
+ [자동화된 기계 학습](concept-automated-ml.md)에 대한 자세한 정보
+ 분류 메트릭 및 차트에 대한 자세한 내용은 [자동화된 기계 학습 결과 이해](how-to-understand-automated-ml.md#classification) 문서를 참조하세요.

>[!NOTE]
> 이 은행 마케팅 데이터 집합은 [Creative Commons (CCO: 공용 도메인) 라이선스](https://creativecommons.org/publicdomain/zero/1.0/)에서 사용할 수 있습니다. 데이터베이스의 개별 콘텐츠에 대한 모든 권한은 [데이터베이스 콘텐츠 라이선스](https://creativecommons.org/publicdomain/zero/1.0/)를 따르며 [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)에서 사용할 수 있습니다. 이 데이터 세트는 원래 [UCI Machine Learning 데이터베이스](https://archive.ics.uci.edu/ml/datasets/bank+marketing) 내에 제공됩니다.<br><br>
> [Moro 외, 2014] S. Moro, P. Cortez 및 P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, 2014년 6월.
