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
ms.openlocfilehash: 93ac5ef5f03f800a8f90259db3e382b3bc5c5e2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235170"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Ukládání dat identity Evropského zákazníků v Azure Active Directory
Identita data se ukládají ve službě Azure AD v zeměpisné umístění na základě adresy poskytnuté vaší organizací při přihlášení k odběru služby Microsoft Online, jako je Office 365 a Azure. Informace o uložení údajů o identitě, můžete použít [kde jsou vaše data umístěné?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) části Microsoft Trust Center.

Pro zákazníky, kteří k dispozici adresu v Evropě Azure AD udržuje většina dat identity v evropských datových centrech. Tento dokument obsahuje informace o veškerá data, která se ukládá mimo Evropa pomocí služby Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure Multi-Factor authentication (MFA)
    
- Všechny dvoufaktorovým ověřováním pomocí telefonní hovory nebo SMS pocházejí z datacentra v USA a směrují globální poskytovateli.
- Nabízená oznámení pomocí Microsoft Authenticator aplikace pocházejí z datová centra v USA. Kromě toho určité služby výrobce zařízení může také začne play a tyto služby možná mimo Evropa.
- Kódy OATH se vždy ověřují v USA. 

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C zásad konfiguračních dat a kontejnery klíčů se ukládají v datových centrech USA. Tyto neobsahuje žádné osobní údaje uživatelů. Další informace o konfiguraci zásad najdete v článku [Azure Active Directory B2C: Předdefinované zásady](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) článku.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD s B2B pozvánky úložišť s uplatnit propojit a přesměrovat adresu URL informace v datových centrech USA. Kromě toho e-mailové adresy uživatelů, kteří se odhlásit od přijetí pozvánky B2B se také ukládají v datových centrech USA.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS uchovává data uživatelů ve stejném umístění jako virtuální síť Azure vybranou zákazníkem. Takže pokud se tato síť nachází mimo Evropu, data se replikují a uchovávají mimo Evropu.

## <a name="other-considerations"></a>Další důležité informace

Služby a aplikace, které se integrují s Azure AD mají přístup k datům identity. Vyhodnocení jednotlivé služby a aplikace, kterou používáte k určení, jak se zpracovávají data identit podle této konkrétní služby nebo aplikace, a zda splňují požadavky na úložiště dat vaší společnosti.

Další informace o rezidenci dat služeb Microsoftu najdete v části [Jaké je umístění vašich dat?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v Centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další postup
Další informace o všech vlastností a funkcí popsaných výše najdete v těchto článcích:
- [Co je Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Samoobslužné resetování hesla Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Co je Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Co je spolupráce B2B ve službě Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
