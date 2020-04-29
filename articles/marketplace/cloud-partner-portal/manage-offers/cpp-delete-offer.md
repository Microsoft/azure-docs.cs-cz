---
title: Odstranit nabídky Marketplace | Azure Marketplace
description: Odstraňte nabídky na tržištích Azure a AppSource pomocí portál partnerů cloudu
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: dsindona
ms.openlocfilehash: fa8ee834bd6d9261c47c540e9517c200c894b6c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80286451"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Odstranit Azure Marketplace a nabídky AppSource a SKU

Z různých důvodů se můžete rozhodnout, že svou nabídku budete z webu Microsoft Marketplace stáhnout, což může mít dvě formy:

- *Odebrání nabídky* zajišťuje, že noví zákazníci už nemůžou nabídku koupit ani nasadit, ale nemá žádný vliv na stávající zákazníky, které musíte podporovat v souladu s licenční smlouvou a souvisejícími zákony. 
- *Ukončení nabídky* je proces ukončení služby a/nebo licenční smlouvy mezi vámi a vašimi stávajícími zákazníky. 

Doprovodné materiály a zásady týkající se odebrání a ukončení nabídky se řídí [smlouvou Microsoft Marketplace vydavatele](https://go.microsoft.com/fwlink/?LinkID=699560) a [zásadami pro účast](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (oddíl [nabízející pozastavení a odstraňování](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal)). 

Tento článek pojednává o různých podporovaných scénářích odstraňování a krocích potřebných k jejich provedení.  

> [!NOTE]
> Nabídku, která nebyla publikována, můžete odstranit pouhým kliknutím na tlačítko **Odstranit** na panelu nástrojů na kartě **Editor** .


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Odstranění publikované SKU z Azure Marketplace

Publikovanou SKLADOVOU položku můžete z Azure Marketplace odstranit pomocí následujících kroků:

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2.  Na stránce **všechny nabídky** vyberte svoji nabídku.  Vaše nabídka by se měla zobrazit na kartě **Editor** .
3.  Na levém panelu nástrojů vyberte kartu **SKU** . 
4.  Vyberte skladovou jednotku, kterou chcete odstranit, a klikněte na tlačítko **Odstranit** .
5.  [Znovu publikujte](./cpp-publish-offer.md) nabídku Azure Marketplace.

Po publikování upravené nabídky do Azure Marketplace již nebude vybraná SKU uvedena v Azure Marketplace a Azure Portal.


## <a name="roll-back-to-a-previous-sku-version"></a>Vrátit se zpátky k předchozí verzi SKU

Aktuální verzi publikované SKU z Azure Marketplace můžete odstranit pomocí tohoto postupu. Po dokončení procesu se skladová položka vrátí zpět na předchozí verzi.

1. Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. Na stránce **všechny nabídky** vyberte svoji nabídku.  Vaše nabídka by se měla zobrazit na kartě **Editor** .
3. Na levém panelu nástrojů vyberte kartu **SKU** . 
4. Odstraní nejnovější verzi přidruženého prostředku řešení ze seznamu verzí disků.  V závislosti na typu nabídky může být toto pole **verze disku**, **verze balíčku**nebo podobný prostředek. 
5. [Znovu publikujte](./cpp-publish-offer.md) nabídku Azure Marketplace.

Po publikování upravené nabídky na webu theAzure Marketplace už nebude uvedená aktuální verze uvedené SKU. v Azure Marketplace a Azure Portal.  SKU se vrátí zpět na předchozí verzi.


## <a name="delete-a-live-offer"></a>Odstraní živou nabídku.

Existují různé postupy, obchodní a právní aspekty odebrání živé nabídky. Pomocí následujících kroků získejte od týmu podpory pokyny, jak odebrat živou nabídku z Azure Marketplace:

1.  Vyvolejte lístek podpory pomocí stránky [vytvořit incident](https://go.microsoft.com/fwlink/?linkid=844975) nebo kliknutím na **Podpora** v pravém horním rohu [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V seznamu **typ problému** vyberte svůj konkrétní typ nabídky a v seznamu **kategorie** vyberte možnost **Odebrat publikovanou nabídku** .

3.  Odešlete žádost.

Tým podpory vás provede procesem odstraňování nabídky.

> [!NOTE]
> Odstranění nabídky (nebo SKU) nebude mít vliv na aktuální nákupy této nabídky (ani SKU). Tyto nákupy budou i nadále fungovat jako dříve. Odstraněné nabídky nebo skladové položky však nebudou k dispozici pro žádné budoucí nákupy.


## <a name="next-steps"></a>Další kroky

Jakmile se seznámíte se základními operacemi, které se používají ke správě nabídek, budete připraveni vytvořit instanci nabídky na [webu Microsoft Marketplace](../cpp-marketplace-offers.md).
