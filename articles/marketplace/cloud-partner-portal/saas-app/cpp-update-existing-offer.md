---
title: Aktualizace stávající nabídky aplikace Azure SaaS | Dokumentace Microsoftu
description: Jak aktualizovat stávající nabídky SaaS aplikace na Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 9d490a0ec09e351e4cee00e7c30c9161bdfac3e6
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196884"
---
# <a name="update-an-existing-saas-application-offer"></a>Aktualizace stávající nabídky SaaS aplikace

Existují různé typy aktualizací, které můžete chtít provést pro vaši nabídku poté, co je publikována a je v provozu. Všechny změny provedené v nové verzi nabídky by měla uložit a publikovat jej odrážejí na webu Marketplace. Tento článek popisuje postup přes různé aspekty vaší nabídky SaaS v aktualizaci [portál partnerů cloudu](https://cloudpartner.azure.com/).

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