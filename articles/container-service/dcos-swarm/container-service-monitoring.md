---
title: ZASTARALÉ Monitorování clusteru Azure DC/OS – služby Datadog
description: Monitorujte cluster Azure Container Service pomocí služby Datadog. K nasazení agentů služby Datadog do clusteru použijte webové uživatelské rozhraní DC/OS.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcb005e39f89298b35bf0f3a0ad1e19601ae4d13
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166137"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>ZASTARALÉ Monitorování Azure Container Service clusteru DC/OS pomocí služby Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto článku nasadíme agenty služby Datadog do všech uzlů agentů v clusteru Azure Container Service. Pro tuto konfiguraci budete potřebovat účet s služby Datadog. 

## <a name="prerequisites"></a>Požadavky
[Nasaďte](container-service-deployment.md) a [připojte](../container-service-connect.md) cluster nakonfigurovaný službou Azure Container Service. Prozkoumejte [uživatelské rozhraní Marathon](container-service-mesos-marathon-ui.md). Přejít na [https://datadoghq.com](https://datadoghq.com) a nastavit účet služby Datadog. 

## <a name="datadog"></a>Datadog
Služby Datadog je monitorovací služba, která shromažďuje data monitorování z vašich kontejnerů v rámci vašeho clusteru Azure Container Service. Služby Datadog má řídicí panel integrace Docker, kde vidíte konkrétní metriky v rámci kontejnerů. Metriky shromážděné z vašich kontejnerů jsou uspořádané podle procesoru, paměti, sítě a vstupně-výstupních operací. Služby Datadog rozdělí metriky do kontejnerů a imagí. Příklad toho, jak uživatelské rozhraní vypadá jako v případě využití procesoru, je níže.

![Uživatelské rozhraní služby Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Konfigurace nasazení služby Datadog pomocí Marathon
Tyto kroky vám ukážou, jak nakonfigurovat a nasadit aplikace služby Datadog do clusteru pomocí Marathon. 

Přístup k uživatelskému rozhraní DC/ `http://localhost:80/`OS prostřednictvím. Jakmile v uživatelském rozhraní DC/OS přejdete na "Universe", který je vlevo dole, vyhledejte "služby Datadog" a klikněte na nainstalovat.

![Služby Datadog balíček v universu DC/OS](./media/container-service-monitoring/datadog1.png)

Teď k dokončení konfigurace budete potřebovat účet služby Datadog nebo bezplatný zkušební účet. Až se přihlásíte k webu služby Datadog, vyhledejte vlevo a pak přejít na integrace – > [rozhraní API](https://app.datadoghq.com/account/settings#api). 

![Klíč rozhraní API služby Datadog](./media/container-service-monitoring/datadog2.png)

V dalším kroku zadejte svůj klíč rozhraní API do konfigurace služby Datadog v rámci Universe DC/OS. 

![Konfigurace služby Datadog v universu DC/OS](./media/container-service-monitoring/datadog3.png) 

Ve výše uvedených konfiguračních instancích se nastaví na 10000000, takže když se do clusteru služby Datadog přidá nový uzel, automaticky se do tohoto uzlu nasadí agent. Toto je dočasné řešení. Po instalaci balíčku byste měli přejít zpátky na web služby Datadog a najít[řídicí panely](https://app.datadoghq.com/dash/list). Tady uvidíte řídicí panely pro vlastní a integraci. [Řídicí panel Docker](https://app.datadoghq.com/screen/integration/docker) bude mít všechny metriky kontejnerů, které potřebujete pro monitorování clusteru. 

