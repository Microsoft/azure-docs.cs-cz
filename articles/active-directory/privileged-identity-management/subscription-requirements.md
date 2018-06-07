---
title: Privileged Identity Management odběry - Azure | Microsoft Docs
description: Vysvětluje, předplatné a licenční požadavky pro správu a použití ve vašem klientovi Azure AD Privileged Identity Management
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: mwahl
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 9e49ddb8fca9ce193c92f27c307942d5f3d3d2fd
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807988"
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Požadavky předplatné služby Azure Active Directory Privileged Identity Management

Azure AD Privileged Identity Management je k dispozici jako součást na edici Premium P2 služby Azure AD. Další informace o dalších funkcích P2 a srovnání se Premium P1 najdete v tématu [edice služby Azure Active Directory](../active-directory-editions.md).

>[!NOTE]
Pokud Azure Active Directory (Azure AD) Privileged Identity Management ve verzi preview, nebyly k dispozici žádné licence kontroly pro klienta k vyzkoušení služby.  Teď, když Azure AD Privileged Identity Management bylo dosaženo obecné dostupnosti, musí být k dispozici pro klienta můžete pokračovat v používání Privileged Identity Management po prosinci 2016 zkušební nebo placené předplatné.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>Potvrďte zkušebnímu nebo placenému odběru

Pokud nejste jistí, jestli má vaše organizace zkušební verzi nebo zakoupit předplatné, můžete zkontrolovat, zda je odběr ve vašem klientovi pomocí příkazů, které jsou součástí Azure Active Directory modulu pro Windows PowerShell V1. 
1. Otevřete okno PowerShell.
2. Zadejte `Connect-MsolService` k ověření jako uživatel ve vašem klientovi.
3. Zadejte `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`.

Tento příkaz načte seznam předplatných ve vašem klientovi. Pokud nejsou žádné řádky vrátil, musíte získat Azure AD Premium P2 vyzkoušení, zakoupení Azure AD Premium P2 předplatné nebo EMS E5 předplatné použít Azure AD Privileged Identity Management.  Chcete-li získat zkušební verze a začít používat Azure AD Privileged Identity Management, přečtěte si [Začínáme s Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

Pokud tento příkaz vrátí řádek, ve které SkuPartNumber je "AAD_PREMIUM_P2" nebo "EMSPREMIUM" a IsTrial je "True", to znamená, že je k dispozici v klientovi zkušební verzi Azure AD Premium P2.  Pokud stav předplatného není povoleno a není nutné zakoupit předplatné služby Azure AD Premium P2 nebo EMS E5, pak musíte koupit Azure AD Premium P2 předplatné nebo předplatné EMS E5-li nadále používat Azure AD Privileged Identity Management.

Je k dispozici prostřednictvím Azure AD Premium P2 [Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), [otevřete multilicenčního programu](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx)a [programu poskytovatele cloudových řešení](https://partner.microsoft.com/en-US/cloud-solution-provider). Odběratelé Azure a Office 365 může taky koupit Azure AD Premium P2 online.  Další informace o cenách Azure AD Premium a postup pořadí online naleznete na [Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Není k dispozici v klientovi Azure AD Privileged Identity Management

Azure AD Privileged Identity Management už nebude k dispozici ve vašem klientovi pokud:
- Při byl ve verzi preview a není zakoupit předplatné Azure AD Premium P2 nebo EMS E5 předplatné, byla vaše organizace používá Azure AD Privileged Identity Management.
- Vaše organizace měla služby Azure AD Premium P2 nebo EMS E5 zkušební verzi, jehož platnost vypršela.
- Vaše organizace měla zakoupené předplatné, jehož platnost vypršela.

Když Azure AD Premium P2 předplatné nebo EMS E5 předplatné vyprší platnost, nebo organizaci, která se pomocí Azure AD Privileged Identity Management ve verzi preview není získat předplatné Azure AD Premium P2 nebo EMS E5:

- Trvalá role přiřazení role se službou Azure AD, zůstanou beze změn.
- Rozšíření Azure AD Privileged Identity Management v portálu Azure a také rozhraní Graph API rutiny a PowerShell rozhraní Azure AD Privileged Identity Management už nebude k dispozici pro uživatele aktivovat privilegované role, spravovat oprávněními získat přístup, nebo provést přístup recenze privilegovaných rolí.
- Role vhodné přiřazení rolí Azure AD bude odebrat, jelikož uživatelé nebudou moci uživatel aktivovat privilegované role.
- Všechny probíhající přístup recenze rolí Azure AD se ukončí a nastavení konfigurace Azure AD Privileged Identity Management se odeberou.
- Azure AD Privileged Identity Management už pošle e-mailů na změny v přiřazení role.

## <a name="next-steps"></a>Další postup

- [Začínáme s Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md)
- [Role v Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-roles.md)
