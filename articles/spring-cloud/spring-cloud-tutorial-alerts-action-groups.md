---
title: 'Kurz: monitorování prostředků cloudové služby Azure na jaře pomocí výstrah a skupin akcí | Microsoft Docs'
description: Naučte se používat výstrahy na jaře Cloud.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.openlocfilehash: cf7e075244a83190b83e629ef300a4903b114a6d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "77920072"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Kurz: monitorování jarních cloudových prostředků pomocí výstrah a skupin akcí

Výstrahy služby Azure jaře Cloud podporují monitorování prostředků na základě podmínek, jako je dostupné úložiště, rychlost požadavků nebo využití dat. Výstraha pošle oznámení, pokud sazby nebo podmínky splňují definované specifikace.

Pro nastavení kanálu výstrah existují dva kroky: 
1. Nastavte skupinu akcí s akcemi, které se mají provést, když se aktivuje výstraha, například e-mail, SMS, Runbook nebo Webhook. Skupiny akcí lze znovu použít mezi různými výstrahami.
2. Nastavte pravidla upozornění. Pravidla vážou modely metrik se skupinami akcí na základě cílového prostředku, metriky, podmínky, agregace času atd.

## <a name="prerequisites"></a>Požadavky
Kromě požadavků na jarní Azure je tento kurz závislý na následujících zdrojích.

* Nasazená instance cloudu Azure pro jaře.  Začněte podle našeho [rychlého](spring-cloud-quickstart-launch-app-cli.md) startu.

* Prostředek Azure, který se má monitorovat Tento příklad monitoruje instanci jarního cloudu.
 
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

1. Na stránce **vytvořit pravidlo** zadejte **prostředek**.

1. Nastavení **podmínky** poskytuje mnoho možností monitorování prostředků vaší **jarního cloudu** .  Kliknutím na **Přidat** otevřete podokno **Konfigurovat logiku signálu** .

1. Vyberte podmínku. V tomto příkladu se používá **procento využití procesoru v systému**.

   ![Nové pravidlo výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-3-1.png)

1. Posuňte se dolů na podokno **Konfigurovat logiku signálu** a nastavte **prahovou hodnotu** , která se má monitorovat.

   ![Nové pravidlo výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-3-2.png)

1. Klikněte na **Done** (Hotovo).

Podrobnosti o podmínkách, které je možné monitorovat, najdete v tématu [Možnosti metrik portálu User Portal](spring-cloud-concept-metrics.md#user-metrics-options).

 V části **Akce**klikněte na **Vybrat skupinu akcí**. V podokně **Akce** vyberte dříve definovanou **skupinu akcí**.

   ![Nové pravidlo výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-3-3.png) 

1. Posuňte se dolů a v části **Podrobnosti o výstraze**pojmenujte pravidlo upozornění.

1. Nastavte **závažnost**.

1. Klikněte na **vytvořit pravidlo výstrahy**.

   ![Nové pravidlo výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-3-4.png)

Ověřte, zda je povoleno nové pravidlo upozornění.

   ![Nové pravidlo výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-4.png)

Pravidlo je také možné vytvořit pomocí stránky **metriky** :

   ![Nové pravidlo výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Další kroky
* [Možnosti metrik portálu User Portal](spring-cloud-concept-metrics.md#user-metrics-options)
* [Vytváření a Správa skupin akcí v Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [Chování výstrah SMS ve skupinách akcí](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Kurz: použití distribuovaného trasování u jarního cloudu Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
