---
title: Co jsou kolekce pracovních prostorů Power BI?
description: Power BI Embedded umožňuje integrovat sestavy Power BI do vašich webových nebo mobilních aplikací, takže nemusíte sestavovat vlastní řešení.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 73b13dcea84ba322072217c3feacebdefb3bf1e6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43052035"
---
# <a name="what-are-power-bi-workspace-collections"></a>Co jsou kolekce pracovních prostorů Power BI?

S **kolekce pracovních prostorů Power BI**, můžete integrovat sestavy Power BI přímo do vašich webových nebo mobilních aplikací.

![Diagram aplikace](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Kolekce pracovních prostorů Power BI se **služby Azure** , která umožňuje nezávislým výrobcům softwaru a vývojáře aplikací na zařízení surface prostředí dat Power BI do jejich aplikací. Jako vývojář začlenění aplikace a tyto aplikace mají své vlastní uživatele a odlišnou sadu funkcí. Tyto aplikace může také dojít mají některé předdefinované datové prvky jako grafy a sestavy, které můžou nyní využívat technologii kolekce pracovních prostorů Power BI společnosti Microsoft. Není nutné aplikaci mohli používat účet Power BI. Můžete pokračovat k přihlášení do aplikace stejně jako předtím a zobrazení a interakci s Power BI, reporting prostředí bez nutnosti další licence.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Licencování pro Microsoft Power BI pracovního prostoru kolekce

V **kolekce pracovních prostorů Power BI Microsoft** využití modelu licencování pro Power BI není odpovědnosti koncovému uživateli.  Místo toho **relací** se kupují vývojář aplikace, které využívá vizuály a k předplatnému, která vlastní tyto prostředky se účtují. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Kolekce pracovních prostorů Power BI Microsoft konceptuální model

![Běh aplikace s kolekcemi pracovních prostorů](media/what-are-power-bi-workspace-collections/model.png)

Stejně jako ostatní služby v Azure, prostředky pro kolekce pracovních prostorů Power BI se zřizují prostřednictvím [rozhraní API Azure Resource Manageru](https://msdn.microsoft.com/library/mt712306.aspx). V takovém případě je prostředek, který je zřizování **kolekce pracovních prostorů Power BI**.

## <a name="workspace-collection"></a>Kolekce pracovních prostorů

A **kolekce pracovních prostorů** je nejvyšší úrovni Azure kontejner prostředků, která obsahuje 0 nebo více **pracovní prostory**.  A **pracovní prostor** **kolekce** obsahují všechny standardní vlastnosti Azure a také následující:

* **Přístupové klíče** – klíče použité při zabezpečené volání rozhraní API Power BI (popsané v další části).
* **Uživatelé** – Azure Active Directory (AAD), kteří mají práva správce ke správě kolekce pracovních prostorů Power BI na webu Azure portal nebo rozhraní API Azure Resource Manageru.
* **Oblast** – jako součást zřizování **kolekce pracovních prostorů**, můžete vybrat oblast, které se mají zřídit v. Další informace najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Pracovní prostor

A **pracovní prostor** , je kontejner Power BI obsahu, který může obsahovat datové sady a sestavy. A **pracovní prostor** je prázdný, při prvním vytvoření. Budete vytvářet obsah pomocí Power BI Desktopu a programově nasadíte soubor PBIX do pracovního prostoru pomocí [rozhraní API v Power BI importovat](https://msdn.microsoft.com/library/mt711504.aspx). Můžete také programově vytvořit datovou sadu a pak vytvářet sestavy v rámci vaší aplikace namísto použití Power BI Desktopu.

## <a name="using-workspace-collections-and-workspaces"></a>Pomocí kolekcí pracovních prostorů a pracovní prostory

**Kolekce pracovních prostorů** a **pracovní prostory** jsou kontejnery, obsahu, který se používá a uspořádány do vyhovuje vybírá nejlepší způsob návrhu aplikace, kterou vytváříte. Bude existovat mnoho různých způsobů, že by mohla uspořádání obsahu v nich. Můžete vložit veškerý obsah v rámci jednoho pracovního prostoru a později pomocí tokenů aplikace dál rozdělit obsah mezi zákazníky. Můžete také umístit všechny vaše zákazníky do samostatných pracovních prostorů, tak, aby se některé oddělení mezi nimi. Nebo můžete se rozhodnout pro uspořádání uživatelů podle oblasti, nikoli podle zákazníka. Tento flexibilní návrh umožňuje zvolit nejlepší způsob, jak uspořádat obsah.

## <a name="cached-datasets"></a>Datové sady v mezipaměti

Dá se datové sady v mezipaměti.  Však nelze aktualizovat data uložená v mezipaměti, jakmile byla načtena do **kolekce pracovních prostorů Power BI Microsoft**. Datové sady v mezipaměti znamená, že jste naimportovali data do Power BI Desktopu namísto použití DirectQuery.

## <a name="authentication-and-authorization-with-app-tokens"></a>Ověřování a autorizace pomocí tokenů aplikace

**Kolekce pracovních prostorů Power BI Microsoft** odloží do vaší aplikace provést všechny potřebné uživatelské ověřování a autorizace. Neexistuje žádný explicitní požadavek, aby vaši koncoví uživatelé se zákazníci služby Azure Active Directory (Azure AD).  Místo toho vyjadřuje vaší aplikace do **kolekce pracovních prostorů Power BI Microsoft** autorizace k vykreslení sestavy Power BI s použitím **ověřovacích tokenů aplikace (App tokeny)**.  Tyto **tokenů aplikace** vytvářejí podle potřeby vaší aplikace chce vykreslit sestavu.

![Diagram použití tokenu aplikace](media/what-are-power-bi-workspace-collections/app-tokens.png)

**Aplikací ověřovacích tokenů (tokeny aplikací)** se používají k ověřování na základě **kolekce pracovních prostorů Power BI Microsoft**.  Existují tři typy **tokenů aplikace**:

1. Zřizování tokeny - použité při zřizování nového **pracovní prostor** do **kolekce pracovních prostorů**
2. Vývoj tokeny - používá při volání přímo **REST API Power BI**
3. Vkládání tokenů - používá při volání k vykreslení sestavy v vložený element iframe

Tyto tokeny se používají pro různé fáze vaši interakci s **kolekce pracovních prostorů Power BI Microsoft**.  Tokeny jsou navržené tak, aby z vaší aplikace můžete delegovat oprávnění k Power BI. Další informace najdete v tématu [tok tokenu aplikace](app-token-flow.md).

## <a name="create-or-edit-reports-within-your-application"></a>Vytvoření nebo úpravě sestav v rámci vaší aplikace

Teď můžete upravit existující sestavy nebo vytvářet nové sestavy přímo ve vaší aplikaci bez nutnosti použití Power BI Desktopu. Tento postup vyžaduje, že existují datové sady v rámci pracovního prostoru.

## <a name="see-also"></a>Další informace najdete v tématech

[Běžné scénáře kolekce pracovních prostorů Microsoft Power BI](scenarios.md)  
[Začínáme s kolekcemi pracovních prostorů Microsoft Power BI](get-started.md)  
[Začínáme s ukázkou](get-started-sample.md)  
[Vložení sestavy](embed-report.md)  
[Ověřování a autorizace v kolekcích Pracovních prostorů Power BI](app-token-flow.md)  
[Vložená ukázka JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Úložiště Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Power BI uzlu úložiště Git](https://github.com/Microsoft/PowerBI-Node)  

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)
