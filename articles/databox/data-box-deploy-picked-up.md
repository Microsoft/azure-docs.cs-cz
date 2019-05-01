---
title: Kurz k odeslání zařízení Azure Data Box zpět | Dokumentace Microsoftu
description: Zjistěte, jak odeslat Azure Data Box do Microsoftu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: 659dfbec1f73ca713cf03a2945361b794b8aaea6
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924944"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Kurz: Vrátí zařízení Azure Data Box a ověřte nahrání dat do Azure

Tento kurz popisuje, jak vrátit Azure Data Box a ověřit nahrání dat do Azure.

V tomto kurzu se seznámíte například s následujícími tématy:

> [!div class="checklist"]
> * Požadavky
> * Příprava k odeslání
> * Odeslání Data Boxu do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

- Když jste dokončili [kurzu: Kopírování dat do služby Azure Data Box a ověřte](data-box-deploy-copy-data.md). 
- Kopírování úloh se dokončí. Příprava k odeslání nelze spustit, pokud probíhají úlohy kopírování.

## <a name="prepare-to-ship"></a>Příprava k odeslání

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Vrácení Data Boxu

1. Ujistěte se, že je zařízení vypnuté a nejsou k němu zapojené žádné kabely. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
2. Ujistěte se, že se na displeji s elektronickým inkoustem zobrazuje expediční štítek, a naplánujte vyzvednutí přepravcem. Pokud popisek poškození nebo ztráty nebo se nezobrazuje na displeji E-ink, obraťte se na Microsoft Support. Pokud podpora navrhuje, pak můžete přejít na **přehled > stáhnout Expediční štítek** na webu Azure Portal. Stáhnout Expediční štítek a opatří na zařízení. 
3. S UPS vyzvednutí naplánujte, pokud vrácení zařízení. Chcete-li vyzvednutí naplánovat:

    - Volání místní UPS (specifické pro zemi bezplatné číslo).
    - Ve volání nabídka zpětné dodávky sledovací číslo, jak je znázorněno v E-ink zobrazení nebo Tisk popisku.
    - Pokud není uveden v uvozovkách sledovacího čísla, bude vyžadovat UPS vám umožní platit poplatek za další využití během vyzvednutí.

    Namísto plánování vyzvednutí, můžete také odkládací pole údaj na nejbližší umístění pro odložení.
4. Jakmile přepravce vyzvedne a naskenuje Data Box, stav objednávky na portálu se změní na **Vyzvednuto**. Zobrazí se také ID sledování.

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Jakmile Microsoft přijme a naskenuje zařízení, stav objednávky se změní na **Přijato**. Zařízení pak projde fyzickým ověřením poškození nebo známek manipulace.

Po dokončení ověřování se Data Box připojí k síti v datacentru Azure. Automaticky se spustí kopírování dat. V závislosti na velikosti dat může operace kopírování trvat několik hodin až dní. Průběh kopírování můžete sledovat na portálu.

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

## <a name="erasure-of-data-from-data-box"></a>Vymazání dat z Data Boxu
 
Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Požadavky
> * Příprava k odeslání
> * Odeslání Data Boxu do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Boxu

V následujícím článku se dozvíte, jak spravovat Data Box přes místní webové uživatelské rozhraní.

> [!div class="nextstepaction"]
> [Správa Azure Data Boxu pomocí místního webového uživatelského rozhraní](./data-box-local-web-ui-admin.md)


