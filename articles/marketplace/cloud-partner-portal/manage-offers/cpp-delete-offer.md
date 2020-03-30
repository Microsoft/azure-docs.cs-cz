---
title: Odstranit nabídky tržiště | Azure Marketplace
description: Odstranění nabídek na tržištích Azure a AppSource pomocí portálu partnerů cloudu
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: dsindona
ms.openlocfilehash: fa8ee834bd6d9261c47c540e9517c200c894b6c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286451"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Odstranění nabídek Azure Marketplace a AppSource nebo virtuálních aplikací

Z různých důvodů se můžete rozhodnout stáhnout svou nabídku z jejího trhu společnosti Microsoft, který může mít dvě formy:

- *Odebrání nabídky* zajišťuje, že noví zákazníci již nemusí vaši nabídku zakoupit ani nasadit, ale nemají žádný vliv na stávající zákazníky, které musíte podporovat podle licenční smlouvy a relevantních zákonů. 
- *Ukončení nabídky* je proces ukončení služby a/nebo licenční smlouvy mezi vámi a vašimi stávajícími zákazníky. 

Pokyny a zásady týkající se odebrání a ukončení nabídky se řídí [smlouvou vydavatele na webu Microsoft Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560) a zásadami [účasti](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (oddíl [Nabídka pozastavení a odebrání).](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal) 

Tento článek popisuje různé podporované scénáře odstranění a kroky potřebné k provedení každého.  

> [!NOTE]
> Nabídku, která nebyla publikována, můžete odstranit pouhým výběrem tlačítka **Odstranit** na panelu nástrojů na kartě **Editor.**


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Odstranění publikované skladové položky z Azure Marketplace

Publikovanou skladovou položku můžete odstranit z Azure Marketplace pomocí následujících kroků:

1.  Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).
2.  Na stránce **Všechny nabídky** vyberte svou nabídku.  Vaše nabídka by měla být zobrazena na kartě **Editor.**
3.  Na levém panelu nástrojů vyberte kartu **SKU.** 
4.  Vyberte skladovou položku, kterou chcete odstranit, a klepněte na tlačítko **Odstranit.**
5.  [Znovu publikujte](./cpp-publish-offer.md) nabídku na Azure Marketplace.

Po publikování upravené nabídky na Azure Marketplace se vybraná skladová položka už nebude uvedena na webu Azure Marketplace a na webu Azure Portal.


## <a name="roll-back-to-a-previous-sku-version"></a>Návrat k předchozí verzi skladové položky

Aktuální verzi publikované skladové položky můžete odstranit z Azure Marketplace pomocí kroků zde. Po dokončení procesu skladové položky je vrácena zpět na předchozí verzi.

1. Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).
2. Na stránce **Všechny nabídky** vyberte svou nabídku.  Vaše nabídka by měla být zobrazena na kartě **Editor.**
3. Na levém panelu nástrojů vyberte kartu **SKU.** 
4. Odstraňte nejnovější verzi přidruženého datového zdroje řešení ze seznamu verzí disků.  V závislosti na typu nabídky může být toto pole **Verze disku**, **Verze balíčků**nebo podobný datový zdroj. 
5. [Znovu publikujte](./cpp-publish-offer.md) nabídku na Azure Marketplace.

Po publikování upravené nabídky na Azure Marketplace, aktuální verze uvedené skladové položky již nebudou uvedeny. na Azure Marketplace a na webu Azure Portal.  Skladová položka je vrácena zpět na předchozí verzi.


## <a name="delete-a-live-offer"></a>Odstranění živé nabídky

Odstranění živé nabídky má různé procesní, obchodní a právní aspekty. Podle následujících kroků získáte pokyny od týmu podpory k odebrání živé nabídky z Azure Marketplace:

1.  Navýšit lístek podpory pomocí vytvořit [incident](https://go.microsoft.com/fwlink/?linkid=844975) stránky nebo kliknutím na **podporu** v pravém horním rohu [portálu partnerů cloudu](https://cloudpartner.azure.com/).

2.  V seznamu **Typ problému** vyberte konkrétní typ nabídky a v seznamu **Kategorie** vyberte **Odebrat publikovanou nabídku.**

3.  Odešlete žádost.

Tým podpory vás provede procesem odstranění nabídky.

> [!NOTE]
> Odstranění nabídky (nebo Skladové položky) nebude mít vliv na aktuální nákupy této nabídky (nebo Skladové položky). Tyto nákupy budou i nadále fungovat jako dříve. Odstraněné nabídky nebo sku však nebudou k dispozici pro žádné budoucí nákupy.


## <a name="next-steps"></a>Další kroky

Po seznámení se základními operacemi používanými ke správě nabídek jste připraveni vytvořit instanci [nabídky webu](../cpp-marketplace-offers.md)Microsoft Marketplace .
