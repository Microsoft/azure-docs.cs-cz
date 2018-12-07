---
title: (NEPOUŽÍVANÉ) Monitorování clusteru Azure DC/OS – služby Datadog
description: Monitorování clusteru služby Azure Container Service pomocí služby Datadog. Umožňuje nasadit agenty služby Datadog ke clusteru pomocí webového uživatelského rozhraní DC/OS.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d881a5c0f994b627b4c7c3da362672b3b887cd5e
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52996159"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(NEPOUŽÍVANÉ) Monitorování clusteru služby Azure Container Service DC/OS pomocí služby Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto článku nasadíme na všechny agentské uzly v clusteru Azure Container Service agenty služby Datadog. Pro tuto konfiguraci budete potřebovat účet pomocí služby Datadog. 

## <a name="prerequisites"></a>Požadavky
[Nasaďte](container-service-deployment.md) a [připojte](../container-service-connect.md) cluster nakonfigurovaný službou Azure Container Service. Prozkoumejte [uživatelské rozhraní Marathon](container-service-mesos-marathon-ui.md). Přejděte na [ http://datadoghq.com ](http://datadoghq.com) nastavení účtu služby Datadog. 

## <a name="datadog"></a>Datadog
Služby Datadog je monitorovací služba, která shromažďuje data monitorování z vašeho kontejnerů v clusteru Azure Container Service. Služby Datadog má řídicí panel integrace Dockeru ve kterém uvidíte konkrétní metriky v rámci kontejnerů. Metrik shromážděných z kontejnerů jsou uspořádané podle procesoru, paměti, síťové a vstupně-výstupních operací. Služby Datadog rozdělí metriky do kontejnerů a obrázky. Příklad vypadá uživatelského rozhraní pro využití procesoru je pod.

![Uživatelské rozhraní služby Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Konfigurace nasazení služby Datadog pomocí Marathonu
Tyto kroky ukazují postup při konfiguraci a nasazení služby Datadog aplikací do clusteru pomocí Marathonu. 

Přístup k vaší uživatelské rozhraní DC/OS prostřednictvím [ http://localhost:80/ ](http://localhost:80/). Jednou v Uživatelském rozhraní DC/OS přejděte "Universe" který je vlevo dole a potom vyhledejte "Služby Datadog" a klikněte na tlačítko "Instalaci".

![Balíček služby Datadog v rozhraní DC/OS Universe](./media/container-service-monitoring/datadog1.png)

Teď k dokončení konfigurace budete potřebovat účet služby Datadog nebo Bezplatný zkušební účet. Po přihlášení na webu vzhled služby Datadog vlevo a přejděte do integrace -> pak [rozhraní API](https://app.datadoghq.com/account/settings#api). 

![Klíč rozhraní API služby Datadog](./media/container-service-monitoring/datadog2.png)

Dále zadejte svůj klíč rozhraní API do služby Datadog konfigurace v rámci rozhraní DC/OS Universe. 

![Konfigurace služby Datadog v rozhraní DC/OS Universe](./media/container-service-monitoring/datadog3.png) 

V konfiguraci uvedené výš instance jsou nastaveny na hodnotu 10000000 tak pokaždé, když je přidán nový uzel do clusteru služby Datadog automaticky nasadí agent do tohoto uzlu. To je to dočasné řešení. Po instalaci balíčku by měl přejít zpět na web služby Datadog a vyhledejte "[řídicí panely](https://app.datadoghq.com/dash/list)." Tady se zobrazí vlastní a integrace řídicí panely. [Řídicí panel Docker](https://app.datadoghq.com/screen/integration/docker) budou mít všechny metriky kontejnerů pro monitorování vašeho clusteru potřebujete. 

