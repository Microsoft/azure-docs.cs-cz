---
title: Cluster Azure DC/OS monitorování - Dynatrace
description: Monitorování clusteru Azure Container Service DC/OS s Dynatrace. Nasaďte Dynatrace OneAgent pomocí řídicího panelu DC/OS.
services: container-service
author: MartinGoodwell
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 3d1bfc3bb61781d487c40831edd5da6fcb5a7df9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>Monitorování clusteru Azure Container Service DC/OS s Dynatrace SaaS/spravované

V tomto článku jsme ukazují, jak nasadit [Dynatrace](https://www.dynatrace.com/) OneAgent monitorovat všechny agenta uzly v clusteru Azure Container Service. Potřebujete účet s Dynatrace SaaS/spravované pro tuto konfiguraci. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/spravované
Dynatrace je řešení monitorování cloudu nativní pro vysoce dynamické kontejneru a prostředí clusteru. Umožňuje lépe optimalizovat nasazení kontejnerů a přidělení paměti s použitím data o využití v reálném čase. Je schopný automaticky přesným rozpoznáním aplikace a infrastrukturu problémů tím, že poskytuje automatizované monitorování standardních hodnot, korelace problém a zjištění hlavní příčiny.

Následující obrázek znázorňuje Dynatrace uživatelského rozhraní:

![Dynatrace uživatelského rozhraní](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Požadavky 
[Nasazení](container-service-deployment.md) a [připojit](./../container-service-connect.md) do clusteru s podporou konfigurovat tak, že Azure Container Service. Prozkoumejte [uživatelské rozhraní Marathon](container-service-mesos-marathon-ui.md). Přejděte na [ https://www.dynatrace.com/trial/ ](https://www.dynatrace.com/trial/) nastavit účet Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Konfigurace nasazení Dynatrace pomocí Marathonu
Tyto kroky ukazují, jak nakonfigurovat a nasadit aplikace Dynatrace ke clusteru pomocí Marathonu.

1. Přístup přes uživatelské rozhraní DC/OS [ http://localhost:80/ ](http://localhost:80/). Jednou v uživatelském rozhraní DC/OS, přejděte na **Universe** kartě a poté vyhledejte **Dynatrace**.

    ![Dynatrace v Universe DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. K dokončení konfigurace, potřebujete účet Dynatrace SaaS nebo Bezplatný zkušební účet. Po přihlášení do řídicího panelu Dynatrace vybrat **nasazení Dynatrace**.

    ![Nastavení Dynatrace PaaS integrace](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na stránce vyberte **nastavení integrace PaaS**. 

    ![Rozhraní API Dynatrace tokenu](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Zadejte token vaše rozhraní API do konfigurace Dynatrace OneAgent v rámci Universe DC/OS. 

    ![Konfigurace Dynatrace OneAgent v Universe DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Nastavte instance na počet uzlů, který chcete spustit. Nastaví větší číslo také funguje, ale DC/OS bude nadále pokoušet najít nové instance toto číslo je ve skutečnosti ukončen. Pokud dáváte přednost, můžete tuto vlastnost nastavit na hodnotu jako 1000000. V takovém případě vždy, když do clusteru je přidán nový uzel, Dynatrace automaticky nasadí agent do tohoto nového uzlu za cenu neustále se snažíte nasadit další instance DC/OS.

    ![Konfigurace Dynatrace v DC/OS Universe-instance](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Další postup

Jakmile jste nainstalovali balíček, přejděte zpět na řídicí panel Dynatrace. Použití jiné metriky pro kontejnery můžete prozkoumat v rámci clusteru. 