---
title: Rozhraní Api pro graf y Služby Azure AD | Dokumenty společnosti Microsoft
description: Přehled a průvodce rychlým startem pro rozhraní API Azure AD Graph, který umožňuje programový přístup k Azure AD prostřednictvím koncových bodů rozhraní REST API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9515238449b3ece79815c9b3c616bd715b3b3400
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476545"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API

> [!IMPORTANT]
> Důrazně doporučujeme používat [Microsoft Graph](https://developer.microsoft.com/graph) místo rozhraní Azure AD Graph API pro přístup k prostředkům Azure Active Directory (Azure AD). Náš vývojový program se nyní soustředí na Microsoft Graph a pro Azure AD Graph API nejsou plánovaná žádná další vylepšení. Existuje velmi omezený počet scénářů, pro které rozhraní API Azure AD Graph může být stále vhodné; Další informace najdete v [tématu Microsoft Graph nebo Azure AD Graph](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) blogu a [migrace aplikací Azure AD Graph do Microsoft Graphu](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Tento článek se vztahuje na rozhraní API Azure AD Graph. Podobné informace související s rozhraním Microsoft Graph API najdete [v tématu Použití rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api).

Rozhraní Graph API služby Azure Active Directory poskytuje programový přístup k Azure AD prostřednictvím koncových bodů rozhraní REST API. Aplikace můžou pomocí rozhraní Azure AD Graph API provádět operace vytváření, čtení, aktualizace a odstraňování (CRUD) na datech adresáře a objektech. Například rozhraní API azure ad graf podporuje následující běžné operace pro objekt uživatele:

* Vytvoření nového uživatele v adresáři
* Získání podrobných vlastností uživatele, například jeho skupin
* Aktualizace vlastností uživatele, například jeho polohy a telefonního čísla, nebo změna hesla
* Kontrola členství uživatele ve skupině pro přístup založený na rolích
* Zakázání uživatelského účtu nebo jeho úplné odstranění

Kromě toho můžete provádět podobné operace na jiné objekty, jako jsou například skupiny a aplikace. Chcete-li volat rozhraní API Azure AD Graph v adresáři, musí být vaše aplikace registrovaná ve službě Azure AD. Vaše aplikace musí být také udělen přístup k rozhraní API Azure AD Graph. Tohoto přístupu je obvykle dosaženo prostřednictvím toku souhlasu uživatele nebo správce.

Pokud chcete začít používat rozhraní Azure Active Directory Graph API, přečtěte [si příručku quickstart](active-directory-graph-api-quickstart.md)rozhraní AZURE AD Graph API nebo si prohlédněte referenční [dokumentaci k interaktivnímu rozhraní API rozhraní Azure AD Graph .](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

## <a name="features"></a>Funkce

Rozhraní Azure AD Graph API poskytuje následující funkce:

* **Koncové body rozhraní REST API**: Rozhraní API azure ad graph u je služba RESTful složená z koncových bodů, ke kterým se přistupuje pomocí standardních požadavků HTTP. Rozhraní Azure AD Graph API podporuje typy obsahu XML nebo Javascript Object Notation (JSON) pro požadavky a odpovědi. Další informace naleznete v [tématu Azure AD Graph REST API odkaz](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Ověřování pomocí Azure AD**: Každý požadavek na rozhraní API Azure AD Graph musí být ověřen připojením webového tokenu JSON (JWT) v hlavičce autorizace požadavku. Tento token se získá tak, že požadavek na koncový bod tokenu Azure AD a poskytuje platná pověření. Můžete použít tok pověření klienta OAuth 2.0 nebo tok udělení autorizačního kódu získat token pro volání grafu. Další informace [OAuth 2.0 ve službě Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Autorizace založená na rolích (RBAC):** Skupiny zabezpečení se používají k provádění RBAC v rozhraní API grafu Azure AD. Například pokud chcete zjistit, zda má uživatel přístup k určitému prostředku, aplikace může volat [zkontrolovat členství ve skupině (přenosité)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) operace, která vrátí true nebo false.
* **Rozdílový dotaz:** Rozdílový dotaz umožňuje sledovat změny v adresáři mezi dvěma časovými obdobími bez nutnosti provádět časté dotazy na rozhraní API azure ad graphu. Tento typ požadavku vrátí pouze změny provedené mezi předchozí rozdílový dotaz požadavku a aktuální požadavek. Další informace naleznete v [tématu Rozdílový dotaz rozhraní API rozhraní Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Rozšíření adresářů**: Do objektů adresáře můžete přidat vlastní vlastnosti bez nutnosti externího úložiště dat. Pokud například vaše aplikace vyžaduje vlastnost Skype ID pro každého uživatele, můžete novou vlastnost zaregistrovat v adresáři a bude k dispozici pro použití u každého objektu uživatele. Další informace naleznete v [tématu rozšíření schématu adresáře rozhraní API rozhraní Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Zabezpečené obory oprávnění**: Rozhraní API Azure AD Graph poskytuje obory oprávnění, které umožňují zabezpečený přístup k datům Azure AD pomocí OAuth 2.0. Podporuje různé typy klientských aplikací, včetně:
  
  * uživatelská rozhraní, která jsou udělena delegovaný přístup k datům prostřednictvím autorizace od přiřazovaného uživatele (delegované)
  * aplikace service/daemon, které pracují na pozadí, aniž by byl přítomen přihlášený uživatel, a používají řízení přístupu založené na rolích definované aplikací
    
    Delegovaná i aplikační oprávnění představují oprávnění vystavené rozhraním API azure ad graphu a klientské aplikace je můžou požadovat prostřednictvím funkcí oprávnění pro registraci aplikací na [webu Azure Portal](https://portal.azure.com). [Obory oprávnění rozhraní API rozhraní Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) poskytuje informace o tom, co je k dispozici pro použití klientské aplikace.

## <a name="scenarios"></a>Scénáře

Rozhraní Azure AD Graph API umožňuje mnoho scénářů aplikací. Následující scénáře jsou nejběžnější:

* **Aplikace oboru podnikání :** V tomto scénáři funguje podnikový vývojář pro organizaci, která má předplatné Office 365. Vývojář vytváří webovou aplikaci, která spolupracuje s Azure AD k provádění úkolů, jako je přiřazení licence uživateli. Tato úloha vyžaduje přístup k rozhraní API Azure AD Graph, takže vývojář zaregistruje aplikaci jednoho klienta ve službě Azure AD a nakonfiguruje oprávnění ke čtení a zápisu pro rozhraní API Azure AD Graph. Pak aplikace je nakonfigurován tak, aby používat buď vlastní pověření nebo ty aktuálně přihlašovací ho uživatele získat token pro volání rozhraní API Azure AD Graph.
* **Software jako aplikace služby (Multi-Tenant)**: V tomto scénáři nezávislý dodavatel softwaru (ISV) vyvíjí hostovizované víceklientské webové aplikace, která poskytuje funkce pro správu uživatelů pro jiné organizace, které používají Azure AD. Tyto funkce vyžadují přístup k objektům adresáře, takže aplikace musí volat rozhraní API Azure AD Graph. Vývojář zaregistruje aplikaci ve službě Azure AD, nakonfiguruje ji tak, aby vyžadovala oprávnění ke čtení a zápisu pro rozhraní API azure ad graphu, a pak povolí externí přístup, aby ostatní organizace mohly souhlasit s použitím aplikace ve svém adresáři. Pokud se uživatel v jiné organizaci poprvé ověří v aplikaci, zobrazí se mu dialogové okno souhlasu s oprávněními, která aplikace požaduje. Udělení souhlasu pak udělí aplikaci požadovaná oprávnění k rozhraní API Azure AD Graph api v adresáři uživatele. Další informace o rámci souhlasu naleznete v [tématu Přehled rámce pro souhlas](consent-framework.md).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat rozhraní Azure Active Directory Graph API, přečtěte si následující témata:

* [Průvodce rychlým startem rozhraní API rozhraní Azure AD Graph](active-directory-graph-api-quickstart.md)
* [Dokumentace k grafu Azure AD REST](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
