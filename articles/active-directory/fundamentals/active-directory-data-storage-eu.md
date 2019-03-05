---
title: Ukládání dat identity pro zákazníky, Evropského – Azure Active Directory | Dokumentace Microsoftu
description: Přečtěte si o kde Azure Active Directory souvisejícím s identitou data ukládá po dobu jeho Evropského zákazníky.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b21f82dc0a1eb8edf571da13e0d34fecae5f401b
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337681"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Ukládání dat identity Evropského zákazníků v Azure Active Directory
Azure Active Directory (Azure AD) pomáhá spravovat identity uživatelů a vytvářet zásady přístupu využitím inteligentních funkcí, které pomáhají zabezpečit prostředky vaší organizace. Data identit se uchovávají v umístění na základě adresy vaší organizace, kterou jste uvedli při přihlášení k odběru služby. Například při přihlášení k odběru Office 365 nebo Azure. Konkrétní informace o tom, kde se uchovávají data vašich identit, najdete v části [Jaké je umístění vašich dat?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v Centru zabezpečení Microsoftu.

Přestože většinu služeb Azure AD související Evropského identity data zůstanou v evropských datových centrech, existují některé konkrétní služby, provozní data potřebná pro normální operace služby Azure AD, která jsou uložena v USA a neobsahuje žádné osobní údaje.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Data pro evropské zákazníky uložená mimo evropská datacentra

Většina dat evropských identit souvisejících s Azure AD pro organizace s evropskou adresou zůstává v evropských datacentrech. Azure AD data, která má uložená v evropských datových centrech a také replikují do datacentra v USA, zahrnují:

- **Microsoft Azure Multi-Factor Authentication (MFA) a samoobslužné resetování hesla (SSPR) Azure AD**
    
    MFA uchovává všechna neaktivní uložená data uživatelů v evropských datacentrech. Určitá data specifická pro službu MFA se však uchovávají v USA:
    
    - Dvoufaktorové ověřování a související osobní údaje se můžou uchovávat v USA, pokud využíváte MFA nebo SSPR.

        - Veškeré dvoufaktorové ověřování pomocí telefonních hovorů nebo SMS můžou vykonávat operátoři v USA.
    
        - Nabízená oznámení prostřednictvím aplikace Microsoft Authenticator vyžadují oznámení ze služby oznámení výrobce (Apple nebo Google), která se může nacházet mimo Evropu.
    
        - Kódy OATH se vždy ověřují v USA. 
    
    - Určité protokoly MFA a SSPR se po dobu 30 dnů uchovávají v USA, a to bez ohledu na typ ověřování.

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    Azure AD B2C uchovává všechna neaktivní uložená data uživatelů v evropských datacentrech. Operační protokoly (z nichž se odeberou osobní údaje) však zůstávají v umístění, odkud uživatel přistupuje ke službám. Pokud například uživatel B2C přistupuje ke službě v USA, zůstanou operační protokoly v USA. Kromě toho se výhradně v USA uchovávají veškerá konfigurační data zásad, která neobsahují osobní údaje. Další informace o konfiguraci zásad najdete v článku [Azure Active Directory B2C: Předdefinované zásady](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) článku.

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    Azure AD B2B uchovává všechna neaktivní uložená data uživatelů v evropských datacentrech. Jiná než osobní metadata však B2B uchovává v tabulkách v rámci datacenter v USA. Tato tabulka obsahuje pole jako redeemUrl, invitationTicket, resource tenant Id, InviteRedirectUrl a InviterAppId.

- **Microsoft Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS uchovává data uživatelů ve stejném umístění jako virtuální síť Azure vybranou zákazníkem. Takže pokud se tato síť nachází mimo Evropu, data se replikují a uchovávají mimo Evropu.

- **Služby a aplikace integrované s Azure AD**

    Všechny služby a aplikace, které se integrují s Azure AD, mají přístup k datům identit. Vyhodnoťte jednotlivé služby a aplikace a určete, jak konkrétní služby a aplikace zpracovávají data identit a jestli splňují požadavky vaší společnosti na ukládání dat.

    Další informace o rezidenci dat služeb Microsoftu najdete v části [Jaké je umístění vašich dat?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v Centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další postup
Další informace o všech vlastností a funkcí popsaných výše najdete v těchto článcích:
- [Co je Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Samoobslužné resetování hesla Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Co je Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Co je spolupráce B2B ve službě Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
