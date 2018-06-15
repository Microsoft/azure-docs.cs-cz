---
title: Azure Active Directory Graph API | Microsoft Docs
description: Přehled a rychlé spuštění Průvodce pro Azure AD Graph API, která umožňuje programový přístup ke službě Azure AD prostřednictvím koncových bodů rozhraní REST API.
services: active-directory
documentationcenter: ''
author: mtillman
manager: mtillman
editor: mbaldwin
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: 4b4f698042f6688e3db484f7d96ccfb06c5cdd4f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158009"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API
> [!IMPORTANT]
> Důrazně doporučujeme pro přístup k prostředkům Azure Active Directory použít [Microsoft Graph](https://graph.microsoft.io/) místo Azure AD Graph API. Náš vývojový program se nyní soustředí na Microsoft Graph a pro Azure AD Graph API nejsou plánovaná žádná další vylepšení. Existuje velmi omezený počet scénářů, pro které může být Azure AD Graph API stále vhodné. Další informace najdete v příspěvku [Microsoft Graph nebo Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blogu na webu Office Dev Center.
> 
> 

Azure Active Directory Graph API zajišťují programový přístup ke službě Azure AD prostřednictvím koncových bodů rozhraní REST API. Aplikace můžete použít Azure AD Graph API k provedení vytvářet, číst, aktualizovat a odstraňovat operace na data adresáře a objekty. Například Azure AD Graph API podporuje následující běžných operací pro objekt uživatele:

* Vytvoření nového uživatele v adresáři
* Získat podrobné vlastnosti uživatele, jako je například jejich skupin
* Aktualizovat vlastnosti uživatele, například jejich umístění a telefonní číslo, nebo změnit své heslo
* Zkontrolovat členství ve skupinách uživatele pro přístup na základě rolí
* Zakažte účet uživatele nebo zcela odstranit

Kromě toho můžete provést podobnými operacemi na jiné objekty, jako jsou skupiny a aplikace. Pro volání rozhraní API Azure AD Graph v adresáři, musí být zaregistrován aplikaci s Azure AD. Aplikace musí také udělen přístup k Azure AD Graph API. Tento přístup se obvykle dosahuje prostřednictvím k toku souhlasu uživatele nebo správce.

Chcete-li začít používat Azure Active Directory Graph API, přečtěte si téma [Azure AD Graph API rychlé spuštění průvodce](active-directory-graph-api-quickstart.md), nebo můžete zobrazit [interaktivní referenční dokumentace rozhraní Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Funkce
Azure AD Graph API poskytuje následující funkce:

* **Koncové body REST API**: Azure AD Graph API je služba RESTful skládá z koncových bodů, které jsou přístupné pomocí standardní požadavky HTTP. Azure AD Graph API podporuje typy obsahu XML nebo Javascript Object Notation (JSON) pro požadavky a odpovědi. Další informace najdete v tématu [Azure AD Graph REST API – referenční informace](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Ověřování s Azure AD**: každý požadavek do Azure AD Graph API musí být ověřeny připojením JSON Web Token (JWT) v hlavičce autorizace požadavku. Tento token se získávají pomocí vytváření požadavku na koncový bod tokenu Azure AD a poskytnutí platné přihlašovací údaje. Můžete použít tok přihlašovacích údajů klienta OAuth 2.0 nebo tok k získání tokenu pro volání grafu poskytování autorizačních kódů. Další informace najdete [OAuth 2.0 ve službě Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Na základě rolí autorizace (RBAC)**: skupiny zabezpečení jsou používány k provádění RBAC v Azure AD Graph API. Například, pokud chcete určit, zda má uživatel přístup ke konkrétní prostředek, můžete volat aplikace [zkontrolovat členství ve skupině (přenosné)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) operaci, která vrátí hodnotu true nebo false.
* **Rozdílovou dotazu**: rozdílové dotazu umožňuje sledovat změny v adresáři mezi dvěma časových období bez nutnosti provádět časté dotazy k Azure AD Graph API. Tento typ požadavku vrátí pouze změny provedené mezi předchozí požadavek rozdílové dotazu a aktuální žádost. Další informace najdete v tématu [Azure AD Graph API rozdílové dotazu](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Rozšíření adresáře**: můžete přidat vlastní vlastnosti objektů adresáře bez nutnosti externím úložišti. Například pokud vaše aplikace vyžaduje Skype ID vlastnosti pro každého uživatele, můžete zaregistrovat nové vlastnosti v adresáři a bude k dispozici pro použití na každý objekt uživatele. Další informace najdete v tématu [Azure AD Graph API rozšíření schématu služby Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Zabezpečené obory oprávnění**: Azure AD Graph API zpřístupní obory oprávnění, které umožňují zabezpečený přístup k datům Azure AD pomocí OAuth 2.0. Podporuje celou řadu typů aplikace klienta, včetně:
  
  * uživatelská rozhraní, které jsou uvedeny Delegovaný přístup k datům prostřednictvím autorizace od přihlášeného uživatele (delegovaný)
  * Služba nebo démon aplikace, které fungují na pozadí bez přihlášeného uživatele být přítomen a pomocí řízení přístupu na základě rolí definované aplikací
    
    Obě delegovat a oprávnění aplikací představují oprávnění vystavené Azure AD Graph API a může požadovat klientské aplikace prostřednictvím funkce oprávnění registrace aplikace [portál Azure](https://portal.azure.com). [Azure AD Graph API oprávnění obory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) poskytuje informace o co je k dispozici pro použití klientské aplikace.

## <a name="scenarios"></a>Scénáře
Azure AD Graph API umožňuje mnoho scénářů s aplikací. Nejobvyklejší jsou následující scénáře:

* **Obchodní (jednoho klienta) aplikace**: V tomto scénáři funguje vývojář enterprise pro organizaci, která má předplatné služeb Office 365. Vývojář se vytváření webové aplikace, která komunikuje se službou Azure AD k provádění úloh takové přiřazení licence pro uživatele. Tato úloha vyžaduje přístup k Azure AD Graph API, tak, aby vývojář zaregistruje jednoho klienta aplikace v Azure AD a nakonfiguruje oprávnění čtení a zápisu pro Azure AD Graph API. Pak aplikace je nakonfigurovaná k použití svoje vlastní přihlašovací údaje nebo těch, které aktuálně přihlášení uživatele k získání tokenu pro volání rozhraní Azure AD Graph API.
* **Software jako služba aplikace (víceklientské)**: V tomto scénáři je nezávislý dodavatel softwaru (ISV) vývoj hostované víceklientské webové aplikace, která poskytuje funkce správy uživatele pro jiné organizace, které používají Azure AD. Tyto funkce vyžadují přístup k objektům adresář, a proto aplikace musí volat Azure AD Graph API. Vývojář zaregistruje aplikaci ve službě Azure AD, nakonfiguruje se vyžadují pro čtení a oprávnění pro Azure AD Graph API pro zápis a pak umožňuje externí přístup, takže se můžete k používání aplikace v jejich adresář souhlas jiných organizací. Při ověření uživatele v jiné organizaci k aplikaci poprvé, zobrazí se dialogové okno souhlasu s oprávněními, které aplikace požaduje. Udělení souhlasu pak získáte aplikace ty požadovaná oprávnění k Azure AD Graph API v adresáři uživatele. Další informace o rozhraní souhlasu najdete v tématu [přehled rozhraní souhlas](active-directory-integrating-applications.md).

## <a name="see-also"></a>Viz také
[Průvodce rychlým zahájením Azure AD Graph API](active-directory-graph-api-quickstart.md)

[Dokumentace k Azure AD Graph REST](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Příručka pro vývojáře pro službu Azure Active Directory](active-directory-developers-guide.md)

