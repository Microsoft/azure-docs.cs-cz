---
title: Kurz vrácení Azure Data Boxu| Microsoft Docs
description: Zjistěte, jak odeslat Azure Data Box do Microsoftu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 8/27/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 5b78dc8b815802502cda5baf2bf5e9646922d671
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70241432"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Kurz: Vrácení Azure Data Boxu a ověření nahrání dat do Azure

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Vrácení Data Boxu a ověření nahrání dat do Azure

::: zone-end

::: zone target="docs"

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

- Dokončili jste [Kurz: Kopírování dat do Azure Data Boxu a jejich ověření](data-box-deploy-copy-data.md). 
- Dokončily se úlohy kopírování. Pokud probíhají úlohy kopírování, nemůže proběhnout příprava k odeslání.

## <a name="prepare-to-ship"></a>Příprava k odeslání

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Po dokončení kopírování dat připravíte a odešlete zařízení. Když zařízení dorazí do datacentra Azure, data se automaticky nahrají do Azure.

## <a name="prepare-to-ship"></a>Příprava k odeslání

Než začnete s přípravou k odeslání, ujistěte se, že se dokončily úlohy kopírování.

1. Přejděte na stránku **Prepare to ship** (Připravit k odeslání) v místním webovém uživatelském rozhraní a zahajte přípravu k odeslání. 
2. Z místního webového uživatelského rozhraní vypněte zařízení. Odpojte od zařízení kabely. 

Další kroky závisí na tom, kam zařízení vracíte.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Vrácení Data Boxu

Ujistěte se, že se dokončilo kopírování dat do zařízení a úspěšně proběhla **příprava k odeslání**. Postup se liší v závislosti na oblasti, kam zařízení odesíláte.

::: zone-end

## <a name="ship-in-us-canada-europe"></a>Odeslání v USA, Kanadě nebo Evropě

Pokud zařízení vracíte v USA, Kanadě nebo Evropě, proveďte následující kroky.

1. Ujistěte se, že je zařízení vypnuté a nejsou k němu zapojené žádné kabely. 
2. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
3. Ujistěte se, že se na displeji s elektronickým inkoustem zobrazuje expediční štítek, a naplánujte vyzvednutí přepravcem. Pokud se štítek poškodí, ztratí nebo nezobrazí na displeji s elektronickým inkoustem, obraťte se na podporu Microsoftu. Pokud vám to podpora navrhne, můžete pak na webu Azure Portal přejít do části **Přehled > Stáhnout expediční štítek**. Stáhněte si expediční štítek a připevněte ho na zařízení. 
4. Pokud zařízení vracíte, domluvte si vyzvednutí s UPS. Naplánování vyzvednutí:

    - Zavolejte do místní pobočky UPS (bezplatné číslo pro příslušnou zemi/oblast).
    - Během volání uveďte sledovací číslo zpětné zásilky, jak je uvedené na displeji s elektronickým inkoustem nebo na vytištěném štítku.
    - Pokud sledovací číslo neuvedete, bude společnost UPS vyžadovat, abyste při vyzvednutí zaplatili dodatečné poplatky.

    Místo naplánování vyzvednutí můžete také Data Box dovézt na nejbližší sběrné místo.
4. Jakmile přepravce vyzvedne a naskenuje Data Box, stav objednávky na portálu se změní na **Vyzvednuto**. Zobrazí se také ID sledování.


## <a name="ship-in-australia"></a>Odeslání v Austrálii

Datacentra Azure v Austrálii mají další bezpečnostní oznámení. Všechny příchozí zásilky musí mít předchozí oznámení. Při odesílání v Austrálii proveďte následující kroky.


1. Ponechejte si původní obal, ve které jste zařízení obdrželi, pro zpáteční zásilku.
2. Ujistěte se, že se dokončilo kopírování dat do zařízení a úspěšně proběhla **příprava k odeslání**.
3. Vypněte zařízení a odpojte kabely.
4. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
5. Odešlete společnosti Quantium Solutions e-mail s žádostí o vyzvednutí. Uveďte referenční číslo služby uvedené na webu Azure Portal. Použijte následující šablonu e-mailu: *Žádost o zpáteční expediční štítek s kódem TAU*. V e-mailu nezapomeňte uvést následující podrobnosti: 

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
6. Společnost Quantium Solutions Austrálie vám e-mailem pošle zpáteční expediční štítek.
7. Zpáteční štítek vytiskněte a připevněte ho na obal zásilky.
8. Předejte balíček přepravci.

V případě potřeby můžete kontaktovat podporu společnosti Quantium Solutions na e-mailu Azure@quantiumsolutions.com nebo přes telefon.

V případě dotazů souvisejících s vaší objednávkou přes telefon:

- Nejprve odešlete e-mail s žádostí o vyzvednutí.
- Přes telefon uveďte název vaší objednávky.

## <a name="ship-in-japan"></a>Odeslání v Japonsku 

1. Ponechejte si původní obal, ve které jste zařízení obdrželi, pro zpáteční zásilku.
2. Vypněte zařízení a odpojte kabely.
3. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
4. Vyplňte název vaší společnosti a adresu do nákladního listu do části informací o odesílateli.
5. Odešlete e-mail společnosti Quantium Solutions pomocí následující e-mailové šablony.

    - V případě, že nákladní list japonské pošty Chakubarai nebyl obsažen nebo chybí, uveďte to v e-mailu. Společnost Quantium Solutions Japonsko požádá japonskou poštu, aby nákladní list při vyzvednutí přinesla.
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

3. Po rezervaci vyzvednutí obdržíte e-mailové potvrzení od společnosti Quantium Solutions. E-mailové potvrzení obsahuje také informace o nákladním listu Chakubarai.

V případě potřeby můžete kontaktovat podporu společnosti Quantium Solutions (v japonštině) následujícími způsoby: 

- E-mailem：Customerservice.JP@quantiumsolutions.com 
- Telefonicky: 03-5755-0150 

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

Jakmile Microsoft přijme a naskenuje zařízení, stav objednávky se změní na **Přijato**. Zařízení pak projde fyzickým ověřením poškození nebo známek manipulace.

Po dokončení ověřování se Data Box připojí k síti v datacentru Azure. Automaticky se spustí kopírování dat. V závislosti na velikosti dat může operace kopírování trvat několik hodin až dní. Průběh kopírování můžete sledovat na portálu.

Po dokončení kopírování se stav objednávky změní na **Dokončeno**.

Než odstraníte data ze zdroje, ujistěte se, že se nahrála do Azure. Vaše data můžou být v následujících umístěních:

- Vaše účty Azure Storage. Když data zkopírujete do Data Boxu, v závislosti na jejich typu se nahrají do jedné z následujících cest v účtu služby Azure Storage.

  - Objekty blob bloku a objekty blob stránky: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Soubory Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternativně můžete přejít na svůj účet Azure Storage na webu Azure Portal a dokončit navigaci tam.

- Vaše skupiny prostředků spravovaných disků. Při vytváření spravovaných disků se virtuální pevné disky nahrají jako objekty blob stránky a pak se převedou na spravované disky. Spravované disky se připojí ke skupinám prostředků zadaným při vytváření objednávky. 

    - Pokud vaše kopírování na spravované disky v Azure proběhne úspěšně, můžete na webu Azure Portal přejít na **Podrobnosti objednávky** a poznamenat si skupiny prostředků zadané pro spravované disky.

        ![Identifikace skupin prostředků spravovaných disků](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Přejděte do skupiny prostředků, kterou jste si poznamenali, a vyhledejte své spravované disky.

        ![Spravovaný disk připojený ke skupinám prostředků](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Pokud kopírujete VHDX nebo dynamický nebo rozdílový virtuální pevný disk, VHDX/VHD se nahraje do přípravného účtu úložiště jako objekt blob stránky, ale převod virtuálního pevného disku na spravovaný disk selže. Přejděte do svého přípravného **účtu úložiště do části Objekty blob** a vyberte odpovídající kontejner – StandardSSD, Standard HDD nebo PremiumSSD. Virtuální pevné disky se nahrají do přípravného účtu úložiště jako objekty blob stránky.

::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

::: zone-end

## <a name="erasure-of-data-from-data-box"></a>Vymazání dat z Data Boxu
 
Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone target="docs"

## <a name="next-steps"></a>Další kroky

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

::: zone-end


