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
ms.openlocfilehash: 2bfa10441cf5531c9383527a21b033da26322b34
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073234"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Aktualizace stávající nabídky pro Azure Marketplace 
==============================================

Existují různé typy aktualizací, které můžete chtít provést pro vaši nabídku po za provozu, například:

1.  Přidání nových \"balíčku\" na existující skladové položky
2.  Přidání nové skladové položky do existující nabídky
3.  Aktualizuje se nabídka/SKU marketplace metadat

Bude používat portál partnerů cloudu k implementaci těchto změn a pak znovu publikovat vaši nabídku. Tento článek vás provede různé aspekty aktualizace vaši nabídku aplikací Azure.

<a name="unpermitted-changes-to-azure-application-offer"></a>Nedovolené změny na nabídku aplikace Azure 
-----------------------------------------------

Existují atributy Azure Application/SKU nabídky, která se nemůže modifikovat po nabídky uvedete na webu Azure Marketplace.

* ID vydavatele, nabídky a ID nabídky.
* ID SKU existující skladové položky.
* Aktualizujte balíček, který byl publikován.

<a name="adding-a-new-package-to-an-existing-sku"></a>Přidání nového balíčku do existující skladové položky 
---------------------------------------

Vydavatel může chtít přidat nové verze balíčku za účelem aktualizace existujícího balíčku. Toto přidání můžete udělat tak, že nahrajete nový balíček s jinou verzi číslo.

1.  Přihlaste se k [portál partnerů cloudu](http://cloudpartner.azure.com)
2.  V **všechny nabídky**, najít nabídky, které chcete aktualizovat
3.  Na **SKU** formulář a potom klikněte na skladovou Položku kdo\'měl chcete aktualizovat balíček s
4.  Klikněte na \"nový balíček\" a zadejte novou verzi
5.  Nahrajte nový soubor .zip obsahuje soubor aktualizovanou šablonu
6.  Klikněte na publikovat pusťte se do pracovního postupu publikovat mít nové skladové jednotky vysílat živě.

<a name="adding-a-new-sku-to-an-existing-offer"></a>Přidání nové skladové položky do existující nabídky
-------------------------------------

Můžete se rozhodnout zpřístupnit novou skladovou Položku pro váš stávající nabídky provede následující kroky:

1.  Přihlaste se k [portál partnerů cloudu](http://cloudpartner.azure.com)
2.  V **všechny nabídky**, najít nabídky, které chcete aktualizovat
3.  Na **SKU** formuláře, klikněte na Přidat novou skladovou Položku a zadat ID SKU v místní nabídce.
4.  Postupujte podle zbývajících kroků uvedených [tady](./cloud-partner-portal-managed-app-publish.md).
5.  Klikněte na **publikovat** zahájíte publikovat pracovní postup má vysílat živě nové skladové jednotky.

<a name="updating-offer-marketplace-metadata"></a>Aktualizace metadat nabídky Marketplace 
-----------------------------------

Můžete mít scénáře, které je potřeba aktualizovat marketplace metadata přidružená k vaší nabídky, jako je aktualizace vašeho loga společnosti atd. Postupujte následovně.

1.  Přihlaste se k portálu pro partnery Cloud.
2.  V **všechny nabídky**, najít nabídky, které chcete aktualizovat
3.  Přejít na webu Marketplace formuláře a postupujte podle pokynů [tady](../cloud-partner-portal/azure-applications/cpp-marketplace-tab.md) žádné změny.
4.  Klikněte na publikovat pusťte se do pracovního postupu publikovat vaše změny budou vysílat živě.
