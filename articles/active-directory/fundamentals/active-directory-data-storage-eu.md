---
title: Úložiště dat identity pro evropské zákazníky – Azure AD
description: Přečtěte si, kde Azure Active Directory ukládá data týkající se identit pro své evropské zákazníky.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 874df2cc17e291cb6811ca07d01237f01b523860
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565034"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Úložiště dat identity pro evropské zákazníky v Azure Active Directory
Data identity ukládá služba Azure AD v geografickém umístění na základě adresy poskytnuté vaší organizací při přihlášení k odběru online služby Microsoftu, jako je Microsoft 365 a Azure. Informace o tom, kde jsou uložená data identity, můžete najít v části [umístění vašich dat?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v centru zabezpečení Microsoftu.

Pro zákazníky, kteří získali adresu v Evropě, uchovává Azure AD většinu dat identity v rámci evropských datových center. Tento dokument poskytuje informace o všech datech, která jsou uložená mimo Evropu službami Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure Multi-Factor Authentication (MFA)
    
- Všechna dvojúrovňové ověřování pomocí telefonních hovorů nebo SMS pocházejících z Datacenter USA a jsou směrována i globálními poskytovateli.
- Nabízená oznámení, která používají aplikaci Microsoft Authenticator, pocházejí z datových center USA. Kromě toho mohou vzniknout také služby specifické pro dodavatele zařízení a tyto služby můžou být mimo Evropu.
- Kódy OATH se vždy ověřují v USA. 

Další informace o tom, jaké informace o uživateli shromažďuje služba Azure Multi-Factor Authentication Server (MFA Server) a cloudová služba Azure MFA, najdete v článku [shromažďování uživatelských dat v azure Multi-Factor Authentication](../authentication/howto-mfa-reporting-datacollection.md).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Konfigurační data zásad Azure AD B2C a kontejnery klíčů se ukládají v datacentrech v USA. Neobsahují žádné osobní údaje uživatele. Další informace o konfiguracích zásad najdete v článku [Azure Active Directory B2C: Předdefinované zásady](../../active-directory-b2c/user-flow-overview.md).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B ukládá pozvánky s informacemi o uplatnění odkazu a adresy URL pro přesměrování v datacentrech USA. Kromě toho se v datacentrech USA ukládají i e-mailové adresy uživatelů, kteří odhlásili odběr pozvání B2B.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS uchovává data uživatelů ve stejném umístění jako virtuální síť Azure vybranou zákazníkem. Takže pokud se tato síť nachází mimo Evropu, data se replikují a uchovávají mimo Evropu.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federace v systému Microsoft Exchange Server 2013
    
- Identifikátor aplikace (AppID) – jedinečné číslo vygenerované systémem ověřování Azure Active Directory k identifikaci organizací Exchange.
- Seznam schválených federovaných domén pro aplikaci
- Veřejný klíč pro podepsání tokenu aplikace 

Další informace o federaci na serveru Microsoft Exchange naleznete v článku [federace: Exchange 2013 Help](/exchange/federation-exchange-2013-help) .


## <a name="other-considerations"></a>Další důležité informace

Služby a aplikace, které se integrují se službou Azure AD, mají přístup k datům identity. Vyhodnoťte každou službu a aplikaci, které používáte k určení způsobu zpracování dat identity pomocí konkrétní služby a aplikace a zda splňují požadavky na úložiště dat vaší společnosti.

Další informace o rezidenci dat služeb Microsoftu najdete v části [Jaké je umístění vašich dat?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v Centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky
Další informace o všech funkcích a funkcích popsaných výše najdete v těchto článcích:
- [Co je Multi-Factor Authentication?](../authentication/concept-mfa-howitworks.md)

- [Samoobslužné resetování hesla Azure AD](../authentication/concept-sspr-howitworks.md)

- [Co je Azure Active Directory B2C?](../../active-directory-b2c/overview.md)

- [Co je spolupráce B2B ve službě Azure AD?](../external-identities/what-is-b2b.md)

- [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md)