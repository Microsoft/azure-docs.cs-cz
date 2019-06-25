---
title: Zřízení a správě Azure čas řady ve verzi Preview | Dokumentace Microsoftu
description: Pochopení způsobu, jak zřizovat a spravovat Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 6251df2317ceff9dded92f2d829bfab0503fdf1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237587"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Zřízení a správě Azure čas Series Insights ve verzi Preview

Tento článek popisuje, jak vytvořit a spravovat pomocí prostředí Azure čas Series Insights ve verzi Preview [webu Azure portal](https://portal.azure.com/).

## <a name="overview"></a>Přehled

Prostředí Azure čas Series Insights ve verzi Preview je prostředí průběžných plateb (PAYG).

Při zřizování prostředí Azure čas Series Insights ve verzi Preview, vytvoříte dva prostředky Azure:

* Prostředí Azure čas Series Insights ve verzi Preview  
* Účet služby Azure Storage pro obecné účely v1
  
Přečtěte si [postup plánování vašeho prostředí](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> Ve verzi Preview, ujistěte se, že používáte Azure Storage pro obecné účely v1 (GPv1) účet.

Můžete případně přidružit zdroje událostí každé prostředí Azure čas Series Insights ve verzi Preview. Další informace najdete v článku [přidání zdroje událostí centra](./time-series-insights-how-to-add-an-event-source-eventhub.md) a [přidání zdroje IoT hub](./time-series-insights-how-to-add-an-event-source-iothub.md). Zadejte vlastnost ID časového razítka a Skupina jedinečných uživatelů během tohoto kroku. Tím se zajistí, že prostředí má přístup k příslušné události.

Jakmile je zřizování dokončeno, můžete upravit zásady přístupu a další atributy prostředí tak, aby vyhovoval vašim podnikovým požadavkům.

## <a name="create-the-environment"></a>Vytvoření prostředí

Následující kroky popisují, jak vytvořit prostředí Azure čas Series Insights ve verzi Preview:

1. Vyberte **PAYG** tlačítko **SKU** nabídky. Zadat název prostředí a zvolte možnost, která skupina předplatné a skupina prostředků. Vyberte prostředí, které budou hostované v podporovaném umístění.

   [![Vytvoření instance Azure Time Series Insights.](media/v2-update-manage/manage_three.PNG)](media/v2-update-manage/manage_three.PNG#lightbox)

1. Zadejte časové řady ID.

    >[!NOTE]
    > * ID řady čas je velká a malá písmena a neměnné. (Ho nelze změnit po nastavení.)
    > * ID řady čas může být až tři klíče.
    > * Další informace o výběru ID řady času, najdete v článku [zvolte ID řady času](./time-series-insights-update-how-to-id.md).

1. Vytvoření účtu služby Azure storage, že vyberete název účtu úložiště a určování výběru replikace. To proto automaticky vytvoří účet služby Azure Storage pro obecné účely v1. Vytvoří se ve stejné oblasti jako, který jste dříve vybrali prostředí Azure čas Series Insights ve verzi Preview.

    [![Vytvoření účtu služby Azure storage pro vaši instanci](media/v2-update-manage/manage_five.PNG)](media/v2-update-manage/manage_five.PNG#lightbox)

1. Volitelně můžete přidat zdroje událostí.

   * Time Series Insights podporuje [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) a [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) jako možnosti. I když se v okamžiku vytvoření prostředí můžete přidat pouze jednu událost zdroje, můžete přidat další zdroje událostí později. Doporučujeme vytvořit skupinu jedinečných uživatelů k zajištění, že všechny události jsou viditelné pro vaše instance Azure čas Series Insights ve verzi Preview. Můžete vybrat existující skupinu uživatelů nebo vytvořit novou skupinu uživatelů, při přidávání zdroje událostí.

   * Měli byste také zvolit odpovídající vlastnost časového razítka. Ve výchozím nastavení používá Azure Time Series Insights čas zařazení do fronty zpráv pro každý zdroj událostí.

     > [!TIP]
     > Čas zařazení do fronty zpráv nemusí být nejvhodnější nakonfigurované nastavení používané k události služby batch nebo historická data nahrávání scénáře. Ujistěte se, že k ověření rozhodnout a použít nebo není v takových případech vlastnost časového razítka.

     [![Karta Zdroj události](media/v2-update-manage/manage_two.PNG)](media/v2-update-manage/manage_two.PNG#lightbox)

1. Potvrďte, že vaše prostředí byly zajišťovaný pomocí požadovaného nastavení.

    [![Zkontrolovat a vytvořit kartu](media/v2-update-manage/manage_three.PNG)](media/v2-update-manage/manage_three.PNG#lightbox)

## <a name="manage-the-environment"></a>Správa prostředí

Prostředí Azure čas Series Insights ve verzi Preview můžete spravovat pomocí webu Azure portal. Tady jsou hlavní rozdíly ve správě serverů průběžné platby Azure čas Series Insights ve verzi Preview prostředí, na rozdíl od S1 nebo S2, pomocí webu Azure portal:

* Azure portal **přehled** okno je beze změny v Azure Time Series Insights, s výjimkou následujícími způsoby:
  * Kapacita je odebrat, protože tento koncept se nevztahuje na průběžné platby prostředí.
  * Byla přidána vlastnost ID řady času. Určuje, jak se vaše data rozdělit na oddíly.
  * Referenční datové sady se odeberou.
  * Zobrazenou adresu URL vás směruje k [Průzkumníka Azure čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md).
  * Je uveden název účtu úložiště Azure.

* Azure portal **konfigurovat** okno byly odebrané v Azure čas Series Insights ve verzi Preview, protože prostředí průběžné platby se nedají konfigurovat.

* Azure portal **odkazují na data** okno byly odebrané v Azure čas Series Insights ve verzi Preview, protože referenčních dat není součástí prostředí průběžné platby.

[![Prostředí Time Series Insights ve verzi Preview na webu Azure Portal](media/v2-update-manage/manage_four.PNG)](media/v2-update-manage/manage_four.PNG#lightbox)

## <a name="next-steps"></a>Další postup

- Čtení [plánování prostředí](./time-series-insights-update-plan.md).

- Zjistěte, jak [přidání zdroje událostí centra](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Konfigurace [zdroj IoT hub](./time-series-insights-how-to-add-an-event-source-iothub.md).