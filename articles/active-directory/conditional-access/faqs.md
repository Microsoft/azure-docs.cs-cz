---
title: Časté otázky podmíněného přístupu služby Azure Active Directory | Dokumenty společnosti Microsoft
description: Získejte odpovědi na nejčastější dotazy týkající se podmíněného přístupu ve službě Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6842338bd27e4bea3436f0b249380ab773d60de6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368084"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Časté otázky podmíněného přístupu služby Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>Které aplikace pracují se zásadami podmíněného přístupu?

Informace o aplikacích, které fungují se zásadami podmíněného přístupu, najdete v [tématu Aplikace a prohlížeče, které používají pravidla podmíněného přístupu ve službě Azure Active Directory](concept-conditional-access-cloud-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Jsou zásady podmíněného přístupu vynuceny pro spolupráci B2B a uživatele typu Host?

Zásady jsou vynuceny pro uživatele spolupráce mezi podniky (B2B). V některých případech však uživatel nemusí být schopen splnit požadavky zásad. Například organizace uživatele typu Host nemusí podporovat vícefaktorové ověřování. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Platí zásady SharePointu Online taky na OneDrivu pro firmy?

Ano. Zásady SharePointu Online platí taky pro OneDrive pro firmy.

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Proč nemůžu nastavit zásady přímo u klientských aplikací, jako je Word nebo Outlook?

Zásady podmíněného přístupu nastavují požadavky pro přístup ke službě. Je vynuceno při ověřování této služby dojde. Zásada není nastavena přímo v klientské aplikaci. Místo toho se použije, když klient volá službu. Zásada nastavená na SharePointu se například vztahuje na klienty volající na SharePoint. Zásada nastavená na Serveru Exchange se vztahuje na aplikaci Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Platí zásady podmíněného přístupu pro účty služeb?

Zásady podmíněného přístupu platí pro všechny uživatelské účty. To zahrnuje uživatelské účty, které se používají jako účty služeb. Účet služby, který běží bez obslužné rutiny, často nemůže splňovat požadavky zásad podmíněného přístupu. Může být například vyžadováno vícefaktorové ověřování. Účty služeb lze vyloučit ze zásad pomocí nastavení správy zásad podmíněného přístupu. 

## <a name="are-microsoft-graph-apis-available-for-configuring-conditional-access-policies"></a>Jsou rozhraní API aplikace Microsoft Graph k dispozici pro konfiguraci zásad podmíněného přístupu?

V současné době ne. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Jaké jsou výchozí zásady vyloučení pro nepodporované platformy zařízení?

V současné době jsou zásady podmíněného přístupu selektivně vynucovány na uživatele zařízení se systémem iOS a Android. Aplikace na jiných platformách zařízení nejsou ve výchozím nastavení ovlivněny zásadami podmíněného přístupu pro zařízení se systémem iOS a Android. Správce klienta se může rozhodnout přepsat globální zásady a zakázat přístup uživatelům na platformách, které nejsou podporovány.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Jak fungují zásady podmíněného přístupu pro Microsoft Teams?

Microsoft Teams do značné míry spoléhá na Exchange Online a SharePoint Online pro základní scénáře produktivity, jako jsou schůzky, kalendáře a sdílení souborů. Zásady podmíněného přístupu, které jsou nastavené pro tyto cloudové aplikace, platí pro Microsoft Teams, když se uživatel přihlásí přímo do Microsoft Teams.

Microsoft Teams se taky podporuje samostatně jako cloudová aplikace v zásadách podmíněného přístupu Azure Active Directory. Zásady podmíněného přístupu, které jsou nastavené pro cloudovou aplikaci, platí pro Microsoft Teams, když se uživatel přihlásí. Bez správných zásad v jiných aplikacích, jako jsou Exchange Online a SharePoint Online, však uživatelé mohou mít stále přímý přístup k těmto prostředkům.

Desktopoví klienti Microsoft Teams pro Windows a Mac podporují moderní ověřování. Moderní ověřování přináší přihlášení na základě Azure Active Directory Authentication Library (ADAL) do klientských aplikací Microsoft Office napříč platformami.

## <a name="next-steps"></a>Další kroky

- Pokud chcete nakonfigurovat zásady podmíněného přístupu pro vaše prostředí, přečtěte si [doporučené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md). 
