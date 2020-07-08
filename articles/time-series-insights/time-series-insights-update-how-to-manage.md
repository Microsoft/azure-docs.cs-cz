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
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1ec0d9c7ecf16c60c32abdf08b358268f460edb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77087213"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Zřizování a Správa Azure Time Series Insights ve verzi Preview

Tento článek popisuje, jak vytvořit a spravovat prostředí Azure Time Series Insights ve verzi Preview pomocí [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Přehled

Prostředí Azure Time Series Insights ve verzi Preview představují prostředí s *průběžnými platbami* (PAYG).

Když zřizujete prostředí Azure Time Series Insights Preview, vytvoříte tyto prostředky Azure:

* Prostředí Azure Time Series Insights Preview  
* Účet Azure Storage pro obecné účely v1
* Volitelný záložní úložiště pro rychlejší a neomezené dotazy

> [!TIP]
> * Naučte [se plánovat prostředí](./time-series-insights-update-plan.md).
> * Přečtěte si o tom, jak [Přidat zdroj centra událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md) nebo jak [Přidat zdroj služby IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

V tomto kurzu se naučíte:

1. **(Volitelné)** Jednotlivé prostředí pro Azure Time Series Insights ve verzi Preview Přidružte ke zdroji událostí. Také zadáte vlastnost ID časového razítka a jedinečnou skupinu uživatelů, aby bylo zajištěno, že prostředí bude mít přístup k příslušným událostem.

   > [!NOTE]
   > Předchozí krok je nepovinný při zřizování prostředí. Pokud tento krok přeskočíte, musíte později připojit zdroj události k prostředí, aby k nim bylo možné připojovat data v prostředí.

1. Po dokončení zřizování můžete upravit zásady přístupu a další atributy prostředí tak, aby vyhovovaly vašim obchodním požadavkům.

## <a name="create-the-environment"></a>Vytvoření prostředí

Vytvoření prostředí Azure Time Series Insights Preview:

1. Jako **vrstvu**vyberte **PAYG** . Zadejte název prostředí a vyberte skupinu předplatných a skupinu prostředků, kterou chcete použít. Pak vyberte podporované umístění pro hostování prostředí.

   [![Vytvořte instanci Azure Time Series Insights.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Zadejte ID časové řady.

    > [!NOTE]
    > * V ID časové řady jsou rozlišována *malá a velká písmena* a *neměnná*. (Po nastavení se nedá změnit.)
    > * ID časových řad můžou být až *tři* klíče.
    > * Přečtěte si další informace o [tom, jak zvolit ID časové řady](time-series-insights-update-how-to-id.md) .

1. Vytvořte účet Azure Storage výběrem názvu účtu úložiště a určením volby replikace. Tím se automaticky vytvoří Azure Storage účet pro obecné účely v1. Účet se vytvoří ve stejné oblasti jako prostředí Azure Time Series Insights verze Preview, které jste předtím vybrali.

    [![Konfigurace studeného úložiště](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Volitelné)** Pokud potřebujete rychlejší a neomezené dotazy nad nejnovějšími daty ve vašem prostředí, povolte pro své prostředí služby pro zajištění tepla. Můžete také vytvořit nebo odstranit záložní úložiště pomocí možnosti **Konfigurace úložiště** v levém navigačním podokně po vytvoření prostředí Time Series Insights Preview.

    [![Konfigurace služby teplé úložiště](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Volitelné)** Nyní můžete přidat zdroj události. Můžete také počkat až po zřízení instance.

   * Time Series Insights podporuje [azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) a [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) jako možnosti zdrojů událostí. I když při vytváření prostředí můžete přidat jenom jeden zdroj události, můžete později přidat další zdroj události. 
   
     Můžete vybrat existující skupinu uživatelů nebo vytvořit novou skupinu uživatelů, když přidáte zdroj události. Je nejvhodnější vytvořit jedinečnou skupinu uživatelů, aby se zajistilo, že se všechny události budou zobrazovat v prostředí Azure Time Series Insights Preview.

   * Vyberte odpovídající vlastnost časového razítka. Ve výchozím nastavení Azure Time Series Insights používá pro každý zdroj události čas zařazený do fronty zpráv.

     > [!TIP]
     > Čas zařazený do fronty nemusí být nejlepším nakonfigurovaným nastavením pro použití ve scénářích událostí Batch nebo historických scénářích odesílání dat. V takových případech Nezapomeňte ověřit, jestli chcete použít nebo nepoužívat vlastnost timestamp.

     [![Karta Konfigurace zdroje událostí](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Zkontrolujte, jestli je vaše prostředí zřízené a nakonfigurované tak, jak chcete.

    [![Revize + vytvořit kartu](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Správa prostředí

Prostředí pro správu Azure Time Series Insights ve verzi Preview můžete spravovat pomocí Azure Portal. K dispozici je několik klíčových rozdílů mezi prostředím PAYG Azure Time Series Insights Preview a všeobecně dostupnými prostředími S1 a S2, která vám pomají na paměti při správě prostředí prostřednictvím Azure Portal:

* Okno s **přehledem** Azure Portal Preview obsahuje následující změny:

  * Kapacita se odebere, protože se nevztahuje na PAYG prostředí.
  * Přidá se vlastnost **ID časové řady** . Určuje, jak jsou data rozdělená na oddíly.
  * Referenční sady dat se odeberou.
  * Zobrazovaná adresa URL vás přesměruje do [průzkumníka Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).
  * Zobrazí se název vašeho účtu Azure Storage.

* Okno **konfigurace** Azure Portal se odebere v Azure Time Series Insights Preview, protože prostředí PAYG se nedají konfigurovat. Můžete ale použít **konfiguraci úložiště** a nakonfigurovat nově zavedený obchod za tepla.

* Okno **referenčních dat** Azure Portal se v Azure Time Series Insights Preview odebere, protože referenční data nejsou součástí prostředí PAYG.

[![Prostředí Time Series Insights Preview v Azure Portal](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o Time Series Insights všeobecně dostupných prostředích a prostředích Preview díky [Naplánování prostředí](./time-series-insights-update-plan.md).

- Naučte se [Přidat zdroj centra událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Nakonfigurujte [Zdroj služby IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).
