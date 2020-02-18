---
title: Úložiště dat identity pro australské zákazníky – Azure AD
description: Přečtěte si, kde Azure Active Directory ukládá data týkající se identit pro své australské zákazníky.
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
ms.openlocfilehash: cbe4befa824990c2f8b2627e7709913f564c0486
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371524"
---
# <a name="identity-data-storage-for-australian-customers-in-azure-active-directory"></a>Úložiště dat identity pro australské zákazníky v Azure Active Directory

Data identity ukládá služba Azure AD v geografickém umístění na základě adresy poskytnuté vaší organizací při přihlášení k odběru online služby Microsoftu, jako je třeba Office 365 a Azure. Informace o tom, kde jsou uložená vaše data o zákaznících identity, můžete [najít v části kde se vaše data nacházejí?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v centru zabezpečení Microsoftu.

> [!NOTE]
> Služby a aplikace, které se integrují se službou Azure AD, mají přístup k zákaznickým datům identity. Vyhodnoťte každou službu a aplikaci, které používáte k určení způsobu zpracování zákaznických dat pomocí konkrétní služby a aplikace, a to, jestli splňují požadavky na úložiště dat vaší společnosti. Další informace o zaregistrování dat služeb společnosti Microsoft najdete v části kde se nacházejí vaše data? v části centra zabezpečení Microsoftu.

Pro zákazníky, kteří získali adresu v Austrálii, uchovává Azure AD data o identitě pro tyto služby v australském datacentru: 
- Správa adresáře služby Azure AD 
- Ověřování

Všechny ostatní služby Azure AD ukládají zákaznická data v globálních datových centrech. Pokud chcete najít datacentrum pro službu, přečtěte si téma [Azure Active Directory – kde se nacházejí vaše data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure Multi-Factor Authentication (MFA)

MFA ukládá data o zákaznících identity v globálních datových centrech. Další informace o informacích o uživateli shromažďovaných a uložených pomocí cloudových Azure MFA a Azure MFA serveru najdete v tématu [shromažďování uživatelských dat v azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency).

## <a name="next-steps"></a>Další kroky
Další informace o všech funkcích a funkcích popsaných výše najdete v těchto článcích:
- [Co je Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
