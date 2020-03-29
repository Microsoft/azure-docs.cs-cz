---
title: (ZASTARALÉ) Monitorování clusteru Azure DC/OS – datadog
description: Monitorujte cluster služby Azure Container Service pomocí datadogu. Pomocí webového uživatelského uživatelského uživatelského uživatelského nastavení dc/OS nasadit agenty Datadog do clusteru.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 530092dfabacb0b07f4002a82078dd3535cd7e8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275253"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(ZASTARALÉ) Monitorování clusteru azure container service DC/OS pomocí Datadogu

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto článku nasadíme agenty Datadog u všech uzlů agenta v clusteru služby Azure Container Service. Budete potřebovat účet s Datadog pro tuto konfiguraci. 

## <a name="prerequisites"></a>Požadavky
[Nasaďte](container-service-deployment.md) a [připojte](../container-service-connect.md) cluster nakonfigurovaný službou Azure Container Service. Prozkoumejte [ui.](container-service-mesos-marathon-ui.md) Přejděte [https://datadoghq.com](https://datadoghq.com) na nastavení účtu Datadog. 

## <a name="datadog"></a>Datový pes
Datadog je monitorovací služba, která shromažďuje data monitorování z vašich kontejnerů v rámci clusteru služby Azure Container Service. Datadog má řídicí panel integrace Dockeru, kde můžete vidět konkrétní metriky v rámci kontejnerů. Metriky získané z kontejnerů jsou uspořádány podle procesoru, paměti, sítě a vstupně-výkon. Datadog rozděluje metriky do kontejnerů a obrázků. Příklad toho, co ui vypadá pro využití procesoru je níže.

![Datové unové i.](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Konfigurace nasazení Datadogu pomocí marathonu
Tyto kroky vám ukážou, jak nakonfigurovat a nasadit aplikace Datadog do clusteru pomocí marathonu. 

Přístup k vašemu dc/OS uzemnit přes [http://localhost:80/](http://localhost:80/). Poté, co v DC / OS UI přejděte na "Universe", který je v levém dolním rohu a pak hledat "Datadog" a klikněte na tlačítko "Instalovat".

![Balíček Datadog v rámci DC/OS Universe](./media/container-service-monitoring/datadog1.png)

Nyní k dokončení konfigurace budete potřebovat účet Datadog nebo bezplatný zkušební účet. Jakmile jste přihlášeni na webové stránky Datadog podívejte se doleva a přejděte na Integrace -> pak [API](https://app.datadoghq.com/account/settings#api). 

![Klíč rozhraní API Datadog](./media/container-service-monitoring/datadog2.png)

Dále zadejte klíč API do konfigurace Datadog v rámci DC / OS Universe. 

![Konfigurace Datadogu v DC/OS Universe](./media/container-service-monitoring/datadog3.png) 

Ve výše uvedených konfiguračních instancích jsou nastaveny na 10000000, takže při každém přidání nového uzlu do clusteru Datadog automaticky nasadí agenta do tohoto uzlu. Jedná se o prozatímní řešení. Po instalaci balíčku byste měli přejít zpět na web Datadog a najít "[Dashboards](https://app.datadoghq.com/dash/list)". Odtud uvidíte vlastní a integrační řídicí panely. [Řídicí panel Dockeru](https://app.datadoghq.com/screen/integration/docker) bude mít všechny metriky kontejneru, které potřebujete pro monitorování clusteru. 

