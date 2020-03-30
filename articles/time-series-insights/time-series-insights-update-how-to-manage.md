---
title: Zřízení a správa prostředí Preview – Azure Time Series | Dokumenty společnosti Microsoft
description: Přečtěte si, jak zřídit a spravovat prostředí Azure Time Series Insights Preview.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087213"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Zřizování a správa Azure Time Series Insights Preview

Tento článek popisuje, jak vytvořit a spravovat prostředí Azure Time Series Insights Preview pomocí [portálu Azure](https://portal.azure.com/).

## <a name="overview"></a>Přehled

Prostředí Azure Time Series Insights Preview jsou prostředí *pay-as-you-go* (PAYG).

Když zřídíte prostředí Azure Time Series Insights Preview, vytvoříte tyto prostředky Azure:

* Prostředí Azure Time Series Insights Preview  
* Účet azure storage pro obecné účely v1
* Volitelný teplý obchod pro rychlejší a neomezené dotazy

> [!TIP]
> * Přečtěte [si, jak naplánovat prostředí](./time-series-insights-update-plan.md).
> * Přečtěte si, jak [přidat zdroj centra událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md) nebo jak přidat zdroj centra [IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

V tomto kurzu se naučíte:

1. **(Nepovinné)** Přidružte každé prostředí Azure Time Series Insights Preview ke zdroji událostí. Poskytnete také vlastnost ID časového razítka a jedinečnou skupinu spotřebitelů, abyste zajistili, že prostředí bude mít přístup k příslušným událostem.

   > [!NOTE]
   > Předchozí krok je volitelný při zřizování prostředí. Pokud tento krok přeskočíte, musíte později připojit zdroj události k prostředí, aby bylo možné přistupovat k datům v prostředí.

1. Po dokončení zřizování můžete upravit zásady přístupu a další atributy prostředí tak, aby vyhovovaly vašim obchodním požadavkům.

## <a name="create-the-environment"></a>Vytvoření prostředí

Vytvoření prostředí Azure Time Series Insights Preview:

1. Vyberte **PAYG** jako **úroveň**. Zadejte název prostředí a zvolte skupinu předplatného a skupinu prostředků, které chcete použít. Pak vyberte podporované umístění pro hostování prostředí.

   [![Vytvořte instanci Azure Time Series Insights.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Zadejte ID časové řady.

    > [!NOTE]
    > * ID časové řady *rozlišuje malá a velká písmena* a *neměnná*. (Po nastavení jej nelze změnit.)
    > * ID časové řady mohou být až *tři* klávesy.
    > * Přečtěte si více o [tom, jak si vybrat ID časové řady](time-series-insights-update-how-to-id.md)

1. Vytvořte účet Úložiště Azure výběrem názvu účtu úložiště a určením volby replikace. Tím se automaticky vytvoří účet Azure Storage pro obecné účely v1. Účet se vytvoří ve stejné oblasti jako prostředí Azure Time Series Insights Preview, které jste dříve vybrali.

    [![Konfigurace chladírenského úložiště](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Nepovinné)** Pokud chcete rychlejší a neomezené dotazy na nejnovější data ve vašem prostředí, povolte teplé úložiště pro vaše prostředí. Po vytvoření prostředí Náhled přehledů časové řady můžete také vytvořit nebo odstranit teplé úložiště prostřednictvím **možnosti Konfigurace úložiště** v levém navigačním podokně.

    [![Konfigurace teplého úložiště](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Nepovinné)** Nyní můžete přidat zdroj událostí. Můžete také počkat, až po zřízení instance.

   * Time Series Insights podporuje [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) a [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) jako možnosti zdroje událostí. I když při vytváření prostředí můžete přidat pouze jeden zdroj událostí, můžete později přidat další zdroj událostí. 
   
     Při přidání zdroje událostí můžete vybrat existující skupinu spotřebitelů nebo vytvořit novou skupinu spotřebitelů. Nejlepší je vytvořit jedinečnou skupinu spotřebitelů, která zajistí, že všechny události budou viditelné pro vaše prostředí Azure Time Series Insights Preview.

   * Zvolte příslušnou vlastnost časového razítka. Ve výchozím nastavení Azure Time Series Insights používá čas zařazený do fronty zpráv pro každý zdroj událostí.

     > [!TIP]
     > Čas zařazený do fronty zpráv nemusí být nejlépe nakonfigurovaným nastavením pro použití ve scénářích dávkových událostí nebo v historických scénářích odesílání dat. V takových případech se ujistěte, ověřit vaše rozhodnutí použít nebo nepoužívat timestamp vlastnost.

     [![Karta konfigurace Zdroje událostí](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Zkontrolujte, zda bylo vaše prostředí zřízeno a nakonfigurováno požadovaným způsobem.

    [![Karta Revize + Vytvoření](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Správa prostředí

Prostředí Azure Time Series Insights Preview můžete spravovat pomocí portálu Azure. Existuje několik klíčových rozdílů mezi prostředím PAYG Azure Time Series Insights Preview a obecně dostupnými prostředími S1 nebo S2, které je třeba mít na paměti při správě prostředí prostřednictvím portálu Azure:

* Okno **Přehled** náhledu portálu Azure obsahuje následující změny:

  * Kapacita je odebrána, protože se nevztahuje na prostředí PAYG.
  * Je přidána vlastnost **ID časové řady.** Určuje, jak jsou data rozdělena na oddíly.
  * Referenční datové sady jsou odebrány.
  * Zobrazená adresa URL vás přesměruje na [průzkumník Náhled přehledů Azure Time Series](./time-series-insights-update-explorer.md).
  * Je v seznamu uvedený název účtu Azure Storage.

* Okno **Konfigurace** na webu Azure Portal se odebere ve verzi Azure Time Series Insights Preview, protože prostředí PAYG nelze konfigurovat. **Konfiguraci úložiště** však můžete použít ke konfiguraci nově zavedeného teplého úložiště.

* Okno **referenčních dat** portálu Azure portal se odebere ve verzi Azure Time Series Insights Preview, protože referenční data nejsou součástí prostředí PAYG.

[![Prostředí Time Series Insights Preview na webu Azure Portal](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Další informace o obecně dostupných prostředích Time Series Insights a prostředích náhledu najdete v načíst [naplánování prostředí](./time-series-insights-update-plan.md).

- Přečtěte si, jak [přidat zdroj centra událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Konfigurace [zdroje centra IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).
