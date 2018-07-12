---
title: Privileged Identity Management předplatná – Azure | Dokumentace Microsoftu
description: Vysvětluje, předplatné a licenční požadavky pro správu a použití ve vašem tenantovi Azure AD Privileged Identity Management
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 06/01/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 681169fc3db69c2dfb8aa36d6e2896e0ddba8b56
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38548124"
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Požadavky na předplatné Azure Active Directory Privileged Identity Management

Azure AD Privileged Identity Management je k dispozici jako součást v edici Premium P2 služby Azure AD. Další informace o jiných funkcích P2 a jejím srovnání s Premium P1, naleznete v tématu [edice Azure Active Directory](../active-directory-editions.md).

>[!NOTE]
Když Azure Active Directory (Azure AD) Privileged Identity Management byla ve verzi preview, nebyly žádné kontroly licence pro tenanta pro službu si můžete vyzkoušet.  Teď, když Azure AD Privileged Identity Management bylo dosaženo obecné dostupnosti, zkušebnímu nebo placenému odběru musí být k dispozici pro tenanta, abyste mohli nadále používat Privileged Identity Management po. prosince 2016.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>Potvrďte zkušebnímu nebo placenému odběru

Pokud si nejste jistí, jestli má vaše organizace zkušební verze nebo zakoupili předplatné, můžete zkontrolovat, jestli je předplatné ve vašem tenantovi pomocí příkazů v Azure Active Directory modulu pro Windows PowerShell V1. 
1. Otevřete okno Powershellu.
2. Zadejte `Connect-MsolService` ověřit se jako uživatel ve vašem tenantovi.
3. Zadejte `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`.

Tento příkaz načte seznam předplatných ve vašem tenantovi. Pokud neexistují žádné řádky vrátil, je potřeba získat vyzkoušení, zakoupení Azure AD Premium P2 Azure AD Premium P2 předplatné nebo předplatné EMS E5, které chcete použít Azure AD Privileged Identity Management.  Získejte zkušební verzi a začněte používat Azure AD Privileged Identity Management, přečtěte si téma [Začínáme s Azure AD Privileged Identity Management](pim-getting-started.md).

Pokud tento příkaz vrátí řádek v které SkuPartNumber je "AAD_PREMIUM_P2" nebo "EMSPREMIUM" a IsTrial je "True", znamená to, že je k dispozici v tenantovi zkušební verzi Azure AD Premium P2.  Pokud se stav předplatného není povolená, a nemáte předplatné Azure AD Premium P2 nebo EMS E5 nákupu, pak musíte koupit Azure AD Premium P2 předplatné nebo předplatné EMS E5 a pokračujte v používání Azure AD Privileged Identity Management.

Je dostupná prostřednictvím Azure AD Premium P2 [Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), [programu Open Volume License](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx)a [programu Cloud Solution Provider](https://partner.microsoft.com/en-US/cloud-solution-provider). Předplatitelé Azure a Office 365 si můžete koupit i online Azure AD Premium P2.  Další informace o cenách Azure AD Premium a jak objednat online najdete v [cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Není k dispozici v tenantovi Azure AD Privileged Identity Management

Bude nadále již nebudou k dispozici ve vašem tenantovi Azure AD Privileged Identity Management pokud:
- Vaše organizace používal Azure AD Privileged Identity Management, když byla ve verzi preview a nekoupí předplatné Azure AD Premium P2 nebo předplatné EMS E5.
- Vaše organizace měla Azure AD Premium P2 nebo EMS E5 zkušební verze, jehož platnost vypršela.
- Vaše organizace má platné předplatné, jehož platnost vypršela.

Když vyprší platnost Azure AD Premium P2 předplatné nebo předplatné EMS E5, nebo nezíská předplatného služby Azure AD Premium P2 nebo EMS E5 organizace, která byla ve verzi preview pomocí Azure AD Privileged Identity Management:

- Přiřazení trvalých rolí pro role Azure AD, zůstanou beze změn.
- Rozšíření Azure AD Privileged Identity Management na webu Azure portal, jakož i rutiny rozhraní Graph API a rozhraní prostředí PowerShell služby Azure AD Privileged Identity Management, nebudou k dispozici pro uživatele aktivovat privilegované role, spravovat oprávnění přístup nebo provádět kontroly přístupu privilegovaných rolí.
- Role oprávněné přiřazení role Azure AD se odebere, jak uživatelé už nebudou moci uživatel aktivovat privilegované role.
- Žádné kontroly přístupu probíhající rolí Azure AD se ukončí a nastavení konfigurace Azure AD Privileged Identity Management se odeberou.
- Azure AD Privileged Identity Management už pošle e-mailů na změny v přiřazení role.

## <a name="next-steps"></a>Další postup

- [Začínáme s Azure AD Privileged Identity Management](pim-getting-started.md)
- [Role ve službě Azure AD Privileged Identity Management](pim-roles.md)
