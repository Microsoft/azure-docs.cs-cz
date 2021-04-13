---
title: 'ML Studio (klasický): migrace do Azure Machine Learning – využívání koncových bodů kanálu'
description: Integrujte koncové body kanálu s klientskými aplikacemi v Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: fc7e222d400c2ded602e3d26d504896302fff014
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311783"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>Využívání koncových bodů kanálu od klientských aplikací

V tomto článku se dozvíte, jak integrovat klientské aplikace s koncovými body Azure Machine Learning. Další informace o psaní kódu aplikace najdete v tématu [spotřeba Azure Machine Learningho koncového bodu](../how-to-consume-web-service.md).

Tento článek je součástí studia (Classic) pro Azure Machine Learning řady migrace. Další informace o migraci na Azure Machine Learning najdete v [článku Přehled migrace](migrate-overview.md).

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Pracovní prostor služby Azure Machine Learning. [Vytvořte pracovní prostor Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- [Azure Machine Learning koncový bod nebo koncový bod kanálu v reálném](migrate-rebuild-web-service.md)čase.


## <a name="consume-a-real-time-endpoint"></a>Využití koncového bodu v reálném čase 

Pokud jste model nasadili jako **koncový bod v reálném čase**, můžete najít jeho koncový bod Rest a předem vygenerovaný kód spotřeby v jazycích C#, Python a R:

1. Přejít na Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Přejít na kartu **koncové body** .
1. Vyberte koncový bod v reálném čase.
1. Vyberte možnost **spotřebovat**.

> [!NOTE]
> Specifikaci Swagger pro svůj koncový bod můžete také najít na kartě **Podrobnosti** . Použijte definici Swagger k pochopení schématu koncového bodu. Další informace o definici Swagger najdete v [dokumentaci k Swagger oficiální dokumentaci](https://swagger.io/docs/specification/2-0/what-is-swagger/).


## <a name="consume-a-pipeline-endpoint"></a>Využití koncového bodu kanálu

Koncový bod kanálu můžete využívat dvěma způsoby:

- REST API volání
- Integrace s Azure Data Factory

### <a name="use-rest-api-calls"></a>Použití REST API volání

Zavolejte koncový bod REST z klientské aplikace. Pomocí specifikace Swagger pro koncový bod můžete pochopit jeho schéma:

1. Přejít na Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Přejít na kartu **koncové body** .
1. Vyberte **koncové body kanálu**.
1. Vyberte koncový bod kanálu.
1. V podokně s **přehledem koncového bodu kanálu** vyberte odkaz v části **dokumentace koncového bodu REST**.

### <a name="use-azure-data-factory"></a>Použití Azure Data Factory

Svůj kanál Azure Machine Learning můžete volat jako krok v kanálu Azure Data Factory. Další informace najdete v tématu [spouštění kanálů Azure Machine Learning v Azure Data Factory](../../data-factory/transform-data-machine-learning-service.md).


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak najít schéma a ukázkový kód pro koncové body kanálu. Další informace o využívání koncových bodů z klientské aplikace najdete v tématu věnovaném [Azure Machine Learning koncovému bodu](../how-to-consume-web-service.md).

Podívejte se na ostatní články v Azure Machine Learning seriálu migrace: 
1. [Přehled migrace](migrate-overview.md)
1. [Migrujte datovou sadu](migrate-register-dataset.md).
1. [Znovu sestavte školicí kanál studia (Classic)](migrate-rebuild-experiment.md).
1. [Opětovné sestavení webové služby studia (Classic)](migrate-rebuild-web-service.md).
1. **Integrujte webovou službu Azure Machine Learning s klientskými aplikacemi**.
1. [Migrace spouštěného skriptu R](migrate-execute-r-script.md)