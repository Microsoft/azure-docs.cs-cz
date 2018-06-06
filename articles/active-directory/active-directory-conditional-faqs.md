---
title: Azure Active Directory podmíněného přístupu nejčastější dotazy | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy týkající se podmíněného přístupu v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: ea93dab7df920f5a8ff8a1ae518b0bdbdcc80ae9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723809"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory podmíněného přístupu nejčastější dotazy

## <a name="which-applications-work-with-conditional-access-policies"></a>Aplikace, které podporují zásady podmíněného přístupu?

Informace o aplikacích, které pracují se zásadami podmíněného přístupu najdete v tématu [aplikací a prohlížeče, které používají pravidla podmíněného přístupu v Azure Active Directory](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Prosazují zásady podmíněného přístupu pro spolupráci B2B a uživatele typu Host?

Zásady se vynucují pro business-to-business (B2B) spolupráce uživatele. V některých případech nemusí být schopen dál uspokojit požadavky zásad uživatele. Například uživatel typu Host organizace nemusí podporovat službu Multi-Factor authentication. 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Zásady Sharepointu Online také platí pro OneDrive pro firmy?

Ano. Zásady Sharepointu Online platí také pro OneDrive pro firmy.


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Proč nelze nastavit zásadu pro klientské aplikace, jako je Word nebo Outlook?

Požadavky pro přístup k službě nastavuje zásady podmíněného přístupu. Prosazuje se při ověřování k této službě. Zásady není nastaven přímo na klientskou aplikaci. Místo toho se použije, když klient zavolá službu. Například zásadu nastavit na webu služby SharePoint se vztahuje na klienty, na které se volání služby SharePoint. Zásadu nastavit u Exchange platí pro aplikaci Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Platí pro účty služeb zásady podmíněného přístupu?

Zásady podmíněného přístupu platí pro všechny uživatelské účty. To zahrnuje uživatelské účty, které se používají jako účty služeb. Účet služby, který spouští bezobslužné často nelze vyhovět jejich požadavkům zásady podmíněného přístupu. Služba Multi-Factor authentication může být například vyžaduje. Účty služby můžete vyloučit ze zásady pomocí nastavení zásad řízení podmíněného přístupu. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Rozhraní Graph API jsou k dispozici ke konfiguraci zásad podmíněného přístupu?

V současné době není. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Co je výchozí zásady vyloučení pro platformy nepodporované zařízení?

V současné době se na uživatele se systémy iOS a zařízení se systémem Android selektivně vynucují zásady podmíněného přístupu. Aplikace na jiných platformách zařízení se ve výchozím nastavení, není vliv zásady podmíněného přístupu pro zařízení iOS a Android. Správce klienta můžete se rozhodnout přepsat globální zásady tak, aby zakázala přístup uživatelům na platformách, které nejsou podporovány.


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Jak fungují zásady podmíněného přístupu pro Teams společnosti Microsoft?

Microsoft Teams velice spoléhá na Exchange Online a SharePoint Online pro základní produktivitu scénáře, jako je schůzek, kalendáře a sdílení souborů. Zásady podmíněného přístupu, které jsou nastavené na těchto cloudových aplikací platí pro Microsoft Teams při uživatel se přihlásí přímo do Teams společnosti Microsoft.

Teams společnosti Microsoft je podporováno také samostatně jako cloudové aplikace v Azure Active Directory zásady podmíněného přístupu. Zásady podmíněného přístupu, které jsou nastavené pro cloudové aplikace použít u Teams společnosti Microsoft, když se uživatel přihlásí. Ale bez správných zásad na jiné aplikace, jako je Exchange Online a SharePoint Online uživatelé stále možné získat přímo přístup k tyto prostředky.

Klienti plocha Teams společnosti Microsoft pro systém Windows a Mac podporují moderní ověřování. Moderní ověřování poskytuje přihlašování založené na Azure Active Directory Authentication Library (ADAL) pro klientské aplikace Microsoft Office napříč platformami.