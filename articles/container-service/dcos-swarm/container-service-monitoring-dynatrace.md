---
title: (NEPOUŽÍVANÉ) Monitorování clusteru Azure DC/OS – Dynatrace
description: Monitorování clusteru služby Azure Container Service DC/OS pomocí služby Dynatrace. Nasazení Dynatrace OneAgent pomocí řídicího panelu DC/OS.
services: container-service
author: MartinGoodwell
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 8f34a00d9256c288a2842e905c06d5336522eece
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53002001"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(NEPOUŽÍVANÉ) Monitorování clusteru pomocí služby Dynatrace SaaS nebo spravované služby Azure Container Service DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto článku ukážeme, jak nasadit [Dynatrace](https://www.dynatrace.com/) OneAgent monitorovat všechny agentské uzly v clusteru Azure Container Service. Pro tuto konfiguraci potřebujete účet s Dynatrace SaaS nebo spravované. 

## <a name="dynatrace-saasmanaged"></a>SaaS a spravované služby Dynatrace
Dynatrace je řešení pro monitorování nativních pro cloud pro prostředí clusteru a vysoce dynamických kontejneru. Umožňuje lépe optimalizovat vaše nasazení kontejnerů a přidělení paměti pomocí dat o využití v reálném čase. Je schopen automaticky přesné určení problémů aplikace a infrastrukturu díky automatické monitorování standardních hodnot, problém korelace a zjišťování příčiny.

Následující obrázek znázorňuje Dynatrace uživatelského rozhraní:

![Dynatrace uživatelského rozhraní](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Požadavky 
[Nasazení](container-service-deployment.md) a [připojení](./../container-service-connect.md) pro cluster nakonfigurovaný službou Azure Container Service. Prozkoumejte [uživatelské rozhraní Marathon](container-service-mesos-marathon-ui.md). Přejděte na [ https://www.dynatrace.com/trial/ ](https://www.dynatrace.com/trial/) nastavení účtu Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Ke konfiguraci nasazení Dynatrace Marathon
Tyto kroky ukazují, jak nakonfigurovat a nasadit aplikace Dynatrace ke clusteru pomocí Marathonu.

1. Přístup k vaší uživatelské rozhraní DC/OS prostřednictvím [ http://localhost:80/ ](http://localhost:80/). Jednou v uživatelském rozhraní DC/OS, přejděte **Universe** kartu a potom vyhledejte **Dynatrace**.

    ![Dynatrace v DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. K dokončení konfigurace budete potřebovat účet Dynatrace SaaS nebo Bezplatný zkušební účet. Po přihlášení do řídicího panelu Dynatrace vyberte **nasazení Dynatrace**.

    ![Dynatrace nastavení integrace PaaS](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na stránce vybrat **nastavení integrace PaaS**. 

    ![Token Dynatrace API](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Zadejte svůj token rozhraní API do Dynatrace OneAgent konfigurace v rámci rozhraní DC/OS Universe. 

    ![Dynatrace OneAgent konfigurace v rozhraní DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Nastavte instance na počet uzlů, které chcete spustit. Také nastaví větší číslo, funguje ale DC/OS, se snaží najít nové instance, dokud není skutečně dosaženo dané číslo. Pokud dáváte přednost, můžete také nastavit toto na hodnotu jako 1000000. V takovém případě při každém přidání nového uzlu do clusteru, Dynatrace automaticky nasadí určitého agenta do tohoto nového uzlu cenu stále usilujeme o to nasadit další instance DC/OS.

    ![Konfigurace Dynatrace v DC/OS Universe – instance](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Další postup

Po instalaci balíčku přejděte zpátky na řídicí panel Dynatrace. Můžete prozkoumat různé metriky využití pro kontejnery v rámci vašeho clusteru. 