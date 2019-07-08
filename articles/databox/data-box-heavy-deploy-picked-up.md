---
title: Kurz k odeslání zpět Azure Data Box náročné | Dokumentace Microsoftu
description: Zjistěte, jak dodávat váš Azure Data Box náročné společnosti Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 84db33e4c7ac612353c590ac9d2904ac3bc48d38
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592391"
---
# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Kurz: Vrátí Azure Data Box náročné a ověřte nahrání dat do Azure


Tento kurz popisuje, jak vrátit Azure Data Box náročné a ověřte nahrání dat do Azure.

V tomto kurzu se seznámíte například s následujícími tématy:

> [!div class="checklist"]
> * Požadavky
> * Příprava k odeslání
> * Silná pole příjemce dat společnosti Microsoft
> * Ověření nahrání dat do Azure
> * Výmaz dat z datového pole náročné

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

- Dokončili jste [kurzu: Kopírování dat do služby Azure Data Box a ověřte](data-box-heavy-deploy-copy-data.md).
- Kopírování úloh se dokončí. Příprava k odeslání nelze spustit, pokud probíhají úlohy kopírování.

## <a name="prepare-to-ship"></a>Příprava k odeslání

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

## <a name="ship-data-box-heavy-back"></a>Příjemce dat pole náročné zpět

1. Ujistěte se, že zařízení je vypnutý a odeberou se všechny kabely. Zařazování a bezpečně umístit 4 napájecích zásobník, který se dá dostat z zpět zařízení.
2. Zařízení se dodává LTL freight prostřednictvím FedEx v USA a DHL v EU

    1. Oslovení [operace s daty pole](mailto:DataBoxOps@microsoft.com) informovat o vyzvednutí a získat zpětný Expediční štítek.
    2. Zavolejte na místní číslo pro vaše dodací dopravce vyzvednutí naplánovat.
    3. Ujistěte se, že Expediční štítek se zobrazují přednostně na vnější dodávky.
    4. Ujistěte se, že se ze zařízení odeberou staré popisky přesouvání z předchozích odeslání.
3. Po náročné pole Data neexistoval a zkontrolovat vaším operátorem, aktualizace stavu objednávky na portálu **nenačítají**. Zobrazí se také ID sledování.

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Jakmile Microsoft přijme a naskenuje zařízení, stav objednávky se změní na **Přijato**. Zařízení pak projde fyzickým ověřením poškození nebo známek manipulace.

Po dokončení ověření náročné pole dat je připojené k síti v datovém centru Azure. Automaticky se spustí kopírování dat. V závislosti na velikosti dat může operace kopírování trvat několik hodin až dní. Průběh kopírování můžete sledovat na portálu.

Po dokončení kopírování se stav objednávky změní na **Dokončeno**.

Ověřte, že vaše data před odstraněním ze zdroje nahraje do Azure. Vaše data můžou být v:

- Vaše účty úložiště Azure. Když data zkopírujete do Data Boxu, v závislosti na jejich typu se nahrají do jedné z následujících cest v účtu služby Azure Storage.

  - Objekty blob bloku a objekty blob stránky: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Soubory Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternativně můžete přejít na svůj účet Azure Storage na webu Azure Portal a dokončit navigaci tam.

- Vaší skupiny prostředků spravovaného disku. Při vytváření spravované disky, virtuální pevné disky jsou odeslat jako objekty BLOB stránky a poté převeden na spravované disky. Spravované disky jsou připojené ke skupinám prostředků zadané v době vytvoření objednávky. 

    - Pokud vaše kopie na managed disks v Azure byla úspěšná, může použít **podrobnosti objednávky** webu Azure portal a zkontrolujte zadaná poznámka skupin prostředků za spravované disky.

        ![Identifikujte skupiny prostředků spravovaného disku](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Přejděte do skupiny prostředků uvedené a vyhledejte spravované disky.

        ![Spravovaný disk připojený do skupin prostředků](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Pokud jste si zkopírovali disk VHDX nebo dynamický nebo rozdílový virtuální pevný disk, VHDX nebo VHD je nahrát do přípravného účtu úložiště jako objekt blob stránky, ale převod virtuálního pevného disku na spravovaný disk selže. Přejděte k vaší pracovní **účet úložiště > objekty BLOB** a pak vyberte příslušný kontejner – SSD na úrovni Standard, Standard HDD nebo Premium SSD. Virtuální pevné disky jsou odeslány jako objekty BLOB stránky v přípravného účtu úložiště.

## <a name="erasure-of-data-from-data-box-heavy"></a>Výmaz dat z datového pole náročné
 
Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Po dokončení mazání můžete [stažení historie objednávek](data-box-portal-admin.md#download-order-history).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Požadavky
> * Příprava k odeslání
> * Silná pole příjemce dat společnosti Microsoft
> * Ověření nahrání dat do Azure
> * Výmaz dat z datového pole náročné

Přejděte k následujícím článku se dozvíte, jak spravovat Data Box náročné prostřednictvím místního webového uživatelského rozhraní.

> [!div class="nextstepaction"]
> [Správa Azure Data Boxu pomocí místního webového uživatelského rozhraní](./data-box-local-web-ui-admin.md)


