---
title: Kurz pro dodávání Azure Data Box Heavy zpět | Microsoft Docs
description: Informace o tom, jak dodávat Azure Data Box Heavy Microsoftu
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: d8d9478441ffe3962f450bed7bfddf2776386617
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164394"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Kurz: Vrácení Azure Data Box Heavy a ověření nahrávání dat do Azure

::: zone-end

::: zone target = "chromeless"

# <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Vrácení Azure Data Box Heavy a ověření nahrávání dat do Azure

::: zone-end

::: zone target = "docs"

V tomto kurzu se dozvíte, jak vrátit Azure Data Box Heavy a ověřit data nahraná do Azure.

V tomto kurzu se seznámíte například s následujícími tématy:

> [!div class="checklist"]
> * Požadavky
> * Připravit k odeslání
> * Dodávat Data Box Heavy Microsoftu
> * Ověření nahrání dat do Azure
> * Mazání dat z Data Box Heavy

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

- Dokončili [jste kurz: Zkopírujte data Azure Data Box a ověřte](data-box-heavy-deploy-copy-data.md).
- Kopírování úloh bylo dokončeno. Příprava k odeslání nemůže běžet, pokud probíhá kopírování úloh.


## <a name="prepare-to-ship"></a>Připravit k odeslání

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>Připravit k odeslání

Než dokončíte přípravu na odeslání, ujistěte se, že jsou dokončené úlohy kopírování.

1. Přejít na stránku Příprava k odeslání v místním webovém uživatelském rozhraní a zahájit přípravu expedice.
2. Z místního webového uživatelského rozhraní vypněte zařízení. Odpojte od zařízení kabely.

Teď jste připraveni dodávat své zařízení zpátky.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Dodávat Data Box Heavy zpátky

1. Ujistěte se, že je zařízení vypnuté a že se odeberou všechny kabely. Zařazování a bezpečné umístění 4 napájecích šňůr do zásobníku, ke kterým můžete přistupovat z back-of zařízení.
2. Zařízení dodává LTL dopravného prostřednictvím FedEx v USA a DHL v EU.

    1. Přihlaste se k [data box operací](mailto:DataBoxOps@microsoft.com) , abyste informovali o vyzvednutí a získali expediční štítek s návratem.
    2. Pro naplánování vyzvednutí zavolejte místní číslo pro vašeho přepravce.
    3. Zajistěte, aby se expediční štítek zobrazoval na vnějšku dodávky.
    4. Ujistěte se, že se ze zařízení odeberou staré expediční štítky z předchozí dodávky.
3. Jakmile se Data Box Heavy vyzvednout a prohledá váš operátor, stav objednávky na portálu se aktualizuje na vyskladněno. Zobrazí se také ID sledování.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Jakmile Microsoft přijme a naskenuje zařízení, stav objednávky se změní na **Přijato**. Zařízení pak projde fyzickým ověřením poškození nebo známek manipulace.

Po dokončení ověření se Data Box Heavy připojí k síti v datovém centru Azure. Automaticky se spustí kopírování dat. V závislosti na velikosti dat může operace kopírování trvat několik hodin až dní. Průběh kopírování můžete sledovat na portálu.

Po dokončení kopírování se stav objednávky změní na **Dokončeno**.

Před odstraněním ze zdroje ověřte, že jsou vaše data nahraná do Azure. Vaše data můžou být v:

- Váš účet Azure Storage. Když data zkopírujete do Data Boxu, v závislosti na jejich typu se nahrají do jedné z následujících cest v účtu služby Azure Storage.

  - Objekty blob bloku a objekty blob stránky: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Soubory Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternativně můžete přejít na svůj účet Azure Storage na webu Azure Portal a dokončit navigaci tam.

- Skupiny prostředků spravovaného disku. Při vytváření spravovaných disků se virtuální pevné disky nahrají jako objekty blob stránky a pak se převedou na spravované disky. Spravované disky jsou připojené ke skupinám prostředků zadaným v době vytváření objednávky. 

    - Pokud byla vaše kopie na spravované disky v Azure úspěšná, můžete přejít na **Podrobnosti objednávky** v Azure Portal a poznamenat si skupiny prostředků zadané pro službu Managed disks.

        ![Identifikace skupin prostředků spravovaných disků](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Přejděte do vyznačené skupiny prostředků a vyhledejte své spravované disky.

        ![Spravovaný disk připojený ke skupinám prostředků](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Pokud jste zkopírovali disk VHDX nebo dynamický/Rozdílový virtuální pevný disk, soubor VHDX/VHD se nahraje do pracovního účtu úložiště jako objekt blob stránky, ale převod virtuálního pevného disku na spravovaný disk se nezdařil. Do svého přípravného **účtu úložiště > objekty blob** a potom vyberte příslušný kontejner-SSD úrovně Standard, HDD úrovně Standard nebo SSD úrovně Premium. Virtuální pevné disky se nahrají jako objekty blob stránky v přípravném účtu úložiště.
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Když je zařízení Data Box Heavy připojené k síti datacenter Azure, automaticky se spustí nahrávání dat do Azure. Služba Data Box vás upozorní, že se kopie dat dokončuje prostřednictvím Azure Portal.

- Zkontrolujte všechna selhání v protokolech chyb a proveďte potřebné kroky.
- Než odstraníte data ze zdroje, ujistěte se, že je máte v účtech úložiště.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Mazání dat z Data Box Heavy
 
Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Po dokončení mazání si můžete [Stáhnout historii objednávek](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Požadavky
> * Připravit k odeslání
> * Dodávat Data Box Heavy Microsoftu
> * Ověření nahrání dat do Azure
> * Mazání dat z Data Box Heavy

V následujícím článku se dozvíte, jak spravovat Data Box Heavy prostřednictvím místního webového uživatelského rozhraní.

> [!div class="nextstepaction"]
> [Správa Azure Data Boxu pomocí místního webového uživatelského rozhraní](./data-box-local-web-ui-admin.md)

::: zone-end


