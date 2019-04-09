---
title: Vrácení Microsoft Azure Data Box Disku| Microsoft Docs
description: V tomto výukovém kurzu se naučíte, jak odeslat Azure Data Box Disk zpátky do Microsoftu
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7d1d38a2e8d3216414ab215f3023d4a188c57120
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282895"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Kurz: Vrátí disku Azure Data Box a ověřte nahrání dat do Azure

Toto je poslední kurzu této série: Nasazení disku Azure Data Box. V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Odeslání Data Box Disku do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Box Disku

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste dokončili [kurzu: Kopírování dat do Azure Data Box Disk a ověřte](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Odeslání Data Box Disku zpátky

1. Po dokončení ověření dat odpojte disky. Odeberte propojovací kabely.
2. Zabalte všechny disky a propojovací kabely do bublinkové fólie a vložte je do přepravního boxu. Pokud chybí příslušenství se můžou vztahovat poplatky.
    - Znovu použít balení z počáteční dodávky.  
    - Doporučujeme vám, aktualizací Service pack disků pomocí dobře zabezpečené probublání zalamování řádků.
    - Zajistěte, aby že se bezpečně ke snížení jakékoli pohybů plb typu v rámci pole přizpůsobit.

Další kroky jsou určeny ve kterém jsou vrácení zařízení.

### <a name="pick-up-in-us-canada"></a>Pokračovat v USA, Kanadě

Proveďte následující kroky, pokud vrací zařízení v USA a Kanadě.

1. Použijte zpáteční expediční štítek v průhledném plastovém obalu připevněném k tomuto boxu. Pokud dojde k poškození nebo ztrátě popisku:
    - Přejděte na **Přehled > Stáhnout expediční štítek**.

        ![Stáhnout expediční štítek](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Tato akce stáhne zpáteční expediční štítek, jak je zobrazeno dál.

        ![Ukázkový expediční štítek](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Opatří štítek na zařízení.

2. Zapečeťte přepravní box a zkontrolujte, že zpáteční expediční štítek je vidět.
3. Vyzvednutí s UPS naplánujte.


### <a name="pick-up-in-europe"></a>Pokračovat v Evropě

Pokud vrácení zařízení v Evropě, proveďte následující kroky.

1. Použijte zpáteční expediční štítek v průhledném plastovém obalu připevněném k tomuto boxu. Pokud dojde k poškození nebo ztrátě popisku:
    - Přejděte na **Přehled > Stáhnout expediční štítek**.

        ![Stáhnout expediční štítek](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Tato akce stáhne zpáteční expediční štítek, jak je zobrazeno dál.

        ![Ukázkový expediční štítek](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Opatří štítek na zařízení.

2. Zapečeťte přepravní box a zkontrolujte, že zpáteční expediční štítek je vidět.
3. Pokud zařízení vracíte v Evropě prostřednictvím DHL, vyžádejte si vyzvednutí tak, že navštívíte web DHL a zadáte číslo přepravního lístku.
4. Přejděte na web DHL Express a zvolte **Naplánovat vyzvednutí pro zpáteční zásilku**.

    ![Zpáteční zásilku DHL](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Zadejte číslo nákladového listu a kliknutím na tlačítko **Naplánovat vyzvednutí** zařiďte vyzvednutí.

      ![Naplánovat vyzvednutí](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Pokračovat v oblasti Asie a Tichomoří

Tato oblast obsahuje pokyny pro vyzvednutí v oblasti Japonsko, Koreji a Austrálii.

#### <a name="pick-up-in-australia"></a>Pokračovat v Austrálii

Datacentra Azure v Austrálii mít oznámení o zvýšení zabezpečení. Všechny příchozí dodávky musí mít oznámení o pokročilé. Proveďte následující kroky pro vyzvednutí v Austrálii.

1. E-mailu `adbops@microsoft.com` na žádost o odeslání popisek s jedinečné ID příchozí nebo TAU kód. Umístěte požadavek alespoň 3 dny před datem plánované dodání zobrazíte popisek v čase.
2. Předmět e-mailu by měla být – *žádost o zpětný Expediční štítek s kódem TAU*. Ujistěte se, že v e-mailu uveďte následující podrobnosti: 

    - Název objednávky
    - Adresa
    - Jméno kontaktu

#### <a name="pick-up-in-japan"></a>Pokračovat v Japonsku

1. Nezapomeňte uvést návratový connote Chakubarai Japonsko příspěvku.
2. Zápis vaší společnosti název a adresu informace o connote jako vaše informace odesílatele.
3. Zavolejte na číslo žádost o vyzvednutí Japonsko příspěvek 0800-0800-111 (bezplatné volání). Vytočit 7 číslic PSČ výstupní adresy a potom je předejte pro nejbližší pobočku příspěvku.
    - Dostupné časy pro žádost o vyzvednutí závisí na příslušné pobočky příspěvku.
    - Informujte, že zásilka je pro Japonsko příspěvek Chakubarai Yu-Pack.
    - Použití Chakubarai connote, která byla zahrnuta.
4. Pokud Japonsko příspěvek Chakubarai connote nejsou zahrnuty, e-mailu *Quantium řešení* na `Customerservice.JP@quantiumsolutions.com`. *Řešení Quantium* požádá o příspěvek Japonsko sbírání a požádejte ho, aby přenést connote na vyzvednutí.
    - Označuje odkaz na číslo Chakubarai connote příspěvek sloupec, který přinese Japonsko příspěvku.
    - Zadejte adresu pro doručení, jak je znázorněno níže:   
        ```
        3F N7 Prologis Park Tokyo Ohta, 1-4-3 Tokai Ohta-ku, Tokyo 143-0001
        Microsoft Service Center c/o Quantium Solutions Japan
        TEL: 03-5755-0150
        ```

Pokud Chakubarai connote chybí, můžete požádat o vyzvednutí e-mailu. Pomocí následující šablony e-mailovou žádost o vyzvednutí.

```
To: Customerservice.JP@quantiumsolutions.com
Subject: Pickup request for Azure Data Box Disk｜Job Name： 
Body: 
- Azure Data Box Disk job name：
- Reference number:  
- Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
```

#### <a name="pick-up-in-korea"></a>Pokračovat v Koreji

1. Nezapomeňte uvést návratový connote.
2. Chcete-li žádost o vyzvednutí:
    1. Volání *Quantium Solutions International* linku na 070 1418 8231 během hodin (10: 00 do 17: 00, pondělí až pátku). Nabídka *Microsoft vyzvednutí* a číslo connote uspořádat pro kolekci.  
    2. Pokud linka pro je zaneprázdněný, e-mailu `microsoft@rocketparcel.com`, s předmět e-mailu *Pickup Microsoft* a číslo connote jako odkaz.
    3. Pokud Kurýrní nedorazí pro kolekci, zavolejte *Quantium Solutions International* linku pro alternativní režimu. 

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Jakmile přepravce vyzvedne disky, stav objednávky na portálu se změní na **Vyzvednuto**. Zobrazí se také ID sledování.

![Disky jsou vyzvednuté](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Když Microsoft dostane disky a prohledá je, stav úlohy se změní na **Přijato**. 

![Disky jsou přijaté](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Jakmile jsou disky připojené k serveru v datovém centru Azure, data se automaticky zkopírují. V závislosti na velikosti dat může operace kopírování trvat několik hodin až dní. Průběh kopírování můžete sledovat na portálu.

Po dokončení kopírování se stav objednávky změní na **Dokončeno**.

![Kopírování dat je dokončené](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Než odstraníte data ze zdroje, ujistěte se, že je máte v účtech úložiště. Vaše data můžou být v:

- Vaše účty úložiště Azure. Když data zkopírujete do Data Boxu, v závislosti na jejich typu se nahrají do jedné z následujících cest v účtu služby Azure Storage.

  - Pro objekty BLOB bloku a objekty BLOB stránky: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Pro soubory Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternativně můžete přejít na svůj účet Azure Storage na webu Azure Portal a dokončit navigaci tam.

- Vaší skupiny prostředků spravovaného disku. Při vytváření spravované disky, virtuální pevné disky jsou odeslat jako objekty BLOB stránky a poté převeden na spravované disky. Spravované disky jsou připojené ke skupinám prostředků zadané v době vytvoření objednávky.

  - Pokud vaše kopie na managed disks v Azure byla úspěšná, může použít **podrobnosti objednávky** webu Azure portal a zkontrolujte poznamenejte si skupinu prostředků zadaná pro spravované disky.

      ![Zobrazit podrobnosti objednávky](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Přejděte do skupiny prostředků uvedené a vyhledejte spravované disky.

      ![Skupina prostředků za spravované disky](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Pokud jste si zkopírovali disk VHDX nebo dynamický nebo rozdílový virtuální pevný disk, pak VHDX nebo VHD se nahrají do přípravného účtu úložiště jako objekt blob bloku. Přejděte k vaší pracovní **účet úložiště > objekty BLOB** a pak vyberte příslušný kontejner – StandardSSD, StandardHDD nebo PremiumSSD. VHDX nebo VHD se měla zobrazit jako objekty BLOB bloku do přípravného účtu úložiště.

Pokud chcete ověřit, že se data nahrála do Azure, postupujte takto:

1. Přejděte k účtu úložiště přidruženému k vaší objednávce disků.
2. Přejděte na **Blob service > Procházet objekty blob**. Zobrazí se seznam kontejnerů. Ve vašem účtu úložiště se vytvoří kontejnery se stejným názvem jako podsložky, které jste vytvořili ve složkách *BlockBlob* a *PageBlob*.
    Pokud názvy složek neodpovídají zásadám vytváření názvů v Azure, nahrávání dat do Azure selže.

4. K ověření, že se nahrála kompletní datová sada, použijte Průzkumníka služby Azure Storage. Připojte účet úložiště odpovídající objednávce pronájmu disků a prohlédněte si seznam kontejnerů objektů blob. Vyberte kontejner, klikněte na **…Další** a potom klikněte na **statistiku složky**. V podokně **Aktivity** se zobrazí statistika pro příslušnou složku, včetně počtu objektů blob a jejich celkové velikosti. Celková velikost objektů blob v bajtech by měla odpovídat velikosti datové sady.

    ![Statistika složky v Průzkumníku služby Storage](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Vymazání dat z Data Box Disku

Jakmile se kopírování dokončí a ověříte, že data jsou v účtu úložiště Azure, disky se bezpečně vymažou v souladu se standardem NIST.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Box Diskem, například jste se naučili:

> [!div class="checklist"]
> * Odeslání Data Box Disku do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Box Disku


Přejděte k dalšímu postupu, kde se naučíte, jak spravovat Data Box Disk prostřednictvím webu Azure Portal.

> [!div class="nextstepaction"]
> [Pomocí webu Azure portal ke správě disku Azure Data Box](./data-box-portal-ui-admin.md)


