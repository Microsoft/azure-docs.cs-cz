---
title: Soukromé skutkové prodejné a plané plány | Azure Marketplace
description: Jak používat soukromé skum pro správu dostupnosti nabídky.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280384"
---
<a name="private-skus-and-plans"></a>Soukromé skum a plány
============

Soukromé skum umožňují omezit dostupnost skum pro konkrétní zákazníky. Když je skladová položka označena jako soukromá, není dostupná v žádném veřejném katalogu, včetně [Azure Marketplace](https://azuremarketplace.microsoft.com) a [portálu Azure](https://portal.azure.com). Na webu Azure Portal ho můžou vidět jenom zákazníci s přístupem ke skladové jednotce. Kromě toho by také byli vyzváni, aby měli přístup k soukromým nabídkám.

>[!NOTE]
>Soukromé skladové položky musí mít nové jedinečné skladové položky nebo id plánu, aby se zabránilo konfliktu s veřejnými skladovými položkami.

Soukromé skum můžete použít ke zpracování následujících scénářů:

1.  Publikujte software, který chcete veřejně zpřístupnit pouze konkrétním zákazníkům a nikoli veřejně dostupný.
2.  Publikujte varianty veřejného softwaru za přizpůsobenou cenu pro konkrétní zákazníky.
3.  Publikujte varianty veřejného softwaru s přizpůsobeným popisem a podmínkami (prostřednictvím nové nabídky).

Pokud chcete pouze změnit cenu, můžete znovu použít disky z jiné skladové položky ve stejné nabídce. U soukromých sku, není třeba znovu odesílat disky napříč sku.

<a name="mark-a-sku-private"></a>Označit skladovou položku jako soukromou
---------------------

Chcete-li označit skladovou položku jako soukromou, přepněte možnost s dotazem, zda je skladová položka soukromá:

![Označení skladové položky jako soukromé](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Disky můžete znovu použít v jiné skladové jednotce a upravit ceny nebo popis. Chcete-li disky znovu použít, vyberte **možnost Ano** jako odpověď na výzvu "Má tato skladová položka znovu použít bitové kopie z veřejné skladové položky".

Pokud je skladová položka označena jako soukromá a nabídka má další skladové položky s opakovaně použitelnými disky, je nutné označit, že skladová položka opakovaně používá disky z jiné skladové položky. Je také nutné zadat cílovou skupinu pro privátní skladovou položku.

>[!NOTE]
>Po publikování nelze veřejnou skladovou položku vytvořit jako soukromou.

<a name="select-an-image"></a>Výběr obrázku
------------------

Můžete zadat nové disky pro soukromou skladovou položku nebo znovu použít stejné disky, které již byly k dispozici v jiné skladové jednotce, a pouze upravit ceny nebo popis. Chcete-li disky znovu použít, vyberte **možnost Ano** jako odpověď na výzvu "Má tato skladová položka znovu použít bitovou kopii z veřejné skladové položky".

![Označení opakovaného použití obrázku](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Po potvrzení, že skladová položka opakovaně používá obrazy, vyberte zdrojnebo *základní* skladovou položku pro obrazy:

![Výběr obrázku](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Při publikování nabídky by byly obrázky z vybrané skladové položky zpřístupněny pod privátním ID skladové položky s vlastními sazbami/podmínkami. Soukromá skladová položka by byla viditelná pouze cílovému publiku.

Pro aktualizace bitových obrázků budete muset pouze aktualizovat základní sku image. Na pozadí bude automaticky aktualizován také obrázek soukromé skladové položky. Podobně pokud odstraníte obrázek ze základní skladové položky, obraz bude také odebrán ze soukromé skladové položky.

<a name="restricting-the-audience"></a>Omezení publika
------------------------

Soukromé nabídky mohou najít a nasadit pouze cíloví uživatelé.
V současné době podporujeme cílení na uživatele pomocí ID předplatného.

Tato předplatná lze zadat prostřednictvím manuálového vstupního formuláře **až pro 10 odběrů**nebo nahráním souboru CSV, který umožňuje **až 20 000 předplatných**.

Ruční vstup pro omezené publikum:

![Ruční omezení cílové skupiny](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Nahrávání csv pro omezené publikum:

![Omezení publika pomocí csv](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Ukázkový obsah souboru CSV:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Když přepnete z ručního vstupu na zobrazení nahrávání CSV nebo z CSV na ruční zadávání, starý seznam ID předplatného s přístupem k skladové jednotce není zachován. Zobrazí se upozornění a seznam je přepsán pouze při uložení nabídky.

<a name="managing-private-audiences"></a>Správa soukromých publik
-------------------------

**Chcete-li aktualizovat okruh uživatelů bez opětovného publikování celé nabídky, provedete požadované změny publika (pomocí rozhraní uI nebo rozhraní API) a poté zahájíte akci Synchronizovat soukromé okruhy uživatelů.**

Pokud je váš okruh uživatelů 10 nebo méně předplatných, můžete ho spravovat zcela pomocí ui CPP.

Pokud je váš okruh uživatelů více než 10 předplatných, můžete ho spravovat pomocí souboru CSV, který můžete buď nahrát do rozhraní CPP, nebo pomocí rozhraní API.

Pokud používáte rozhraní API a nechcete udržovat soubor CSV, můžete spravovat okruh uživatelů přímo pomocí rozhraní API podle níže uvedených pokynů.

> [!NOTE]
> K přidání cílové skupiny do soukromé nabídky použijte ID předplatného Azure (plány a sloky) nebo ID klienta (jenom plány).

###  <a name="managing-subscriptions-with-the-api"></a>Správa předplatných pomocí rozhraní API

Pomocí rozhraní API můžete buď nahrát csv nebo spravovat okruh uživatelů přímo (bez použití CSV). Obecně platí, že stačí načíst nabídku, `restrictedAudience` aktualizovat objekt a poté odeslat tyto změny zpět do nabídky, abyste mohli přidat nebo odebrat členy publika.

Seznam publika můžete programově aktualizovat takto:

1. Načtení údajů o [nabídce:](cloud-partner-portal-api-retrieve-specific-offer.md)

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Pomocí tohoto dotazu JPath vyhledejte objekty cílové skupiny s omezeným přístupem v každé skladové jednotce nabídky:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Aktualizujte objekty omezeného okruhu uživatelů pro vaši nabídku.

    **Pokud jste původně nahráli seznam odběrů pro svou soukromou nabídku ze souboru CSV:**

    Objekty *s omezeným přístupem bude* vypadat takto.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Pro každý objekt s omezeným přístupem:

    a. Stáhněte si `restrictedAudience.uploadedCsvUri`obsah . Obsah je jednoduše soubor CSV se záhlavími. Například:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Podle potřeby přidejte nebo odstraňte odběry ve staženém souboru CSV.

    c. Nahrajte aktualizovaný soubor CSV do umístění, jako je [azure blob storage](../../storage/blobs/storage-blobs-overview.md) nebo [OneDrive](https://onedrive.live.com), a vytvořte odkaz jen pro čtení k souboru. To to bude váš nový *SasUrl*.

    d. Aktualizujte `restrictedAudience.uploadedCsvUri` klíč pomocí nového *sasurl*.

    **Pokud jste ručně zadali původní seznam předplatných pro svou soukromou nabídku z portálu partnerů cloudu:**

    Objekty *restrictedAudience* budou vypadat nějak takto:

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. Pro každý objekt cílové skupiny s `restrictedAudience.manualEntries` omezeným přístupem přidejte nebo odstraňte položky v seznamu podle potřeby.

4. Po dokončení aktualizace všech *objektů restrictedAudience* pro každou skladovou položku vaší soukromé nabídky [aktualizujte nabídku](cloud-partner-portal-api-creating-offer.md):

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Díky tomu je nyní v platnosti váš aktualizovaný seznam publika.

<a name="previewing-private-offers"></a>Náhled soukromých nabídek
-------------------------

Během kroku náhledu nebo přípravy budou mít přístup k skladové jednotce pouze předplatná na úrovni nabídky. V této fázi testování můžete zobrazit náhled nabídky tak, jak by se zdálo vašim cílovým zákazníkům.

Předplatná náhledu na úrovni nabídky pro přístup k fázovaným nabídkám:

![Náhled ID předplatného](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Po živé nabídce budou moci zobrazit a nasadit soukromou skladovou položku pouze předplatná s omezeným přístupem (zadaná ručním zadáváním nebo csv). Doporučujeme **vždy zahrnout vlastní předplatná v omezeném okruhu uživatelů** pro soukromé skladové položky pro účely ověření.

>[!NOTE]
>Pro účely ladění budou mít k těmto soukromým nabídkám také přístup k těmto soukromým nabídkám týmy podpory a inženýrských sítí společnosti Microsoft.
