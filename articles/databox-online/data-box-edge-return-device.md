---
title: Vrácení nebo nahrazení zařízení Azure Data Box Edge | Microsoft Docs
description: Popisuje, jak vrátit nebo nahradit zařízení Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 07/19/2019
ms.author: alkohli
ms.openlocfilehash: a5fee604a529e9ca6153f6c189f199577ae65426
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356159"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Vrácení nebo nahrazení zařízení Azure Data Box Edge

Tento článek popisuje, jak vymazat data a vrátit zařízení Azure Data Box Edge. Po vrácení zařízení můžete také odstranit prostředek přidružený k zařízení nebo objednat náhradní zařízení.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vymazání dat z datových disků v zařízení
> * Otevření lístku podpory pro vrácení zařízení
> * Sbalení zařízení a naplánování výdeje
> * Odstranit prostředek v Azure Portal
> * Získat náhradní zařízení

## <a name="erase-data-from-the-device"></a>Vymaže data ze zařízení.

Pokud chcete vymazat data z datových disků vašeho zařízení, musíte zařízení resetovat. Zařízení můžete obnovit pomocí místního webového uživatelského rozhraní nebo rozhraní PowerShell.

Než obnovíte tovární nastavení, v případě potřeby vytvořte v zařízení kopii místních dat. Data ze zařízení můžete zkopírovat do kontejneru Azure Storage.

Pokud chcete zařízení obnovit pomocí místního webového uživatelského rozhraní, proveďte následující kroky.

1. V místním webovém uživatelském rozhraní přejdete do části **údržba > resetování zařízení**.
2. Vyberte **obnovit zařízení**.

    ![Resetování zařízení](media/data-box-edge-return-device/device-reset-1.png)

3. Po zobrazení výzvy k potvrzení si přečtěte upozornění a pokračujte výběrem **Ano** .

    ![Potvrdit resetování](media/data-box-edge-return-device/device-reset-2.png)  

Resetování smaže data z datových disků zařízení. V závislosti na množství dat v zařízení tento proces trvá přibližně 30-40 minut.

Případně se připojte k rozhraní PowerShell zařízení a pomocí `Reset-HcsAppliance` rutiny smažte data z datových disků. Další informace najdete v tématu [resetování zařízení](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Pokud vyměňujete nebo upgradujete na nové zařízení, doporučujeme, abyste zařízení obnovili až po obdržení nového zařízení.
> - Resetování zařízení odstraní jenom všechna místní data v zařízení. Data, která jsou v cloudu, se neodstraňují a shromažďují se [poplatky](https://azure.microsoft.com/pricing/details/storage/). Tato data se musí odstranit samostatně pomocí nástroje pro správu cloudového úložiště, jako je [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Otevření lístku podpory

Chcete-li zahájit proces návratu, proveďte následující kroky.

1. Otevřete lístek podpory s podpora Microsoftu, který indikuje, že chcete zařízení vrátit. Vyberte typ problému jako **data box Edge hardware**.

    ![Otevřít lístek podpory](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Vás bude kontaktovat pracovník podpora Microsoftu. Zadejte podrobnosti expedice.
3. Pokud potřebujete pole pro návrat do expedice, můžete si ho vyžádat. Odpověď **Ano** na otázku **vyžaduje prázdné pole, které se má vrátit**.


## <a name="schedule-a-pickup"></a>Naplánování výdeje

1. Vypněte zařízení. V místním webovém uživatelském rozhraní přejdete do části **údržba > nastavení napájení**.
2. Vyberte **vypnout**. Po zobrazení výzvy k potvrzení klikněte na **Ano** a pokračujte. Další informace najdete v tématu [Správa napájení](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Odpojte napájecí kabely a odeberte ze zařízení všechny síťové kabely.
4. Připravte balíček dodávek pomocí vlastního pole nebo prázdného pole, které jste dostali z Azure. Umístěte zařízení a napájecí šňůry, které byly dodávány se zařízením v poli.
5. Připevněte expediční štítek, který jste dostali z Azure v balíčku.
6. Naplánujte vyzvednutí pomocí svého regionálního dopravce. Pokud se zařízení vrátí do USA, je váš dopravce. Naplánování výdeje:

    1. Zavolejte místní zdroje UPS (bezplatné číslo pro telefonní číslo pro konkrétní zemi).
    2. Ve svém volání citujete číslo sledování zpětné dodávky, jak je znázorněno na vytištěném popisku.
    3. Pokud číslo sledování není v uvozovkách, bude zdroj napájení vyžadovat, abyste během vyzvednutí zaplatili dodatečné poplatky.

    Místo plánování vyzvednutí můžete také Data Box Edge umístit na nejbližší místo pro vyřazení.

## <a name="delete-the-resource"></a>Odstranit prostředek

Po přijetí zařízení v datovém centru Azure se v zařízení zkontroluje poškození nebo jakékoli případné známky manipulace.

- Pokud zařízení dorazí na nedotčené a dobrý tvar, měřič účtování se zastaví pro daný prostředek. Podpora Microsoftu vás bude kontaktovat, abyste ověřili, že se zařízení vrátilo. Pak můžete odstranit prostředek přidružený k zařízení v Azure Portal.
- Pokud zařízení dorazí k výraznému poškození, mohou se vztahovat pokuty. Podrobnosti najdete v nejčastějších [dotazech o ztracených nebo poškozených](https://azure.microsoft.com/pricing/details/databox/edge/) [licenčních službách](https://www.microsoft.com/licensing/product-licensing/products)zařízení a produktu.  


Zařízení můžete v Azure Portal odstranit:
-   Po umístění objednávky a před tím, než je zařízení připraveno společností Microsoft.
-   Po tom, co zařízení vrátíte do Microsoftu, projde fyzickou kontrolu v datacentru Azure a podpora Microsoftu volání, která potvrdí, že se zařízení vrátilo.

Pokud jste zařízení aktivovali v jiném předplatném nebo umístění, Microsoft přesune vaši objednávku na nové předplatné nebo umístění během jednoho pracovního dne. Po přesunutí objednávky můžete tento prostředek odstranit.


Proveďte následující kroky a odstraňte zařízení a prostředek v Azure Portal.

1. V Azure Portal přejdete do svého prostředku a pak na **Přehled**. Na panelu příkazů vyberte **Odstranit**.

    ![Vybrat odstranit](media/data-box-edge-return-device/delete-resource-1.png)

2. V okně **Odstranit zařízení** zadejte název zařízení, které chcete odstranit, a vyberte **Odstranit**.

    ![Potvrdit odstranění](media/data-box-edge-return-device/delete-resource-2.png)

Po úspěšném odstranění zařízení a přidruženého prostředku budete upozorněni.

## <a name="get-a-replacement-device"></a>Získat náhradní zařízení

Náhradní zařízení je potřeba, když dojde k selhání hardwaru v existujícím zařízení nebo pokud potřebuje upgrade. Pokud má vaše zařízení problém s hardwarem, proveďte následující kroky:

1. [Otevřete lístek podpory pro problém hardwaru](#open-a-support-ticket). Podpora Microsoftu určí, že jednotka pro nahrazení pole (FRU) není pro tuto instanci k dispozici, nebo zařízení potřebuje upgradovat hardware. V obou případech bude podpora vyřídit náhradní zařízení.
2. [Vytvořte nový prostředek](data-box-edge-deploy-prep.md#create-a-new-resource) pro náhradní zařízení. Nezapomeňte zaškrtnout políčko pro **zařízení data box Edge**. 
3. Jakmile obdržíte náhradní zařízení, [nainstalujte](data-box-edge-deploy-install.md) a [aktivujte](data-box-edge-deploy-connect-setup-activate.md) náhradní zařízení pro nový prostředek.
4. Pomocí všech kroků vraťte původní zařízení:
    1. Otevřete jiný lístek a vraťte původní zařízení.
    2. [Vymaže data na zařízení](#erase-data-from-the-device).
    3. [](#schedule-a-pickup)Naplánování výdeje.
    5. [Odstraní prostředek](#delete-the-resource) přidružený ke vrácenému zařízení.



## <a name="next-steps"></a>Další kroky

- Další informace o [správě šířky pásma](data-box-edge-manage-bandwidth-schedules.md).
