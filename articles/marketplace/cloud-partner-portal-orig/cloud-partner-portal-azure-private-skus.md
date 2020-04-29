---
title: Soukromé SKU a plány | Azure Marketplace
description: Jak používat soukromé SKU ke správě dostupnosti nabídky
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280384"
---
<a name="private-skus-and-plans"></a>Soukromé SKU a plány
============

Soukromé SKU umožňují omezit dostupnost SKU na konkrétní zákazníky. Pokud je SKU označená jako soukromá, není k dispozici v žádném veřejném katalogu, včetně [Azure Marketplace](https://azuremarketplace.microsoft.com) a [Azure Portal](https://portal.azure.com). Na Azure Portal uvidí jenom zákazníci, kteří mají přístup k této SKU. Kromě toho by se také zobrazila výzva k tomu, že mají přístup k soukromým nabídkám.

>[!NOTE]
>Aby nedocházelo ke konfliktu s vašimi veřejnými SKU, musí mít privátní skladové položky nové jedinečné ID SKU nebo plánu.

Soukromé SKU můžete použít k obsluze následujících scénářů:

1.  Publikování softwaru, který má být k dispozici pouze pro konkrétní zákazníky a není veřejně dostupný.
2.  Publikování variací veřejného softwaru za účelem vlastní ceny pro konkrétní zákazníky
3.  Publikujte variace veřejného softwaru s přizpůsobeným popisem a podmínkami (prostřednictvím nové nabídky).

Pokud chcete změnit jenom cenu, můžete znovu použít disky z jiné SKU ve stejné nabídce. U soukromých SKU nemusíte znovu odesílat disky mezi SKU.

<a name="mark-a-sku-private"></a>Označit jako soukromou SKLADOVOU položku
---------------------

Chcete-li označit SKU jako soukromou, přepněte možnost s dotazem, zda je SKU soukromá:

![Označení SKU jako soukromého](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Můžete znovu použít disky v jiné SKU a změnit ceny nebo popis. Chcete-li znovu použít disky, vyberte možnost **Ano** jako odpověď na příkaz "provede tuto položku k opakovanému použití imagí z veřejné SKU".

Pokud je SKU označená jako soukromá a nabídka má jiné SKU s opakovaně použitelnými disky, je nutné určit, že SKU znovu používá disky z jiné SKU. Je také nutné zadat cílovou cílovou skupinu pro soukromou SKU.

>[!NOTE]
>Po publikování se veřejná SKU nedá nastavit jako soukromá.

<a name="select-an-image"></a>Vybrat obrázek
------------------

Můžete zadat nové disky pro soukromou SKU nebo znovu použít stejné disky, které už jsou v jiné SKU, a to jenom změnou cen nebo popisu. Chcete-li znovu použít disky, vyberte možnost **Ano** jako odpověď na příkaz "provede tento obrázek opakovaného použití SKU z veřejné SKU".

![Indikovat opakované použití obrázku](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Po potvrzení, že SKU znovu používá Image, vyberte zdrojovou nebo *základní* SKU pro Image:

![Vybrat obrázek](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Při publikování této nabídky by se image z vybrané SKU nastavily v rámci ID privátní SKU s vlastními sazbami/podmínkami. Soukromá SKU by byla viditelná jenom pro cílovou cílovou skupinu.

V případě aktualizací imagí budete potřebovat jenom aktualizaci image původní SKU. Obrázek pro soukromou SKU se na pozadí aktualizuje také automaticky. Podobně platí, že pokud odstraníte bitovou kopii z podkladové SKU, bitová kopie bude také odebrána z privátní SKU.

<a name="restricting-the-audience"></a>Omezení cílové skupiny
------------------------

Soukromé nabídky lze najít a nasadit pouze cílené uživatele.
V současné době podporujeme cílení uživatelů pomocí ID předplatných.

Tyto odběry je možné zadat prostřednictvím ručního vstupního formuláře **až po 10 předplatných**nebo nahráním souboru CSV, který umožňuje **až 20 000 předplatných**.

Ruční zadání pro skupinu s omezeným přístupem:

![Ručně omezit cílovou skupinu](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Nahrání sdíleného svazku clusteru pro skupinu s omezeným přístupem:

![Omezení cílové skupiny pomocí CSV](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Ukázkový obsah souboru CSV:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Když přepnete z ručního zadání do zobrazení pro odeslání souboru CSV nebo ze sdíleného svazku clusteru do ručního zadání, starý seznam ID předplatných s přístupem k SKU se nezachová. Zobrazí se upozornění a seznam bude po uložení nabídky přepsán.

<a name="managing-private-audiences"></a>Správa privátních cílových skupin
-------------------------

**Aby bylo možné cílovou skupinu aktualizovat bez nutnosti opětovného publikování celé nabídky, kterou požadujete, pomocí uživatelského rozhraní nebo rozhraní API, a potom spusťte akci synchronizace privátních cílových skupin.**

Pokud je vaše cílová skupina 10 nebo méně předplatných, můžete ji spravovat výhradně pomocí uživatelského rozhraní CPP.

Pokud je vaše cílová skupina více než 10 předplatných, můžete ji spravovat pomocí souboru CSV, který můžete buď nahrát do uživatelského rozhraní CPP, nebo pomocí rozhraní API.

Pokud používáte rozhraní API a nechcete spravovat soubor CSV, můžete spravovat cílovou skupinu přímo pomocí rozhraní API podle níže uvedených pokynů.

> [!NOTE]
> Pomocí ID předplatného Azure (plány a SKU) nebo ID tenanta (jenom plány) přidejte cílovou skupinu do vaší soukromé nabídky.

###  <a name="managing-subscriptions-with-the-api"></a>Správa předplatných pomocí rozhraní API

Rozhraní API můžete použít buď k nahrání sdíleného svazku clusteru, nebo k přímé správě cílové skupiny (bez použití sdíleného svazku clusteru). Obecně stačí jednoduše načíst nabídku, aktualizovat `restrictedAudience` objekt a následně tyto změny odeslat zpět do vaší nabídky, aby bylo možné přidat nebo odebrat členy cílové skupiny.

Tady je postup, jak programově aktualizovat seznam cílových skupin:

1. [Načíst data nabídky](cloud-partner-portal-api-retrieve-specific-offer.md) :

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. V každé SKU nabídky Najděte objekty s omezeným přístupem pomocí tohoto dotazu JPath:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Aktualizujte objekty skupiny s omezeným přístupem pro vaši nabídku.

    **Pokud jste původně nahráli seznam předplatných pro soukromou nabídku ze souboru CSV:**

    Vaše objekty *restrictedAudience* budou vypadat takto.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Pro každý objekt skupiny s omezeným přístupem:

    a. Stáhněte si obsah `restrictedAudience.uploadedCsvUri`. Obsah je jednoduše souborem CSV se záhlavími. Příklad:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Podle potřeby přidejte nebo odstraňte odběry v staženém souboru CSV.

    c. Nahrajte aktualizovaný soubor CSV do umístění, jako je [Azure Blob Storage](../../storage/blobs/storage-blobs-overview.md) nebo [OneDrive](https://onedrive.live.com), a vytvořte odkaz na soubor určený jen pro čtení. Toto bude váš nový *SasUrl*.

    d. Aktualizujte `restrictedAudience.uploadedCsvUri` klíč pomocí nového *SasUrl*.

    **Pokud jste ručně zadali původní seznam předplatných pro Vaši soukromou nabídku z portál partnerů cloudu:**

    Vaše objekty *restrictedAudience* budou vypadat přibližně takto:

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

    a. Pro každý objekt skupiny s omezeným přístupem přidejte nebo odstraňte položky `restrictedAudience.manualEntries` v seznamu podle potřeby.

4. Až se dokončí aktualizace všech *restrictedAudience* objektů pro každou SKU vaší soukromé nabídky, [aktualizujte nabídku](cloud-partner-portal-api-creating-offer.md):

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    V takovém případě je seznam aktualizovaných cílových skupin nyní aktivní.

<a name="previewing-private-offers"></a>Zobrazení náhledu soukromých nabídek
-------------------------

Během kroku Preview/přípravy budou mít přístup k této SKU pouze předplatná úrovně nabídky Preview. V této fázi testování můžete zobrazit náhled nabídky tak, jak by se zobrazily cílovým zákazníkům.

Předplatná Preview úrovně nabídky pro přístup k připraveným nabídkám:

![Náhled ID předplatných](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Po skončení nabídky bude možné zobrazit a nasadit soukromou SKU pouze omezený počet předplatných cílových skupin (zadaných prostřednictvím ručního vstupu nebo sdíleného svazku clusteru). Doporučujeme, abyste **vždycky zahrnuli vlastní odběry do skupiny omezeného** přístupu pro SOUKROMou skladovou položku pro účely ověření.

>[!NOTE]
>Pro účely ladění budou mít přístup k těmto soukromým nabídkám také přístup a technické týmy společnosti Microsoft.
