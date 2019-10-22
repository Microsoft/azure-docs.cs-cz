---
title: Azure Application Insights를 사용하여 성능 문제 진단 | Microsoft Docs
description: Azure Application Insights를 사용하여 애플리케이션에서 성능 문제를 찾고 진단하는 자습서입니다.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/13/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 9768191d98bf1987ac24564869107cdd6bf19e8d
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "69032105"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Azure Application Insights를 사용하여 성능 문제 찾기 및 진단

Azure Application Insights는 애플리케이션에서 원격 분석을 수집하여 해당 작업 및 성능 분석을 돕습니다.  이 정보를 사용하여 발생할 수 있는 문제를 식별하거나 사용자에게 가장 큰 영향을 주는 애플리케이션에 대한 개선 사항을 식별할 수 있습니다.  이 자습서에서는 애플리케이션의 서버 구성 요소 및 클라이언트 관점의 성능을 분석하는 프로세스를 안내합니다.  다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 서버 쪽 작업의 성능 식별
> * 성능 저하의 근본 원인을 파악하는 서버 작업 분석
> * 가장 느린 클라이언트 쪽 작업 식별
> * 쿼리 언어를 사용하여 페이지 보기의 세부 정보 분석


## <a name="prerequisites"></a>전제 조건

이 자습서를 완료하려면 다음이 필요합니다.

- 다음 워크로드로 [Visual Studio 2019](https://www.visualstudio.com/downloads/)를 설치합니다.
    - ASP.NET 및 웹 개발
    - Azure 개발
- Azure에 .NET 애플리케이션을 배포하고 [Application Insights SDK를 사용하도록 설정](../../azure-monitor/app/asp-net.md)합니다.
- 애플리케이션에 대한 [Application Insights 프로파일러를 활성화합니다](../../azure-monitor/app/profiler.md#installation).

## <a name="log-in-to-azure"></a>Azure에 로그인
Azure Portal([https://portal.azure.com](https://portal.azure.com))에 로그인합니다.

## <a name="identify-slow-server-operations"></a>느린 서버 작업 식별
Application Insights는 애플리케이션에서 다른 작업에 대한 성능 정보를 수집합니다. 가장 긴 기간으로 이러한 작업을 식별하여 잠재적인 문제를 진단하거나 진행 중인 개발을 대상으로 하여 애플리케이션의 전반적인 성능을 개선할 수 있습니다.

1. **Application Insights**를 선택한 다음, 구독을 선택합니다.  
1. **성능** 패널을 열려면 **조사** 메뉴 아래의 **성능**을 선택하거나 **서버 응답 시간** 그래프를 클릭합니다.

    ![성능 중심](media/tutorial-performance/1-overview.png)

2. **성능** 패널은 애플리케이션에 대한 각 작업의 수 및 평균 기간을 표시합니다.  이 정보를 사용하여 사용자에게 가장 큰 영향을 주는 해당 작업을 식별할 수 있습니다. 이 예제에서는 **고객/세부 정보 가져오기** 및 **홈/인덱스 가져오기**는 상대적으로 높은 기간 및 호출 수로 인해 조사할 후보일 가능성이 있습니다.  다른 작업은 더 높은 기간을 가질 수 있지만 거의 호출되지 않으므로 해당 개선의 효과는 최소화됩니다.  

    ![성능 서버 패널](media/tutorial-performance/2-server-operations.png)

3. 현재 그래프에는 일정 기간 동안의 선택한 작업에 대한 평균 기간이 표시되어 있습니다. 95번째 백분위수로 전환하여 성능 문제를 찾을 수 있습니다. 그래프에 고정하여 관심이 있는 작업을 추가합니다.  일부 조사할 가치가 있는 최대치가 있다는 것을 보여 줍니다.  그래프의 시간 창을 축소하여 이를 더 분리합니다.

    ![고정 작업](media/tutorial-performance/3-server-operations-95th.png)

4.  오른쪽의 성능 패널에는 선택한 작업에의 다양한 요청에 대한 시간 분포를 보여 줍니다.  창을 줄여 95번째 백분위수에서 시작합니다. "상위 3개 종속성" 인사이트 카드에서 외부 종속성이 느린 트랜잭션에 영향을 줄 수 있음을 한눈에 알 수 있습니다.  샘플 목록을 보려면 샘플 수가 있는 단추를 클릭합니다. 그런 다음, 샘플을 선택하여 트랜잭션 세부 정보를 확인하면 됩니다.

5.  Fabrikamaccount Azure 테이블에 대한 호출이 전체 트랜잭션 기간에 가장 많이 영향을 주고 있음을 한눈에 볼 수 있습니다. 또한 예외로 인해 실패한 호출도 볼 수 있습니다. 목록에서 항목을 클릭하면 오른쪽에서 해당 세부 정보를 볼 수 있습니다. [트랜잭션 진단 환경에 대한 자세한 정보](../../azure-monitor/app/transaction-diagnostics.md)

    ![작업 엔드투엔드 세부 정보](media/tutorial-performance/4-end-to-end.png)
    

6.  **프로파일러**는 작업에 대해 실행된 실제 코드 및 각 단계에 필요한 시간을 표시하여 코드 수준 진단을 더 자세히 수행하는 데 도움이 됩니다. 일부 작업은 프로파일러가 주기적으로 실행되므로 추적이 없을 수 있습니다.  시간이 지남에 따라 더 많은 작업에 추적이 있어야 합니다.  작업에 대한 프로파일러를 시작하려면 **프로파일러 추적**을 클릭합니다.
5.  추적은 각 작업에 대한 개별 이벤트를 보여주므로 전반적인 작업의 기간에 대한 근본 원인을 진단할 수 있습니다.  가장 긴 기간을 포함하는 상위 예 중 하나를 클릭합니다.
6.  **실행 부하 과다 경로**를 클릭하여 작업의 총 기간에 가장 큰 영향을 주는 이벤트의 특정 경로를 강조 표시합니다.  이 예에서는 가장 느린 호출이 *FabrikamFiberAzureStorage.GetStorageTableData* 메서드에서 비롯되었음을 확인할 수 있습니다. 대부분의 시간을 사용하는 파트는 *CloudTable.CreateIfNotExist* 메서드입니다. 함수가 호출될 때마다 이 코드 줄을 실행하면 불필요한 네트워크 호출 및 CPU 리소스가 사용됩니다. 코드를 수정하는 가장 좋은 방법은 한 번만 실행하는 일부 시작 메서드에 이 줄을 배치하는 것입니다.

    ![프로파일러 세부 정보](media/tutorial-performance/5-hot-path.png)

7.  화면 위쪽의 **성능 팁**은 과도한 기간이 대기로 인한 것인 평가를 지원합니다.  다양한 유형의 이벤트 해석에 대한 설명서의 **대기** 링크를 클릭합니다.

    ![성능 팁](media/tutorial-performance/6-perf-tip.png)

8.  추가 분석을 위해 **추적 다운로드**를 클릭하여 Visual Studio에 추적을 다운로드할 수 있습니다.

## <a name="use-logs-data-for-server"></a>서버에 대한 로그 데이터 사용
 로그는 Application Insights에서 수집된 모든 데이터를 분석할 수 있는 풍부한 쿼리 언어를 제공합니다. 이를 사용하여 요청 및 성능 데이터에 대한 심층 분석을 수행할 수 있습니다.

1. 작업 세부 정보 패널로 돌아가서 ![로그 아이콘](media/tutorial-performance/app-viewinlogs-icon.png)**로그에서 보기(분석)** 를 클릭합니다.

2. 로그가 패널에서 각 보기에 대한 쿼리와 함께 열립니다.  그대로 이러한 쿼리를 실행하거나 요구 사항에 맞게 수정할 수 있습니다.  첫 번째 쿼리는 시간이 지남에 따른 이 작업에 대한 기간을 보여 줍니다.

    ![로그 쿼리](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>느린 클라이언트 작업 식별
Application Insights는 최적화를 위한 서버 프로세스 식별 외에도 클라이언트 브라우저의 관점을 분석할 수 있습니다.  이를 통해 클라이언트 구성 요소에 대한 잠재적인 개선 사항을 식별하고 다양한 브라우저 또는 다른 위치에서 문제를 식별할 수도 있습니다.

1. **조사**에서 **브라우저**를 선택한 다음, **브라우저 성능**을 클릭하거나 **조사**에서 **성능**을 선택하고 맨 위 오른쪽에 있는 서버/브라우저 토글 단추를 클릭하여 **브라우저** 탭으로 전환하고 브라우저 성능 요약을 엽니다. 이는 브라우저의 관점에서 애플리케이션의 다양한 원격 분석의 시각적 요약을 제공합니다.

    ![브라우저 요약](media/tutorial-performance/8-browser.png)

2. 작업 이름 중 하나를 선택한 다음, 오른쪽 아래에 있는 파란색 샘플 단추를 클릭하고 작업을 선택합니다. 그러면 엔드투엔드 트랜잭션 세부 정보가 표시되고 오른쪽에서 **페이지 보기 속성**을 볼 수 있습니다. 여기에서 브라우저의 유형 및 해당 위치를 포함하여 페이지를 요청하는 클라이언트의 세부 정보를 볼 수 있습니다. 이 정보는 특정 유형의 클라이언트와 관련된 성능 문제가 있는지 여부를 결정하도록 지원할 수 있습니다.

    ![페이지 보기](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>클라이언트에 대한 로그 데이터 사용
서버 성능에 대해 수집된 데이터와 같이 Application Insights는 로그를 사용하는 심층 분석에 모든 클라이언트 데이터를 사용할 수 있도록 합니다.

1. 브라우저 요약으로 돌아가서 ![로그 아이콘](media/tutorial-performance/app-viewinlogs-icon.png) **로그에서 보기(분석)** 를 클릭합니다.

2. 로그가 패널에서 각 보기에 대한 쿼리와 함께 열립니다. 첫 번째 쿼리는 시간이 지남에 따른 다양한 페이지 보기에 대한 기간을 보여 줍니다.

    ![로그 쿼리](media/tutorial-performance/10-page-view-logs.png)

3.  스마트 진단은 데이터에서 고유한 패턴을 식별하는 로그의 기능입니다. 꺾은선형 차트에서 스마트 진단 점을 클릭하면 변칙을 발생한 레코드 없이 동일한 쿼리가 실행됩니다. 이러한 레코드의 세부 정보는 과도한 기간을 유발시키는 해당 페이지 보기의 속성을 식별할 수 있도록 쿼리의 주석 섹션에 표시됩니다.

    ![스마트 진단을 통한 로그](media/tutorial-performance/11-page-view-logs-dsmart.png)


## <a name="next-steps"></a>다음 단계
이제 런타임 예외를 식별하는 방법을 배웠으므로 오류에 대한 응답으로 경고를 생성하는 방법을 알아보는 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [애플리케이션 상태에 대한 경고](../../azure-monitor/learn/tutorial-alert.md)
