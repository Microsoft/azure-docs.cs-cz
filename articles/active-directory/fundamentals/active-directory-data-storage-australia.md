---
title: Úložiště dat identity pro australské zákazníky Austrálie a New Zéland – Azure AD
description: Přečtěte si, kde Azure Active Directory ukládá data týkající se identit pro zákazníky Austrálie a Nový Zéland.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bb095e93a3728835e26cbe283f79569c91b7487
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479062"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Úložiště dat identity pro zákazníky Austrálie a Nového Zélandu v Azure Active Directory

Data identity ukládá služba Azure AD v geografickém umístění na základě adresy poskytnuté vaší organizací při přihlášení k odběru online služby Microsoftu, jako je Microsoft 365 a Azure. Informace o tom, kde jsou uložená vaše data o zákaznících identity, můžete [najít v části kde se vaše data nacházejí?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v centru zabezpečení Microsoftu.

> [!NOTE]
> Služby a aplikace, které se integrují se službou Azure AD, mají přístup k zákaznickým datům identity. Vyhodnoťte každou službu a aplikaci, které používáte k určení způsobu zpracování zákaznických dat pomocí konkrétní služby a aplikace, a to, jestli splňují požadavky na úložiště dat vaší společnosti. Další informace o rezidenci dat služeb Microsoftu najdete v části Jaké je umístění vašich dat? v Centru zabezpečení Microsoftu.

Pro zákazníky, kteří získali adresu v Austrálii nebo na Novém Zélandu, služba Azure AD uchovává data o identitě pro tyto služby v rámci Datacenter v australském centru: 
- Správa adresáře služby Azure AD 
- Authentication

Všechny ostatní služby Azure AD ukládají zákaznická data v globálních datových centrech. Pokud chcete najít datacentrum pro službu, přečtěte si téma [Azure Active Directory – kde se nacházejí vaše data?](https://aka.ms/AADDataMap)

## <a name="microsoft-azure-ad-multi-factor-authentication-mfa"></a>Multi-Factor Authentication Microsoft Azure AD (MFA)

MFA ukládá data o zákaznících identity v globálních datových centrech. Další informace o tom, jak se shromažďují informace o uživateli shromažďované a ukládané cloudovou službou Azure AD MFA a Azure MFA serverem, najdete v článku [shromažďování uživatelských dat v azure Multi-Factor Authentication](../authentication/concept-mfa-data-residency.md).

## <a name="next-steps"></a>Další kroky
Další informace o všech funkcích a funkcích popsaných výše najdete v těchto článcích:
- [Co je Multi-Factor Authentication?](../authentication/concept-mfa-howitworks.md)
