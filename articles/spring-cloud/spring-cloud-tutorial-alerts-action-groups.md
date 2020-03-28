---
title: 'Kurz: Monitorování prostředků Azure Spring Cloud pomocí výstrah a skupin akcí | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak používat upozornění spring cloudu.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.openlocfilehash: cf7e075244a83190b83e629ef300a4903b114a6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77920072"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Kurz: Sledování prostředků spring cloudu pomocí výstrah a skupin akcí

Výstrahy Azure Spring Cloud podporují prostředky monitorování na základě podmínek, jako je dostupné úložiště, rychlost požadavků nebo využití dat. Výstraha odešle oznámení, pokud sazby nebo podmínky splňují definované specifikace.

Existují dva kroky k nastavení kanálu výstrah: 
1. Nastavte skupinu akcí s akcemi, které se mají akce přijmout při aktivaci výstrahy, jako je e-mail, SMS, Runbook nebo Webhook. Skupiny akcí lze znovu použít mezi různými výstrahami.
2. Nastavte pravidla výstrahy. Pravidla svazují metrické vzory se skupinami akcí na základě cílového prostředku, metriky, podmínky, agregace času atd.

## <a name="prerequisites"></a>Požadavky
Kromě požadavků Azure Spring tento kurz závisí na následujících prostředků.

* Nasazená instance Azure Spring Cloud.  Začněte podle našeho [rychlého startu.](spring-cloud-quickstart-launch-app-cli.md)

* Prostředek Azure ke sledování. Tento příklad monitoruje instanci Spring Cloud.
 
Následující postupy inicializují **skupinu akcí** i **výstrahu** počínaje možností **Výstrahy** v levém navigačním podokně instance Spring Cloud. (Postup můžete také spustit ze stránky **Přehled monitorování** na webu Azure Portal.) 

Přejděte ze skupiny prostředků do instance Spring Cloud. V levém podokně vyberte **Výstrahy** a pak **vyberte Spravovat akce**:

![Stránka skupiny prostředků portálu Snímat stránku portálu](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Nastavit skupinu akcí

Chcete-li zahájit postup inicializaci nové **skupiny akcí**, vyberte **možnost + Přidat skupinu akcí**.

![Portál s obrazovky Přidat skupinu akcí](media/alerts-action-groups/action-1.png)

Na stránce **Přidat skupinu akcí:**

 1. Zadejte **název skupiny akcí** a krátký **název**.

 1. Zadejte **odběr** a **skupinu prostředků**.

 1. Zadejte **název akce**.

 1. Vyberte **typ akce**.  Otevře se další podokno vpravo a definujete akci, která bude provedena při aktivaci.

 1. Definujte akci pomocí možností v pravém podokně.  Tento případ používá e-mailové oznámení.

 1. V pravém podokně akcí klikněte na **OK.**

 1. V dialogovém okně **Přidat skupinu akcí** klepněte na **tlačítko OK.** 

  ![Akce pro definici portálu obrazovky](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Nastavit výstrahu 

Předchozí kroky vytvořily **skupinu akcí,** která používá e-mail. Můžete také použít oznámení telefonu, webhooky, funkce Azure atd.  

Chcete-li nakonfigurovat **výstrahu**, přejděte zpět na stránku **Výstrahy** a klepněte na tlačítko **Spravovat pravidla výstrah**.

  ![Upozornění na definici portálu obrazovky](media/alerts-action-groups/alerts-2.png)

1. Vyberte **zdroj** pro výstrahu.

1. Klepněte na tlačítko **+ Nové pravidlo výstrah**.

   ![Nové pravidlo výstrahy portálu snímek obrazovky](media/alerts-action-groups/alerts-3.png)

1. Na stránce **Vytvořit pravidlo** zadejte **zdroj**.

1. Nastavení **PODMÍNKA** poskytuje mnoho možností pro sledování prostředků **Spring Cloud.**  Klepnutím na tlačítko **Přidat** otevřete podokno **Logika signálu Konfigurace.**

1. Vyberte podmínku. Tento příklad používá **procento využití procesoru systému**.

   ![Nové pravidlo výstrahy portálu snímek obrazovky](media/alerts-action-groups/alerts-3-1.png)

1. Posunutím dolů v podokně **Logika signálu** nastavte **hodnotu Práh** pro monitorování.

   ![Nové pravidlo výstrahy portálu snímek obrazovky](media/alerts-action-groups/alerts-3-2.png)

1. Klikněte na **Done** (Hotovo).

Podrobnosti o podmínkách, které jsou k dispozici pro sledování, naleznete v [tématu Možnosti metrik uživatelského portálu](spring-cloud-concept-metrics.md#user-metrics-options).

 V části **AKCE**klikněte na **Vybrat skupinu akcí**. V podokně **AKCE** vyberte dříve definovanou **skupinu akcí**.

   ![Nové pravidlo výstrahy portálu snímek obrazovky](media/alerts-action-groups/alerts-3-3.png) 

1. Posuňte se dolů a v části **PODROBNOSTI VÝSTRAHY**pojmenujte pravidlo výstrahy.

1. Nastavte **závažnost**.

1. Klepněte na **tlačítko Vytvořit pravidlo výstrahy**.

   ![Nové pravidlo výstrahy portálu snímek obrazovky](media/alerts-action-groups/alerts-3-4.png)

Ověřte, zda je nové pravidlo výstrahy povoleno.

   ![Nové pravidlo výstrahy portálu snímek obrazovky](media/alerts-action-groups/alerts-4.png)

Pravidlo lze také vytvořit pomocí stránky **Metriky:**

   ![Nové pravidlo výstrahy portálu snímek obrazovky](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Další kroky
* [Možnosti metrik uživatelského portálu](spring-cloud-concept-metrics.md#user-metrics-options)
* [Vytváření a správa skupin akcí na webu Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [Chování upozornění sms ve skupinách akcí](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Kurz: Použití distribuovaného trasování pomocí Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
