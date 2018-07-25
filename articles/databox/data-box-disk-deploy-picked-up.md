---
title: Vrácení Microsoft Azure Data Box Disku| Microsoft Docs
description: V tomto výukovém kurzu se naučíte, jak odeslat Azure Data Box Disk zpátky do Microsoftu
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 783c837bbb9ce22e7354252523e6725753776836
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011054"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Kurz: Vrácení Azure Data Box Disku a ověření nahrání dat do Azure

Toto je poslední kurz ze série Nasazení Azure Data Box Disku. V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Odeslání Data Box Disku do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Box Disku

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste dokončili [kurz, který se týká kopírování dat na Azure Data Box Disk a ověření](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Odeslání Data Box Disku zpátky

1. Po dokončení ověření dat odpojte disky. Odeberte propojovací kabely.
2. Zabalte všechny disky a propojovací kabely do bublinkové fólie a vložte je do přepravního boxu.
3. Použijte zpáteční expediční štítek v průhledném plastovém obalu připevněném k tomuto boxu. Pokud se tento štítek ztratil nebo je poškozený, z webu Azure Portal si stáhněte nový expediční štítek a připevněte ho k zařízení. Přejděte na **Přehled > Stáhnout expediční štítek**. 

    ![Stáhnout expediční štítek](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    Tato akce stáhne zpáteční expediční štítek, jak je zobrazeno dál.

    ![Ukázkový expediční štítek](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)

4. Zapečeťte přepravní box a zkontrolujte, že zpáteční expediční štítek je vidět.
5. Pokud zařízení vracíte v USA, domluvte si vyzvednutí s UPS. Pokud zařízení vracíte v Evropě prostřednictvím DHL, vyžádejte si vyzvednutí tak, že navštívíte web DHL a zadáte číslo přepravního lístku. Přejděte na web DHL Express a zvolte **Naplánovat vyzvednutí pro zpáteční zásilku**.

    ![Zpáteční zásilka DHL](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
    Zadejte číslo nákladového listu a kliknutím na tlačítko **Naplánovat vyzvednutí** zařiďte vyzvednutí.

      ![Naplánovat vyzvednutí](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

7. Jakmile přepravce vyzvedne disky, stav objednávky na portálu se změní na **Vyzvednuto**. Zobrazí se také ID sledování.

    ![Disky jsou vyzvednuté](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Když Microsoft dostane disky a prohledá je, stav úlohy se změní na **Přijato**. 

![Disky jsou přijaté](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Jakmile jsou disky připojené k serveru v datovém centru Azure, data se automaticky zkopírují. V závislosti na velikosti dat může operace kopírování trvat několik hodin až dní. Průběh kopírování můžete sledovat na portálu.

Po dokončení kopírování se stav objednávky změní na **Dokončeno**.

![Kopírování dat je dokončené](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Než odstraníte data ze zdroje, ujistěte se, že je máte v účtech úložiště. Pokud chcete ověřit, že se data nahrála do Azure, postupujte takto:

1. Přejděte k účtu úložiště přidruženému k vaší objednávce disků.
2. Přejděte na **Blob service > Procházet objekty blob**. Zobrazí se seznam kontejnerů. Ve vašem účtu úložiště se vytvoří kontejnery se stejným názvem jako podsložky, které jste vytvořili ve složkách *BlockBlob* a *PageBlob*.
    Pokud názvy složek neodpovídají zásadám vytváření názvů v Azure, nahrávání dat do Azure selže.

4. K ověření, že se nahrála kompletní datová sada, použijte Průzkumníka služby Azure Storage. Připojte účet úložiště odpovídající objednávce pronájmu disků a prohlédněte si seznam kontejnerů objektů blob. Vyberte kontejner, klikněte na **…Další** a potom klikněte na **statistiku složky**. V podokně **Aktivity** se zobrazí statistika pro příslušnou složku, včetně počtu objektů blob a jejich celkové velikosti. Celková velikost objektů blob v bajtech by měla odpovídat velikosti datové sady.

    ![Statistika složky v Průzkumníku služby Storage](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Vymazání dat z Data Box Disku

Jakmile se kopírování dokončí a ověříte, že data jsou v účtu úložiště Azure, disky se bezpečně vymažou v souladu se standardem NIST. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Odeslání Data Box Disku do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Box Disku


Přejděte k dalšímu postupu, kde se naučíte, jak spravovat Data Box Disk prostřednictvím webu Azure Portal.

> [!div class="nextstepaction"]
> [Použití webu Azure Portal ke správě Azure Data Box Disku](./data-box-portal-ui-admin.md)


