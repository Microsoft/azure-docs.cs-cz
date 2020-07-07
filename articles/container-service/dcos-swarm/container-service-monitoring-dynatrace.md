---
title: ZASTARALÉ Monitorování clusteru Azure DC/OS – dynaTrace
description: Monitorujte Azure Container Service cluster DC/OS pomocí dynaTrace. Nasaďte OneAgent dynaTrace pomocí řídicího panelu DC/OS.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: ab6bb116c93aad8501da21dc5688d7e39f4195fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82166185"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>ZASTARALÉ Monitorování Azure Container Service clusteru DC/OS pomocí dynaTrace SaaS/Managed

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto článku vám ukážeme, jak nasadit [dynaTrace](https://www.dynatrace.com/) OneAgent pro monitorování všech uzlů agentů v clusteru Azure Container Service. Pro tuto konfiguraci potřebujete účet s dynaTrace SaaS/Managed. 

## <a name="dynatrace-saasmanaged"></a>DynaTrace SaaS/Managed
DynaTrace je cloudové řešení pro monitorování pro vysoce dynamická prostředí kontejnerů a clusterů. Umožňuje lepší optimalizaci nasazení kontejnerů a přidělení paměti pomocí dat využití v reálném čase. Umožňuje automaticky určit problémy s aplikací a infrastrukturou, protože poskytuje automatizované monitorování standardních hodnot, korelaci problémů a detekci hlavní příčiny.

Následující obrázek ukazuje uživatelské rozhraní dynaTrace:

![Uživatelské rozhraní dynaTrace](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Požadavky 
[Nasazení](container-service-deployment.md) a [připojení](./../container-service-connect.md) ke clusteru nakonfigurovanému pomocí Azure Container Service. Prozkoumejte [uživatelské rozhraní Marathon](container-service-mesos-marathon-ui.md). Pokud chcete [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) nastavit účet dynaTrace SaaS, pokračujte na.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Konfigurace nasazení dynaTrace pomocí Marathon
Tyto kroky ukazují, jak nakonfigurovat a nasadit aplikace v dynaTrace do clusteru pomocí Marathon.

1. Přístup k uživatelskému rozhraní DC/OS prostřednictvím `http://localhost:80/` . V uživatelském rozhraní DC/OS přejděte na kartu **Universe** a vyhledejte **dynaTrace**.

    ![DynaTrace v universu DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. K dokončení konfigurace potřebujete účet dynaTrace SaaS nebo bezplatný zkušební účet. Po přihlášení k řídicímu panelu dynaTrace vyberte **nasadit dynaTrace**.

    ![DynaTrace nastavení integrace PaaS](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na stránce vyberte **nastavit integraci PaaS**. 

    ![Token rozhraní API dynaTrace](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Zadejte token rozhraní API do konfigurace dynaTrace OneAgent v rámci Universe DC/OS. 

    ![Konfigurace dynaTrace OneAgent v universu DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Nastavte instance na počet uzlů, které máte v úmyslu spustit. Nastavení vyššího čísla funguje taky, ale DC/OS se bude pokoušet najít nové instance, dokud nebude toto číslo skutečně dosaženo. Pokud chcete, můžete ji také nastavit na hodnotu jako 1000000. V takovém případě, když se do clusteru přidá nový uzel, dynaTrace automaticky nasadí agenta na tento nový uzel. při současném pokusu o nasazení dalších instancí se stále neustále pokouší nasadit další instance.

    ![Konfigurace dynaTrace v universu DC/OS – instance](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Další kroky

Po instalaci balíčku přejděte zpátky na řídicí panel dynaTrace. Můžete prozkoumat různé metriky využití pro kontejnery v rámci clusteru. 
