---
title: Vrácení nebo výměna zařízení Azure Data Box Edge | Dokumenty společnosti Microsoft
description: Popisuje, jak vrátit nebo nahradit zařízení Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: 521277b2eed7edfba016f6a80e8f877decfb0ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651096"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Vrácení nebo výměna zařízení Azure Data Box Edge

Tento článek popisuje, jak vymazat data a pak vrátit zařízení Azure Data Box Edge. Po vrácení zařízení můžete také odstranit prostředek přidružený k zařízení nebo objednat náhradní zařízení.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vymazání dat z datových disků v zařízení
> * Otevření lístku podpory pro vrácení zařízení
> * Sbalte si zařízení a naplánujte vyzvednutí
> * Odstranění prostředku na webu Azure Portal
> * Získání náhradního zařízení

## <a name="erase-data-from-the-device"></a>Vymazání dat ze zařízení

Chcete-li vymazat data z datových disků zařízení, je třeba zařízení resetovat. Zařízení můžete resetovat pomocí místního webového uživatelského rozhraní nebo rozhraní prostředí PowerShell.

Před resetováním vytvořte kopii místních dat v zařízení v případě potřeby. Data ze zařízení můžete zkopírovat do kontejneru úložiště Azure.

Chcete-li zařízení resetovat pomocí místního webového uživatelského rozhraní, postupujte takto.

1. V místním webovém uživatelském rozhraní přejděte na **obnovení > údržby zařízení**.
2. Vyberte **Obnovit zařízení**.

    ![Resetování zařízení](media/data-box-edge-return-device/device-reset-1.png)

3. Po zobrazení výzvy k potvrzení zkontrolujte upozornění a pokračujte vyberte **Ano.**

    ![Potvrdit obnovení](media/data-box-edge-return-device/device-reset-2.png)  

Resetvy vymaže data z datových disků zařízení. V závislosti na množství dat v zařízení tento proces trvá přibližně 30-40 minut.

Případně se připojte k rozhraní PowerShell zařízení `Reset-HcsAppliance` a pomocí rutiny vymazat data z datových disků. Další informace naleznete v [tématu Resetování zařízení](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Pokud si vyměňujete nebo upgradujete na nové zařízení, doporučujeme zařízení resetovat až po obdržení nového zařízení.
> - Resetování zařízení odstraní ze zařízení pouze všechna místní data. Data, která jsou v cloudu, se neodstraní a shromáždí [poplatky](https://azure.microsoft.com/pricing/details/storage/). Tato data je třeba odstranit samostatně pomocí nástroje pro správu cloudového úložiště, jako je [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Otevření lístku podpory

Chcete-li zahájit proces vrácení, postupujte podle následujících kroků.

1. Otevřete lístek podpory s podporou společnosti Microsoft označující, že chcete vrátit zařízení. Vyberte typ problému jako **Data Box Edge Hardware**.

    ![Otevření lístku podpory](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Bude vás kontaktovat technik podpory společnosti Microsoft. Uveďte podrobnosti o přepravě.
3. Pokud potřebujete zpáteční přepravní krabici, můžete o ni požádat. Odpověď **Ano** na otázku **Potřebujete prázdné pole pro návrat**.


## <a name="schedule-a-pickup"></a>Naplánovat vyzvednutí

1. Vypněte zařízení. V místním webovém uživatelském prostředí přejděte na **nastavení údržby > napájení**.
2. Vyberte **Vypnout**. Po zobrazení výzvy k potvrzení pokračujte klepnutím na tlačítko **Ano.** Další informace naleznete v [tématu Správa napájení](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Odpojte napájecí kabely a vyjměte ze zařízení všechny síťové kabely.
4. Připravte si zásilku pomocí vlastní krabice nebo prázdné krabice, kterou jste obdrželi z Azure. Umístěte zařízení a napájecí kabely, které byly dodány se zařízením, do pole.
5. Přiřazujte na balíček expediční štítek, který jste obdrželi z Azure.
6. Naplánujte vyzvednutí místní přepravní službou. Pokud zařízení vrátíte v USA, může být vaším operátorem ups nebo fedex. Jak naplánovat vyzvednutí se společností UPS:

    1. Zavolejte na místní UPS (bezplatné telefonní číslo pro jednotlivé země).
    2. Ve výzvě uveďte zpětné sledovací číslo zásilky, jak je uvedeno na vytištěném štítku.
    3. Pokud sledovací číslo není uvedeno, společnost UPS bude vyžadovat, abyste během vyzvednutí zaplatili příplatek.

    Namísto plánování vyzvednutí můžete také vysadit okraj datové schránky na nejbližším místě předání.

## <a name="delete-the-resource"></a>Odstranění prostředku

Po přijetí zařízení v datovém centru Azure je zařízení zkontrolováno na poškození nebo známky manipulace.

- Pokud zařízení dorazí beze změny a v dobrém stavu, měřič fakturace se zastaví pro tento prostředek. Podpora společnosti Microsoft vás bude kontaktovat a otvrdí, že zařízení bylo vráceno. Potom můžete odstranit prostředek přidružený k zařízení na webu Azure Portal.
- Pokud zařízení dorazí výrazně poškozeno, mohou být uloženy pokuty. Podrobnosti naleznete v [nejčastějších dotazech ke ztraceným nebo poškozeným zařízením](https://azure.microsoft.com/pricing/details/databox/edge/) a [smluvních podmínkách produktu](https://www.microsoft.com/licensing/product-licensing/products).  


Zařízení můžete odstranit na webu Azure Portal:
-   Po provedení objednávky a před tím, než je zařízení připraveno společností Microsoft.
-   Po vrácení zařízení společnosti Microsoft předá fyzickou kontrolu v datovém centru Azure a volání podpory Microsoftu, abyste potvrdili, že zařízení bylo vráceno.

Pokud jste zařízení aktivovali proti jinému předplatnému nebo umístění, Microsoft přesune vaši objednávku do nového předplatného nebo místa během jednoho pracovního dne. Po přesunutí objednávky můžete tento zdroj odstranit.


Pomocí následujících kroků odstraňte zařízení a prostředek na webu Azure Portal.

1. Na webu Azure Portal přejděte na svůj prostředek a potom na **Přehled**. Na panelu příkazů vyberte **Odstranit**.

    ![Vybrat odstranit](media/data-box-edge-return-device/delete-resource-1.png)

2. Do okna **Odstranit zařízení** zadejte název zařízení, které chcete odstranit, a vyberte **Odstranit**.

    ![Potvrzení odstranění](media/data-box-edge-return-device/delete-resource-2.png)

Budete upozorněni po zařízení a přidruženého prostředku je úspěšně odstraněn.

## <a name="get-a-replacement-device"></a>Získání náhradního zařízení

Náhradní zařízení je potřeba, pokud stávající zařízení došlo k selhání hardwaru nebo potřebuje upgrade. Pokud má vaše zařízení problém s hardwarem, postupujte takto:

1. [Otevřete lístek podpory pro problém s hardwarem](#open-a-support-ticket). Podpora společnosti Microsoft určí, že jednotka výměny polí (FRU) není pro tuto instanci k dispozici nebo že zařízení potřebuje upgrade hardwaru. V obou případech podpora objedná náhradní zařízení.
2. [Vytvořte nový prostředek](data-box-edge-deploy-prep.md#create-a-new-resource) pro náhradní zařízení. Ujistěte se, že zaškrtnete políčko proti **mám data box edge zařízení**. 
3. Po obdržení náhradního zařízení [nainstalujte](data-box-edge-deploy-install.md) a [aktivujte](data-box-edge-deploy-connect-setup-activate.md) náhradní zařízení proti novému prostředku.
4. Podle všech kroků vraťte původní zařízení:
    1. Otevřete další lístek a vraťte původní zařízení.
    2. [Vymažte data v zařízení](#erase-data-from-the-device).
    3. [Naplánujte vyzvednutí](#schedule-a-pickup).
    5. [Odstraňte prostředek](#delete-the-resource) přidružený k vrácené zařízení.



## <a name="next-steps"></a>Další kroky

- Další informace o [správě šířky pásma](data-box-edge-manage-bandwidth-schedules.md).
