---
title: Kurz vracení Azure Data Box Disku| Microsoft Docs
description: V tomto kurzu se naučíte, jak vrátit Azure Data Box Disk. Pokyny pro vyzvednutí závisí na tom, kam toto zařízení vracíte.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 11/20/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: f1d667f55a8be1b701fda6240895dd8f96315cff
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986313"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Kurz: Vrácení Azure Data Box Disku

Tento kurz popisuje, jak vrátit Azure Data Box Disk. Pokyny pro vyzvednutí závisí na tom, kam toto zařízení vracíte.

V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
>
> * Odeslání Data Box Disku do Microsoftu

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste dokončili [Kurz: Kopírování dat na Azure Data Box Disk a jejich ověření](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Odeslání Data Box Disku zpátky

::: zone-end

::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Vrácení Azure Data Box Disku

::: zone-end

1. Po dokončení ověření dat odpojte disky. Odeberte propojovací kabely.
2. Zabalte všechny disky a propojovací kabely do bublinkové fólie a vložte je do přepravního boxu. Pokud chybí příslušenství, můžou se účtovat poplatky.
    - Znovu použijte balení z prvotní dodávky.  
    - Doporučujeme zabalit disky pomocí odolné bublinkové fólie.
    - Ujistěte se, že vše dobře sedí, aby se zamezilo jakýmkoli posunům v krabici.

Další kroky závisí na tom, kam zařízení vracíte. Pokyny se liší pro USA a Kanadu, Evropskou unii (EU), Austrálii nebo země/oblasti v Asii.

### <a name="us-or-canada"></a>[USA nebo Kanada](#tab/in-us-or-canada)

Při vrácení zařízení v USA nebo Kanadě proveďte následující kroky.

1. Použijte zpáteční expediční štítek v průhledném plastovém obalu připevněném k tomuto boxu. Pokud je popisek poškozený nebo se ztratil:
    - Přejděte na **Přehled > Stáhnout expediční štítek** a stáhněte zpáteční expediční štítek.
    - Připevněte štítek na zařízení.

2. Zapečeťte přepravní box a zkontrolujte, že zpáteční expediční štítek je vidět.
3. Naplánujte vyzvednutí službou UPS. Naplánování vyzvednutí:

    - Zavolejte do místní pobočky UPS (bezplatné číslo pro příslušnou zemi/oblast).
    - Během volání uveďte sledovací číslo zpětné zásilky, jak je uvedené na vytištěném štítku.
    - Pokud sledovací číslo neuvedete, bude společnost UPS vyžadovat, abyste při vyzvednutí zaplatili dodatečné poplatky.
    - Místo naplánování vyzvednutí můžete také Data Box Disk dovézt na nejbližší sběrné místo.


### <a name="europe-or-uk"></a>[Evropa nebo Velká Británie](#tab/in-europe-or-uk)

Při vracení zařízení v Evropě nebo ve Velké Británii proveďte následující kroky.

1. Použijte zpáteční expediční štítek v průhledném plastovém obalu připevněném k tomuto boxu. Pokud je popisek poškozený nebo se ztratil:
    - Přejděte na **Přehled > Stáhnout expediční štítek** a stáhněte zpáteční expediční štítek.
    - Připevněte štítek na zařízení.

2. Zapečeťte přepravní krabici a zkontrolujte, že je vidět zpáteční expediční štítek.
3. Přejděte na web DHL Express pro vaši zemi/oblast a vyberte **Naplánovat vyzvednutí**. V části **Potřebujete expediční štítek?** vyberte **Ne** > **Mám číslo nákladního listu DHL**.
4. Zadejte číslo nákladního listu a kliknutím na tlačítko **Naplánovat vyzvednutí** zařiďte vyzvednutí.

### <a name="australia"></a>[Austrálie](#tab/in-australia)

Datacentra Azure v Austrálii mají další bezpečnostní oznámení. Všechny příchozí zásilky musí mít předchozí oznámení. Při vyzvednutí v Austrálii proveďte následující kroky.

1. Použijte poskytnutý zpáteční expediční štítek a ujistěte se, že je na něm uvedený kód TAU (referenční číslo). Pokud zpáteční expediční štítek chybí nebo máte nějaké jiné problémy, pošlete e-mail oddělení [Data Box Asia Operations](mailto:adbo@microsoft.com). Zadejte v hlavičce název objednávky a uveďte podrobnosti o problému, se kterým se potýkáte.
2. Připevněte štítek na krabici.
3. Zarezervujte si vyzvednutí online pomocí odkazu https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference.

### <a name="japan"></a>[Japonsko](#tab/in-japan)

1. Vyplňte název vaší společnosti a adresu do nákladního listu do části informací o odesílateli.
2. Odešlete e-mail společnosti Quantium Solutions pomocí následující e-mailové šablony.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00
        b. 13：00-15：00
        c. 15：00-17：00
        d. 17：00-19：00
    ```
    - **Pokud má vyzvednutí proběhnout v Ósace**, upravte předmět v e-mailové šabloně na `Pickup request for Microsoft Azure OSA`.
    - V případě, že nákladní list japonské pošty Chakubarai nebyl obsažen nebo chybí, uveďte to v e-mailu. Společnost Quantium Solutions Japonsko požádá japonskou poštu, aby nákladní list při vyzvednutí přinesla.
    - Pokud máte více objednávek, e-mailem zajistěte individuální vyzvednutí.

3. Po rezervaci vyzvednutí obdržíte e-mailové potvrzení od společnosti Quantium Solutions. E-mailové potvrzení obsahuje také informace o nákladním listu Chakubarai.

V případě potřeby můžete kontaktovat podporu společnosti Quantium Solutions (v japonštině) následujícími způsoby: 

- E-mail：[Customerservice.JP@quantiumsolutions.com](mailto:Customerservice.JP@quantiumsolutions.com)
- Telefonicky: 03-5755-0150 

### <a name="korea"></a>[Jižní Korea](#tab/in-korea)

1. Nezapomeňte zahrnout zpáteční nákladní list.
2. Pokud je nákladní list k dispozici, vyžádejte si vyzvednutí takto:
    1. Zavolejte na zákaznickou linku společnosti *Quantium Solutions International* na čísle 070-8231-1418 během pracovní doby (od 10:00 do 17:00, od pondělí do pátku). Při žádosti o vyzvednutí uveďte, že se jedná o *vyzvednutí pro Microsoft Azure*, a číslo žádosti o službu.  
    2. Pokud je linka obsazená, pošlete e-mail na adresu `microsoft@rocketparcel.com` s předmětem *Microsoft Azure Pickup* a s číslem žádosti o službu jako referencí.
    3. Pokud se kurýr k vyzvednutí nedostaví, zavolejte zákaznickou linku společnosti *Quantium Solutions International* a domluvte se na náhradním řešení.
    4. Obdržíte e-mailové potvrzení naplánovaného vyzvednutí.
3. Tento krok proveďte pouze v případě, že nákladní list není k dispozici. Vyžádání si vyzvednutí:
    1. Zavolejte na zákaznickou linku společnosti *Quantium Solutions International* na čísle 070-8231-1418 během pracovní doby (od 10:00 do 17:00, od pondělí do pátku). Při žádosti o vyzvednutí uveďte, že se jedná o *vyzvednutí pro Microsoft Azure*, a číslo žádosti o službu. Uveďte, že k zajištění vyzvednutí potřebujete nový nákladní list. Zadejte odesílatele (zákazník), informace o příjemci (datacentrum Azure) a referenční číslo (číslo žádosti o službu). 
    2. Pokud je linka obsazená, pošlete e-mail na adresu `microsoft@rocketparcel.com` s předmětem *Microsoft Azure Pickup* a s číslem žádosti o službu jako referencí.
    3. Pokud se kurýr k vyzvednutí nedostaví, zavolejte zákaznickou linku společnosti *Quantium Solutions International* a domluvte se na náhradním řešení.
    4. V případě, že požadavek provádíte telefonicky, dostane se vám ústního potvrzení.

### <a name="singapore"></a>[Singapur](#tab/in-singapore)

1. Vytiskněte expediční štítek a připevněte ho na krabici. Pokud je popisek poškozený nebo se ztratil:
    - Přejděte na **Přehled > Stáhnout expediční štítek** a získejte zpáteční expediční štítek.
    - Připevněte štítek na zařízení. Ujistěte se, že je štítek vidět.

2. Pokud si chcete vyžádat vyzvednutí, pomocí následující šablony napište na oddělení péče o zákazníky společnosti SingPost e-mail, ve kterém na řádku předmětu uvedete sledovací číslo (sledovací číslo najdete na zpátečním štítku, který je součástí doručeného balíčku).

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - XZ00001234567
    Body:
     a.    Requestor name
     b.    Requestor contact number
     c.    Requestor collection address
     d.    Preferred collection date
    ```

   > [!NOTE]
   > Žádosti o rezervaci přijaté v pracovní den:
   >
   > * Před 15. hodinou: Vyzvednutí proběhne následující pracovní den od 9:00 do 13:00.
   > * Po 15. hodině: Vyzvednutí proběhne následující den mezi 14:00 a 18:00.

   V případě jakýchkoli potíží se laskavě obraťte na Data Box Operations Asia na adrese adbo@microsoft.com. V hlavičce uveďte název úlohy a problém, ke kterému došlo.

3. Předejte zásilku kurýrovi.

### <a name="south-africa"></a>[Jižní Afrika](#tab/in-sa)

Při vracení zařízení v Jihoafrické republice proveďte následující kroky.

1. Na krabici připevněte poskytnutý expediční štítek. Tento štítek obsahuje sledovací číslo. Pokud expediční štítek chybí, můžete si ho stáhnout pomocí příkazu **Přehled > Zobrazit expediční štítek**.

2. Zapečeťte přepravní box a zkontrolujte, že zpáteční expediční štítek je vidět.

3. Vyžádejte si návratový kód od provozního týmu služby Azure Data Box. Návratový kód je potřeba pro doručení balíčku zpátky do datacentra. Odešlete e-mail na adresu [adbops@microsoft.com](mailto:adbops@microsoft.com). Poznamenejte tento kód na expediční štítek vedle adresy pro vrácení, aby byl jasně viditelný.

4. Pomocí jedné z následujících metod si objednejte vyzvednutí společností DHL:
   * Přejděte na [DHL Express South Africa, **Schedule a Pickup**](https://mydhl.express.dhl/za/en/schedule-pickup.html#/schedule-pickup#label-reference) a objednejte si vyzvednutí online.
   * Pomocí následující šablony odešlete e-mail na adresu [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com):

     ```output
     To: Priority.Support@dhl.com
     Subject: Pickup request for Microsoft Azure
     Body: Need pick up for the below shipment
       *  DHL tracking number: (reference number/waybill number)
       *  Requested pickup date: yyyy/mm/dd;time:HH MM
       *  Shipper contact: (company name)
       *  Contact person: 
       *  Phone number: 
       *  Full physical address: 
       *  Item to be collected: Azure Dt
     ```

    * Balíček můžete předat i na nejbližší pobočce DHL.

5. Pokud dojde k nějakým problémům, pošlete e-mail na adresu [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) a uveďte podrobnosti o problému, ke kterému došlo. Do řádku Předmět: nezapomeňte uvést číslo nákladového listu. Můžete také zavolat na číslo +27(0)119213902.

### <a name="china"></a>[Čína](#tab/in-china)

Při vracení zařízení v Číně proveďte následující kroky.

1. Přiložte poskytnutý expediční štítek a upevněte ho na krabici. Tento štítek obsahuje sledovací číslo. Pokud expediční štítek chybí, můžete si ho stáhnout pomocí příkazu **Přehled > Zobrazit expediční štítek**.

2. Pomocí e-mailové šablony níže pošlete e-mail na oddělení FedEx Premier Customer Care a sdělte v něm sledovací číslo (referenční číslo zásilky), aby bylo možné zařídit vyzvednutí:  

   ```output
   To: ying.bao@fedex.com;739951@fedex.com
   Subject: Pickup request for Microsoft Azure : Order Name
   Body: Need pick up for the below shipment
   * FedEx tracking number (reference number)
   * Requested pickup date：yyyy/mm/dd; time: HH MM
   ```

3. Až se registrace vyzvednutí dokončí, dostanete od FedExu potvrzení e-mailem.  

4. Pokud dojde k nějakým problémům, pošlete prosím e-mail na adresu [DL-DC-SHA@oe.21vianet.com](mailto:DL-DC-SHA@oe.21vianet.com) a uveďte podrobnosti o problému, ke kterému došlo.

#### <a name="premier-customer-care-contact-information"></a>Kontaktní informace pro Premier Customer Care

<ins>Primární</ins>

| Kontaktní informace | Podrobnosti |
|---|---|
|Název:       | Bao Ying|
|Označení | Vedoucí zástupce pro OneCall |
|Telefon:      | 400.889.6066 linka 3693 |
|E-mail:     | [ying.bao@fedex.com](mailto:ying.bao@fedex.com) |

<ins>Backup</ins>

| Kontaktní informace | Podrobnosti |
|---|---|
|Název:       | He Xun|
|Označení | Vedoucí zástupce pro OneCall |
|Telefon:      | 400.889.6066 linka 3603 |
|E-mail:     | [739951@fedex.com](mailto:739951@fedex.com) |

### <a name="self-managed"></a>[V případě svépomoci](#tab/in-selfmanaged)

Pokud používáte Data Box Disk ve státní správě USA, v Japonsku, Singapuru, Koreji, Západní Evropě, Jižní Africe nebo Indii a při vytváření objednávky jste vybrali možnost samoobslužné expedice, postupujte podle těchto pokynů.

1. Na webu Azure Portal přejděte do okna **Přehled** pro vaši objednávku. Projděte si pokyny zobrazené po výběru **Naplánovat vyzvednutí**. Měl by se zobrazit autorizační kód, který se používá při předávání objednávky.

2. Až budete připraveni vrátit toto zařízení, použijte následující šablonu a odešlete e-mail provoznímu týmu služby Azure Data Box.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box Disk drop-off for order: 'orderName'
    Body:
     a. Order name
     b. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```
3. Provozní tým služby Azure Data Box se s vámi spojí a domluví předání v datacentru Azure.

---

::: zone target="docs"

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Box Diskem, například jste se naučili:

> [!div class="checklist"]
>
> * Odeslání Data Box Disku do Microsoftu

Přejděte k dalšímu postupu, kde se dozvíte, jak ověřit nahrání dat z Data Box Disku do účtu Azure Storage.

> [!div class="nextstepaction"]
> [Ověření nahrání dat z Azure Data Box Disku](./data-box-disk-deploy-upload-verify.md)

::: zone-end
