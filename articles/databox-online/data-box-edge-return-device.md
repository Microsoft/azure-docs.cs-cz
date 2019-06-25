---
title: Vrátí zařízení Azure Data Box Edge | Dokumentace Microsoftu
description: Popisuje způsob vrácení zařízení Azure Data Box Edge a odstranit objednávky zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.openlocfilehash: 9aeae0ab68d809b36a3316054f12a5a9657721f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65468601"
---
# <a name="return-your-azure-data-box-edge-device"></a>Vrátí zařízení Azure Data Box Edge

Tento článek popisuje, jak vymazat data a pak se vraťte zařízení Azure Data Box Edge. Po návratu zařízení, můžete také odstranit prostředek přidružené k zařízení.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vymazání dat z datových disků na zařízení
> * Otevřete lístek podpory a vrátí zařízení
> * Zabalit zařízení a vyzvednutí naplánovat
> * Odstranění prostředku na webu Azure portal

## <a name="erase-data-from-the-device"></a>Vymazání dat ze zařízení

K vymazání dat z datové disky vašeho zařízení, budete muset obnovit v zařízení. Můžete resetovat zařízení pomocí místního webového uživatelského rozhraní nebo rozhraní PowerShell.

Před restartováním, vytvořte v případě potřeby kopírování místních dat v zařízení. Můžete zkopírovat data ze zařízení do kontejneru služby Azure Storage.

Postup resetování zařízení pomocí místního webového uživatelského rozhraní, proveďte následující kroky.

1. V místním webovém uživatelském rozhraní, přejděte na **údržby > resetovat zařízení**.
2. Vyberte **resetování zařízení**.

    ![Resetování zařízení](media/data-box-edge-return-device/device-reset-1.png)

3. Po zobrazení výzvy k potvrzení, přečtěte si upozornění a vyberte **Ano** pokračujte.

    ![Potvrdit obnovení](media/data-box-edge-return-device/device-reset-2.png)  

Obnovení vymaže dat mimo zařízení datové disky. V závislosti na objemu dat na vašem zařízení tento proces trvá přibližně 30 – 40 minut.

Můžete také připojit k Powershellovém rozhraní zařízení a které používají `Reset-HcsAppliance` rutiny vymazat data z datových disků. Další informace najdete v tématu [tovární nastavení zařízení](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Pokud jste výměna nebo upgrade na nová zařízení, doporučujeme resetuji svoje zařízení až po jsme obdrželi nové zařízení.
> - Jenom resetování zařízení odstraní všechna místní data vypnout zařízení. Data, která je v cloudu se neodstraní a shromažďuje [poplatky](https://azure.microsoft.com/pricing/details/storage/). Tato data je nutné odstranit samostatně pomocí nástroje management cloudové úložiště, jako je [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Vytvořit lístek podpory

K zahájení procesu návratový, proveďte následující kroky.

1. Otevřete lístek podpory Microsoft Support označující, že chcete vrátit zařízení. Vyberte typ problému jako **Data Box Edge hardwaru**.

    ![Otevřete lístek podpory](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Vás bude kontaktovat pracovníkem Microsoft Support. Zadejte podrobnosti o dopravě.
3. Pokud potřebujete návratový přenosů pole, můžete požádat o jeho. Odpověď **Ano** na otázku **potřebovat prázdného pole vrátit**.


## <a name="schedule-a-pickup"></a>Vyzvednutí naplánovat

1. Vypněte zařízení. V místním webovém uživatelském rozhraní, přejděte na **údržby > nastavení napájení**.
2. Vyberte **vypnout**. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano** pokračujte. Další informace najdete v tématu [spravovat power](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Odpojte napájecích kabelů a odeberte všechny síťové kabely ze zařízení.
4. Příprava balíčku s dodávky pomocí vlastní pole nebo prázdné pole, které jste získali od Azure. Umístíte zařízení a napájecích, dodané se zařízením v poli.
5. Opatří Expediční štítek, který jste dostali z Azure na balíčku.
6. Vyzvednutí naplánujte s vaší místní operátora. Pokud zařízení se vrátí v USA, je váš poskytovatel služeb UPS. Chcete-li vyzvednutí naplánovat:

    1. Volání místní UPS (specifické pro zemi bezplatné číslo).
    2. Ve volání nabídka zpětné dodávky sledovací číslo, jak je vidět na tištěné popisek.
    3. Pokud není uveden v uvozovkách sledovacího čísla, bude vyžadovat UPS vám umožní platit poplatek za další využití během vyzvednutí.

    Namísto plánování vyzvednutí, lze také přetáhnout okraj pole Data na nejbližší umístění pro odložení.

## <a name="delete-the-resource"></a>Odstranění prostředku

Po přijetí zařízení v datacentru Azure se pro škody nebo jakékoli projevy manipulaci prozkoumá zařízení.

- Pokud zařízení doručení beze změny a v dobrém stavu, zastaví se účtování počítat pro daný prostředek. Microsoft Support bude vás kontaktovat a potvrďte, že byla vrácena zařízení. Pak můžete odstranit prostředek přidružený k zařízení na portálu Azure portal.
- Pokud zařízení doručení významně poškozen, mohou platit omezení pokut. Podrobnosti najdete v tématu [– nejčastější dotazy na ztracené nebo poškozené zařízení](https://azure.microsoft.com/pricing/details/databox/edge/) a [produktu podmínkami služby](https://www.microsoft.com/licensing/product-licensing/products).  


Můžete odstranit zařízení na portálu Azure portal:
-   Po vložení pořadí a před zařízení je připraveno společností Microsoft.
-   Poté, co jste vrátí zařízení do Microsoftu, předá prohlídce v datacentru Azure a Microsoft Support volá potvrďte, že byla vrácena zařízení.

Pokud po dokončení aktivace zařízení proti jiné předplatné nebo umístění, Microsoft vaši objednávku přesunout do nové předplatné nebo umístění do jednoho pracovního dne. Po přesunu pořadí, můžete odstranit tento prostředek.


Proveďte následující kroky k odstranění zařízení a prostředků na webu Azure portal.

1. Na webu Azure Portal, přejděte na váš prostředek a potom do **přehled**. Na panelu příkazů vyberte **odstranit**.

    ![Vyberte odstranit](media/data-box-edge-return-device/delete-resource-1.png)

2. V **odstranit zařízení** okně zadejte název zařízení, kterou chcete odstranit a vyberte **odstranit**.

    ![Potvrzení odstranění](media/data-box-edge-return-device/delete-resource-2.png)

Zobrazí se oznámení po zařízení a přidružený prostředek se úspěšně odstranil.

## <a name="next-steps"></a>Další postup

- Další informace o [správě šířky pásma](data-box-edge-manage-bandwidth-schedules.md).
