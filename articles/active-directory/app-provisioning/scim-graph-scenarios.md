---
title: Použití SCIM, Microsoft Graph a Azure AD ke zřízení uživatelů a obohacení aplikací s daty
description: Použití SCIM a Microsoft Graph dohromady ke zřízení uživatelů a rozšíření vaší aplikace s potřebnými daty.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/26/2020
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: b69e2c9b12b2db34f3eb70e54d2c6aede6b54784
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88235498"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Použití SCIM a Microsoft Graph společně ke zřízení uživatelů a rozšíření vaší aplikace s potřebnými daty

**Cílová skupina:** Tento článek je zaměřený na vývojáře, kteří sestavují aplikace, které se mají integrovat s Azure Active Directory (Azure AD). Pokud chcete používat aplikace, které už jsou integrované s Azure AD, jako je například Lupa, ServiceNow a DropBox, můžete tento článek přeskočit a přečíst si [kurzy](../saas-apps/tutorial-list.md) specifické pro aplikaci nebo zkontrolovat, [Jak služba zřizování funguje](./how-provisioning-works.md).

**Typické scénáře**

Azure AD poskytuje od služby Box službu pro zřizování a rozšiřitelnou platformu pro vytváření aplikací na. Rozhodovací strom popisuje, jak má vývojář použít [SCIM](https://aka.ms/scimoverview) a [Microsoft Graph](/graph/overview) k automatizaci zřizování. 

> [!div class="checklist"]
> * Automaticky vytvářet uživatele v mojí aplikaci
> * Automaticky odebrat uživatele z aplikace, když k nim nemají přístup
> * Integrujte moji aplikaci s více zprostředkovateli identity pro zřizování
> * Rozšířit svou aplikaci o data ze služeb Microsoftu, jako jsou týmy, Outlook a Office
> * Automatické vytváření, aktualizace a odstraňování uživatelů a skupin v Azure AD a Active Directory

![Rozhodovací strom grafu SCIM](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scénář 1: automatické vytváření uživatelů v mojí aplikaci
V dnešní době správci IT zřídí uživatele ručním vytvářením uživatelských účtů nebo pravidelným nahráváním souborů CSV do své aplikace. Proces je časově náročný pro zákazníky a zpomaluje přijetí naší aplikace. Vše Potřebuji jenom základní uživatelské informace, jako je jméno, e-mail a userPrincipalName, abyste mohli vytvořit uživatele. 

**Doporučení**: 
* Pokud vaši zákazníci používají různé zprostředkovatelů identity a nechcete spravovat synchronizační modul pro integraci s každým, podporují koncový bod SCIM splňující požadavky [/Users](https://aka.ms/scimreferencecode) . Vaši zákazníci budou moct tento koncový bod snadno použít k integraci se službou zřizování Azure AD a automaticky vytvářet uživatelské účty, když potřebují přístup. Koncový bod můžete vytvořit jednou a bude kompatibilní se všemi zprostředkovatelů identity. Další informace o tom, jak se uživatel vytvoří pomocí SCIM, najdete v níže uvedeném příkladu.
* Pokud vyžadujete, aby byla uživatelská data v objektu uživatele v Azure AD a dalších datech od Microsoftu, zvažte vytvoření koncového bodu SCIM pro zřizování uživatelů a volání do Microsoft Graph k získání zbytku dat. 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Scénář 2: Automatické odebrání uživatelů z mé aplikace
Zákazníci, kteří používají moji aplikaci, jsou zaměřené na zabezpečení a mají požadavky na zásady správného řízení pro odebrání účtů, pokud je už zaměstnanci nepotřebují. Jak můžu automatizovat zrušení zřizování z mé aplikace?

**Doporučení:** Podpora koncového bodu SCIM splňujícího požadavky/Users. Služba zřizování Azure AD pošle žádosti o zakázání a odstranění, když uživatel nebude mít přístup už. Doporučujeme, abyste podporovali zakazování i odstraňování uživatelů. V níže uvedených příkladech najdete informace o tom, jak se má požadavek na zakázání a odstranění zobrazit. 

Zakázat uživatele
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Odstranění uživatele
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scénář 3: automatizace správy členství ve skupinách v aplikaci
Moje aplikace spoléhá na skupiny pro přístup k různým prostředkům a zákazníci chtějí znovu použít skupiny, které mají ve službě Azure AD. Jak můžu importovat skupiny z Azure AD a nechat je aktualizovat, jakmile se členství změní?  

**Doporučení:** Podpora [koncového bodu](https://aka.ms/scimreferencecode)/Groups kompatibilního s SCIM. Služba zřizování Azure AD se postará o vytváření skupin a správu aktualizací členství ve vaší aplikaci. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scénář 4: rozšíření aplikace o data ze služeb Microsoftu, jako jsou týmy, Outlook a OneDrive
Moje aplikace je integrovaná do Microsoft teams a spoléhá na data zpráv. Kromě toho ukládáme soubory pro uživatele na OneDrivu. Jak můžu aplikaci rozšířit o data z těchto služeb a přes Microsoft?

**Doporučení:** [Microsoft Graph](/graph/) je vaším vstupním bodem pro přístup k datům Microsoftu. Každé zatížení zveřejňuje rozhraní API s daty, která potřebujete. Microsoft Graph se dá použít společně s [zřizováním SCIM](./use-scim-to-provision-users-and-groups.md) pro výše uvedené scénáře. SCIM můžete použít ke zřízení základních uživatelských atributů do vaší aplikace při volání do grafu, abyste získali všechna další data, která potřebujete. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scénář 5: sledování změn v rámci služeb Microsoftu, jako jsou týmy, Outlook a Azure AD
Potřebuji mít schopnost sledovat změny týmů a zpráv Outlooku a reagovat na ně v reálném čase. Jak mohu tyto změny načíst do mé aplikace?

**Doporučení:** Microsoft Graph poskytuje [oznámení o změnách](/graph/webhooks) a [sledování změn](/graph/delta-query-overview) pro různé prostředky. Všimněte si následujících omezení upozornění na změny:
- Pokud příjemce události potvrdí událost, ale z nějakého důvodu ji nedokáže zpracovat, může dojít ke ztrátě události.
- Pořadí, ve kterém byly změny přijaty, není zaručeno chronologicky.
- Oznámení o změnách neobsahují vždy [data prostředků](/graph/webhooks-with-resource-data) z výše uvedených důvodů. vývojáři často používají oznámení o změnách spolu se sledováním změn pro synchronizační scénáře. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scénář 6: zřízení uživatelů a skupin ve službě Azure AD
Moje aplikace vytvoří informace o uživateli, který potřebují v Azure AD. Může to být aplikace pro personální oddělení, než Správa pronájmu, komunikační aplikace, která vytvoří telefonní čísla pro uživatele nebo jinou aplikaci, která generuje data, která by byla užitečná v Azure AD. Pomocí těchto dat Návody naplnit záznam uživatele v Azure AD? 

**Doporučení** Microsoft Graph zpřístupňuje body/Users a/Groups, které můžete integrovat s dnešním účelem pro zřizování uživatelů do Azure AD. Upozorňujeme, že Azure Active Directory nepodporuje zápis uživatelů zpátky do služby Active Directory. 

> [!NOTE]
> Microsoft má službu zřizování, která vyžádá data z aplikací pro personální oddělení, jako je například Workday a SuccessFactors. Tyto integrace jsou sestavené a spravované Microsoftem. Pro registraci nové aplikace v rámci služby HR pro naši službu ji můžete požádat na [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests). 

## <a name="related-articles"></a>Související články

- [Projděte si dokumentaci Microsoft Graph synchronizace.](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrace vlastní aplikace v SCIM s Azure AD](use-scim-to-provision-users-and-groups.md)