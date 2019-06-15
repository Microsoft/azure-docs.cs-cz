---
title: Nabídka požadavky aplikace na Azure | Azure Marketplace
description: Požadavky pro publikování aplikací Azure nabízet na webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 64039234a3863332ca19b915fb59a5271625d695
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258189"
---
# <a name="azure-application-prerequisites"></a>Požadavky na aplikace Azure

Tento článek popisuje technické a podnikové požadavky pro publikování nabídka spravované aplikace na Azure Marketplace.  Pokud jste tak již neučinili, projděte si následující zdroje informací:
- V závislosti na vaší typ SKU buď [aplikací Azure: Šablona řešení nabízí průvodce publikováním](../../marketplace-solution-templates.md) nebo [aplikace Azure: Nabídka spravované aplikace, Průvodce publikováním](../../marketplace-managed-apps.md)
- [Vytváření šablon řešení a spravované aplikace pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) videa


## <a name="technical-requirements"></a>Technické požadavky

Technické požadavky zahrnují následující položky:

*   Šablony Azure Resource Manageru pro další informace najdete v tématu [Princip struktury a syntaxe šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Tento článek popisuje strukturu šablony Azure Resource Manageru. Představuje různé části šablony a vlastnosti, které jsou k dispozici v těchto oddílech. Šablona se skládá z JSON a z výrazů, které můžete použít k vytvoření hodnot pro vaše nasazení. 
* Šablony rychlý start Azure.<br> Další informace naleznete v tématu:

  * [Šablony rychlý start Azure](https://azure.microsoft.com/documentation/templates/). Nasazujte prostředky Azure prostřednictvím Azure Resource Manageru za použití šablon dodaných komunitou a udělejte víc práce. Azure Resource Manager umožňuje zřizovat aplikace pomocí deklarativní šablony. S jednou šablonou můžete nasadit několik služeb společně s jejich závislostmi. Stejnou šablonu můžete použít k opakovanému nasazení aplikace během každé fáze životního cyklu této aplikace.
  * [GitHub: Šablony rychlý start Azure Resource Manageru](https://github.com/azure/azure-quickstart-templates). Toto úložiště obsahuje všechny aktuálně k dispozici Azure Resource Manageru šablony přispěla komunita. Index prohledávatelných šablon je udržován na úrovni https://azure.microsoft.com/documentation/templates/.
* Vytvoření definice uživatelského rozhraní<br>
Další informace najdete v tématu [vytvořit Azure portal uživatelského rozhraní pro vaše spravovaná aplikace](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Tento článek představuje základní koncepty createUiDefinition.json souboru. Na webu Azure portal tento soubor používá k vygenerování uživatelského rozhraní pro vytvoření spravované aplikace.


## <a name="business-requirements"></a>Obchodní požadavky

Obchodní požadavky zahrnují následující procesní, smluvní a právní závazky:

* Musí být registrovaný vydavatele na webu Marketplace cloudu. Pokud nejsou zaregistrovány, postupujte podle kroků v článku [stát vydavatelem cloudové Tržiště](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>K přihlášení na portál Cloud Partner musí použít stejný účet registrace Microsoft Developer Center. Pro svoje nabídky na webu Azure Marketplace byste měli mít jenom jeden účet Microsoft. Tento účet nesmí být specifické pro jednotlivé služby nebo nabídky.

* Vaše společnost (nebo její pobočka) musí být v zákazník z – země/oblasti podporuje Azure Marketplace. Aktuální seznam těchto zemí nebo oblastí najdete v tématu [zásadách pro účast na Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Váš produkt musí mít licenci způsobem, který je kompatibilní s modely fakturace podporovanými webem Azure Marketplace. Další informace najdete v tématu [možností fakturace](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) na webu Azure Marketplace.
* Zodpovídáte za zpřístupnění technické podpory pro zákazníky přiměřené způsobem. Tato podpora může být zdarma, placená nebo prostřednictvím komunity přístupy.
* Zodpovídáte za licencování váš software a všechny závislosti softwaru třetích stran.
* Je nutné zadat obsah, který splňuje kritéria pro vaší nabídky na webu Azure Marketplace a na webu Azure Portal.
* Musíte souhlasit s podmínkami zásadách pro účast na Microsoft Azure Marketplace a smlouvě s vydavatelem.
* Musíte dodržovat Microsoft Azure web podmínky použití, prohlášení o ochraně osobních údajů Microsoft a Microsoft Azure Certified Program Agreement.


## <a name="publishing-requirements"></a>Publikování požadavky

Chcete-li publikovat novou nabídku aplikací Azure, musí splňovat následující požadavky:

* Máte připravený k použití metadata. V následujícím seznamu (doplňovat) ukazuje příklad těchto metadat:
  * Název
  * Popis (ve formátu HTML)
  * Obrázek loga (ve formátu PNG) a v nich pevné velikosti obrázků: 40 x 40 pixelů, 90 x 90 pixelů, 115 x 115 pixelů a 255 x 115 pixelů.
* A *Terms of Use* a *zásady ochrany osobních údajů* dokumentů
* Dokumentace ke službě Application
* Kontaktní údaje podpory


## <a name="next-steps"></a>Další postup

Po splnění všech požadavků, budete připraveni k [vytvořte nabídku aplikací Azure](./cpp-create-offer.md). 
 
