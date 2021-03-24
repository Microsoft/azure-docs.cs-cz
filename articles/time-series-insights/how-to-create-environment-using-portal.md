---
title: Nastavení Gen2 prostředí pomocí Azure Portal-Azure Time Series Insights Gen2 | Microsoft Docs
description: Naučte se, jak nastavit prostředí v Azure Time Series Insights Gen2 pomocí Azure Portal.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 09068d966df871d4b6804978a543db50bccbee37
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952843"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>Vytvoření prostředí Azure Time Series Insights Gen2 pomocí Azure Portal

Tento článek popisuje, jak vytvořit prostředí Azure Time Series Insights Gen2 pomocí [Azure Portal](https://portal.azure.com/).

Návod k zřizování prostředí vás provede procesem. Dozvíte se, jak vybrat správné ID časové řady a příklady zobrazení ze dvou datových částí JSON.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]

## <a name="overview"></a>Přehled

Když zřizujete prostředí Azure Time Series Insights Gen2, vytvoříte tyto prostředky Azure:

* Prostředí Azure Time Series Insights Gen2, které odpovídá cenovému modelu průběžných plateb.
* Účet Azure Storage
* Volitelný záložní úložiště pro rychlejší a neomezené dotazy

> [!TIP]
>
> * Naučte [se plánovat prostředí](./how-to-plan-your-environment.md).
> * Přečtěte si o tom, jak [Přidat zdroj centra událostí](./how-to-ingest-data-event-hub.md) nebo jak [Přidat zdroj služby IoT Hub](./how-to-ingest-data-iot-hub.md).

V tomto kurzu se naučíte:

1. Každé Azure Time Series Insights prostředí Gen 2 Přidružte ke zdroji událostí. Také zadáte vlastnost ID časového razítka a jedinečnou skupinu uživatelů, aby bylo zajištěno, že prostředí bude mít přístup k příslušným událostem.

1. Po dokončení zřizování můžete upravit zásady přístupu a další atributy prostředí tak, aby vyhovovaly vašim obchodním potřebám.

   > [!NOTE]
   > První krok je nepovinný při zřizování prostředí. Pokud tento krok přeskočíte, musíte později připojit zdroj události k prostředí, aby se data mohla začít předávat do svého prostředí a je možné k němu přihlédnout prostřednictvím dotazů.

## <a name="create-the-environment"></a>Vytvoření prostředí

Vytvoření Azure Time Series Insightsho prostředí Gen 2:

1. Vytvořte prostředek Azure Time Series Insights v části *Internet věcí* na [Azure Portal](https://portal.azure.com/).

1. Jako **vrstvu** vyberte **Gen2 (L1)** . Zadejte název prostředí a vyberte skupinu předplatných a skupinu prostředků, kterou chcete použít. Pak vyberte podporované umístění pro hostování prostředí.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="Vytvořte instanci Azure Time Series Insights." lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. Zadejte ID časové řady.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="Vytvoření konfigurace prostředí Azure Time Series Insights a pokračování." lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * V ID časové řady jsou rozlišována *malá a velká písmena* a *neměnná*. (Po nastavení se nedá změnit.)
   > * ID časových řad můžou být až *tři* klíče. V databázi si můžete představit jako primární klíč, který jednoznačně představuje každý senzor zařízení, který by odesílal data do vašeho prostředí. Může to být jedna vlastnost nebo kombinace až tří vlastností.
   > * Přečtěte si další informace o [tom, jak zvolit ID časové řady](./how-to-select-tsid.md) .

1. Vytvořte účet Azure Storage výběrem názvu účtu úložiště, druhu účtu a určením volby [replikace](../storage/common/redundancy-migration.md?tabs=portal) . Tím se automaticky vytvoří účet Azure Storage. Ve výchozím nastavení se vytvoří účet pro [obecné účely v2](../storage/common/storage-account-overview.md) . Účet se vytvoří ve stejné oblasti jako prostředí Azure Time Series Insights Gen2, které jste předtím vybrali.
Alternativně můžete při vytváření nového prostředí Azure Time Series Gen2 využít vlastní úložiště (BYOS) i [šablonu Azure Resource Manager](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) .

1. **(Volitelné)** Pokud potřebujete rychlejší a neomezené dotazy nad nejnovějšími daty ve vašem prostředí, povolte pro své prostředí služby pro zajištění tepla. V levém navigačním podokně můžete vytvořit nebo odstranit záložní úložiště pomocí možnosti **Konfigurace úložiště** , a to po vytvoření prostředí Azure Time Series Insights Gen2.

1. **(Volitelné)** Nyní můžete přidat zdroj události. Můžete také počkat až po zřízení instance.

   * Azure Time Series Insights podporuje [azure IoT Hub](./how-to-ingest-data-iot-hub.md) a [Azure Event Hubs](./how-to-ingest-data-event-hub.md) jako možnosti zdrojů událostí. I když při vytváření prostředí můžete přidat jenom jeden zdroj události, můžete později přidat další zdroj události.

     Můžete vybrat existující skupinu uživatelů nebo vytvořit novou skupinu uživatelů, když přidáte zdroj události. Ujistěte se, že zdroj události používá jedinečnou skupinu uživatelů pro vaše prostředí ke čtení dat.

   * Vyberte odpovídající vlastnost časového razítka. Ve výchozím nastavení Azure Time Series Insights používá pro každý zdroj události čas zařazený do fronty zpráv.

     > [!TIP]
     > Čas zařazený do fronty nemusí být nejlepším nakonfigurovaným nastavením pro použití ve scénářích událostí Batch nebo historických scénářích odesílání dat. V takových případech Nezapomeňte ověřit, jestli chcete použít nebo nepoužívat vlastnost timestamp.

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="Karta Konfigurace zdroje událostí" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. Výběrem možnosti **zkontrolovat + vytvořit** potvrďte, že je vaše prostředí zřízené a nakonfigurované tak, jak chcete.

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="Revize + vytvořit kartu" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o Azure Time Series Insights všeobecně dostupných prostředích a prostředích Gen2. načtěte [si plánování prostředí](./how-to-plan-your-environment.md).
* Přečtěte si o [zdrojích událostí příjmu streamování](./concepts-streaming-ingestion-event-sources.md) pro prostředí Azure Time Series Insights Gen2.
* Přečtěte si další informace o [tom, jak spravovat vaše prostředí](./how-to-provision-manage.md).
