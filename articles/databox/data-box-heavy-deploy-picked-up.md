---
title: Kurz vrácení Azure Data Boxu Heavy | Microsoft Docs
description: Zjistěte, jak odeslat Azure Data Box Heavy do Microsoftu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: cfb9f54f5ba219a4db87144ab1e7ebff2b72b69e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380228"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Kurz: vrácení Azure Data Box Heavy a ověření nahrávání dat do Azure

::: zone-end

::: zone target = "chromeless"

# <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Vrácení Azure Data Boxu Heavy a ověření nahrání dat do Azure

::: zone-end

::: zone target = "docs"

Tento kurz popisuje, jak vrátit Azure Data Box Heavy a ověřit nahrání dat do Azure.

V tomto kurzu se seznámíte například s následujícími tématy:

> [!div class="checklist"]
> * Předpoklady
> * Příprava k odeslání
> * Odeslání Data Boxu Heavy do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Boxu Heavy

## <a name="prerequisites"></a>Předpoklady

Než začnete, ujistěte se, že:

- Dokončili jste [kurz: kopírování dat pro Azure Data box a ověření](data-box-heavy-deploy-copy-data.md).
- Dokončily se úlohy kopírování. Pokud probíhají úlohy kopírování, nemůže proběhnout příprava k odeslání.


## <a name="prepare-to-ship"></a>Příprava k odeslání

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>Příprava k odeslání

Než začnete s přípravou k odeslání, ujistěte se, že se dokončily úlohy kopírování.

1. V místním webovém uživatelském rozhraní přejděte na stránku Příprava k odeslání a zahajte přípravu k odeslání.
2. Z místního webového uživatelského rozhraní vypněte zařízení. Odpojte od zařízení kabely.

Teď jste připraveni vrátit své zařízení.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Vrácení Data Boxu Heavy

1. Ujistěte se, že je zařízení vypnuté a všechny kabely jsou odpojené. Smotejte 4 napájecí kabely a bezpečně je umístěte do zásobníku v zadní části zařízení.
2. Zařízení se přepravuje jako částečná dokládková zásilka přes společnost FedEx v USA a společnost DHL v EU.

    1. Spojte se s [provozním oddělením Data Boxu](mailto:DataBoxOps@microsoft.com), informujte je o vyzvednutí a získejte od nich zpáteční expediční štítek.
    2. Zavolejte na místní číslo vašeho přepravce a naplánujte vyzvednutí.
    3. Ujistěte se, že je expediční štítek umístěný na viditelném místě na vnějším obalu zásilky.
    4. Nezapomeňte ze zařízení odstranit staré expediční štítky z předchozí zásilky.
3. Jakmile přepravce vyzvedne a naskenuje Data Box Heavy, stav objednávky na portálu se změní na **Vyzvednuto**. Zobrazí se také ID sledování.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Jakmile Microsoft přijme a naskenuje zařízení, stav objednávky se změní na **Přijato**. Zařízení pak projde fyzickým ověřením poškození nebo známek manipulace.

Po dokončení ověřování se Data Box Heavy připojí k síti v datacentru Azure. Automaticky se spustí kopírování dat. V závislosti na velikosti dat může operace kopírování trvat několik hodin až dní. Průběh kopírování můžete sledovat na portálu.

Po dokončení kopírování se stav objednávky změní na **Dokončeno**.

Než odstraníte data ze zdroje, ujistěte se, že se nahrála do Azure. Vaše data můžou být v následujících umístěních:

- Vaše účty Azure Storage. Když data zkopírujete do Data Boxu, v závislosti na jejich typu se nahrají do jedné z následujících cest v účtu služby Azure Storage.

  - Objekty blob bloku a objekty blob stránky: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Soubory Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternativně můžete přejít na svůj účet Azure Storage na webu Azure Portal a dokončit navigaci tam.

- Vaše skupiny prostředků spravovaných disků. Při vytváření spravovaných disků se virtuální pevné disky nahrají jako objekty blob stránky a pak se převedou na spravované disky. Spravované disky se připojí ke skupinám prostředků zadaným při vytváření objednávky. 

    - Pokud vaše kopírování na spravované disky v Azure proběhne úspěšně, můžete na webu Azure Portal přejít na **Podrobnosti objednávky** a poznamenat si skupiny prostředků zadané pro spravované disky.

        ![Identifikace skupin prostředků spravovaných disků](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Přejděte do skupiny prostředků, kterou jste si poznamenali, a vyhledejte své spravované disky.

        ![Spravovaný disk připojený ke skupinám prostředků](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Pokud kopírujete VHDX nebo dynamický nebo rozdílový virtuální pevný disk, VHDX/VHD se nahraje do přípravného účtu úložiště jako objekt blob stránky, ale převod virtuálního pevného disku na spravovaný disk selže. Přejděte do svého přípravného **účtu úložiště do části Objekty blob** a vyberte odpovídající kontejner – StandardSSD, Standard HDD nebo PremiumSSD. Virtuální pevné disky se nahrají do přípravného účtu úložiště jako objekty blob stránky.
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Když se zařízení Data Box Heavy připojí k síti v datacentru Azure, automaticky se zahájí nahrávání dat do Azure. Jakmile se kopírování dat dokončí, služba Data Box vám přes web Azure Portal pošle oznámení.

- Zkontrolujte všechna selhání v protokolech chyb a proveďte potřebné kroky.
- Než odstraníte data ze zdroje, ujistěte se, že je máte v účtech úložiště.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Vymazání dat z Data Boxu Heavy
 
Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Po vymazání si můžete [stáhnout historii objednávky](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Předpoklady
> * Příprava k odeslání
> * Odeslání Data Boxu Heavy do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Boxu Heavy

V následujícím článku se dozvíte, jak spravovat Data Box Heavy přes místní webové uživatelské rozhraní.

> [!div class="nextstepaction"]
> [Správa Azure Data Boxu pomocí místního webového uživatelského rozhraní](./data-box-local-web-ui-admin.md)

::: zone-end


