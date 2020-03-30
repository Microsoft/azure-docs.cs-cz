---
title: Úložiště dat identit pro evropské zákazníky – Azure AD
description: Přečtěte si, kde Služba Azure Active Directory ukládá data související s identitou pro své evropské zákazníky.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7360b11f41cc08c2beb3ffa227e1658881798502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75423001"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Úložiště dat identit pro evropské zákazníky ve službě Azure Active Directory
Data identity se ukládají službou Azure AD v geografickém umístění na základě adresy poskytnuté vaší organizací při přihlášení k odběru služby Microsoft Online, jako je Office 365 a Azure. Informace o tom, kde jsou uložena data vaší identity, naleznete v centru zabezpečení společnosti Microsoft v části [Kde se data nacházejí.](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

Pro zákazníky, kteří poskytli adresu v Evropě, Azure AD uchovává většinu dat identity v rámci evropských datových center. Tento dokument poskytuje informace o všech datech, která jsou uložená mimo Evropu službami Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Vícefaktorové ověřování Microsoft Azure (MFA)
    
- Všechny dvoufaktorové ověřování pomocí telefonních hovorů nebo SMS pocházejí z datových center v USA a jsou také směrovány globálními poskytovateli.
- Nabízená oznámení pomocí aplikace Microsoft Authenticator pocházejí z datových center v USA. Kromě toho mohou být do hry zahrnuty také služby specifické pro dodavatele zařízení a tyto služby mohou být mimo Evropu.
- Kódy OATH se vždy ověřují v USA. 

Další informace o tom, jaké informace o uživateli shromažďuje server Azure Multi-Factor Authentication Server (MFA Server) a cloudová služba Azure MFA, najdete v [tématu Shromažďování uživatelských dat azure vícefaktorového ověřování](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Data konfigurace zásad Azure AD B2C a kontejnery klíčů jsou uloženy v datových centrech v USA. Neobsahují žádné osobní údaje uživatelů. Další informace o konfiguracích zásad najdete v článku [Azure Active Directory B2C: Předdefinované zásady](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B ukládá pozvánky s informacemi o propojení uplatnění a přesměrování adres URL v datových centrech v USA. Kromě toho e-mailová adresa uživatelů, kteří se odhlásí z přijímání pozvánek B2B, jsou také uloženy v datových centrech v USA.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS uchovává data uživatelů ve stejném umístění jako virtuální síť Azure vybranou zákazníkem. Takže pokud se tato síť nachází mimo Evropu, data se replikují a uchovávají mimo Evropu.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federace na Microsoft Exchange Serveru 2013
    
- Identifikátor aplikace (AppID) – jedinečné číslo generované ověřovacím systémem Azure Active Directory k identifikaci organizací Exchange.
- Seznam schválených federovaných domén pro aplikaci
- Veřejný klíč podepisování tokenů aplikace 

Další informace o federaci na serveru Microsoft Exchange najdete v článku [nápověda federace: Exchange 2013.](https://docs.microsoft.com/exchange/federation-exchange-2013-help)


## <a name="other-considerations"></a>Další aspekty

Služby a aplikace, které se integrují s Azure AD, mají přístup k datům identit. Vyhodnoťte každou službu a aplikaci, kterou používáte, abyste zjistili, jak jsou data identity zpracovávána danou konkrétní službou a aplikací a zda splňují požadavky vaší společnosti na ukládání dat.

Další informace o rezidenci dat služeb Microsoftu najdete v části [Jaké je umístění vašich dat?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v Centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky
Další informace o všech výše popsaných funkcích a funkcích naleznete v těchto článcích:
- [Co je Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Samoobslužné resetování hesla Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Co je Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Co je spolupráce B2B ve službě Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
