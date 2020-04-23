---
title: Použití SCIM, Microsoft Graphu a zřizovací služby Azure AD pro zřizování uživatelů a obohacení aplikace o data, která potřebuje | Dokumenty společnosti Microsoft
description: Společné použití scim a microsoft graphu k zřizování uživatelů a obohacování aplikace o data, která potřebuje .
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 134237b66a803abaf07621112e3a4a518a3ae8a7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087618"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Společné používání SCIM a Microsoft Graphu k zřizování uživatelů a obohacování aplikace o data, která potřebuje

**Cílová skupina:** Tento dokument je určen pro vývojáře vytváření aplikací integrovaných s Azure AD. Pro ostatní, kteří chtějí integrovat stávající aplikace, jako je Zoom, ServiceNow a DropBox můžete přeskočit a zkontrolujte aplikace specifické [návody](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). 

**Typické scénáře**

> [!div class="checklist"]
> * Automatické vytváření uživatelů v mé aplikaci
> * Automatické odebrání uživatelů z mé aplikace, když už k ní nemají přístup
> * Integrace mé aplikace s více poskytovateli identit pro zřizování
> * Obohaťte aplikaci o data ze služeb Microsoftu, jako je Sharepoint, Outlook a Office.
> * Automatické vytváření, aktualizace a odstraňování uživatelů a skupin ve službě Azure AD a Active Directory

![Rozhodovací strom scim grafu](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scénář 1: Automatické vytváření uživatelů v mé aplikaci
Správci IT dnes ručně vytvářejí uživatelské účty v mé aplikaci pokaždé, když někdo potřebuje přístup nebo pravidelně nahrává soubory CSV. Tento proces je časově náročný pro zákazníky a zpomaluje přijetí mé žádosti. Vše, co potřebuji, je základní informace o [uživateli,](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) jako je jméno, e-mail a userPrincipalName k vytvoření uživatele. Kromě toho moji zákazníci používají různé ipadresy a nemám prostředky k údržbě synchronizačního modulu a vlastních integrací s každým idp. 

**Doporučení:** Podpora koncový bod kompatibilní s SCIM [/Users.](https://aka.ms/scimreferencecode) Vaši zákazníci budou moci snadno použít tento koncový bod k integraci se službou zřizování Azure AD a automaticky vytvářet uživatelské účty, když potřebují přístup. Můžete vytvořit koncový bod jednou a bude kompatibilní se všemi ipadresy, aniž by bylo nutné udržovat synchronizační modul. Podívejte se na ukázkový požadavek níže, jak by byl vytvořen uživatel.

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
Zákazníci, kteří používají moji aplikaci, jsou zaměřeni na zabezpečení a mají požadavky na zásady, aby odebrali účty, když je zaměstnanci již nepotřebují. Jak lze automatizovat deprovisioning z mé aplikace?

**Doporučení:** Podpora koncový bod kompatibilní s parametrem SCIM /Users. Služba zřizování Azure AD bude odesílat požadavky na zakázání a odstranění, když uživatel už nemá přístup. Doporučujeme podporovat zakázání i odstranění uživatelů. Podívejte se na příklady níže, jak vypadá požadavek na zakázání a odstranění. 

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

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scénář 3: Automatizace správy členství ve skupinách v mé aplikaci
Moje aplikace spoléhá na skupiny pro přístup k různým prostředkům a zákazníci chtějí znovu použít skupiny, které mají ve službě Azure AD. Jak můžu importovat skupiny z Azure AD a aktualizovat je, jak se mění členství?  

**Doporučení:** Podpora [koncového bodu](https://aka.ms/scimreferencecode)/Groups kompatibilního s SCIM . Služba zřizování Azure AD se postará o vytváření skupin a správu aktualizací členství ve vaší aplikaci. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scénář 4: Obohaťte aplikaci o data ze služeb Microsoftu, jako jsou Teams, Outlook a OneDrive.
Moje aplikace je integrována do Microsoft Teams a spoléhá na data zpráv. Kromě toho ukládáme soubory pro uživatele na OneDrivu. Jak mohu obohatit svou aplikaci o data z těchto služeb a napříč společností Microsoft?

**Doporučení:** [Microsoft Graph](https://docs.microsoft.com/graph/) je vstupní bod pro přístup k datům společnosti Microsoft. Každá úloha zveřejňuje api s daty, která potřebujete. Graf Microsoft lze použít spolu s [zřizování SCIM](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) pro výše uvedené scénáře. Pomocí scim můžete zřídit základní uživatelské atributy do aplikace při volání do grafu získat další data, která potřebujete. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scénář 5: Sledujte změny ve službách Microsoftu, jako jsou Teams, Outlook a Azure AD.
Musím být schopen sledovat změny v Teams a Outlook zprávy a reagovat na ně v reálném čase. Jak lze tyto změny zasunout do aplikace?

**Doporučení:** Microsoft Graph poskytuje [oznámení o změnách](https://docs.microsoft.com/graph/webhooks) a sledování změn pro různé prostředky. Všimněte si následujících omezení oznámení o změnách:
- Pokud příjemce události událost potvrdí událost, ale z jakéhokoli důvodu na ní nezareaguje, může dojít ke ztrátě události.
- Pokud příjemce události událost potvrdí událost, ale z jakéhokoli důvodu na ní nezareaguje, může dojít ke ztrátě události.
- Oznámení o změnách neobsahují vždy [data o prostředcích](https://docs.microsoft.com/graph/webhooks-with-resource-data) Z výše uvedených důvodů vývojáři často používají oznámení o změnách spolu se sledováním změn pro scénáře synchronizace. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scénář 6: Zřizování uživatelů a skupin ve službě Azure AD.
Moje aplikace vytvoří informace o uživateli, které zákazníci potřebují ve službě Azure AD. Může se jedná o hr aplikace než spravuje nábor, komunikační aplikace, která vytváří telefonní čísla pro uživatele, nebo jiné aplikace, která generuje data, která by byla cenná ve službě Azure AD. Jak naplním uživatelský záznam ve službě Azure AD těmito daty? 

**Doporučení** Graf Microsoftu zveřejňuje koncové body /Users a /Groups, se kterými můžete dnes integrovat a zřídit uživatele do Azure AD. Upozorňujeme, že služba Azure Active Directory nepodporuje zápis těchto uživatelů zpět do služby Active Directory. 

> [!NOTE]
> Společnost Microsoft má zřizovací službu, která získává data z hr aplikací, jako je Workday a SuccessFactors. Tyto integrace jsou vytvořeny a spravovány společností Microsoft. Pro připojení nové hr aplikace do naší služby, můžete si ji vyžádat na [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests). 

## <a name="related-articles"></a>Související články

- [Kontrola dokumentace k synchronizaci aplikace Microsoft Graph](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrace vlastní aplikace SCIM s Azure AD](use-scim-to-provision-users-and-groups.md)
