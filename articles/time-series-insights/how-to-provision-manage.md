---
title: Zřizování a Správa prostředí Gen 2 – Azure Time Series | Microsoft Docs
description: Naučte se zřizovat a spravovat Azure Time Series Insightsho prostředí Gen 2.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: e54e8e9de1df4c8a1c870285d36e4580daaa698a
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667822"
---
# <a name="provision-and-manage-azure-time-series-insights-gen2"></a>Zřizování a Správa Azure Time Series Insights Gen2

Tento článek popisuje, jak vytvořit a spravovat prostředí Azure Time Series Insights Gen2 pomocí [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Přehled

Když zřizujete prostředí Azure Time Series Insights Gen2, vytvoříte tyto prostředky Azure:

* Prostředí Azure Time Series Insights Gen2, které odpovídá cenovému modelu průběžných plateb.
* Účet Azure Storage
* Volitelný záložní úložiště pro rychlejší a neomezené dotazy

> [!TIP]
>
> * Naučte [se plánovat prostředí](./time-series-insights-update-plan.md).
> * Přečtěte si o tom, jak [Přidat zdroj centra událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md) nebo jak [Přidat zdroj služby IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

V tomto kurzu se naučíte:

1. Každé Azure Time Series Insights prostředí Gen 2 Přidružte ke zdroji událostí. Také zadáte vlastnost ID časového razítka a jedinečnou skupinu uživatelů, aby bylo zajištěno, že prostředí bude mít přístup k příslušným událostem.

1. Po dokončení zřizování můžete upravit zásady přístupu a další atributy prostředí tak, aby vyhovovaly vašim obchodním potřebám.

   > [!NOTE]
   > První krok je nepovinný při zřizování prostředí. Pokud tento krok přeskočíte, musíte později připojit zdroj události k prostředí, aby se data mohla začít předávat do svého prostředí a je možné k němu přihlédnout prostřednictvím dotazů.

## <a name="create-the-environment"></a>Vytvoření prostředí

Vytvoření Azure Time Series Insightsho prostředí Gen 2:

1. Vytvořte prostředek Azure Time Series Insights v části *Internet věcí* na [Azure Portal](https://portal.azure.com/).

1. Jako **vrstvu**vyberte **Gen2 (L1)** . Zadejte název prostředí a vyberte skupinu předplatných a skupinu prostředků, kterou chcete použít. Pak vyberte podporované umístění pro hostování prostředí.

   [![Vytvořte instanci Azure Time Series Insights.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Zadejte ID časové řady.

    > [!NOTE]
    >
    > * V ID časové řady jsou rozlišována *malá a velká písmena* a *neměnná*. (Po nastavení se nedá změnit.)
    > * ID časových řad můžou být až *tři* klíče. V databázi si můžete představit jako primární klíč, který jednoznačně představuje každý senzor zařízení, který by odesílal data do vašeho prostředí. Může to být jedna vlastnost nebo kombinace až tří vlastností.
    > * Přečtěte si další informace o [tom, jak zvolit ID časové řady](time-series-insights-update-how-to-id.md) .

1. Vytvořte účet Azure Storage výběrem názvu účtu úložiště, druhu účtu a určením volby [replikace](https://docs.microsoft.com/azure/storage/common/redundancy-migration?tabs=portal) . Tím se automaticky vytvoří účet Azure Storage. Ve výchozím nastavení se vytvoří účet pro [obecné účely v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) . Účet se vytvoří ve stejné oblasti jako prostředí Azure Time Series Insights Gen2, které jste předtím vybrali.
Alternativně můžete při vytváření nového prostředí Azure Time Series Gen2 využít vlastní úložiště (BYOS) i [šablonu ARM](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) .

1. **(Volitelné)** Pokud potřebujete rychlejší a neomezené dotazy nad nejnovějšími daty ve vašem prostředí, povolte pro své prostředí služby pro zajištění tepla. V levém navigačním podokně můžete vytvořit nebo odstranit záložní úložiště pomocí možnosti **Konfigurace úložiště** , a to po vytvoření prostředí Azure Time Series Insights Gen2.

1. **(Volitelné)** Nyní můžete přidat zdroj události. Můžete také počkat až po zřízení instance.

   * Azure Time Series Insights podporuje [azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) a [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) jako možnosti zdrojů událostí. I když při vytváření prostředí můžete přidat jenom jeden zdroj události, můžete později přidat další zdroj události.

     Můžete vybrat existující skupinu uživatelů nebo vytvořit novou skupinu uživatelů, když přidáte zdroj události. Upozorňujeme, že zdroj události vyžaduje pro vaše prostředí jedinečnou skupinu uživatelů, aby na ni bylo možné číst data.

   * Vyberte odpovídající vlastnost časového razítka. Ve výchozím nastavení Azure Time Series Insights používá pro každý zdroj události čas zařazený do fronty zpráv.

     > [!TIP]
     > Čas zařazený do fronty nemusí být nejlepším nakonfigurovaným nastavením pro použití ve scénářích událostí Batch nebo historických scénářích odesílání dat. V takových případech Nezapomeňte ověřit, jestli chcete použít nebo nepoužívat vlastnost timestamp.

     [![Karta Konfigurace zdroje událostí](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Zkontrolujte, jestli je vaše prostředí zřízené a nakonfigurované tak, jak chcete.

    [![Revize + vytvořit kartu](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Správa prostředí

Prostředí Azure Time Series Insights Gen2 můžete spravovat pomocí Azure Portal. Existuje několik klíčových rozdílů mezi prostředím Gen2 a prostředími Gen1 S1 nebo Gen1 S2, které vám pamatujte při správě prostředí prostřednictvím Azure Portal:

* Okno s **přehledem**  Azure Portal Gen2 má následující změny:

  * Kapacita se odebere, protože se nevztahuje na Gen2 prostředí.
  * Přidá se vlastnost **ID časové řady** . Určuje, jak jsou data rozdělená na oddíly.
  * Referenční sady dat se odeberou.
  * Zobrazovaná adresa URL vás přesměruje do [průzkumníka Azure Time Series Insights](./time-series-insights-update-explorer.md).
  * Zobrazí se název vašeho účtu Azure Storage.

* Okno **konfigurace** Azure Portal se odebere, protože jednotky škálování se nevztahují na Azure Time Series Insights Gen2 prostředí. Můžete ale použít **konfiguraci úložiště** a nakonfigurovat nově zavedený obchod za tepla.

* Okno **referenčních dat** Azure Portal se v Azure Time Series Insights Gen2 odebralo, protože koncept referenčních dat byl nahrazen [modelem Time Series model (TSM)](/azure/time-series-insights/concepts-model-overview).

[![Azure Time Series Insights prostředí Gen2 v Azure Portal](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o Azure Time Series Insights všeobecně dostupných prostředích a prostředích Gen2. načtěte [si plánování prostředí](./time-series-insights-update-plan.md).

* Naučte se [Přidat zdroj centra událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md).

* Nakonfigurujte [Zdroj služby IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).
