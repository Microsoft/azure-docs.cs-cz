---
title: Azure Active Directory Graph API | Microsoft Docs
description: Přehled a úvodní příručka pro Azure AD Graph API, která umožňuje programový přístup ke službě Azure AD prostřednictvím koncových bodů REST API.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: c74c02d8a82c27c71ce01cd122082010487b453e
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74845038"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API

> [!IMPORTANT]
> Důrazně doporučujeme použít [Microsoft Graph](https://developer.microsoft.com/graph) místo Graph API Azure AD pro přístup k prostředkům Azure Active Directory (Azure AD). Náš vývojový program se nyní soustředí na Microsoft Graph a pro Azure AD Graph API nejsou plánovaná žádná další vylepšení. Existuje velmi omezený počet scénářů, pro které by mohla být služba Azure AD Graph API vhodná; Další informace najdete v příspěvku blogu [Microsoft Graph nebo Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) a [migrace aplikací Azure ad Graph do Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Tento článek se týká Graph API služby Azure AD. Podobné informace související s Microsoft Graph API najdete v tématu [použití rozhraní api Microsoft Graph](https://docs.microsoft.com/graph/use-the-api).

Rozhraní Graph API služby Azure Active Directory poskytuje programový přístup k Azure AD prostřednictvím koncových bodů rozhraní REST API. Aplikace můžou pomocí Graph API Azure AD provádět operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro data a objekty adresáře. Například služba Azure AD Graph API podporuje následující běžné operace pro objekt uživatele:

* Vytvoření nového uživatele v adresáři
* Získání podrobných vlastností uživatele, například jejich skupin
* Aktualizace vlastností uživatele, jako je jejich umístění a telefonní číslo, nebo změna hesla
* Ověřit členství uživatele ve skupině pro přístup na základě rolí
* Zakázání účtu uživatele nebo jeho úplné odstranění

Kromě toho můžete provádět podobné operace na jiných objektech, jako jsou skupiny a aplikace. Pokud chcete volat Graph API služby Azure AD v adresáři, musí být vaše aplikace zaregistrovaná ve službě Azure AD. Vaše aplikace musí mít taky udělený přístup k Graph API Azure AD. Tento přístup se obvykle dosahuje pomocí toku souhlasu uživatele nebo správce.

Pokud chcete začít používat Graph API Azure Active Directory, přečtěte si [příručku pro rychlý start Graph API služby Azure AD](active-directory-graph-api-quickstart.md)nebo si prohlédněte si [referenční dokumentaci k interaktivním Graph API Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Funkce

Azure AD Graph API nabízí tyto funkce:

* **REST API koncové body**: Azure AD Graph API je služba RESTful tvořená koncovými body, ke kterým se přistupoval pomocí standardních požadavků HTTP. Azure AD Graph API podporuje typy obsahu XML nebo JavaScript Object Notation (JSON) pro žádosti a odpovědi. Další informace najdete v tématu [REST API Reference k Azure AD graphu](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Ověřování pomocí služby Azure AD**: každý požadavek na Graph API Azure AD musí být ověřený připojením JSON web token (Jwt) v autorizační hlavičce požadavku. Tento token se získá tak, že se povede požadavek na koncový bod tokenu služby Azure AD a poskytne platné přihlašovací údaje. K získání tokenu pro volání grafu můžete použít tok přihlašovacích údajů klienta OAuth 2,0 nebo tok udělení autorizačního kódu. Další informace najdete [v žádosti OAuth 2,0 ve službě Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Ověřování na základě role (RBAC)** : skupiny zabezpečení slouží k provádění RBAC v Azure AD Graph API. Například pokud chcete zjistit, zda má uživatel přístup ke konkrétnímu prostředku, aplikace může zavolat operaci [členství ve skupině (přenositelný)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) , která vrátí hodnotu true nebo false.
* **Rozdílový dotaz**: rozdílový dotaz umožňuje sledovat změny v adresáři mezi dvěma časovými obdobími, aniž by bylo nutné provádět časté dotazy Graph API služby Azure AD. Tento typ požadavku vrátí pouze změny provedené mezi předchozím požadavkem rozdílového dotazu a aktuálním požadavkem. Další informace najdete v tématu [dotaz na rozdíl od služby Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Přípony adresářů**: můžete přidat vlastní vlastnosti do objektů adresáře bez nutnosti externího úložiště dat. Pokud například vaše aplikace vyžaduje vlastnost Skype ID pro každého uživatele, můžete novou vlastnost zaregistrovat v adresáři a bude k dispozici pro použití na všech objektech uživatele. Další informace najdete v tématu [rozšíření schématu adresáře Graph API služby Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Zabezpečeno obory oprávnění**: Azure AD Graph API zveřejňuje rozsahy oprávnění, které umožňují zabezpečený přístup k datům Azure AD pomocí OAuth 2,0. Podporuje různé typy klientských aplikací, mezi které patří:
  
  * uživatelská rozhraní, kterým je udělen delegovaný přístup k datům prostřednictvím autorizace od přihlášeného uživatele (delegovaný)
  * aplikace služby/démon, které fungují na pozadí bez přihlášeného uživatele, jsou přítomni a používají řízení přístupu na základě rolí definované aplikací.
    
    Delegovaná i oprávnění aplikací reprezentují oprávnění zveřejněné Graph API Azure AD a klientské aplikace je můžou požadovat prostřednictvím funkcí pro oprávnění k registraci aplikací v [Azure Portal](https://portal.azure.com). [Obory oprávnění Graph API Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) poskytují informace o tom, co je k dispozici pro použití v klientské aplikaci.

## <a name="scenarios"></a>Scénáře

Azure AD Graph API umožňuje mnoho scénářů aplikací. Nejběžnější jsou následující scénáře:

* **Obchodní aplikace (jedna tenant)** : v tomto scénáři funguje vývojář podniku pro organizaci, která má předplatné Office 365. Vývojář vytváří webovou aplikaci, která spolupracuje se službou Azure AD za účelem provádění úloh, jako je například přiřazení licence uživateli. Tato úloha vyžaduje přístup k Graph API Azure AD, takže vývojář zaregistruje jednu klientskou aplikaci v Azure AD a nakonfiguruje oprávnění ke čtení a zápisu pro Graph API Azure AD. Pak je aplikace nakonfigurována tak, aby používala buď vlastní přihlašovací údaje, nebo uživatele aktuálně přihlášeného uživatele k získání tokenu pro volání Graph API služby Azure AD.
* **Aplikace typu software jako služba (víceklientské rozhraní)** : v tomto scénáři vyvíjí nezávislý dodavatel softwaru (ISV) hostovanou webovou aplikaci s více klienty, která poskytuje funkce pro správu uživatelů pro jiné organizace, které používají Azure AD. Tyto funkce vyžadují přístup k objektům adresáře, takže aplikace musí volat Graph API služby Azure AD. Vývojář aplikaci zaregistruje ve službě Azure AD, nakonfiguruje ji tak, aby vyžadovala oprávnění ke čtení a zápisu pro Azure AD Graph API, a pak umožňuje externí přístup, aby ostatní organizace mohli souhlasit s používáním aplikace ve svém adresáři. Když se uživatel v jiné organizaci poprvé ověřuje v aplikaci, zobrazí se dialogové okno pro vyjádření souhlasu s oprávněními, které aplikace požaduje. Udělení souhlasu pak aplikaci poskytne požadovaná oprávnění k Azure AD Graph API v adresáři uživatele. Další informace o rozhraní pro vyjádření souhlasu najdete v tématu [Přehled rozhraní pro vyjádření souhlasu](consent-framework.md).

## <a name="next-steps"></a>Další kroky

Chcete-li začít používat Graph API Azure Active Directory, přečtěte si následující témata:

* [Průvodce rychlým startem pro Azure AD Graph API](active-directory-graph-api-quickstart.md)
* [Dokumentace k REST Azure AD graphu](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
