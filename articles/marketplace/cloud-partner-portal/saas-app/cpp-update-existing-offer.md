---
title: Aktualizace stávající nabídky aplikace Azure SaaS | Azure Marketplace
description: Jak aktualizovat stávající nabídky SaaS aplikace na Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: pbutlerm
ms.openlocfilehash: 2195c9a5e1f0d3683ea8cf6564d97cbabd072f81
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834199"
---
# <a name="update-an-existing-saas-application-offer"></a>Aktualizace stávající nabídky SaaS aplikace

Existují různé typy aktualizací, které můžete chtít provést pro vaši nabídku poté, co je publikována a je v provozu. Všechny změny provedené v nové verzi nabídky by měla uložit a publikovat jej odrážejí na webu Marketplace. Tento článek popisuje postup přes různé aspekty vaší nabídky SaaS v aktualizaci [portál partnerů cloudu](https://cloudpartner.azure.com/).

> [!IMPORTANT] 
> SaaS nabízejí funkce migruje na [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory).  Všechny nové zdroje musí pomocí partnerského centra pro vytvoření nové nabídky SaaS a správu existující nabídky.  Aktuální vydavatele se nabídky SaaS se batchwise migrují z portál partnerů cloudu do partnerského centra.  Portál partnerů cloudu se zobrazí stavové zprávy k označení, když se konkrétní stávající nabídky migrovaly.

Tady je několik důvodů, proč můžete chtít aktualizovat vaši nabídku, jako například:

- Přidává se nová verze do existující aplikace.
- Aktualizuje se aplikace.
- Přidává nové funkce do aplikace.
- Aktualizace metadat marketplace nabídky.

Které vám pomohou tyto úpravy, portál poskytuje **porovnání** a **historie** funkce.

## <a name="unpermitted-changes-to-a-saas-offer"></a>Nedovolené změny nabídky SaaS

Existují atributy nabídky SaaS, kterou nelze změnit poté, co tato nabídka je za provozu na webu Azure Marketplace. Nelze změnit následující nastavení:

- ID nabídky a ID vydavatele, nabídky
- Verze značky, například: `1.0.1`
- Model fakturace nebo licencí se změní na stávající nabídky.

## <a name="common-update-operations"></a>Běžné operace update
 
Následující operace update jsou společná.

### <a name="update-offer-contacts"></a>Aktualizovat kontakty nabídky

Následujícím postupem aktualizovat kontakty podpory pro vaši nabídku.

1. Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**, najít nabídky, které chcete aktualizovat.
3. Přejděte **kontakty** kartu. Aktualizace kontaktů.
4. Vyberte **publikovat** ke spuštění pracovního postupu publikovat provedené změny.


### <a name="update-offer-marketplace-metadata"></a>Aktualizace nabídky marketplace metadat

Použijte následující postup k aktualizaci marketplace metadata související s vaší nabídkou. (Příklad: název společnosti, loga, atd.)

1. Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**, najít nabídky, které chcete aktualizovat.
3. Přejděte **podrobnosti z prodejních míst** kartu. Postupujte podle pokynů v [publikovat SaaS nabízejí](./cpp-publish-offer.md) článku provést změny metadat.
4. Vyberte **publikovat** ke spuštění pracovního postupu publikovat provedené změny.

## <a name="compare-feature"></a>Porovnání funkcí

Když provedete změny v publikované nabídky, můžete použít funkci porovnání auditovat změny, které jste udělali. Následující snímek obrazovky ukazuje možnost Compare publikované nabídky.

![Použít porovnání zobrazíte nabízejí změnami portál Cloud Partner](./media/saas-offer-compare.png)

### <a name="to-use-the-compare-feature"></a>Jak používat funkci porovnání:

1. V každém okamžiku procesu úprav vyberte porovnání pro vaši nabídku.
2. Podívejte se na verzích vedle sebe marketingové prostředky a metadata.

## <a name="publishing-history"></a>Publikování historie

Pokud chcete zobrazit Historický publikační aktivity, vyberte **historie** karty na levé navigační nabídky panelu z cloudu portál pro partnery. Zobrazí se tedy akce prováděné po celou dobu životnosti vaší nabídky na webu Azure Marketplace.

![Viz nabídky historie v portál Cloud Partner](./media/saas-offer-history.png)

Na stránce Historie auditu můžete použít k vyhledání konkrétní nabídky a použít filtry, např. vydavatele, nabídky a typu události (například OfferGoLiveRequested.) Historie auditu můžete také stáhnout jako soubor csv.


## <a name="next-steps"></a>Další postup

[Nabídka aplikace SaaS](./cpp-saas-offer.md)