---
title: Kurz pro dodávání Azure Data Box zpět | Microsoft Docs
description: Zjistěte, jak odeslat Azure Data Box do Microsoftu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 8/27/2019
ms.author: alkohli
ms.openlocfilehash: 368439d6e15d6c94bbb96d67fcb48ab006234c95
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098831"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Kurz: Vrácení Azure Data Box a ověření nahrávání dat do Azure

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Vrácení Data Box a ověření nahrávání dat do Azure

::: zone-end

::: zone target="docs"

Tento kurz popisuje, jak vrátit Azure Data Box a ověřit nahrání dat do Azure.

V tomto kurzu se seznámíte například s následujícími tématy:

> [!div class="checklist"]
> * Požadavky
> * Připravit k odeslání
> * Odeslání Data Boxu do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

- Dokončili jste tento [kurz: Zkopírujte data Azure Data Box a ověřte](data-box-deploy-copy-data.md). 
- Kopírování úloh bylo dokončeno. Příprava k odeslání nemůže běžet, pokud probíhá kopírování úloh.

## <a name="prepare-to-ship"></a>Připravit k odeslání

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Po dokončení kopírování dat zařízení připravíte a dodáte. Když zařízení dosáhne datacentra Azure, data se automaticky odešlou do Azure.

## <a name="prepare-to-ship"></a>Připravit k odeslání

Než dokončíte přípravu na odeslání, ujistěte se, že jsou dokončené úlohy kopírování.

1. Přejděte na stránku **Prepare to ship** (Připravit k odeslání) v místním webovém uživatelském rozhraní a zahajte přípravu k odeslání. 
2. Z místního webového uživatelského rozhraní vypněte zařízení. Odpojte od zařízení kabely. 

Další kroky určíte tak, že zařízení vracíte.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Vrácení Data Boxu

Zajistěte, aby byla kopie dat do zařízení dokončena a **Příprava k odeslání** spuštění proběhlo úspěšně. Postup se liší v závislosti na oblasti, ve které se zařízení dodává.

::: zone-end

## <a name="ship-in-us-canada-europe"></a>Dodat v USA, Kanadě, Evropě

Při vracení zařízení v USA, Kanadě nebo Evropě proveďte následující kroky.

1. Ujistěte se, že je zařízení vypnuté, a odeberou se kabely. 
2. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
3. Ujistěte se, že se na displeji s elektronickým inkoustem zobrazuje expediční štítek, a naplánujte vyzvednutí přepravcem. Pokud je popisek poškozený nebo ztracený nebo se nezobrazuje v zobrazení E-tiskovou barvou, kontaktujte podpora Microsoftu. Pokud je tato podpora navrhována, můžete přejít na **přehled > stáhnout expediční štítek** v Azure Portal. Stáhněte si expediční štítek a přihlaste se k zařízení. 
4. Naplánujte vyzvednutí se zdrojem napájení při vracení zařízení. Naplánování výdeje:

    - Zavolejte místní zdroje UPS (bezplatné číslo země/oblasti).
    - Ve svém volání citujete číslo sledování zpětné dodávky, jak je znázorněno v zobrazení elektronického inkoustu nebo ve vytištěném popisku.
    - Pokud sledovací číslo není v uvozovkách, bude zdroj napájení vyžadovat, abyste během vyzvednutí zaplatili dodatečné poplatky.

    Místo plánování vyzvednutí můžete také Data Box umístit na nejbližší místo pro vyřazení.
4. Jakmile přepravce vyzvedne a naskenuje Data Box, stav objednávky na portálu se změní na **Vyzvednuto**. Zobrazí se také ID sledování.


## <a name="ship-in-australia"></a>Dodávat v Austrálii

Datacentra Azure v Austrálii mají další bezpečnostní oznámení. Všechny příchozí zásilky musí mít rozšířené oznámení. Proveďte následující kroky, které dodáte v Austrálii.


1. Uchová původní pole použité k odeslání zařízení pro dodávku vratky.
2. Ujistěte se, že je kopírování dat do zařízení dokončeno a **Příprava k odeslání spuštění** proběhlo úspěšně.
3. Vypněte zařízení a odeberte kabely.
4. Zařazování a bezpečné umístění napájecí šňůry, která byla v zařízení k dispozici na zadní straně zařízení.
5. Odešlete e-mailem Quantium řešení pro vyžádání žádosti o vyzvednutí. Přečtěte si referenční číslo služby zadané v Azure Portal. Použijte následující šablonu e-mailu:- *Request pro zpětný přenos popisku s kódem Tau*. Nezapomeňte do e-mailu zahrnout tyto podrobnosti: 

    ```
    To: Azure@quantiumsolutions.com
    Subject: Pickup request for Azure｜Reference number：XXX XXX XXX
    Body: 
    - Company name：
    - Address:
    - Contact name:
    - Contact number:
    - Requested pickup date: mm/dd
    ```
6. Quantium Solutions Austrálie vám pošle e-mailem zpáteční expediční štítek.
7. Vytiskněte návratový popisek a přihlaste ho v poli pro expedici.
8. Doručním balíčkem na Courier.

V případě potřeby můžete podporu řešení Quantium poslat e- Azure@quantiumsolutions.com mailem na telefon nebo telefonicky.

Pro dotazování týkající se vaší objednávky prostřednictvím telefonu:

- Nejprve odešlete e-mail pro vyzvednutí.
- Zadejte název objednávky na telefonu.

## <a name="ship-in-japan"></a>Dodat v Japonsku 

1. Uchová původní pole použité k odeslání zařízení pro dodávku vratky.
2. Vypněte zařízení a odeberte kabely.
3. Zařazování a bezpečné umístění napájecí šňůry, která byla v zařízení k dispozici na zadní straně zařízení.
4. Jako informace o odesílateli napište název vaší společnosti a uveďte informace na zásilce.
5. Odešlete e-mailem Quantium řešení pomocí následující e-mailové šablony.

    - V případě, že se v tomto e-mailu nezahrnula Poznámka k odeslání do Japonska nebo Chakubarai, Poznámka: Quantium Solutions Japonsko bude požádat o zveřejnění příspěvku za účelem uvedení poznámky k expedici na vyzvednutí.
    - Pokud máte více objednávek, e-mailem zajistěte individuální vyzvednutí.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
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

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Jakmile Microsoft přijme a naskenuje zařízení, stav objednávky se změní na **Přijato**. Zařízení pak projde fyzickým ověřením poškození nebo známek manipulace.

Po dokončení ověřování se Data Box připojí k síti v datacentru Azure. Automaticky se spustí kopírování dat. V závislosti na velikosti dat může operace kopírování trvat několik hodin až dní. Průběh kopírování můžete sledovat na portálu.

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

::: zone-end

## <a name="erasure-of-data-from-data-box"></a>Vymazání dat z Data Boxu
 
Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone target="docs"

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Požadavky
> * Připravit k odeslání
> * Odeslání Data Boxu do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Boxu

V následujícím článku se dozvíte, jak spravovat Data Box přes místní webové uživatelské rozhraní.

> [!div class="nextstepaction"]
> [Správa Azure Data Boxu pomocí místního webového uživatelského rozhraní](./data-box-local-web-ui-admin.md)

::: zone-end


