---
title: Předpoklady nabídky aplikací Azure | Azure Marketplace
description: Předpoklady pro publikování nabídky aplikací Azure na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b927404758dc59419fb3c5e638ac32758d534681
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281744"
---
# <a name="azure-application-prerequisites"></a>Požadavky aplikací Azure

Tento článek popisuje technické a obchodní předpoklady pro publikování nabídky spravované aplikace na Azure Marketplace.  Pokud jste tak ještě neučinili, přečtěte si následující zdroje informací:
- V závislosti na typu skladové položky průvodce publikováním nabídek [azure: šablony řešení](../../marketplace-solution-templates.md) nebo Průvodce [publikováním nabídek spravovaných aplikací](../../marketplace-managed-apps.md)
- [Vytváření šablon řešení a spravované aplikace pro](https://channel9.msdn.com/Events/Build/2018/BRK3603) video azure marketplace


## <a name="technical-requirements"></a>Technické požadavky

Technické požadavky zahrnují tyto položky:

*   Šablony Azure Resource Manageru Další informace najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Tento článek popisuje strukturu šablony Azure Resource Manager. Představuje různé části šablony a vlastnosti, které jsou k dispozici v těchto částech. Šablona se skládá z JSON a výrazy, které můžete použít k vytvoření hodnoty pro vaše nasazení. 
* Šablony Azure Quickstart.<br> Další informace naleznete v tématu:

  * [Azure Úvodní k šablonám](https://azure.microsoft.com/documentation/templates/). Nasazujte prostředky Azure prostřednictvím Azure Resource Manageru pomocí šablon, které přispěly komunitou, abyste toho zvládli víc. Azure Resource Manager umožňuje zřizovat aplikace pomocí deklarativní šablony. S jednou šablonou můžete nasadit několik služeb společně s jejich závislostmi. Stejnou šablonu můžete použít k opakovanému nasazení aplikace během každé fáze životního cyklu této aplikace.
  * [GitHub: Šablony rychlého spuštění Správce prostředků Azure](https://github.com/azure/azure-quickstart-templates). Toto repo obsahuje všechny aktuálně dostupné šablony Azure Resource Manager u přispěl komunitou. Index šablony, který lze https://azure.microsoft.com/documentation/templates/prohledávat, je udržován na adrese .
* Vytvořit definici ui<br>
Další informace najdete [v tématu Vytvoření uživatelského rozhraní portálu Azure pro spravovanou aplikaci](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Tento článek představuje základní koncepty souboru createUiDefinition.json. Portál Azure používá tento soubor ke generování uživatelského rozhraní pro vytvoření spravované aplikace.


## <a name="business-requirements"></a>Obchodní požadavky

Obchodní požadavky zahrnují následující procesní, smluvní a právní povinnosti:

* Musíte být registrovaným vydavatelem Cloud Marketplace. Pokud nejste zaregistrováni, postupujte podle pokynů v článku [Staňte se vydavatelem cloudového webu](https://docs.microsoft.com/azure/marketplace/become-publisher
)Marketplace .

>[!NOTE]
>K přihlášení k portálu partnerů cloudu byste měli použít stejný registrační účet Centra pro vývojáře Microsoft. Pro nabídky Azure Marketplace byste měli mít jenom jeden účet Microsoft. Tento účet by neměl být specifický pro jednotlivé služby nebo nabídky.

* Vaše společnost (nebo její dceřiná společnost) musí být v prodeji ze země nebo oblasti podporované Azure Marketplace. Aktuální seznam těchto zemí nebo oblastí najdete v tématu [Zásady účasti na webu Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Váš produkt musí být licencován způsobem, který je kompatibilní s fakturačními modely podporovanými Azure Marketplace. Další informace najdete v [tématu možnosti fakturace](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) na Azure Marketplace.
* Jste zodpovědní za zpřístupnění technické podpory zákazníkům komerčně přiměřeným způsobem. Tato podpora může být bezplatná, placená nebo prostřednictvím komunitních přístupů.
* Jste zodpovědní za licencování softwaru a jakýchkoli závislostí softwaru třetích stran.
* Musíte poskytnout obsah, který splňuje kritéria pro vaši nabídku, která mají být uvedeny na Azure Marketplace a na webu Azure Portal.
* Musíte souhlasit s podmínkami zásad účasti na webu Microsoft Azure Marketplace a smlouvy s vydavatelem.
* Musíte dodržovat podmínky používání webu Microsoft Azure, prohlášení o zásadách ochrany osobních údajů společnosti Microsoft a smlouvu o certifikovaných programech Microsoft Azure.


## <a name="publishing-requirements"></a>Požadavky na publikování

Chcete-li publikovat novou nabídku aplikací Azure, musíte splnit následující požadavky:

* Připravte si metadata k použití. Následující seznam (nevyčerpávající) ukazuje příklad těchto metadat:
  * Název
  * Popis (ve formátu HTML)
  * Obrázek loga (ve formátu PNG) a v těchto pevných velikostech obrázků: 40 x 40 pixelů, 90 x 90 pixelů, 115 x 115 pixelů a 255 x 115 pixelů.
* *Podmínky použití* a dokumenty *o zásadách ochrany osobních údajů*
* Dokumentace aplikace
* Kontaktní údaje podpory


## <a name="next-steps"></a>Další kroky

Jakmile splníte všechny požadavky, budete připraveni [vytvořit nabídku aplikací Azure](./cpp-create-offer.md). 
 
