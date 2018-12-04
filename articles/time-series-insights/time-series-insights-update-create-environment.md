---
title: Vytvoření prostředí Azure Time Series Insights (preview) | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit prostředí Azure Time Series update
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: 7e256f032c67649a4a8b01311e70d6bc9307ae92
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852904"
---
# <a name="provisioning-and-managing-an-azure-time-series-insights-preview-environment"></a>Zřizování a správa prostředí Azure Time Series Insights (preview)

Tento dokument popisuje, jak zřizovat a spravovat nové prostředí Azure Time Series Insights (preview) na webu Azure Portal.

## <a name="overview"></a>Přehled

Stručný popis o zřizování následující aktualizace služby Time Series Insights:

* Zřízení prostředí Azure Time Series Insights (TSI) aktualizace.
* Jako součást procesu vytváření budete muset zadat **ID řady času**. Může být až **tři** (3) klíče. Další informace o [výběr času řady ID](./time-series-insights-update-how-to-id.md).
* Zřízení prostředí Azure TSI aktualizace vytvoříte dva prostředky Azure, prostředí TSI aktualizace a účet služby Azure Storage pro obecné účely V1.  
* V budoucnu nových zákazníků Azure bude ve výchozím nastavení pouze možné zřídit účet služby Azure Storage pro obecné účely V2, proto budeme podporovat ho po této změně.  
* Nepovolujte studené nebo archivní vlastnosti v účtu úložiště, které budete používat.
* Pak můžete volitelně připojit prostředí update ke zdroji podporovaných událostí Time Series Insights (například služby IoT Hub).
* Tady byste chtěli poskytnout **ID časového razítka** vlastnost a zadejte skupinu příjemců jedinečný a ujistěte se, že prostředí mají přístup ke všem událostem.
* Po zřízení, pak můžete volitelně spravovat zásady přístupu a jiné prostředí atributy, které podporují vaše obchodní požadavky.

## <a name="new-environment-creation"></a>Vytvoření nového prostředí

1. Vyberte `PAYG` z **SKU** rozevíracího seznamu. Budete také zadání názvu prostředí, určit, jaké předplatné a skupinu prostředků, které chcete vytvořit prostředí v a vybrat pro prostředí tak, aby se nacházejí v podporovaném umístění.  

1. Vytvořte nový účet úložiště Azure, že vyberete název účtu úložiště a určování výběru replikace. Tím automaticky vytvořit nový účet pro obecné účely verze 1 služby Azure Storage ve stejné oblasti jako prostředí Azure TSI (preview), které jste dříve vybrali.  

1. Vstup **ID řady času** vlastnost:

    > [!IMPORTANT]
    > **ID řady času** velká a malá písmena neměnná a nelze změnit po nastavení.

    Přečtěte si další podrobnosti o tom, že vyberete vhodné **ID řady času** , přečtěte si téma o [osvědčené postupy pro výběr ID řady času](./time-series-insights-update-how-to-id.md).

    ![environment_details][1]

1. Volitelně můžete přidat zdroje událostí:

    * Azure TSI podporuje jako možnosti služby Azure IoT Hub a Event Hubs. Přestože můžete přidat pouze jednu událost zdroje v okamžiku vytvoření prostředí, je zdrojem událostí další přidat později. Doporučujeme vytvořit skupinu jedinečných uživatelů a ujistěte se, že všechny události jsou viditelné TSI. Můžete vybrat existující skupinu uživatelů nebo vytvořit novou skupinu uživatelů, při přidávání zdroje událostí.

    * Taky určit odpovídající **časové razítko** vlastnost. Ve výchozím nastavení používá TSI čas zařazení do fronty zpráv pro každý zdroj události, které nemusí být správný, pokud jsou dávkové zpracování události nebo nahráním historická data. Proto je nutné zadat vlastnost časového razítka velká a malá písmena, při přidávání zdroje událostí.  

     ![environment_event_sources][2]

1. Zkontrolovat a vytvořit:

    ![environment_review][3]

    Potvrďte, že je vše v pořádku!

## <a name="management"></a>Správa

Budete mít možnost spravovat TSI aktualizované prostředí pomocí webu Azure portal. Uživatelé obeznámeni s TSI bude klidem ihned s aktualizací update TSI protože většina je přenášet mezi verzemi.

Níže jsou uvedené hlavní rozdíly ve správě L1 TSI prostředí, nikoli S1 nebo S2 prostředí pomocí webu Azure portal:

* Portál TSI Azure *přehled* okno:

  * Pomocí kartě s přehledem zůstává stejná s výjimkou:

    * Kapacita je odebrat, protože tento koncept se nevztahuje na L1 prostředí.
    * **ID řady času** byla přidána vlastnost. To je neměnný vlastnost přidána na čas zřízení a definuje, jak se vaše data rozdělit na oddíly.
    * Referenční datové sady se odeberou.

* Portál TSI Azure *konfigurovat* okno:
  
  * Uchovávání dat je odebrat, protože uchovávání dat se nastaví na neomezený počet.

    * Očekáváme, že v budoucnu přidat více ovládacích prvků na to, ale teď nejde nastavit omezení na to.
    * Kapacita, kalkulačky a limit úložiště překročil chování odebrat.

* Portál TSI Azure *odkaz* okno dat:

  * Toto celé okno se odebral jako referenčních dat není součástí L1 prostředí.

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## <a name="next-steps"></a>Další postup

Přečtěte si informace o konfiguraci úložiště TSI:

> [!div class="nextstepaction"]
> [Azure TSI (preview) úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md)

Další informace o modelech řady čas:

> [!div class="nextstepaction"]
> [Azure TSI TSM (ve verzi preview)](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/environment_details.png
[2]: media/v2-update-provision/environment_event_sources.png
[3]: media/v2-update-provision/environment_review.png