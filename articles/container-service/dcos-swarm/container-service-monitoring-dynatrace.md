---
title: (ZASTARALÉ) Monitorování clusteru Azure DC/OS – Dynatrace
description: Monitorujte cluster služby Azure Container Service DC/OS pomocí služby Dynatrace. Nasazení Dynatrace OneAgent pomocí řídicího panelu DC/OS.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a82481c5cb3d12b11179b41999f73e67583ec43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277751"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(ZASTARALÉ) Monitorování clusteru řadičů domény/operačního systému Azure Container Service pomocí služby Dynatrace SaaS/Managed

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto článku vám ukážeme, jak nasadit [Dynatrace](https://www.dynatrace.com/) OneAgent pro monitorování všech uzlů agenta v clusteru služby Azure Container Service. Pro tuto konfiguraci potřebujete účet u Dynatrace SaaS/Managed. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/Managed
Dynatrace je cloudové nativní monitorovací řešení pro vysoce dynamická prostředí kontejnerů a clusterů. Umožňuje lépe optimalizovat nasazení kontejnerů a přidělení paměti pomocí dat o využití v reálném čase. Je schopen automaticky určit problémy s aplikací a infrastrukturou tím, že poskytuje automatické baselining, korelaci problémů a detekci hlavních příčin.

Následující obrázek znázorňuje dynastopové ui:

![Dynatrace UI](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Požadavky 
[Nasazujte](container-service-deployment.md) a [připojujte se](./../container-service-connect.md) k clusteru nakonfigurovanému službou Azure Container Service. Prozkoumejte [ui.](container-service-mesos-marathon-ui.md) Přejděte [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) na nastavení účtu Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Konfigurace nasazení Dynatrace pomocí marathonu
Tyto kroky ukazují, jak nakonfigurovat a nasadit aplikace Dynatrace do clusteru pomocí marathonu.

1. Přístup k vašemu dc/OS uzemnit přes [http://localhost:80/](http://localhost:80/). Poté, co v DC / OS UI, přejděte na kartu **Vesmír** a pak vyhledejte **Dynatrace**.

    ![Dynatrace v DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. K dokončení konfigurace potřebujete účet Dynatrace SaaS nebo bezplatný zkušební účet. Jakmile se přihlásíte do řídicího panelu Dynatrace, vyberte **nasadit Dynatrace**.

    ![Dynatrace Nastavit integraci PaaS](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na stránce vyberte **Nastavit integraci PaaS**. 

    ![Token rozhraní API Dynatrace](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Zadejte token rozhraní API do konfigurace Dynatrace OneAgent v rámci dc/OS Universe. 

    ![Konfigurace Dynatrace OneAgent ve vesmíru DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Nastavte instance na počet uzlů, které chcete spustit. Nastavení vyšší číslo také funguje, ale DC/OS bude pokračovat ve snaze najít nové instance, dokud není skutečně dosaženo tohoto čísla. Pokud chcete, můžete také nastavit na hodnotu, jako je 1000000. V tomto případě při každém přidání nového uzlu do clusteru Dynatrace automaticky nasadí agenta do tohoto nového uzlu za cenu DC/OS, který se neustále pokouší nasadit další instance.

    ![Konfigurace Dynatrace v instancích DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Další kroky

Po instalaci balíčku přejděte zpět na řídicí panel Dynatrace. Můžete prozkoumat různé metriky využití pro kontejnery v rámci clusteru. 
