---
title: Správa času řady ve verzi Preview Azure – jak zřizovat a spravovat Azure čas řady ve verzi Preview. | Dokumenty Microsoft
description: Pochopení způsobu, jak zřizovat a spravovat Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1834a59ff675f109267a406e3c2b03411e3bb50b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273496"
---
# <a name="how-to-provision-and-manage-azure-time-series-insights-preview"></a>Jak zřídit a spravovat Azure čas Series Insights ve verzi Preview

Tento dokument popisuje, jak vytvořit a spravovat pomocí prostředí Azure čas Series Insights ve verzi Preview [webu Azure portal](https://portal.azure.com/).

## <a name="overview"></a>Přehled

Stručný přehled o tom, jak zřídit prostředí Azure čas Series Insights ve verzi Preview je popsán dále:

* Prostředí Azure čas Series Insights ve verzi Preview je **PAYG** prostředí.
  * Jako součást procesu vytváření budete muset zadat čas řady ID. ID řady čas může být až tři klíče. Další informace o výběru ID řady času načtením [návodu k výběru ID řady času](./time-series-insights-update-how-to-id.md).
  * Při zřizování prostředí Azure čas Series Insights ve verzi Preview, vytvoříte dva prostředky Azure:

    * Prostředí Azure čas Series Insights ve verzi Preview  
    * Účet služby Azure Storage pro obecné účely V1
  
    Přečtěte si [postup plánování vašeho prostředí](./time-series-insights-update-plan.md).

    >[!IMPORTANT]
    > Zákazníci, kteří používají účty V2 nepovolujte studené nebo archivní vlastnosti v účtu úložiště, které budete používat.

* Každé prostředí Azure čas Series Insights ve verzi Preview může být (volitelně) přidružený zdroj událostí. Čtení [postup přidání zdroje událostí centra](./time-series-insights-how-to-add-an-event-source-eventhub.md) a [postup přidání služby IoT Hub jako zdroj](./time-series-insights-how-to-add-an-event-source-iothub.md) Další informace.
  * Během tohoto kroku vám poskytne vlastnost ID časového razítka a skupinu příjemců jedinečný. Tím, zajistíte, že prostředí má přístup k příslušné události.

* Jakmile je zřizování dokončeno, můžete upravit zásady přístupu a další atributy prostředí do sady vaše obchodní požadavky.

## <a name="new-environment-creation"></a>Vytvoření nového prostředí

Následující kroky popisují, jak vytvořit prostředí Azure čas Series Insights ve verzi Preview:

1. Vyberte **PAYG** tlačítko **SKU** nabídky. Zadat název prostředí, určete, jakou skupinu předplatné a skupina prostředků. Vyberte prostředí, které budou hostované v podporovaném umístění.

1. Zadejte ID řady času

    >[!NOTE]
    > * ID řady čas je velká a malá písmena a neměnné (ho nelze změnit po nastavení).
    > * ID řady čas může být až 3 klíče.
    > * Další informace o výběru ID řady času načtením [návodu k výběru ID řady času](./time-series-insights-update-how-to-id.md).

1. Vytvoření účtu služby Azure Storage, že vyberete název účtu služby Azure Storage a určování výběru replikace. To proto automaticky vytvoří účet služby Azure Storage pro obecné účely V1. Vytvoří se ve stejné oblasti jako prostředí Azure čas Series Insights ve verzi Preview, které jste předtím vybrali.

    ![Vytvoření instance Azure Time Series Insights.][1]

1. Volitelně můžete přidat zdroje událostí.

   * Time Series Insights podporuje [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) a [Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) jako možnosti. Přestože můžete přidat pouze jednu událost zdroje v okamžiku vytvoření prostředí, je zdrojem událostí další přidat později. Doporučujeme vytvořit skupinu jedinečných uživatelů a ujistěte se, že všechny události jsou viditelné pro vaše instance Azure čas Series Insights ve verzi Preview. Můžete vybrat existující skupinu uživatelů nebo vytvořit novou skupinu uživatelů, při přidávání zdroje událostí.

   * Můžete také určit příslušnou vlastnost časového razítka. Ve výchozím nastavení používá Azure Time Series Insights čas zařazení do fronty zpráv pro každý zdroj událostí.

     > [!TIP]
     > Čas zařazení do fronty zpráv nemusí být nejvhodnější nakonfigurované nastavení používané k události služby batch nebo historická data nahrávání scénáře. Ujistěte se, že k ověření rozhodnout a použít nebo není v takových případech vlastnost časového razítka.

    ![Přidání zdroje událostí do instance služby.][2]

1. Zkontrolovat a vytvořit

    ![Přidání zdroje událostí do instance služby.][3]

Potvrďte, že vaše prostředí byly zajišťovaný pomocí požadovaného nastavení.

## <a name="management"></a>Správa

Budete mít možnost spravovat Azure čas Series Insights ve verzi Preview prostředí webu Azure portal. Tady jsou hlavní rozdíly ve správě serverů **PAYG** prostředí Azure čas Series Insights ve verzi Preview na rozdíl od S1 nebo S2 prostředí pomocí webu Azure Portal:

* Na webu Azure portal *přehled* okno zůstane beze změny v Azure Time Series Insights s výjimkou následujícími způsoby:
  * Kapacita je odebrat, protože tento koncept se nevztahuje na **PAYG** prostředí.
  * Byla přidána vlastnost ID řady času. Určuje, jak se vaše data rozdělit na oddíly.
  * Referenční datové sady se odeberou.
  * Zobrazenou adresu URL vás směruje k [Průzkumníka Azure čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md).
  * Je uveden název svého účtu úložiště Azure.

* Na webu Azure Portal *konfigurovat* okno byla odebrána v Azure čas Series Insights ve verzi Preview, protože je **PAYG** prostředí se nedají konfigurovat.

* Na webu Azure Portal *odkaz* okno dat byla odebrána v Azure čas Series Insights ve verzi Preview, protože referenčních dat není součástí **PAYG** prostředí.

![Ověřte vaše instance.][4]

## <a name="next-steps"></a>Další postup

Čtení [postup plánování vašeho prostředí](./time-series-insights-update-plan.md).

Čtení [postup přidání zdroje událostí centra](./time-series-insights-how-to-add-an-event-source-eventhub.md).

Čtení [postup přidání služby IoT Hub jako zdroj](./time-series-insights-how-to-add-an-event-source-iothub.md).

<!-- Images -->
[1]: media/v2-update-manage/manage_one.png
[2]: media/v2-update-manage/manage_two.png
[3]: media/v2-update-manage/manage_three.png
[4]: media/v2-update-manage/manage_four.png
