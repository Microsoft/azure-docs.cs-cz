---
title: Odstranění nabídky na webu Marketplace – Azure Marketplace | Dokumentace Microsoftu
description: Odstranění nabídky na Azure a AppSource tržišť s řešeními pomocí portál Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: 7b4ee33d3e231dc59ce1d7b4ae6337efb11e96bc
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355502"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Odstranění nabídky Azure Marketplace a AppSource nebo skladové položky

Z různých důvodů se můžete rozhodnout pro stažení vaši nabídku z webu Microsoft marketplace, který může mít dvě formy:

- *Odebrání nabídky* zajistí, že noví zákazníci mohou už koupit a nasadit vaši nabídku, ale nemá žádný vliv na stávající zákazníky, kterým musí podporovat podle licenční smlouvou a které jsou relevantní zákony. 
- *Nabízí ukončení* je proces ukončení služby a/nebo licenční smlouva mezi vámi a vašimi stávajícími zákazníky. Pokyny a zásady týkající se odebrání a ukončení nabídky se řídí [smlouvy vydavatele Microsoft Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560) (sekce 7) a [zásadách pro účast](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (část 6.2). 

Tento článek hovoří o různých nepodporuje odstranění scénáře a kroky potřebné k provedení každého.  

> [!NOTE]
> Můžete odstranit nabídku, která nebyla publikována tak, že jednoduše vyberete **odstranit** tlačítko na panelu nástrojů **Editor** kartu.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Odstranit publikované skladovou Položku z Azure Marketplace

Publikované skladové položky můžete odstranit z Azure Marketplace pomocí následujících kroků:

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2.  V **všechny nabídky** vyberte vaši nabídku.  Vaše nabídka má být zobrazen v **Editor** kartu.
3.  V levém panelu nástrojů vyberte **SKU** kartu. 
4.  Vyberte jednotku SKU, kterou chcete odstranit a klikněte na tlačítko **odstranit** tlačítko.
5.  [Znovu publikujte](./cpp-publish-offer.md) nabídky na webu Azure Marketplace.

Upravené nabídky po publikování na webu Azure Marketplace, vybraná skladová položka už nejsou uvedení v Azure Marketplace a webu Azure portal.


## <a name="roll-back-to-a-previous-sku-version"></a>Vrátit zpět na předchozí verzi SKU

Aktuální verzi publikované skladové položky z Azure Marketplace můžete odstranit pomocí kroků v tomto poli. Po dokončení procesu se skladovou Položku se vrátí zpět na předchozí verzi.

1. Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V **všechny nabídky** vyberte vaši nabídku.  Vaše nabídka má být zobrazen v **Editor** kartu.
3. V levém panelu nástrojů vyberte **SKU** kartu. 
4. Nejnovější verzi prostředku související řešení odstraníte ze seznamu diskových verzí.  V závislosti na typu nabídky, toto pole může být **disková verze**, **verze balíčku**, nebo podobné asset. 
5. [Znovu publikujte](./cpp-publish-offer.md) nabídky na webu Azure Marketplace.

Po publikování upravené nabídky na webu Marketplace theAzure aktuální verzi SKU uvedené už nejsou uvedení. v Tržišti Azure Marketplace a na webu Azure portal.  Skladovou Položku se vrátí zpět na předchozí verzi.


## <a name="delete-a-live-offer"></a>Odstranit živé nabídku

Existují různé procedurální, firmy a právní aspekty na podmínky odebrání za provozu nabídky. Postupujte podle následujících kroků získejte pokyny od týmu podporu k odebrání za provozu nabídky z Azure Marketplace:

1.  Pomocí lístku podpory zvýšit [vytvořit incident](https://go.microsoft.com/fwlink/?linkid=844975) stránky, nebo kliknutím **podporují** v pravém horním rohu [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  Vyberte svůj typ konkrétní nabídky v **typ problému** seznam a vyberte **odebrání publikované nabídky** v **kategorie** seznamu.

3.  Odešlete žádost.

Proces odstranění nabídky vás provede na tým podpory.

> [!NOTE]
> Odstraňuje se nabídka (nebo skladové položky) nebude mít vliv aktuální nákupy této nabídky (nebo skladové položky). Tyto nákupy se budou nadále fungovat jako předtím. Ale odstraněné nabídky a skladové položky nebudou k dispozici pro všechny budoucí nákupy.


## <a name="next-steps"></a>Další postup

Jakmile se seznámíte s základních operací, používané ke správě nabídky, budete chtít vytvořit instanci Microsoft [nabídky marketplace](../cpp-marketplace-offers.md).
