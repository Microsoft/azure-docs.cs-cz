---
title: Kam Azure AD ukládá data identit pro zákazníky, Evropského | Microsoft Docs
description: Informace o kde Microsoft Azure Active Directory ukládá data související s identity pro jeho Evropského zákazníky.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.custom: it-pro
ms.openlocfilehash: 19dc163dbb6dd296a417f5c313a36c7f7c9e50d7
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="where-does-microsoft-azure-active-directory-azure-ad-store-identity-data-for-european-customers"></a>Kde Microsoft Azure Active Directory (Azure AD) ukládá data identit pro zákazníky, Evropského
Azure AD umožňuje ke správě identit uživatelů a k vytvoření zásad přístupu řízené intelligence, které pomáhají zabezpečit prostředkům vaší organizace. Identity data jsou uložena v umístění, která je založená na adresu vaší organizace, které získáte při přihlášení k službě. Například při přihlášení k odběru Office 365 nebo Azure. Konkrétní informace o vaší identity je mají ukládat data, můžete použít [kde je umístěný data?](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located) části Trust Center společnosti Microsoft.

Zatímco většina Azure data související s AD Evropského identit zůstává v datových centrech Evropského, existují pěti atributů související s uživatelem, které se obvykle ukládají v datových centrech US. Tyto atributy jsou GivenName, příjmení, userPrincipalName, domény a PasswordHash. Atribut PasswordHash může být výjimku a nebyly uložené v USA, pokud někdo použije místní, metoda federovaného ověřování, která zastaví PasswordHash hodnotu z synchronizuje se službou Azure AD. Kromě toho je provozní, specifickou pro službu data, která vyžaduje pro normální operace služby Azure AD, která je uložena v USA a neobsahuje žádné osobní údaje.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Data uložená mimo Evropského datových center pro Evropského zákazníky

Většina Azure data související s AD Evropského identit, pro organizace s Evropských adresy, zůstává v Evropského datových centrech. Azure AD data, která nejsou uloženy v Evropského datovými centry, zahrnují:

- **Spojené s atributy**

    Následující atributy spojené s bude replikován do Spojených států:

    - givenName
    - příjmení
    - userPrincipalName
    - Doména
    - PasswordHash
    - sourceAnchor
    - accountEnabled
    - passwordPolicies
    - StrongAuthenticationRequirement
    - ApplicationPassword
    - IDENTIFIKÁTOR PUID

- **Microsoft Azure Multi-Factor authentication (MFA) a Azure AD samoobslužné resetování hesla (SSPR)**
    
    MFA ukládá všechna uživatelská data na rest v Evropského datových centrech. Ale některé vícefaktorového ověřování specifických pro službu data jsou uložena v USA, včetně:
    
    - Dvoufaktorové ověřování a jeho souvisejících osobních dat může být uložen v USA Pokud používáte MFA nebo SSPR.
        - USA prostředníci může dokončit všechny dvoufaktorové ověřování pomocí telefonní hovory nebo SMS.
        - Nabízená oznámení pomocí aplikace Microsoft Authenticator vyžadují oznámení ze služby oznámení od výrobce (Apple nebo Google), což může být mimo Evropu.
        - Kódy OATH jsou vždy ověřit v USA 
    - Některé MFA a SSPR protokoly se ukládají v USA, 30 dní, bez ohledu na typ ověřování.

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    Azure AD B2C ukládá všechna uživatelská data na rest v Evropského datových centrech. Operační protokoly (s osobní data odebraná) však zůstanou v umístění, ze kterých osoba přistupuje služby. Například pokud uživatel B2C přistupovat ke službě v USA, operační protokoly, které se zůstat v USA Kromě toho veškerá data konfigurace zásad neobsahující osobní data budou uložena pouze v USA Další informace o konfiguraci zásad najdete v tématu [Azure Active Directory B2C: integrovaných zásad](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-reference-policies) článku.

- **Microsoft Azure Active Directory s B2B (Azure AD s B2B)** 
    
    Azure AD s B2B ukládá všechna uživatelská data na rest v Evropského datových centrech. B2B však ukládá metadata jeho nejsou osobní v tabulkách v datových centrech US. Tato tabulka obsahuje pole, jako je redeemUrl, invitationTicket, prostředků klienta Id, InviteRedirectUrl a InviterAppId.

- **Microsoft Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS ukládá data uživatele ve stejném umístění jako zákazník vybrané virtuální síť Azure. Ano Pokud síť není mimo Evropu, data jsou replikovat a uložené mimo Evropu.

- **Služby a aplikace, které jsou integrované s Azure AD**

    Všechny služby a aplikace, které se integrují s Azure AD mají přístup k datům identity. Vyhodnoťte jednotlivé služby a aplikace k určení zpracování dat identity tím, že specifické služby a aplikace, a zda splňují požadavky na úložiště dat vaší společnosti.

    Další informace o sídle dat služby společnosti Microsoft najdete v tématu [kde je umístěný data?](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located) části Trust Center společnosti Microsoft.

## <a name="next-steps"></a>Další postup
Další informace o všech funkcí a funkcí popsaných výše naleznete v článcích.
- [Začínáme s Azure Active Directory](get-started-azure-ad.md)
- [Co je služba Multi-Factor Authentication?](https://docs.microsoft.com/en-us/azure/active-directory/authentication/multi-factor-authentication)
- [Azure AD samoobslužného resetování hesel](https://docs.microsoft.com/en-us/azure/active-directory/authentication/active-directory-passwords-overview)
- [Co je Azure Active Directory B2C?](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview)
- [Co je spolupráce B2B ve službě Azure AD?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Doména služby Azure Active Directory (AD)](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-overview)
