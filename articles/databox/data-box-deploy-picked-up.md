---
title: Vrácení Microsoft Azure Data Boxu | Microsoft Docs
description: Zjistěte, jak odeslat Azure Data Box do Microsoftu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/03/2018
ms.author: alkohli
ms.openlocfilehash: 7676360d71dab4da58693221645517c69b56dff8
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090684"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Kurz: Vrácení Azure Data Boxu a ověření nahrání dat do Azure

Tento kurz popisuje, jak vrátit Azure Data Box a ověřit nahrání dat do Azure.

V tomto kurzu se seznámíte například s následujícími tématy:

> [!div class="checklist"]
> * Odeslání Data Boxu do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste dokončili [kurz, který se týká kopírování dat do Azure Data Boxu a jejich ověření](data-box-deploy-copy-data.md).

## <a name="ship-data-box-back"></a>Vrácení Data Boxu

1. Ujistěte se, že je zařízení vypnuté a nejsou k němu zapojené žádné kabely. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
2. Pokud zařízení posíláte v rámci USA, ujistěte se, že se na displeji s elektronickým inkoustem zobrazuje expediční štítek, a naplánujte vyzvednutí přepravcem. Pokud se tento štítek ztratil, je poškozený nebo se nezobrazuje na displeji s elektronickým inkoustem, stáhněte si expediční štítek z webu Azure Portal a připevněte ho k zařízení. Přejděte na **Přehled > Stáhnout expediční štítek**. 

    Pokud zařízení posíláte v Evropě, displej s elektronickým inkoustem nezobrazuje expediční štítek. Místo toho najdete zpětný expediční štítek v průhledném pouzdře pod příchozím expedičním štítkem. Vyjměte starý expediční štítek a ujistěte se, že je expediční štítek dobře vidět.
    
3. Pokud zařízení vracíte v USA, domluvte si vyzvednutí s UPS. Pokud zařízení vracíte v Evropě prostřednictvím DHL, vyžádejte si vyzvednutí tak, že navštívíte web DHL a zadáte číslo přepravního lístku. Přejděte na web DHL Express a zvolte **Naplánovat vyzvednutí pro zpáteční zásilku**. 

    Zadejte číslo nákladového listu a kliknutím na tlačítko **Naplánovat vyzvednutí** zařiďte vyzvednutí.

4. Jakmile přepravce vyzvedne a naskenuje Data Box, stav objednávky na portálu se změní na **Vyzvednuto**. Zobrazí se také ID sledování.

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Jakmile Microsoft přijme a naskenuje zařízení, stav objednávky se změní na **Přijato**. Zařízení pak projde fyzickým ověřením poškození nebo známek manipulace. 

Po dokončení ověřování se Data Box připojí k síti v datacentru Azure. Automaticky se spustí kopírování dat. V závislosti na velikosti dat může operace kopírování trvat několik hodin až dní. Průběh kopírování můžete sledovat na portálu.

Po dokončení kopírování se stav objednávky změní na **Dokončeno**.

Než odstraníte data ze zdroje, ujistěte se, že je máte v účtech úložiště. 

## <a name="erasure-of-data-from-data-box"></a>Vymazání dat z Data Boxu
 
 Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Odeslání Data Boxu do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Boxu

V následujícím článku se dozvíte, jak spravovat Data Box přes místní webové uživatelské rozhraní.

> [!div class="nextstepaction"]
> [Správa Azure Data Boxu pomocí místního webového uživatelského rozhraní](./data-box-local-web-ui-admin.md)


