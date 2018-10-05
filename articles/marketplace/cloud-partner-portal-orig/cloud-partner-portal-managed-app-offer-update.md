---
title: Aktualizace stávající nabídky pro Azure Marketplace
description: Aktualizace stávající nabídky pro Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5633392bdf1293ee9196fafe67cf901e0d8c8014
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809269"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Aktualizace stávající nabídky pro Azure Marketplace 
==============================================

Existují různé typy aktualizací, které můžete chtít provést pro vaši nabídku po za provozu.

1.  Přidání nových \"balíčku\" na existující skladové položky
2.  Přidání nové skladové položky do existující nabídky
3.  Aktualizuje se nabídka/SKU marketplace metadat

Budete muset aktualizovat vaši nabídku v portál partnerů cloudu a znovu publikovat. Tento článek vás provede různé aspekty aktualizace nabídky aplikace Azure.

<a name="unpermitted-changes-to-azure-application-offersku"></a>Nedovolené změny nabídka/SKU aplikace Azure 
--------------------------------------------------

Existují atributy Azure Application/SKU nabídky, která se nemůže modifikovat po nabídky uvedete na webu Azure Marketplace.

1.  ID vydavatele, nabídky a ID nabídky.
2.  ID SKU existující skladové položky.
3.  Aktualizujte balíček, který byl publikován.

<a name="adding-a-new-package-to-an-existing-sku"></a>Přidání nového balíčku do existující skladové položky 
---------------------------------------

Vydavatel může chtít přidat nové verze balíčku za účelem aktualizace existujícího balíčku. To dalo udělat nahráním nového balíčku s jinou verzi číslo.

1.  Přihlaste se k [portál partnerů cloudu](http://cloudpartner.azure.com)
2.  V všechny nabídky najdete nabídku, kterou chcete aktualizovat
3.  Ve formuláři skladové položky, klikněte na skladovou Položku kdo\'měl chcete aktualizovat balíček s
4.  Klikněte na \"nový balíček\" a zadejte novou verzi
5.  Nahrajte nový soubor .zip obsahuje soubor aktualizovanou šablonu
6.  Klikněte na publikovat pusťte se do pracovního postupu publikovat mít nové skladové jednotky vysílat živě.

<a name="adding-a-new-sku-to-an-existing-offer"></a>Přidání nové skladové položky do existující nabídky
-------------------------------------

Můžete zpřístupnit novou skladovou Položku pro váš stávající nabídky. Pokud chcete povolit, postupujte následujících kroků.

1.  Přihlaste se k [portál partnerů cloudu](http://cloudpartner.azure.com)
2.  V všechny nabídky najdete nabídku, kterou chcete aktualizovat
3.  O skladových položkách tvoří clik přidat nové SKU a sdělte mu ID SKU v místní nabídce.
4.  Postupujte podle zbývajících kroků uvedených [tady](./cloud-partner-portal-managed-app-publish.md).
5.  Klikněte na publikovat pusťte se do pracovního postupu publikovat mít nové skladové jednotky vysílat živě.

<a name="updating-offer-marketplace-metadata"></a>Aktualizace metadat nabídky Marketplace 
-----------------------------------

Můžete mít scénáře, které je potřeba aktualizovat marketplace metadata přidružená k vaší nabídky, jako je aktualizace vašeho loga společnosti atd. Postupujte následovně.

1.  Přihlaste se na portál Cloud Partner
2.  V všechny nabídky najdete nabídku, kterou chcete aktualizovat
3.  Přejít na webu Marketplace formuláře a postupujte podle pokynů [tady](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-push-to-staging) žádné změny.
4.  Klikněte na publikovat pusťte se do pracovního postupu publikovat vaše změny budou vysílat živě.
