---
title: Azure Active Directory Nejčastější dotazy podmíněného přístupu | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy týkající se podmíněného přístupu v Azure Active Directory.
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
ms.openlocfilehash: baa44481c4641f69ead5335298316c837062d2c0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186053"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory Nejčastější dotazy týkající se podmíněného přístupu

## <a name="which-applications-work-with-conditional-access-policies"></a>Které aplikace pracují se zásadami podmíněného přístupu?

Informace o aplikacích, které pracují se zásadami podmíněného přístupu, najdete v tématu [aplikace a prohlížeče, které používají pravidla podmíněného přístupu v Azure Active Directory](concept-conditional-access-cloud-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Jsou pro spolupráci B2B a uživatele typu Host vynutily zásady podmíněného přístupu?

Pro uživatele spolupráce B2B (Business-to-Business) se vynutily zásady. V některých případech ale uživatel nemusí být schopný splnit požadavky zásad. Například organizace hostovaného uživatele nemusí podporovat službu Multi-Factor Authentication. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Vztahují se zásady SharePointu Online i na OneDrive pro firmy?

Ano. Zásady SharePointu Online platí i pro OneDrive pro firmy.

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Proč nejde nastavit zásadu přímo na klientských aplikacích, jako je Word nebo Outlook?

Zásada podmíněného přístupu nastavuje požadavky pro přístup ke službě. Vynutilo se při ověřování k této službě. Zásady nejsou nastaveny přímo v klientské aplikaci. Místo toho se použije, když klient zavolá službu. Například zásada nastavená na SharePointu se vztahuje na klienty, kteří volají SharePoint. Zásada nastavená na Exchangi se vztahuje na Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Vztahují se zásady podmíněného přístupu na účty služeb?

Zásady podmíněného přístupu se vztahují na všechny uživatelské účty. To zahrnuje uživatelské účty, které se používají jako účty služeb. Účet služby, který běží bez obsluhy, často nesplňuje požadavky zásad podmíněného přístupu. Například může být vyžadováno Multi-Factor Authentication. Účty služeb se dají vyloučit ze zásady pomocí nastavení správy zásad podmíněného přístupu. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Jsou k dispozici rozhraní Graph API pro konfiguraci zásad podmíněného přístupu?

V současné době ne. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Jaké jsou výchozí zásady vyloučení pro nepodporované platformy zařízení?

V současné době se zásady podmíněného přístupu selektivně vynutily u uživatelů zařízení s iOS a Androidem. Aplikace na jiných platformách zařízení jsou ve výchozím nastavení neovlivněné zásadami podmíněného přístupu pro zařízení s iOS a Androidem. Správce tenanta se může rozhodnout přepsat globální zásady, aby nepovolovaly přístup uživatelům na nepodporovaných platformách.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Jak fungují zásady podmíněného přístupu pro Microsoft Teams?

Microsoft Teams spoléhá na Exchange Online a SharePoint Online ve scénářích základní produktivity, jako jsou schůzky, kalendáře a sdílení souborů. Zásady podmíněného přístupu, které jsou nastavené pro tyto cloudové aplikace, platí pro Microsoft teams, když se uživatel přihlásí přímo do Microsoft Teams.

Microsoft Teams se podporuje taky samostatně jako cloudová aplikace v Azure Active Directory zásadách podmíněného přístupu. Zásady podmíněného přístupu, které jsou nastavené pro cloudovou aplikaci, platí pro Microsoft teams, když se uživatel přihlásí. Ale bez správných zásad na jiných aplikacích, jako je Exchange Online a uživatelé SharePointu Online, můžou mít stále přístup k těmto prostředkům přímo.

Klienti Microsoft Teams Desktop pro Windows a Mac podporují moderní ověřování. Moderní ověřování přináší přihlášení na základě knihovny Azure Active Directory Authentication Library (ADAL), která systém Microsoft Office klientské aplikace napříč platformami.

## <a name="next-steps"></a>Další kroky

- Pokud chcete nakonfigurovat zásady podmíněného přístupu pro vaše prostředí, přečtěte si [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md). 
