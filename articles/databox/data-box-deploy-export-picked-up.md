---
title: Kurz pro dodávání Azure Data Box zpět v pořadí exportu | Microsoft Docs
description: Naučte se, jak dodávat Azure Data Box do Microsoftu po dokončení objednávky exportu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/21/2020
ms.author: alkohli
ms.openlocfilehash: 04e4394e6a439c923558ef90e13c14c0adaa4020
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142107"
---
# <a name="tutorial-return-azure-data-box-preview"></a>Kurz: návratová Azure Data Box (Preview)

V tomto kurzu se dozvíte, jak vrátit Azure Data Box a když se zařízení obdrží v datech Azure, vymaže data.

V tomto kurzu se seznámíte například s následujícími tématy:

> [!div class="checklist"]
>
> * Požadavky
> * Příprava k odeslání
> * Odeslání Data Boxu do Microsoftu
> * Vymazání dat z Data Boxu

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

* Dokončili jste [kurz: kopírování dat z Azure Data box](data-box-deploy-export-copy-data.md).
* Dokončily se úlohy kopírování. Pokud probíhají úlohy kopírování, nemůže proběhnout příprava k odeslání.

## <a name="prepare-to-ship"></a>Příprava k odeslání

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

Další kroky závisí na tom, kam zařízení vracíte.

## <a name="ship-data-box-back"></a>Vrácení Data Boxu

Zajistěte, aby byla kopie dat ze zařízení dokončena a **Příprava k odeslání** spuštění proběhlo úspěšně. Postup se liší v závislosti na oblasti, kam zařízení odesíláte.

## <a name="us-canada-europe"></a>[USA, Kanada, Evropa](#tab/in-us-canada-europe)

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

## <a name="australia"></a>[Austrálie](#tab/in-australia)

Datacentra Azure v Austrálii mají další bezpečnostní oznámení. Všechny příchozí zásilky musí mít předchozí oznámení. Při odesílání v Austrálii proveďte následující kroky.

1. Ponechejte si původní obal, ve které jste zařízení obdrželi, pro zpáteční zásilku.
2. Ujistěte se, že se dokončilo kopírování dat do zařízení a úspěšně proběhla **příprava k odeslání**.
3. Vypněte zařízení a odpojte kabely.
4. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
5. Zarezervujte si vyzvednutí online pomocí [odkazu na DHL](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

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
   >
   > * Před 15. hodinou: Vyzvednutí proběhne následující pracovní den od 9:00 do 13:00.
   > * Po 15. hodině: Vyzvednutí proběhne následující den mezi 14:00 a 18:00.  

## <a name="south-africa"></a>[Jižní Afrika](#tab/in-sa)

1. Uchová původní pole použité k balení zařízení pro odeslání zpětné zásilky.
2. Poznamenejte si referenční číslo (číslo nákladového čísla) zobrazené v místním webovém uživatelském rozhraní zařízení. Toto číslo se zobrazí po úspěšném **spuštění Příprava k odeslání** .
3. Stáhněte a tiskněte expediční štítek, který je k dispozici v místním webovém uživatelském rozhraní zařízení, a přihlaste se k balíčku dodávek.
4. Pokud chcete vyrezervovat vyzvednutí pomocí DHL, vyberte jednu z následujících možností:

    * Zavolejte kontaktní centrum služby zákazníkům před 2:00. odp. na **+ 27 (0) 11 9213600**vyberte možnost 1 a pak zadejte číslo nákladového pole.
    * Pomocí následující šablony odešlete e-mail na adresu [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com):

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * Další možností je donést balíček na nejbližší sběrné místo DHL.

5. Pokud dojde k nějakým problémům, pošlete e-mail na adresu [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) a uveďte podrobnosti o problému, ke kterému došlo. Do řádku Předmět: nezapomeňte uvést číslo nákladového listu. Můžete také zavolat na číslo +27(0)119213902.

## <a name="hong-kong"></a>[Hongkong](#tab/in-hk)

1. Pro zpáteční zásilku zabalte zařízení do původní krabice.
2. Poznamenejte si referenční číslo (sledovací číslo pro zpětnou expedici) zobrazené v místním webovém uživatelském rozhraní zařízení. Toto číslo se zobrazí po úspěšném **spuštění Příprava k odeslání** .
3. Stáhněte a tiskněte expediční štítek, který je k dispozici v místním webovém uživatelském rozhraní zařízení, a přihlaste se k balíčku dodávek.
4. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
5. Během pracovní doby (pondělí až pátek, 9:00 až 18:00) zavolejte na zákaznickou linku společnosti **Quantium Solutions** na čísle **(852) 2318 1213**.  
6. Při žádosti o vyzvednutí uveďte, že se jedná o vyzvednutí pro Microsoft Azure, referenční číslo a také sledovací číslo (nad čárovým kódem) ze zpátečního expedičního štítku.
7. Obdržíte ústní potvrzení naplánovaného vyzvednutí. Pokud se kurýr k vyzvednutí nedostaví, zavolejte zákaznickou linku společnosti Quantium Solutions a domluvte se na náhradním řešení.
8. Při rezervaci vyzvednutí u společnosti Quantium zašlete oddělení [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) potvrzení pomocí následující šablony:

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

Pokud se vyskytnou nějaké problémy, pošlete e-mail Data Box operací Asie [adbo@microsoft.com](mailto:adbo@microsoft.com) a poskytněte název úlohy v hlavičce předmětu a nastaly potíže.

## <a name="self-managed"></a>[V případě svépomoci](#tab/in-selfmanaged)

Pokud používáte Data Box v Japonsku, Singapuru, Indii, Jihoafrické republice, Koreji nebo Západní Evropě a při vytváření objednávky jste vybrali možnost samoobslužné expedice, postupujte podle těchto pokynů.

1. Po úspěšném dokončení tohoto kroku si poznamenejte autorizační kód uvedený na stránce Připravit k odeslání v místním webovém uživatelském rozhraní Data Boxu.
2. Vypněte zařízení a odpojte kabely. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
3. Až budete připraveni toto zařízení vrátit, použijte následující šablonu a odešlete e-mail provoznímu týmu služby Azure Data Box.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

---

## <a name="erasure-of-data-from-data-box"></a>Vymazání dat z Data Boxu
 
Jakmile zařízení dosáhne datacentra Azure, Data Box vymaže data na svých discích podle [pokynů 1 pro aktualizaci SP 800-88 revize 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s tématy, jako například:

> [!div class="checklist"]
> * Požadavky
> * Příprava k odeslání
> * Odeslání Data Boxu do Microsoftu
> * Vymazání dat z Data Boxu

Přejděte k dalšímu článku, kde se dozvíte, jak spravovat Data Box.

> [!div class="nextstepaction"]
> [Správa Data Box přes Azure Portal](./data-box-portal-admin.md)


