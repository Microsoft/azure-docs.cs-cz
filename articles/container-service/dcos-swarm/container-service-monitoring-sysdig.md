---
title: Monitorování clusteru služby Azure Container Service pomocí služby Sysdig
description: Cluster služby Azure Container Service můžete monitorovat pomocí služby Sysdig.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 0c0f4fd1f3a8242061e198d7b5447656f9008e96
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32162132"
---
# <a name="monitor-an-azure-container-service-cluster-with-sysdig"></a>Monitorování clusteru služby Azure Container Service pomocí služby Sysdig

V tomto článku nasadíme na všechny agentské uzly v clusteru služby Azure Container Service agenty služby Sysdig. Pro tuto konfiguraci potřebujete účet se službou Sysdig. 

## <a name="prerequisites"></a>Požadavky
[Nasaďte](container-service-deployment.md) a [připojte](../container-service-connect.md) cluster nakonfigurovaný službou Azure Container Service. Prozkoumejte [uživatelské rozhraní Marathon](container-service-mesos-marathon-ui.md). Přejděte na [ http://app.sysdigcloud.com ](http://app.sysdigcloud.com) nastavit účet Sysdig cloudu. 

## <a name="sysdig"></a>Sysdig
Sysdig je monitorovací služba, která vám umožňuje monitorovat kontejnery v rámci vašeho clusteru. Služba Sysdig pomáhá s odstraňováním potíží, ale obsahuje taky základní monitorovací metriky procesoru, sítí, paměti a vstupně-výstupních procesů. Služba Sysdig nabízí přehled o tom, které kontejnery jsou nejvytíženější nebo využívají nejvíc paměti a výkonu procesoru. To zjistíte v části „Přehled“, který je v současné době dostupný v beta verzi. 

![Uživatelské rozhraní služby Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Konfigurace nasazení služby Sysdig s uživatelským rozhraním Marathon
Tento postup vám ukáže, jak nakonfigurovat aplikace služby Sysdig a nasadit je do clusteru pomocí Marathonu. 

Přístup přes uživatelské rozhraní DC/OS [ http://localhost:80/ ](http://localhost:80/) jednou v uživatelském rozhraní DC/OS přejděte na "základní soubor", který je ve spodní levé a poté vyhledejte "Sysdig."

![Sysdig v rozhraní DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig1.png)

K dokončení konfigurace budete potřebovat cloudový účet služby Sysdig nebo bezplatný zkušební účet. Po přihlášení na web cloudu Sysdig klikněte na uživatelské jméno. Zobrazí se stránka, na které byste měli najít svůj „přístupový klíč“. 

![Klíč rozhraní API služby Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Svůj přístupový klíč zadejte do konfigurace služby Sysdig v rozhraní DC/OS Universe. 

![Konfigurace služby Sysdig v rozhraní DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig3.png)

Teď nastavte instance na hodnotu 10000000, aby služba Sysdig při každém přidání nového uzlu do clusteru automaticky do tohoto nového uzlu nasadila agenta. Je to dočasné řešení, kterým zajistíte, aby se služba Sysdig nasadila do všech nových agentů v clusteru. 

![Konfigurace služby Sysdig v rozhraní DC/OS Universe – instance](./media/container-service-monitoring-sysdig/sysdig4.png)

Po instalaci balíčku přejděte zpátky do uživatelského rozhraní služby Sysdig. Teď už budete moc prozkoumávat různé metriky využití kontejnerů v clusteru. 

Můžete také nainstalovat řídicí panely specifické pro Mesos a Marathon prostřednictvím [průvodce novým řídicím panelem](https://app.sysdigcloud.com/#/dashboards/new).
