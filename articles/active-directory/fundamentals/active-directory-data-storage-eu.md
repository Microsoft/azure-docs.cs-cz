---
title: Kde Azure AD uchovává data identit pro evropské zákazníky | Microsoft Docs
description: Zjistěte, kde Microsoft Azure Active Directory uchovává data související s identitami pro své evropské zákazníky.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/17/2018
ms.custom: it-pro
ms.openlocfilehash: d0015f40714b639d15245827ae0da3ca0f132df4
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733393"
---
# <a name="where-does-microsoft-azure-active-directory-azure-ad-store-identity-data-for-european-customers"></a>Kde Microsoft Azure Active Directory (Azure AD) uchovává data identit pro evropské zákazníky
Azure AD pomáhá spravovat identity uživatelů a vytvářet zásady přístupu s využitím inteligentních funkcí, které pomáhají zabezpečit prostředky vaší organizace. Data identit se uchovávají v umístění na základě adresy vaší organizace, kterou jste uvedli při přihlášení k odběru služby. Například při přihlášení k odběru Office 365 nebo Azure. Konkrétní informace o tom, kde se uchovávají data vašich identit, najdete v části [Jaké je umístění vašich dat?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v Centru zabezpečení Microsoftu.

Přestože většina dat evropských identit souvisejících s Azure AD zůstává v evropských datacentrech, existuje pět atributů souvisejících s uživateli, které se obvykle uchovávají v datacentrech v USA. Tyto atributy jsou: GivenName (Jméno), Surname (Příjmení), userPrincipalName (Hlavní název uživatele), Domain (Doména) a PasswordHash (Hodnota hash hesla). Atribut PasswordHash může být výjimkou a nemusí se uchovávat v USA v případě, že někdo využívá metodu místního federovaného ověřování, která zastaví synchronizaci hodnoty PasswordHash s Azure AD. Kromě toho existují určitá provozní data pro konkrétní služby potřebná pro normální provoz Azure AD, která se uchovávají v USA a nezahrnují žádné osobní údaje.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Data pro evropské zákazníky uložená mimo evropská datacentra

Většina dat evropských identit souvisejících s Azure AD pro organizace s evropskou adresou zůstává v evropských datacentrech. Mezi data Azure AD, která se neuchovávají v evropských datacentrech, patří:

- **Atributy související s identitou**

    Následující atributy související s identitou se budou replikovat do USA:

    - GivenName (Jméno)
    - příjmení
    - userPrincipalName (Hlavní název uživatele)
    - Domain (Doména)
    - PasswordHash (Hodnota hash hesla)
    - SourceAnchor (Zdrojové ukotvení)
    - AccountEnabled (Povolený účet)
    - PasswordPolicies (Zásady hesel)
    - StrongAuthenticationRequirement (Požadavek na silné ověřování)
    - ApplicationPassword (Heslo aplikace)
    - PUID

- **Microsoft Azure Multi-Factor Authentication (MFA) a samoobslužné resetování hesla (SSPR) Azure AD**
    
    MFA uchovává všechna neaktivní uložená data uživatelů v evropských datacentrech. Určitá data specifická pro službu MFA se však uchovávají v USA:
    
    - Dvoufaktorové ověřování a související osobní údaje se můžou uchovávat v USA, pokud využíváte MFA nebo SSPR.
        - Veškeré dvoufaktorové ověřování pomocí telefonních hovorů nebo SMS můžou vykonávat operátoři v USA.
        - Nabízená oznámení prostřednictvím aplikace Microsoft Authenticator vyžadují oznámení ze služby oznámení výrobce (Apple nebo Google), která se může nacházet mimo Evropu.
        - Kódy OATH se vždy ověřují v USA. 
    - Určité protokoly MFA a SSPR se po dobu 30 dnů uchovávají v USA, a to bez ohledu na typ ověřování.

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    Azure AD B2C uchovává všechna neaktivní uložená data uživatelů v evropských datacentrech. Operační protokoly (z nichž se odeberou osobní údaje) však zůstávají v umístění, odkud uživatel přistupuje ke službám. Pokud například uživatel B2C přistupuje ke službě v USA, zůstanou operační protokoly v USA. Kromě toho se výhradně v USA uchovávají veškerá konfigurační data zásad, která neobsahují osobní údaje. Další informace o konfiguracích zásad najdete v článku [Azure Active Directory B2C: Předdefinované zásady](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    Azure AD B2B uchovává všechna neaktivní uložená data uživatelů v evropských datacentrech. Jiná než osobní metadata však B2B uchovává v tabulkách v rámci datacenter v USA. Tato tabulka obsahuje pole jako redeemUrl, invitationTicket, resource tenant Id, InviteRedirectUrl a InviterAppId.

- **Microsoft Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS uchovává data uživatelů ve stejném umístění jako virtuální síť Azure vybranou zákazníkem. Takže pokud se tato síť nachází mimo Evropu, data se replikují a uchovávají mimo Evropu.

- **Služby a aplikace integrované s Azure AD**

    Všechny služby a aplikace, které se integrují s Azure AD, mají přístup k datům identit. Vyhodnoťte jednotlivé služby a aplikace a určete, jak konkrétní služby a aplikace zpracovávají data identit a jestli splňují požadavky vaší společnosti na ukládání dat.

    Další informace o rezidenci dat služeb Microsoftu najdete v části [Jaké je umístění vašich dat?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v Centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další postup
Další informace o všech výše popsaných funkcích najdete v těchto článcích.
- [Co je Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
- [Samoobslužné resetování hesla Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)
- [Co je Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Co je spolupráce B2B ve službě Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
