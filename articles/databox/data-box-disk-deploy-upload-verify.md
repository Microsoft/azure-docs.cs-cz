---
title: Kurz pro ověření nahrávání dat z Azure Data Box Disk do účtu úložiště | Microsoft Docs
description: V tomto kurzu se dozvíte, jak ověřit data odeslaná z Azure Data Box Disk do účtu úložiště Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 08/22/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: e36f009422307b3b70091775d2288ee710839172
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014178"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Kurz: Ověřit nahrávání dat z Azure Data Box Disk

Toto je poslední kurz řady: Nasazení Azure Data Box Disk. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Box Disku

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste dokončili [kurz: Vrátit Azure Data Box Disk](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Jakmile přepravce vyzvedne disky, stav objednávky na portálu se změní na **Vyzvednuto**. Zobrazí se také ID sledování.

![Disky jsou vyzvednuté](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Když Microsoft dostane disky a prohledá je, stav úlohy se změní na **Přijato**. 

![Disky jsou přijaté](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Jakmile jsou disky připojené k serveru v datovém centru Azure, data se automaticky zkopírují. V závislosti na velikosti dat může operace kopírování trvat několik hodin až dní. Průběh kopírování můžete sledovat na portálu.

Po dokončení kopírování se stav objednávky změní na **Dokončeno**.

![Kopírování dat je dokončené](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Pokud se kopie dokončí s chybami, přečtěte si téma [řešení chyb při nahrávání](data-box-disk-troubleshoot-upload.md).

Než odstraníte data ze zdroje, ujistěte se, že je máte v účtech úložiště. Vaše data můžou být v:

- Váš účet Azure Storage. Když data zkopírujete do Data Boxu, v závislosti na jejich typu se nahrají do jedné z následujících cest v účtu služby Azure Storage.

  - Objekty blob bloku a objekty blob stránky: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Soubory Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternativně můžete přejít na svůj účet Azure Storage na webu Azure Portal a dokončit navigaci tam.

- Skupiny prostředků spravovaného disku. Při vytváření spravovaných disků se virtuální pevné disky nahrají jako objekty blob stránky a pak se převedou na spravované disky. Spravované disky jsou připojené ke skupinám prostředků zadaným v době vytváření objednávky.

  - Pokud byla vaše kopie na spravované disky v Azure úspěšná, můžete přejít na **Podrobnosti objednávky** v Azure Portal a poznamenat si skupinu prostředků zadanou pro službu Managed disks.

      ![Zobrazit podrobnosti objednávky](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Přejděte do vyznačené skupiny prostředků a vyhledejte své spravované disky.

      ![Skupina prostředků pro spravované disky](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Pokud jste zkopírovali VHDX nebo dynamický/Rozdílový virtuální pevný disk, pak se disk VHDX/VHD nahraje do pracovního účtu úložiště jako objekt blob bloku. Do svého přípravného **účtu úložiště > objekty blob** a pak vyberte odpovídající kontejner – StandardSSD, StandardHDD nebo PremiumSSD. Soubory VHDX/VHD by se měly zobrazit jako objekty blob bloku v přípravném účtu úložiště.

Pokud chcete ověřit, že se data nahrála do Azure, postupujte takto:

1. Přejděte k účtu úložiště přidruženému k vaší objednávce disků.
2. Přejděte na **Blob service > Procházet objekty blob**. Zobrazí se seznam kontejnerů. Ve vašem účtu úložiště se vytvoří kontejnery se stejným názvem jako podsložky, které jste vytvořili ve složkách *BlockBlob* a *PageBlob*.
    Pokud názvy složek neodpovídají zásadám vytváření názvů v Azure, nahrávání dat do Azure selže.

4. K ověření, že se nahrála kompletní datová sada, použijte Průzkumníka služby Azure Storage. Připojte účet úložiště odpovídající objednávce pronájmu disků a prohlédněte si seznam kontejnerů objektů blob. Vyberte kontejner, klikněte na **…Další** a potom klikněte na **statistiku složky**. V podokně **Aktivity** se zobrazí statistika pro příslušnou složku, včetně počtu objektů blob a jejich celkové velikosti. Celková velikost objektů blob v bajtech by měla odpovídat velikosti datové sady.

    ![Statistika složky v Průzkumníku služby Storage](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Vymazání dat z Data Box Disku

Po dokončení kopírování a ověření, že jsou data v účtu úložiště Azure, jsou disky bezpečně smazány podle standardu NIST.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Box Diskem, například jste se naučili:

> [!div class="checklist"]
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Box Disku


Přejděte k dalšímu postupu, kde se naučíte, jak spravovat Data Box Disk prostřednictvím webu Azure Portal.

> [!div class="nextstepaction"]
> [Použití webu Azure Portal ke správě Azure Data Box Disku](./data-box-portal-ui-admin.md)

::: zone-end

::: zone target="chromeless"

# <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Po nahrání dat do Azure ověřte, že jsou vaše data v účtech úložiště, než je odstraníte ze zdroje. Vaše data můžou být v:

- Váš účet Azure Storage. Když data zkopírujete do Data Boxu, v závislosti na jejich typu se nahrají do jedné z následujících cest v účtu služby Azure Storage.

    - **Pro objekty blob bloku a objekty blob stránky**: https://< storage_account_name >. blob. Core. Windows.<containername>NET//Files/a.txt

    - **Pro soubory Azure**: https://< storage_account_name >. File. Core. Windows. NET/<sharename>/Files/a.txt

    Alternativně můžete přejít na svůj účet Azure Storage na webu Azure Portal a dokončit navigaci tam.

- Skupiny prostředků spravovaného disku. Při vytváření spravovaných disků se virtuální pevné disky nahrají jako objekty blob stránky a pak se převedou na spravované disky. Spravované disky jsou připojené ke skupinám prostředků zadaným v době vytváření objednávky.

::: zone-end


