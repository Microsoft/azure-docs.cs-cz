---
title: Kurz vrácení Azure Data Boxu| Microsoft Docs
description: V tomto kurzu se naučíte, jak vracet Azure Data Box, včetně přípravy na odeslání, expedice Data Boxu Heavy, ověření nahrání dat a mazání dat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 72575725c0924b8ec5f9466f4ffbba86bf071db4
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631527"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Kurz: Vrácení Azure Data Boxu a ověření nahrání dat do Azure

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Vrácení Data Boxu a ověření nahrání dat do Azure

::: zone-end

::: zone target="docs"

Tento kurz popisuje, jak vrátit Azure Data Box a ověřit nahrání dat do Azure.

V tomto kurzu se seznámíte například s následujícími tématy:

> [!div class="checklist"]
>
> * Požadavky
> * Příprava k odeslání
> * Odeslání Data Boxu do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Boxu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

* Dokončili jste [Kurz: Kopírování dat do Azure Data Boxu a jejich ověření](data-box-deploy-copy-data.md).
* Úlohy kopírování jsou kompletní a na stránce **Připojit a kopírovat** nejsou žádné chyby. Příprava k odeslání nemůže proběhnout, pokud jsou spuštěné úlohy kopírování nebo pokud na stránce **Připojit a kopírovat** jsou nějaké chyby.

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

## <a name="us-canada-europe"></a>[USA, Kanada, Evropa](#tab/in-us-canada-europe)

Pokud zařízení vracíte v USA, Kanadě nebo Evropě, proveďte následující kroky.

1. Ujistěte se, že je zařízení vypnuté a nejsou k němu zapojené žádné kabely. 
2. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
3. Ujistěte se, že se na displeji s elektronickým inkoustem zobrazuje expediční štítek, a naplánujte vyzvednutí přepravcem. Pokud se štítek poškodí, ztratí nebo nezobrazí na displeji s elektronickým inkoustem, obraťte se na podporu Microsoftu. Pokud vám to podpora navrhne, můžete pak na webu Azure Portal přejít do části **Přehled > Stáhnout expediční štítek**. Stáhněte si expediční štítek a připevněte ho na zařízení. 
4. Pokud zařízení vracíte, domluvte si vyzvednutí s UPS. Naplánování vyzvednutí:

    * Zavolejte do místní pobočky UPS (bezplatné číslo pro příslušnou zemi/oblast).
    * Během volání uveďte sledovací číslo zpětné zásilky, jak je uvedené na displeji s elektronickým inkoustem nebo na vytištěném štítku.
    * Pokud sledovací číslo neuvedete, bude společnost UPS vyžadovat, abyste při vyzvednutí zaplatili dodatečné poplatky.

    Místo naplánování vyzvednutí můžete také Data Box dovézt na nejbližší sběrné místo.
4. Jakmile přepravce vyzvedne a naskenuje Data Box, stav objednávky na portálu se změní na **Vyzvednuto**. Zobrazí se také ID sledování.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Vymazání dat z Data Boxu

Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="australia"></a>[Austrálie](#tab/in-australia)

Datacentra Azure v Austrálii mají další bezpečnostní oznámení. Všechny příchozí zásilky musí mít předchozí oznámení. Při odesílání v Austrálii proveďte následující kroky.

1. Ponechejte si původní obal, ve které jste zařízení obdrželi, pro zpáteční zásilku.
2. Ujistěte se, že se dokončilo kopírování dat do zařízení a úspěšně proběhla **příprava k odeslání**.
3. Vypněte zařízení a odpojte kabely.
4. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
5. Zarezervujte si vyzvednutí online pomocí [odkazu na DHL](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Vymazání dat z Data Boxu

Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="japan"></a>[Japonsko](#tab/in-japan)

1. Ponechejte si původní obal, ve které jste zařízení obdrželi, pro zpáteční zásilku.
2. Vypněte zařízení a odpojte kabely.
3. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
4. Vyplňte název vaší společnosti a adresu do nákladního listu do části informací o odesílateli.
5. Odešlete e-mail společnosti Quantium Solutions pomocí následující e-mailové šablony.

    * V případě, že nákladní list japonské pošty Chakubarai nebyl obsažen nebo chybí, uveďte to v e-mailu. Společnost Quantium Solutions Japonsko požádá japonskou poštu, aby nákladní list při vyzvednutí přinesla.
    * Pokud máte více objednávek, e-mailem zajistěte individuální vyzvednutí.

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

6. Po rezervaci vyzvednutí obdržíte e-mailové potvrzení od společnosti Quantium Solutions. E-mailové potvrzení obsahuje také informace o nákladním listu Chakubarai.

V případě potřeby můžete kontaktovat podporu společnosti Quantium Solutions (v japonštině) následujícími způsoby: 

* E-mailem：Customerservice.JP@quantiumsolutions.com 
* Telefonicky: 03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Vymazání dat z Data Boxu
 
Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="singapore"></a>[Singapur](#tab/in-singapore)

1. Ponechejte si původní obal, ve které jste zařízení obdrželi, pro zpáteční zásilku.
2. Poznamenejte si sledovací číslo (je uvedené jako referenční číslo na stránce Připravit k odeslání v místním webovém uživatelském rozhraní Data Boxu). Je k dispozici po úspěšném dokončení přípravy k odeslání. Stáhněte si expediční štítek z této stránky a nalepte ho na obal.
3. Vypněte zařízení a odpojte kabely.
4. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení. 
5. Oddělení péče o zákazníky společnosti SingPost odešlete následující e-mailovou šablonu, ve které uvedete sledovací číslo.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > Žádosti o rezervaci přijaté v pracovní den:
   > * Před 15. hodinou: Vyzvednutí proběhne následující pracovní den od 9:00 do 13:00.
   > * Po 15. hodině: Vyzvednutí proběhne následující den mezi 14:00 a 18:00.  

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Vymazání dat z Data Boxu

Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="south-africa"></a>[Jižní Afrika](#tab/in-sa)

1. Pro zpáteční zásilku zabalte zařízení do původní krabice.
2. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
3. Pokud chcete objednat vyzvednutí společností DHL:

    * Zavolejte na kontaktní centrum služeb pro zákazníky na čísle +27(0) 11 9213600, vyberte možnost 1 a potom uveďte číslo nákladového listu.
    * Při plánování vyzvednutí volejte do 14:00.
    * Pomocí následující šablony odešlete e-mail na adresu [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com):

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * Další možností je donést balíček na nejbližší sběrné místo DHL.

4. Pokud dojde k nějakým problémům, pošlete e-mail na adresu [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) a uveďte podrobnosti o problému, ke kterému došlo. Do řádku Předmět: nezapomeňte uvést číslo nákladového listu. Můžete také zavolat na číslo +27(0)119213902.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Vymazání dat z Data Boxu

Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="hong-kong"></a>[Hongkong](#tab/in-hk)

1. Pro zpáteční zásilku zabalte zařízení do původní krabice.
2. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
3. Během pracovní doby (pondělí až pátek, 9:00 až 18:00) zavolejte na zákaznickou linku společnosti **Quantium Solutions** na čísle **(852) 2318 1213**.  
4. Při žádosti o vyzvednutí uveďte, že se jedná o vyzvednutí pro Microsoft Azure, referenční číslo a také sledovací číslo (nad čárovým kódem) ze zpátečního expedičního štítku.
5. Obdržíte ústní potvrzení naplánovaného vyzvednutí. Pokud se kurýr k vyzvednutí nedostaví, zavolejte zákaznickou linku společnosti Quantium Solutions a domluvte se na náhradním řešení.
6. Při rezervaci vyzvednutí u společnosti Quantium zašlete oddělení [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) potvrzení pomocí následující šablony:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

Pokud byste narazili na nějaké potíže, odešlete oddělení Data Box Operations Asia e-mail ([adbo@microsoft.com](mailto:adbo@microsoft.com)) a v záhlaví uveďte název úlohy a potíže, ke kterým došlo.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Vymazání dat z Data Boxu
 
Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the Prepare to Ship page of the Data Box local web UI). This is available after the prepare to ship step successfully completes. Download the shipping label from this page and paste on the packing box. 
3. Power off the device and remove the cables.
4. Spool and securely place the power cord that was provided with the device in the back of the device. 

Request pickup  
If consignment note is present:  

1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number to arrange for a collection.
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.  
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.  
4. You will receive an email confirmation for the pickup schedule.  

Exception process
If the consignment note is not present:
1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number).
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.
4. You get a verbal confirmation if request is made via telephone.  
::: zone target="chromeless"

## Verify data upload to Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## Erasure of data from Data Box
 
Once the upload to Azure is complete, the Data Box erases the data on its disks as per the [NIST SP 800-88 Revision 1 guidelines](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end
-->

## <a name="self-managed"></a>[V případě svépomoci](#tab/in-selfmanaged)

Pokud používáte Data Box ve státní správě USA, v Japonsku, Singapuru, Indii, Jihoafrické republice, Koreji nebo Západní Evropě a při vytváření objednávky jste vybrali možnost samoobslužné expedice, postupujte podle těchto pokynů. 

1. Po úspěšném dokončení tohoto kroku si poznamenejte autorizační kód uvedený na stránce Připravit k odeslání v místním webovém uživatelském rozhraní Data Boxu.
2. Vypněte zařízení a odpojte kabely. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
3. Až budete připraveni toto zařízení vrátit, použijte následující šablonu a odešlete e-mail provoznímu týmu služby Azure Data Box.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

::: zone target="chromeless"

---

## <a name="verify-data-upload-to-azure"></a>Ověření nahrání dat do Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Vymazání dat z Data Boxu
 
Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

