---
title: 'Kurz: monitorování prostředků cloudové služby Azure na jaře pomocí výstrah a skupin akcí | Microsoft Docs'
description: Naučte se používat výstrahy na jaře Cloud.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.custom: devx-track-java
ms.openlocfilehash: c31c52d278c1e40b18b3308ca154b934d060d403
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071172"
---
# <a name="tutorial-how-to-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Kurz: jak monitorovat prostředky jarní cloudu pomocí výstrah a skupin akcí

Výstrahy služby Azure jaře Cloud podporují monitorování prostředků na základě podmínek, jako je dostupné úložiště, rychlost požadavků nebo využití dat. Výstraha pošle oznámení, pokud sazby nebo podmínky splňují definované specifikace.

Pro nastavení kanálu výstrah existují dva kroky: 
1. Nastavte skupinu akcí s akcemi, které se mají provést, když se aktivuje výstraha, například e-mail, SMS, Runbook nebo Webhook. Skupiny akcí lze znovu použít mezi různými výstrahami.
2. Nastavte pravidla upozornění. Pravidla vážou modely metrik se skupinami akcí na základě cílového prostředku, metriky, podmínky, agregace času atd.

## <a name="prerequisites"></a>Předpoklady

Kromě požadavků na jarní kapacitu Azure postup v tomto kurzu pracuje s nasazenou instancí cloudu pro Azure jaře.  Začněte pomocí [rychlého](spring-cloud-quickstart-launch-app-cli.md) startu.

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

1. Klikněte na **Hotovo**.

Podrobnosti o podmínkách, které je možné monitorovat, najdete v tématu [Možnosti metrik portálu User Portal](spring-cloud-concept-metrics.md#user-metrics-options).

 V části **Akce**klikněte na **Vybrat skupinu akcí**. V podokně **Akce** vyberte dříve definovanou **skupinu akcí**.

   ![Nové pravidlo výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-3-3.png) 

1. Posuňte se dolů a v části **Podrobnosti o výstraze**pojmenujte pravidlo upozornění.

1. Nastavte **závažnost**.

1. Klikněte na **Vytvořit pravidlo upozornění**.

   ![Nové pravidlo výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-3-4.png)

Ověřte, zda je povoleno nové pravidlo upozornění.

   ![Nové pravidlo výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-4.png)

Pravidlo je také možné vytvořit pomocí stránky **metriky** :

   ![Nové pravidlo výstrahy na portálu obrazovky](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak nastavit výstrahy a skupiny akcí pro aplikaci pro jarní Cloud v Azure. Další informace o skupinách akcí najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Vytváření a správa skupin akcí na webu Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

> [!div class="nextstepaction"]
> [Chování výstrah SMS ve skupinách akcí](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
