---
title: Azure Active Directory, podmíněného přístupu, nejčastější dotazy | Dokumentace Microsoftu
description: Získejte odpovědi na nejčastější dotazy týkající se podmíněného přístupu v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: e9ce5e1d86f6c1756f8871dc45d60d36cea74515
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629289"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory, podmíněného přístupu, nejčastější dotazy

## <a name="which-applications-work-with-conditional-access-policies"></a>Aplikace, které podporují zásady podmíněného přístupu?

Informace o aplikacích, které pracují se zásadami podmíněného přístupu najdete v tématu [aplikací a prohlížečů, které používají pravidla podmíněného přístupu v Azure Active Directory](technical-reference.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Vynucují zásady podmíněného přístupu pro spolupráci B2B a uživatele typu Host?

Zásady se vynucují pro uživatele spolupráce business-to-business (B2B). V některých případech nemusí být schopen dál uspokojit požadavky na zásady uživatele. Uživatel typu Host organizace například nemusí podporovat ověřování službou Multi-Factor Authentication. 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Zásady Sharepointu Online také vztahuje na OneDrive pro firmy?

Ano. Zásady Sharepointu Online platí také pro OneDrive pro firmy.


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Proč nelze nastavit zásadu pro klientské aplikace, jako je Word nebo Outlook?

Zásady podmíněného přístupu Nastaví požadavky pro přístup k službě. Je vynuceno, když dojde k ověření na tuto službu. Zásady není nastavena přímo v klientské aplikaci. Místo toho se použije, když klient zavolá službu. Například zásadám nastaveným na webu služby SharePoint se vztahuje na klienty volání služby SharePoint. Zásady nastavení na serveru Exchange platí pro aplikaci Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Použít zásady podmíněného přístupu k účtům služeb?

Zásady podmíněného přístupu platí pro všechny uživatelské účty. To zahrnuje uživatelské účty, které se používají jako účty služeb. Účet služby, na kterém běží bezobslužné často, nelze splňují požadavky zásad podmíněného přístupu. Ověřování službou Multi-Factor Authentication může být například povinné. Účty služeb se můžete vyloučit ze zásad s použitím nastavení zásad řízení podmíněného přístupu. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Jsou k dispozici ke konfiguraci zásad podmíněného přístupu rozhraní Graph API?

V současné době ne. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Co jsou zásady výchozí vyloučení pro platformy nepodporované zařízení?

V současné době se pro uživatele zařízení s Iosem a androidem selektivně vynucují zásady podmíněného přístupu. Aplikace na jiných platformách zařízení, ve výchozím nastavení, neovlivní zásady podmíněného přístupu pro iOS a androidem. Správce tenanta můžete zvolit, aby globální zásady, které chcete zakázat přístup uživatele na platformách, které nejsou podporovány.


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Jak fungují zásady podmíněného přístupu pro Microsoft Teams?

Spoléhá Microsoft Teams pro základní scénáře produktivity, jako je schůzky, kalendáře a sdílení souborů na Exchange Online a SharePoint Online. Zásady podmíněného přístupu, které jsou nastavené pro tyto cloudové aplikace platí pro Microsoft Teams, když se uživatel přihlašuje přímo do Microsoft Teams.

Microsoft Teams také je podporovaný samostatně jako cloudové aplikace v rámci zásad podmíněného přístupu Azure Active Directory. Zásady podmíněného přístupu, které jsou nastavené pro cloudové aplikace platí pro Microsoft Teams, když se uživatel přihlásí. Ale bez správné zásady, které na jiné aplikace, jako je Exchange Online a SharePoint Online uživatelé mohou moct dál přístup k těmto prostředkům.

Klienti klasické pracovní plochy Microsoft Teams pro Windows a Mac podporují moderní ověřování. Moderní ověřování poskytuje přihlašování založené na Azure Active Directory ověřování Library (ADAL) pro klientské aplikace Microsoft Office napříč platformami.