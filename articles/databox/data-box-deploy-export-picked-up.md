---
title: Kurz pro dodávání Azure Data Box zpět v pořadí exportu | Microsoft Docs
description: Naučte se, jak dodávat Azure Data Box do Microsoftu po dokončení objednávky exportu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 7023d29bcb559f4edf11b374b9bfb959e968f626
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86210388"
---
# <a name="tutorial-return-azure-data-box-preview"></a>Kurz: návratová Azure Data Box (Preview)


V tomto kurzu se dozvíte, jak vrátit Azure Data Box a když se zařízení obdrží v datech Azure, vymaže data.

V tomto kurzu se seznámíte například s následujícími tématy:

> [!div class="checklist"]
> * Předpoklady
> * Příprava k odeslání
> * Odeslání Data Boxu do Microsoftu
> * Vymazání dat z Data Boxu

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

- Dokončili jste [kurz: kopírování dat z Azure Data box ](data-box-deploy-export-copy-data.md). 
- Dokončily se úlohy kopírování. Pokud probíhají úlohy kopírování, nemůže proběhnout příprava k odeslání.

## <a name="prepare-to-ship"></a>Příprava k odeslání

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

Další kroky závisí na tom, kam zařízení vracíte.

## <a name="ship-data-box-back"></a>Vrácení Data Boxu

Zajistěte, aby byla kopie dat ze zařízení dokončena a **Příprava k odeslání** spuštění proběhlo úspěšně. Postup se liší v závislosti na oblasti, kam zařízení odesíláte.

## <a name="in-us-canada-europe"></a>[V USA, Kanadě a Evropě](#tab/in-us-canada-europe)

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


## <a name="in-australia"></a>[V Austrálii](#tab/in-australia)

Datacentra Azure v Austrálii mají další bezpečnostní oznámení. Všechny příchozí zásilky musí mít předchozí oznámení. Při odesílání v Austrálii proveďte následující kroky.


1. Ponechejte si původní obal, ve které jste zařízení obdrželi, pro zpáteční zásilku.
2. Ujistěte se, že se dokončilo kopírování dat do zařízení a úspěšně proběhla **příprava k odeslání**.
3. Vypněte zařízení a odpojte kabely.
4. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení.
5. Zarezervujte si vyzvednutí online pomocí [odkazu DHL](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

## <a name="in-japan"></a>[V Japonsku](#tab/in-japan) 

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


## <a name="in-singapore"></a>[V Singapuru](#tab/in-singapore) 

1. Ponechejte si původní obal, ve které jste zařízení obdrželi, pro zpáteční zásilku.
2. Poznamenejte si sledovací číslo (je uvedené jako referenční číslo na stránce Připravit k odeslání v místním webovém uživatelském rozhraní Data Boxu). Je k dispozici po úspěšném dokončení přípravy k odeslání. Stáhněte si expediční štítek z této stránky a nalepte ho na obal. 
3. Vypněte zařízení a odpojte kabely.
4. Smotejte napájecí kabel, který jste dostali se zařízením, a bezpečně ho umístěte do zadní části zařízení. 
5. Oddělení péče o zákazníky společnosti SingPost odešlete následující e-mailovou šablonu, ve které uvedete sledovací číslo.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pick-up - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > Žádosti o rezervaci přijaté v pracovní den:
   > - Před 15. hodinou: Vyzvednutí proběhne následující pracovní den od 9:00 do 13:00.
   > - Po 15. hodině: Vyzvednutí proběhne následující den mezi 14:00 a 18:00.  


## <a name="self-managed"></a>[V případě svépomoci](#tab/in-selfmanaged) 

Pokud používáte Data Box v Japonsku, Singapuru, Koreji a Západní Evropě a při vytváření objednávky jste vybrali možnost Expedice řízená svépomocí, postupujte podle těchto pokynů. 

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
> * Předpoklady
> * Příprava k odeslání
> * Odeslání Data Boxu do Microsoftu
> * Vymazání dat z Data Boxu

Přejděte k dalšímu článku, kde se dozvíte, jak spravovat Data Box.

> [!div class="nextstepaction"]
> [Správa Data Box přes Azure Portal](./data-box-portal-admin.md)


