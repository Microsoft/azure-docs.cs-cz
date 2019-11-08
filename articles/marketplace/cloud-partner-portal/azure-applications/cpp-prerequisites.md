---
title: Požadavky na nabídku aplikace Azure | Azure Marketplace
description: Požadavky na publikování nabídky aplikace Azure na Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 261af94e233bdb6189d7819f8f28c7e23b5dd112
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826146"
---
# <a name="azure-application-prerequisites"></a>Předpoklady pro aplikace Azure

Tento článek popisuje technické požadavky pro publikování nabídky spravované aplikace na Azure Marketplace.  Pokud jste to ještě neudělali, Projděte si následující zdroje informací:
- V závislosti na typu SKU buď [aplikace Azure: Šablona řešení nabízí průvodce publikováním](../../marketplace-solution-templates.md) nebo [aplikace Azure: Průvodce publikováním nabídky spravované aplikace](../../marketplace-managed-apps.md)
- [Sestavování šablon řešení a spravovaných aplikací pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) video


## <a name="technical-requirements"></a>Technické požadavky

Technické požadavky zahrnují následující položky:

*   Další informace o Azure Resource Manager šablonách naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Tento článek popisuje strukturu Azure Resource Manager šablony. Zobrazuje různé oddíly šablony a vlastnosti, které jsou k dispozici v těchto oddílech. Šablona se skládá z formátu JSON a výrazů, které můžete použít k sestavení hodnot pro vaše nasazení. 
* Šablony Azure pro rychlý Start.<br> Další informace naleznete v tématu:

  * [Šablony Azure pro rychlý Start](https://azure.microsoft.com/documentation/templates/). Nasazujte prostředky Azure prostřednictvím Azure Resource Manageru za použití šablon dodaných komunitou a udělejte víc práce. Azure Resource Manager umožňuje zřizovat aplikace pomocí deklarativní šablony. S jednou šablonou můžete nasadit několik služeb společně s jejich závislostmi. Stejnou šablonu můžete použít k opakovanému nasazení aplikace během každé fáze životního cyklu této aplikace.
  * [GitHub: Azure Resource Manager šablon pro rychlý Start](https://github.com/azure/azure-quickstart-templates). Toto úložiště obsahuje všechny aktuálně dostupné Azure Resource Manager šablony, které přispěla komunita. Index šablony s možností prohledávání je udržován na https://azure.microsoft.com/documentation/templates/.
* Vytvořit definici uživatelského rozhraní<br>
Další informace najdete v tématu [vytvoření Azure Portal uživatelského rozhraní pro spravovanou aplikaci](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). V tomto článku se seznámíte se základními koncepty souboru createUiDefinition. JSON. Azure Portal používá tento soubor k vygenerování uživatelského rozhraní pro vytvoření spravované aplikace.


## <a name="business-requirements"></a>Obchodní požadavky

Mezi obchodní požadavky patří následující procedurální, smluvní a právní závazky:

* Musíte být registrovaný Vydavatel cloudového webu Marketplace. Pokud nejste registrováni, postupujte podle kroků v článku [Staňte se vydavatelem cloudového Marketplace](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>K přihlášení do portál partnerů cloudu byste měli použít stejný registrační účet Microsoft Developer Center. Pro nabídky Azure Marketplace byste měli mít jenom jeden účet Microsoft. Tento účet by neměl být specifický pro jednotlivé služby nebo nabídky.

* Vaše společnost (nebo její dceřiná společnost) se musí nacházet v zemi nebo oblasti, kterou Azure Marketplace podporuje. Aktuální seznam těchto zemí nebo oblastí najdete v tématu [Microsoft Azure Marketplace zásady pro účast](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Váš produkt musí být licencován způsobem, který je kompatibilní s modely fakturace podporovanými Azure Marketplace. Další informace najdete v tématu [Možnosti fakturace](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) v Azure Marketplace.
* Zodpovídáte za zajištění dostupnosti technické podpory pro zákazníky v obchodním přiměřeném způsobem. Tato podpora může být bezplatná, placená nebo prostřednictvím komunitních přístupů.
* Zodpovídáte za licencování softwaru a všech závislostí softwaru třetích stran.
* Je nutné poskytnout obsah, který splňuje kritéria pro vaši nabídku, aby byla uvedena na Azure Marketplace a Azure Portal.
* Musíte souhlasit s podmínkami zásad Microsoft Azure Marketplace pro účast a smlouvy vydavatele.
* Musíte dodržovat podmínky použití Microsoft Azure webu, prohlášení o zásadách ochrany osobních údajů společnosti Microsoft a Microsoft Azure certifikovaných programů.


## <a name="publishing-requirements"></a>Požadavky na publikování

Pokud chcete publikovat novou nabídku aplikací Azure, musíte splnit následující předpoklady:

* Vaše metadata jsou připravená k použití. Následující seznam (nevyčerpávající) zobrazuje příklad těchto metadat:
  * Název
  * Popis (ve formátu HTML)
  * Obrázek loga (ve formátu PNG) a v těchto pevných velikostech obrázků: 40 × 40 pixelů, 90 × 90 pixelů, 115 x 115 pixelů a 255 x 115 pixelů.
* *Podmínek použití* a dokumentů *zásad ochrany osobních údajů*
* Dokumentace k aplikaci
* Kontakty podpory


## <a name="next-steps"></a>Další kroky

Jakmile splníte všechny požadavky, budete připraveni [vytvořit nabídku aplikace Azure](./cpp-create-offer.md). 
 
