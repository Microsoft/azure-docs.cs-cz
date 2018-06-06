---
title: Azure Active Directory podmíněného přístupu zařízení zásady pro služby Office 365 | Microsoft Docs
description: Další informace o tom, jak zřídit zásady podmíněného přístupu zařízení, abyste měli podnikovým prostředkům informace zabezpečení, při zachování uživatele dodržování předpisů a přístup ke službám.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: cc385edb6bf0b850eb97f0f6a233dd5081acbb3e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723954"
---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>Active Directory podmíněného přístupu zařízení zásady pro služby Office 365

Podmíněný přístup vyžaduje pro práci na více místech. Zahrnuje službu Multi-Factor ověřeného uživatele, ověřené zařízení a kompatibilní zařízení, mezi dalších faktorů. V tomto článku především zaměříme na podmínek založených na zařízení, které vaše organizace může použít k vám pomohou řídit přístup ke službám Office 365. 

Podnikoví uživatelé mají přístup k Office 365 služby, jako je Exchange a SharePoint Online v práci nebo ve škole ze svých osobních zařízení. Budete chtít přístup zabezpečená. Můžete zřídit zařízení zásady podmíněného přístupu k podnikovým prostředkům lépe zabezpečit, současně poskytovat přístup ke službám pro uživatele, kteří používají kompatibilní zařízení. Můžete nastavit zásady podmíněného přístupu k Office 365 na portálu Microsoft Intune podmíněný přístup.

Azure Active Directory (Azure AD) vynucuje zásady podmíněného přístupu můžete líp zabezpečit přístup ke službám Office 365. Můžete vytvořit zásady podmíněného přístupu, které blokuje uživatele, který používá nekompatibilních zařízení z přístup ke službě Office 365. Uživatel musí splňovat zásady společnosti zařízení před udělením přístupu ke službě. Alternativně můžete vytvořit zásadu, která vyžaduje, aby uživatelé registrovat svá zařízení pro přístup do služby Office 365. Zásady můžete použít pro všechny uživatele v organizaci, nebo omezena na několika cílových skupin. Můžete přidat více cílových skupin na zásadu v čase.

Předpokladem pro vynucování zásad zařízení je, že uživatelé musí registrovat svá zařízení se službou registrace zařízení služby Azure AD. Můžete se rozhodnout zapnout vícefaktorové ověřování pro zařízení, která zaregistrovat službu Azure AD device registration service. Doporučuje se vícefaktorového ověřování pro službu Azure Active Directory device registration service. Po zapnutí ověřování Multi-Factor authentication, uživatelé, kteří registrovat svá zařízení se službou registrace zařízení služby Azure AD jsou postiženy pro dvoufaktorové ověřování.

## <a name="how-does-a-conditional-access-policy-work"></a>Jak funguje zásady podmíněného přístupu?

Při žádosti o přístup uživatelů k služby Office 365 ze zařízení podporované platformy, Azure AD ověřuje uživatele a zařízení. Azure AD uděluje přístup ke službě pouze, pokud uživatel odpovídá zásady nastavené pro službu. Uživatelé na zařízeních, která nejsou zaregistrovaná jsou uvedeny pokyny o tom, jak registraci a dodržování předpisů pro přístup k podnikovým službám Office 365. Uživatelé na zařízení iOS a Android musí zaregistrovat svá zařízení pomocí aplikace portál společnosti Intune. Když se uživatel registruje zařízení, zařízení je registrované v Azure AD a že je zaregistrováno pro správu zařízení a dodržování předpisů. Musíte použít službu registrace zařízení služby Azure AD s Microsoft Intune pro správu mobilních zařízení pro služby Office 365. Registrace zařízení je vyžadovaná pro uživatelů ke službám přístup k Office 365, když se vynucují zásady zařízení.

Když se uživatel úspěšně registruje zařízení, stane důvěryhodné zařízení. Azure AD dává ověřený uživatel jednoho přihlášení přístup k aplikacím společnosti. Azure AD vynucuje zásady podmíněného přístupu k udělení přístupu ke službě není pouze při prvním uživatel požaduje přístup, ale pokaždé, když uživatel obnoví žádost o přístup. Uživatel odepřen přístup ke službám, když se změní přihlašovací údaje, ztráty nebo odcizení zařízení nebo při podání žádosti pro obnovení nejsou splněny podmínky zásad.

## <a name="deployment-considerations"></a>Aspekty nasazování

Musíte použít službu Azure AD device registration service k registraci zařízení.

Když místních uživatelů jsou o být ověřen, Active Directory Federation Services (AD FS) (verze 1.0 a novějších verzí) se vyžaduje. Víceúrovňové ověřování pro připojení k pracovní ploše selže, když poskytovatele identit není schopen služby Multi-Factor authentication. Nelze například používání služby Multi-Factor authentication s AD FS 2.0. Ujistěte se, že místní služby AD FS funguje s vícefaktorovým ověřováním, a že je metoda platnou služby Multi-Factor authentication na místě před zapnutím vícefaktorového ověřování pro službu Azure AD device registration service. Například služby AD FS v systému Windows Server 2012 R2 má možnosti služby Multi-Factor authentication. Je třeba také nastavit metodu dodatečného ověřování platné (vícefaktorového ověřování) na serveru služby AD FS před zapnutím vícefaktorového ověřování pro službu Azure AD device registration service. Další informace o metodách podporované vícefaktorového ověřování ve službě AD FS najdete v tématu [konfigurace dalších metod ověřování pro službu AD FS](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs).

## <a name="next-steps"></a>Další postup

*   Odpovědi na časté otázky, naleznete v části [podmíněného přístupu nejčastější dotazy k Azure Active Directory](active-directory-conditional-faqs.md).
