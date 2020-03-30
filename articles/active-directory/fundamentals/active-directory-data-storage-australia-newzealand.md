---
title: Úložiště dat identit pro australské zákazníky – Azure AD
description: Přečtěte si, kde služba Azure Active Directory ukládá data související s identitou pro své australské zákazníky.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dc659f3d580646c1e86e5e97940268311e5546f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460530"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Úložiště dat identit pro zákazníky z Austrálie a Nového Zélandu ve službě Azure Active Directory

Data identity se ukládají službou Azure AD v geografickém umístění na základě adresy poskytnuté vaší organizací při přihlášení k odběru služby Microsoft Online, jako je Office 365 a Azure. Informace o tom, kde jsou uložena zákaznická data identity, naleznete v centru zabezpečení společnosti Microsoft část [Kde se vaše data nacházejí?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

> [!NOTE]
> Služby a aplikace, které se integrují s Azure AD, mají přístup k zákaznickým datům identity. Vyhodnoťte každou službu a aplikaci, kterou používáte, abyste zjistili, jak je zákaznická data identity zpracovávána danou konkrétní službou a aplikací a zda splňují požadavky vaší společnosti na ukládání dat. Další informace o rezidenci dat služeb Microsoftu najdete v části Jaké je umístění vašich dat? v Centru zabezpečení Microsoftu.

Pro zákazníky, kteří poskytli adresu v Austrálii a na Novém Zélandu a používají azure ad free edition, Azure AD udržuje data PII v klidovém stavu v australských datových centrech. 

Všechny ostatní služby Azure AD premium ukládají zákaznická data v globálních datových centrech. Pokud chcete najít datové centrum pro službu, přečtěte si hlavní informace o [službě Azure Active Directory – kde se vaše data nacházejí?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Vícefaktorové ověřování Microsoft Azure (MFA)

Služba MFA ve službě Azure AD ukládá zákaznická data identity v globálních datových centrech v klidovém stavu. Další informace o informacích o uživatelích shromážděných a uložených pomocí cloudových Azure MFA a Azure MFA Server najdete v [tématu Shromažďování uživatelských dat azure vícefaktorového ověřování](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency). Pokud zákazníci používají Vícefaktorové informace, jejich data budou uložena mimo datová centra Austrálie v klidovém stavu. 

## <a name="next-steps"></a>Další kroky
Další informace o všech výše popsaných funkcích a funkcích naleznete v těchto článcích:
- [Co je Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
