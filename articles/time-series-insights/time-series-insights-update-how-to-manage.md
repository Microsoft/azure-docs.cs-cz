---
title: Zřizování a Správa služby Azure Time Series Preview | Microsoft Docs
description: Pochopíte, jak zřídit a spravovat Azure Time Series Insights Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: a84c587a2666982e862f0640b30d2d6bef3b9a42
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744659"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Zřizování a Správa Azure Time Series Insights ve verzi Preview

Tento článek popisuje, jak vytvořit a spravovat prostředí Azure Time Series Insights ve verzi Preview pomocí [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Přehled

Prostředí Azure Time Series Insights ve verzi Preview představují prostředí s průběžnými platbami (PAYG).

Když zřizujete prostředí Azure Time Series Insights Preview, vytvoříte dva prostředky Azure:

* Prostředí Azure Time Series Insights Preview  
* Účet Azure Storage pro obecné účely v1
  
Naučte [se plánovat prostředí](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> Pro verzi Preview se ujistěte, že používáte účet Azure Storage pro obecné účely V1 (GPv1).

Volitelně můžete Azure Time Series Insights každé prostředí ve verzi Preview přidružit ke zdroji událostí. Pokud chcete získat další informace, přečtěte si téma [Přidání zdroje centra událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md) a [Přidání zdroje služby IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md). Během tohoto kroku zadáte vlastnost ID časového razítka a jedinečnou skupinu uživatelů. Tím zajistíte, že prostředí bude mít přístup k odpovídajícím událostem.

Po dokončení zřizování můžete upravit zásady přístupu a další atributy prostředí tak, aby vyhovovaly vašim obchodním požadavkům.

## <a name="create-the-environment"></a>Vytvoření prostředí

Následující postup popisuje, jak vytvořit prostředí Azure Time Series Insights Preview:

1. V nabídce **SKU** vyberte tlačítko **PAYG** . Zadejte název prostředí a vyberte skupinu předplatných a kterou skupinu prostředků použít. Pak vyberte podporované umístění pro prostředí, ve kterém se má hostovat.

   [![Vytvořte instanci Azure Time Series Insights.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Zadejte ID časové řady.

    >[!NOTE]
    > * V ID časové řady jsou rozlišována malá a velká písmena a neměnná. (Po nastavení se nedá změnit.)
    > * ID časových řad můžou být až tři klíče.
    > * Pokud chcete získat další informace o výběru ID časové řady, přečtěte si téma [Výběr ID časové řady](./time-series-insights-update-how-to-id.md).

1. Vytvořte účet úložiště Azure výběrem názvu účtu úložiště a určením volby replikace. Tím se automaticky vytvoří Azure Storage účet pro obecné účely v1. Vytvoří se ve stejné oblasti jako prostředí Azure Time Series Insights verze Preview, které jste předtím vybrali.

    [![Vytvoření účtu služby Azure Storage pro vaši instanci](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. Volitelně můžete přidat zdroj události.

   * Time Series Insights podporuje [azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) a [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) jako možnosti. I když v čase vytvoření prostředí můžete přidat jenom jeden zdroj událostí, můžete později přidat další zdroj události. Je nejvhodnější vytvořit jedinečnou skupinu uživatelů, aby se zajistilo, že všechny události budou viditelné pro vaši instanci služby Azure Time Series Insights Preview. Můžete vybrat existující skupinu uživatelů nebo vytvořit novou skupinu uživatelů při přidávání zdroje událostí.

   * Měli byste také zvolit příslušnou vlastnost časového razítka. Ve výchozím nastavení Azure Time Series Insights používá zprávy ve frontě pro každý zdroj události.

     > [!TIP]
     > Čas zařazený do fronty nemusí být nejlepším nakonfigurovaným nastavením pro použití ve scénářích dávkového zpracování událostí nebo historických dat. Ujistěte se, že jste ověřili, že v takových případech chcete použít nebo nepoužívat vlastnost časového razítka.

     [![Karta zdroje událostí](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Ověřte, že se vaše prostředí zřídilo s požadovaným nastavením.

    [![Revize + vytvořit kartu](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>Správa prostředí

Prostředí pro správu Azure Time Series Insights ve verzi Preview můžete spravovat pomocí Azure Portal. Tady jsou hlavní rozdíly ve správě prostředí PAYG Azure Time Series Insights ve verzi Preview na rozdíl od prostředí S1 nebo S2 pomocí Azure Portal:

* Okno s **přehledem** Azure Portal se v Azure Time Series Insights nezmění, s výjimkou následujících možností:
  * Kapacita se odebere, protože tento koncept není relevantní pro prostředí PAYG.
  * Byla přidána vlastnost ID časové řady. Určuje, jak jsou data rozdělená na oddíly.
  * Referenční sady dat se odeberou.
  * Zobrazovaná adresa URL vás přesměruje do [průzkumníka Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).
  * V seznamu je uveden název vašeho účtu úložiště Azure.

* Okno **konfigurace** Azure Portal se odebralo v Azure Time Series Insights Preview, protože prostředí PAYG se nedají konfigurovat.

* V Azure Time Series Insights Preview se odebralo okno **referenčních dat** Azure Portal, protože referenční data nejsou součástí prostředí PAYG.

[![Prostředí Time Series Insights Preview v Azure Portal](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Další postup

- Přečtěte si téma [plánování prostředí](./time-series-insights-update-plan.md).

- Naučte se [Přidat zdroj centra událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Nakonfigurujte [Zdroj služby IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).