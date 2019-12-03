---
title: 'Kurz: monitorování prostředků cloudové služby Azure na jaře pomocí výstrah a skupin akcí | Microsoft Docs'
description: Naučte se používat výstrahy na jaře Cloud.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: 2be21b20c394ae8505ad18f2c411db7aab06215f
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694002"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Kurz: monitorování jarních cloudových prostředků pomocí výstrah a skupin akcí

Výstrahy služby Azure jaře Cloud podporují monitorování prostředků na základě podmínek, jako je dostupné úložiště, rychlost požadavků nebo využití dat. Výstraha pošle oznámení, pokud sazby nebo podmínky splňují definované specifikace.

Pro nastavení kanálu výstrah existují dva kroky: 
1. Nastavte skupinu akcí s akcemi, které se mají provést, když se aktivuje výstraha, například e-mail, SMS, Runbook nebo Webhook. Skupiny akcí lze znovu použít mezi různými výstrahami.
2. Nastavte pravidla upozornění. Pravidla vážou modely metrik se skupinami akcí na základě cílového prostředku, metriky, podmínky, agregace času atd.

## <a name="prerequisites"></a>Předpoklady
Kromě požadavků na jarní Azure je tento kurz závislý na následujících zdrojích.

* Nasazená instance cloudu Azure pro jaře.  Začněte podle našeho [rychlého](spring-cloud-quickstart-launch-app-cli.md) startu.

* Prostředek Azure, který se má monitorovat, například databáze implementovaná v tomto článku: [Jak používat jarní Data rozhraní API pro Apache Cassandra s Azure Cosmos DB](https://docs.microsoft.com/azure/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
 
Následující postupy inicializují **skupinu akcí** a **výstrahu** počínaje možností **výstrahy** v levém navigačním podokně instance jarního cloudu. (Tento postup lze také spustit na stránce **Přehled monitorování** Azure Portal.) 

Přejděte ze skupiny prostředků do vaší jarní cloudové instance. V levém podokně vyberte **výstrahy** a pak vyberte **Spravovat akce**:

![Stránka skupiny prostředků na portálu obrazovky](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Nastavení skupiny akcí

Chcete-li zahájit postup inicializace nové **skupiny akcí**, vyberte **+ Přidat skupinu akcí**.

![Přidat skupinu akcí na portálu obrazovky](media/alerts-action-groups/action-1.png)

Na stránce **Přidat skupinu akcí** :

 1. Zadejte **název skupiny akcí** a **krátký název**.

 1. Zadejte **předplatné** a **skupinu prostředků**.

 1. Zadejte **název akce**.

 1. Vyberte **typ akce**.  Tím se otevře jiné podokno na pravé straně, kde můžete definovat akci, která se provede při aktivaci.

 1. Pomocí možností v pravém podokně definujte akci.  V tomto případě se používá e-mailové oznámení.

 1. V pravém podokně Akce klikněte na **OK** .

 1. Klikněte na **OK** v dialogovém okně **Přidat skupinu akcí** . 

  ![Akce definování portálu obrazovky](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Nastavit upozornění 

Předchozí kroky vytvořily **skupinu akcí** , která používá e-mail. Můžete také použít oznámení pro telefon, Webhooky, službu Azure Functions atd.  

Chcete-li nakonfigurovat **výstrahu**, přejděte zpět na stránku **výstrahy** a klikněte na možnost **Spravovat pravidla výstrah**.

  ![Definice výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-2.png)

1. Vyberte **prostředek** pro výstrahu.

1. Klikněte na **+ nové pravidlo výstrahy**.

  ![Nové pravidlo výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-3.png)

1. Na stránce **vytvořit pravidlo** zadejte **prostředek**, **podmínku**a **akci**.  V podokně **Akce** vyberte dříve definovanou **skupinu akcí**.

1. V části **Podrobnosti o výstraze**pojmenujte pravidlo upozornění.

1. Klikněte na **vytvořit pravidlo výstrahy**.

  ![Nové pravidlo výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-4.png)

Ověřte, zda je povoleno nové pravidlo upozornění.

  ![Nové pravidlo výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Další kroky
* [Vytváření a Správa skupin akcí v Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [Chování výstrah SMS ve skupinách akcí](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Kurz: použití distribuovaného trasování u jarního cloudu Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
