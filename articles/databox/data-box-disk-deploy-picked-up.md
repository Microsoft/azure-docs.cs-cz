---
title: Kurz pro dodávání Azure Data Box Disk zpět | Microsoft Docs
description: V tomto výukovém kurzu se naučíte, jak odeslat Azure Data Box Disk zpátky do Microsoftu
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: ca42532fe6ba954180ce06ed0e3561e42f1fb447
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424232"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Kurz: Vrácení Azure Data Box Disk a ověření nahrávání dat do Azure

Toto je poslední kurz řady: Nasazení Azure Data Box Disk. V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Odeslání Data Box Disku do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Box Disku

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste dokončili [kurz: Zkopírujte data Azure Data Box Disk a ověřte](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Odeslání Data Box Disku zpátky

1. Po dokončení ověření dat odpojte disky. Odeberte propojovací kabely.
2. Zabalte všechny disky a propojovací kabely do bublinkové fólie a vložte je do přepravního boxu. Poplatky mohou platit, pokud chybí příslušenství.
    - Opětovné použití balíčku z prvotní dodávky  
    - Doporučujeme zabalit disky pomocí dobře zabezpečeného zalomení.
    - Ujistěte se, že je přizpůsobení Snug, aby se snížilo jakékoli přesuny v rámci pole.

Další kroky určíte tak, že zařízení vracíte.

### <a name="pick-up-in-us-canada"></a>Projměte v USA, Kanadě

Při vrácení zařízení v USA nebo Kanadě proveďte následující kroky.

1. Použijte zpáteční expediční štítek v průhledném plastovém obalu připevněném k tomuto boxu. Pokud je popisek poškozený nebo ztracený:
    - Přejděte na **Přehled > Stáhnout expediční štítek**.

        ![Stáhnout expediční štítek](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Tato akce stáhne zpáteční expediční štítek, jak je zobrazeno dál.

        ![Ukázkový expediční štítek](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Připevněte popisek na zařízení.

2. Zapečeťte přepravní box a zkontrolujte, že zpáteční expediční štítek je vidět.
3. Naplánujte vyzvednutí pomocí zdroje UPS. Naplánování výdeje:

    - Zavolejte místní zdroje UPS (bezplatné číslo země/oblasti).
    - Ve svém volání citujete číslo sledování zpětné dodávky, jak je znázorněno v tištěné jmenovce.
    - Pokud sledovací číslo není v uvozovkách, bude zdroj napájení vyžadovat, abyste během vyzvednutí zaplatili dodatečné poplatky.
    - Místo plánování vyzvednutí můžete také Data Box Disk umístit na nejbližší místo pro vyřazení.


### <a name="pick-up-in-europe"></a>Vyzvednout v Evropě

Pokud se zařízení vrátí do Evropy, proveďte následující kroky.

1. Použijte zpáteční expediční štítek v průhledném plastovém obalu připevněném k tomuto boxu. Pokud je popisek poškozený nebo ztracený:
    - Přejděte na **Přehled > Stáhnout expediční štítek**.

        ![Stáhnout expediční štítek](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Tato akce stáhne zpáteční expediční štítek, jak je zobrazeno dál.

        ![Ukázkový expediční štítek](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Připevněte popisek na zařízení.

2. Zapečeťte přepravní box a zkontrolujte, že zpáteční expediční štítek je vidět.
3. Pokud zařízení vracíte v Evropě prostřednictvím DHL, vyžádejte si vyzvednutí tak, že navštívíte web DHL a zadáte číslo přepravního lístku.
4. Přejít na web země/oblast DHL Express a vyberte **kniha, > kterou chcete použít pro eReturnou dodávku**.

    ![Dodávka vratek do DHL](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Zadejte číslo nákladového listu a kliknutím na tlačítko **Naplánovat vyzvednutí** zařiďte vyzvednutí.

      ![Naplánovat vyzvednutí](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Výběr v Asii – Tichomoří – oblast

Tato oblast obsahuje pokyny pro výdej v Japonsku, Koreji, Austrálii a Singapuru.

#### <a name="pick-up-in-australia"></a>Vyzvednout v Austrálii

Datacentra Azure v Austrálii mají další bezpečnostní oznámení. Všechny příchozí zásilky musí mít rozšířené oznámení. Pro vyzvednutí v Austrálii proveďte následující kroky.

1. E-mail `adbops@microsoft.com` , který žádá o odeslání popisku s jedinečným příchozím ID nebo kódem Tau Dejte požadavek aspoň na 3 dny předem od plánovaného data expedice, abyste získali popisek v čase.
2. Předmět e-mailu by měl být *požadavek na označení zpětné expedice s kódem Tau*. Nezapomeňte do e-mailu zahrnout tyto podrobnosti: 

    - Název objednávky
    - Adresa
    - Jméno kontaktu

#### <a name="pick-up-in-japan"></a>Vyzvednout v Japonsku

1. Jako informace o odesílateli napište název vaší společnosti a uveďte informace na zásilce.
2. Odešlete e-mailem Quantium řešení pomocí následující e-mailové šablony.

    - V případě, že se v tomto e-mailu nezahrnula Poznámka k odeslání do Japonska nebo Chakubarai, Poznámka: Quantium Solutions Japonsko bude požádat o zveřejnění příspěvku za účelem uvedení poznámky k expedici na vyzvednutí.
    - Pokud máte více objednávek, e-mailem zajistěte individuální vyzvednutí.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```

3. Po zapsání vyzvednutí obdržíte e-mailové potvrzení z řešení Quantium. Potvrzení e-mailu obsahuje také informace o Chakubarai zásilce.

V případě potřeby můžete kontaktovat podporu řešení Quantium (japonský jazyk) na následujících informacích: 

- ElektronCustomerservice.JP@quantiumsolutions.com 
- Telefon: 03-5755-0150 

#### <a name="pick-up-in-korea"></a>Vyzvednout v Koreji

1. Nezapomeňte zahrnout zpáteční poznámku ze zásilky.
2. Požádat o vyzvednutí, pokud je k dispozici Poznámka k expedici:
    1. *Quantium řešení pro mezinárodní* linku na 070-8231-1418 během hodiny Office (od ráno do 5 hodin, od pondělí do pátku). Citace *Microsoft Azure Pickup* a číslo žádosti o službu pro kolekci.  
    2. Pokud je linka zaneprázdněná, pošlete `microsoft@rocketparcel.com`e-mail s předmětem e-mailu *Microsoft Azure vyzvednutí* a číslem žádosti o službu jako reference.
    3. Pokud Courier nepřijde ke kolekci, zavolejte na mezinárodní linku *řešení Quantium* pro alternativní opatření. 
    4. Obdržíte e-mailové potvrzení pro plán vyzvednutí.
3. Tento krok proveďte pouze v případě, že není k dispozici nákladní lístek. Požadavek na vyzvednutí:
    1. *Quantium řešení pro mezinárodní* linku na 070-8231-1418 během hodiny Office (od ráno do 5 hodin, od pondělí do pátku). Citace *Microsoft Azure Pickup* a číslo žádosti o službu pro kolekci. Určete, že k uspořádání kolekce potřebujete nové nákladní poznámky. Zadejte odesílatele (zákazníka), informace o přijímači (datacentrum Azure) a referenční číslo (číslo žádosti o službu). 
    2. Pokud je linka zaneprázdněná, pošlete `microsoft@rocketparcel.com`e-mail s předmětem e-mailu *Microsoft Azure vyzvednutí* a číslem žádosti o službu jako reference.
    3. Pokud Courier nepřijde ke kolekci, zavolejte na mezinárodní linku *řešení Quantium* pro alternativní opatření. 
    4. V případě, že se požadavek provádí přes telefon, se zobrazí ústní potvrzení.

### <a name="pick-up-in-singapore"></a>Vyzvednout v Singapuru

1. Vytiskněte popisek pro expedici a připojte se k poli. Pokud je popisek poškozený nebo ztracený:
    - Přejděte na **Přehled > Stáhnout expediční štítek**.

        ![Stáhnout expediční štítek](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Tato akce stáhne zpáteční expediční štítek, jak je zobrazeno dál.

        ![Ukázkový expediční štítek](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Připevněte popisek na zařízení. Ujistěte se, že je popisek viditelný.

2. Požadavek na vyzvednutí:
    - Zavolejte **SingPost** linku na **6845 6485** během hodiny Office (9:00 až 17:00, pondělí do pátku).  
    - Nabídka *Microsoft Azure Pickup* a číslo žádosti o službu (sledovací číslo na expedičním popisku pro návrat) k uspořádání kolekce. 
    - Zobrazí se ústní potvrzení pro plán vyzvednutí. 
    - Pokud Courier nepřijde pro kolekci, zavolejte **SingPost** v **6845 6485** pro alternativní opatření. 
3. Předají se na Courier. 


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

Jakmile se kopírování dokončí a ověříte, že data jsou v účtu úložiště Azure, disky se bezpečně vymažou v souladu se standardem NIST.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Box Diskem, například jste se naučili:

> [!div class="checklist"]
> * Odeslání Data Box Disku do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Box Disku


Přejděte k dalšímu postupu, kde se naučíte, jak spravovat Data Box Disk prostřednictvím webu Azure Portal.

> [!div class="nextstepaction"]
> [Použití webu Azure Portal ke správě Azure Data Box Disku](./data-box-portal-ui-admin.md)

::: zone-end


::: zone target="chromeless"

# <a name="return-the-disk-and-verify-upload-to-azure"></a>Vrátit disk a ověřit odeslání do Azure

## <a name="ship-to-azure"></a>Odeslání do Azure

1. Po dokončení ověření dat odpojte disky. Odeberte propojovací kabely.
2. Zabalte všechny disky a propojovací kabely do bublinkové fólie a vložte je do přepravního boxu. Poplatky mohou platit, pokud chybí příslušenství.
    - Opětovné použití balíčku z prvotní dodávky  
    - Doporučujeme zabalit disky pomocí dobře zabezpečeného zalomení.
    - Ujistěte se, že je přizpůsobení Snug, aby se snížilo jakékoli přesuny v rámci pole.
3. Další kroky určíte tak, že zařízení vracíte.
    - [Naplánujte si vyzvednutí v případě, že zařízení vrátíte v USA a Kanadě](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada).
    - [Naplánujte si vyzvednutí v rámci DHL pro Evropu](data-box-disk-deploy-picked-up.md#pick-up-in-europe) návštěvou svého webu a zadáním čísla faktury zpátečního.
    - [Naplánování výdeje pro země v oblasti Austrálie – Tichomoří](data-box-disk-deploy-picked-up.md#pick-up-in-asia-pacific-region) , jako je Austrálie, Japonsko, Korea a Singapur.
4. Jakmile váš operátor vybere disky, stav objednávky na portálu se aktualizuje a zobrazí se ID sledování.

## <a name="verify-upload-to-azure"></a>Ověření nahrávání do Azure

Po nahrání dat do Azure ověřte, že jsou vaše data v účtech úložiště, než je odstraníte ze zdroje. Vaše data můžou být v:

- Váš účet Azure Storage. Když data zkopírujete do Data Boxu, v závislosti na jejich typu se nahrají do jedné z následujících cest v účtu služby Azure Storage.

    - **Pro objekty blob bloku a objekty blob stránky**: https://< storage_account_name >. blob. Core. Windows.<containername>NET//Files/a.txt

    - **Pro soubory Azure**: https://< storage_account_name >. File. Core. Windows. NET/<sharename>/Files/a.txt

    Alternativně můžete přejít na svůj účet Azure Storage na webu Azure Portal a dokončit navigaci tam.

- Skupiny prostředků spravovaného disku. Při vytváření spravovaných disků se virtuální pevné disky nahrají jako objekty blob stránky a pak se převedou na spravované disky. Spravované disky jsou připojené ke skupinám prostředků zadaným v době vytváření objednávky.

::: zone-end


