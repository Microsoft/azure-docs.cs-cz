---
title: Kurz ověření nahrání dat z Azure Data Box Disku do účtu úložiště | Microsoft Docs
description: V tomto kurzu se dozvíte, jak ověřit nahrání dat z Azure Data Box Disku do účtu úložiště Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/17/2019
ms.author: alkohli
ms.openlocfilehash: abe09cf10e241ec95ceed767e7038cde07667fc9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322729"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Kurz: Ověření nahrání dat z Azure Data Box Disku

Toto je poslední kurz série: Nasazení Azure Data Box Disku. V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Box Disku

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste dokončili [Kurz: Vrácení Azure Data Box Disku](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Jakmile přepravce vyzvedne disky, stav objednávky na portálu se změní na **Vyzvednuto**. Zobrazí se také ID sledování.

![Disky jsou vyzvednuté](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Když Microsoft dostane disky a prohledá je, stav úlohy se změní na **Přijato**. 

![Disky jsou přijaté](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Jakmile jsou disky připojené k serveru v datovém centru Azure, data se automaticky zkopírují. V závislosti na velikosti dat může operace kopírování trvat několik hodin až dní. Průběh kopírování můžete sledovat na portálu.

Po dokončení kopírování se stav objednávky změní na **Dokončeno**.

![Kopírování dat je dokončené](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Pokud se kopírování dokončí s chybami, přečtěte si téma věnované [řešení potíží s chybami nahrávání](data-box-disk-troubleshoot-upload.md).

Než odstraníte data ze zdroje, ujistěte se, že je máte v účtech úložiště. Vaše data můžou být v následujících umístěních:

- Vaše účty Azure Storage. Když data zkopírujete do Data Boxu, v závislosti na jejich typu se nahrají do jedné z následujících cest v účtu služby Azure Storage.

  - Objekty blob bloku a objekty blob stránky: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Soubory Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternativně můžete přejít ke svému účtu služby Azure Storage na webu Azure Portal a dokončit navigaci tam.

- Vaše skupiny prostředků spravovaných disků. Při vytváření spravovaných disků se virtuální pevné disky nahrají jako objekty blob stránky a pak se převedou na spravované disky. Spravované disky se připojí ke skupinám prostředků zadaným při vytváření objednávky.

  - Pokud vaše kopírování na spravované disky v Azure proběhne úspěšně, můžete na webu Azure Portal přejít na **Podrobnosti objednávky** a poznamenat si skupinu prostředků zadanou pro spravované disky.

      ![Zobrazení podrobností objednávky](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Přejděte do skupiny prostředků, kterou jste si poznamenali, a vyhledejte své spravované disky.

      ![Skupina prostředků pro spravované disky](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

    > [!NOTE]
    > Pokud se objekt blob stránky během kopírování úspěšně nepřevedl na spravovaný disk, zůstane v účtu úložiště a bude se vám účtovat úložiště.

  -  Pokud kopírujete VHDX nebo dynamický nebo rozdílový virtuální pevný disk, VHDX/VHD se nahraje do přípravného účtu úložiště jako objekt blob bloku. Přejděte do svého přípravného **účtu úložiště do části Objekty blob** a vyberte odpovídající kontejner – StandardSSD, Standard HDD nebo PremiumSSD. Ve vašem přípravném účtu úložiště by se měly zobrazit VHDX/VHD jako objekty blob bloku.
  

  
::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Než po nahrání dat do Azure odstraníte data ze zdroje, ujistěte se, že je máte v účtech úložiště. Vaše data můžou být v následujících umístěních:

- Vaše účty Azure Storage. Když data zkopírujete do Data Boxu, v závislosti na jejich typu se nahrají do jedné z následujících cest v účtu služby Azure Storage.

    - **Objekty blob bloku a objekty blob stránky:** https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt

    - **Soubory Azure:** https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt

- Vaše skupiny prostředků spravovaných disků. Při vytváření spravovaných disků se virtuální pevné disky nahrají jako objekty blob stránky a pak se převedou na spravované disky. Spravované disky se připojí ke skupinám prostředků zadaným při vytváření objednávky.

::: zone-end

Pokud chcete ověřit, že se data nahrála do Azure, postupujte následovně:

1. Přejděte k účtu úložiště přidruženému k vaší objednávce disků.
2. Přejděte na **Blob service > Procházet objekty blob**. Zobrazí se seznam kontejnerů. Ve vašem účtu úložiště se vytvoří kontejnery se stejným názvem jako podsložky, které jste vytvořili ve složkách *BlockBlob* a *PageBlob*.
    Pokud názvy složek neodpovídají zásadám vytváření názvů v Azure, nahrávání dat do Azure selže.

3. K ověření, že se nahrála kompletní datová sada, použijte Průzkumníka služby Azure Storage. Připojte účet úložiště odpovídající objednávce Data Box Disku a prohlédněte si seznam kontejnerů objektů blob. Vyberte kontejner, klikněte na **…Další** a potom klikněte na **statistiku složky**. V podokně **Aktivity** se zobrazí statistika pro příslušnou složku, včetně počtu objektů blob a jejich celkové velikosti. Celková velikost objektů blob v bajtech by měla odpovídat velikosti datové sady.

    ![Statistika složky v Průzkumníku služby Storage](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Vymazání dat z Data Box Disku

Po dokončení nahrávání do Azure se data z disků Data Box Disku vymažou v souladu se standardem [NIST SP 800-88](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone target="docs"

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Box Diskem, například jste se naučili:

> [!div class="checklist"]
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Box Disku


Přejděte k dalšímu postupu, kde se naučíte, jak spravovat Data Box Disk prostřednictvím webu Azure Portal.

> [!div class="nextstepaction"]
> [Použití webu Azure Portal ke správě Azure Data Box Disku](./data-box-portal-ui-admin.md)

::: zone-end




