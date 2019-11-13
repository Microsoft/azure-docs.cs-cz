---
title: Zřizování a Správa prostředí verze Preview – Azure Time Series | Microsoft Docs
description: Naučte se, jak zřídit a spravovat prostředí Azure Time Series Insights ve verzi Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: f66925c20f6767286eb98a7f5fab180845da4c33
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014802"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Zřizování a Správa Azure Time Series Insights ve verzi Preview

Tento článek popisuje, jak vytvořit a spravovat prostředí Azure Time Series Insights ve verzi Preview pomocí [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Přehled

Prostředí Azure Time Series Insights ve verzi Preview představují prostředí s průběžnými platbami (PAYG).

Když zřizujete prostředí Azure Time Series Insights Preview, vytvoříte tyto prostředky Azure:

* Prostředí Azure Time Series Insights Preview  
* Účet Azure Storage pro obecné účely v1
* Volitelný záložní úložiště pro rychlejší a neomezené dotazy
  
Naučte [se plánovat prostředí](./time-series-insights-update-plan.md).

Jednotlivé prostředí pro Azure Time Series Insights ve verzi Preview Přidružte ke zdroji událostí. Pokud chcete získat další informace, přečtěte si téma [Přidání zdroje centra událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md) a [Přidání zdroje služby IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md). Během tohoto kroku zadáte vlastnost ID časového razítka a jedinečnou skupinu uživatelů. Tím zajistíte, že prostředí bude mít přístup k odpovídajícím událostem.

> [!NOTE]
> Předchozí krok je volitelný v pracovním postupu zřizování během vytváření prostředí Time Series Preview. Musíte ale připojit zdroj události k prostředí, aby se data mohla začít přesměrovat do daného prostředí.

Po dokončení zřizování můžete upravit zásady přístupu a další atributy prostředí tak, aby vyhovovaly vašim obchodním požadavkům.

## <a name="create-the-environment"></a>Vytvoření prostředí

Vytvoření prostředí Azure Time Series Insights Preview:

1. V nabídce **SKU** vyberte tlačítko **PAYG** . Zadejte název prostředí a vyberte skupinu předplatných a skupinu prostředků, kterou chcete použít. Pak vyberte podporované umístění pro hostování prostředí.

   [![vytvořit instanci Azure Time Series Insights.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Zadejte ID časové řady.

    >[!NOTE]
    > * V ID časové řady jsou rozlišována malá a velká písmena a neměnná. (Po nastavení se nedá změnit.)
    > * ID časových řad můžou být až tři klíče.
    > * Pokud chcete získat další informace o výběru ID časové řady, přečtěte si téma [Výběr ID časové řady](./time-series-insights-update-how-to-id.md).

1. Vytvořte účet úložiště Azure výběrem názvu účtu úložiště a určením volby replikace. Tím se automaticky vytvoří Azure Storage účet pro obecné účely v1. Účet se vytvoří ve stejné oblasti jako prostředí Azure Time Series Insights verze Preview, které jste předtím vybrali.

    [![vytvořit účet úložiště Azure pro vaši instanci](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. **(Volitelné)** Pokud potřebujete rychlejší a neomezené dotazy nad nejnovějšími daty ve vašem prostředí, povolte pro své prostředí služby pro zajištění tepla. Můžete také vytvořit nebo odstranit záložní úložiště pomocí možnosti **Konfigurace úložiště** v levém navigačním podokně po vytvoření prostředí Time Series Insights Preview.

1. **(Volitelné)** Nyní můžete přidat zdroj události. Případně můžete počkat, dokud se instance nezřídí.

   * Time Series Insights podporuje [azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) a [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) jako možnosti zdrojů událostí. I když při vytváření prostředí můžete přidat jenom jeden zdroj události, můžete později přidat další zdroj události. Můžete vybrat existující skupinu uživatelů nebo vytvořit novou skupinu uživatelů, když přidáte zdroj události. Je nejvhodnější vytvořit jedinečnou skupinu uživatelů, aby se zajistilo, že se všechny události budou zobrazovat v prostředí Azure Time Series Insights Preview.

   * Vyberte odpovídající vlastnost časového razítka. Ve výchozím nastavení Azure Time Series Insights používá pro každý zdroj události čas zařazený do fronty zpráv.

     > [!TIP]
     > Čas zařazený do fronty nemusí být nejlepším nakonfigurovaným nastavením pro použití ve scénářích událostí Batch nebo historických scénářích odesílání dat. V takových případech Nezapomeňte ověřit, jestli chcete použít nebo nepoužívat vlastnost timestamp.

     [Karta zdroje události ![](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Ověřte, že se vaše prostředí zřídilo s požadovaným nastavením.

    [![– kontrola a karta vytvořit](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>Správa prostředí

Prostředí pro správu Azure Time Series Insights ve verzi Preview můžete spravovat pomocí Azure Portal. Při správě prostřednictvím Azure Portal uvidíte několik klíčových rozdílů mezi prostředím Preview Azure Time Series Insights s průběžnými platbami a obecně dostupnými prostředími S1 a S2:

* Okno s **přehledem** Azure Portal se v Azure Time Series Insights nezmění, s výjimkou následujících možností:
  * Kapacita se odebere, protože neplatí pro prostředí s průběžnými platbami.
  * Přidá se vlastnost ID časové řady. Určuje, jak jsou data rozdělená na oddíly.
  * Referenční sady dat se odeberou.
  * Zobrazovaná adresa URL vás přesměruje do [průzkumníka Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).
  * V seznamu je uveden název vašeho účtu úložiště Azure.

* Okno **konfigurace** Azure Portal se odebere v Azure Time Series Insights Preview, protože prostředí PAYG se nedají konfigurovat. Můžete ale použít **konfiguraci úložiště** a nakonfigurovat nově zavedený obchod za tepla.

* Okno **referenčních dat** Azure Portal se v Azure Time Series Insights Preview odebere, protože referenční data nejsou součástí prostředí s průběžnými platbami.

[![Time Series Insights prostředí Preview v Azure Portal](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o Time Series Insights všeobecně dostupných prostředích a prostředích Preview díky [Naplánování prostředí](./time-series-insights-update-plan.md).

- Naučte se [Přidat zdroj centra událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Nakonfigurujte [Zdroj služby IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).
