---
title: Co je směrný plán ochranu v Azure Active Directory podmíněného přístupu | Microsoft Docs
description: Zjistěte, jak směrný plán ochranu zajistí, že můžete mít aspoň základní úroveň zabezpečení povolené ve vašem prostředí.
services: active-directory
keywords: podmíněný přístup k aplikacím, podmíněného přístupu s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 25ae4db2cd4f2a2cea74c428a272c6868acaa5c5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35249238"
---
# <a name="what-is-baseline-protection"></a>Co je ochrana směrného plánu?  

V posledním roce identity útoky vzrůst 300 %. K ochraně před útoky stále rostoucí prostředí, Azure Active Directory (Azure AD) představuje novou funkci s názvem směrného plánu ochrany. Směrný plán ochranu je sada zásady předdefinované podmíněného přístupu. Cílem těchto zásad je zajistěte, abyste měli aspoň základní úroveň zabezpečení povolené ve vašem prostředí. 

Ve verzi Preview budete muset povolit zásady směrného plánu, pokud chcete k jejich aktivaci. Po obecné dostupnosti si tyto zásady jsou ve výchozím nastavení povolené. 

První základní zásady ochrany vyžaduje vícefaktorové ověřování pro privilegované účty. Útočníci, kteří získat kontrolu nad privilegované účty můžete provést strávíte škody, proto je důležité chránit tyto účty, nejprve. Následující privilegované role jsou v oboru pro tuto zásadu: 

- Globální správce  

- Správce SharePointu  

- Správce Exchange  

- Správce podmíněného přístupu  

- Správce zabezpečení  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

## <a name="how-to-get-started"></a>Jak začít 

Pokud chcete povolit zásady směrného plánu:  

1. Přihlaste se k [portál Azure](https://portal.azure.com) jako globální správce, správce zabezpečení nebo podmíněný přístup správce.

2. V **portál Azure**, v levém navigační panel, klikněte na tlačítko **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. Na **Azure Active Directory** stránky v **spravovat** klikněte na tlačítko **podmíněného přístupu**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-baseline-protection/03.png)

4. V seznamu zásad, klikněte na **základní zásady: vyžadovat vícefaktorové ověřování pro správce (Preview)**. 

5. Chcete-li povolit zásady, klikněte na tlačítko **použít zásady okamžitě**.

6. Klikněte na **Uložit**. 
 

Základní zásady vám poskytuje možnost pro vyloučení uživatelé a skupiny. Můžete chtít vyloučit jeden *[nutnosti přístupu k účtu správce](active-directory-admin-manage-emergency-access-accounts.md)* zajistit, abyste neměli zablokován přístup klienta.
  
 

## <a name="what-you-should-know"></a>Důležité informace 

Role adresáře, které jsou součástí základní zásady jsou nejvíce privilegovaných rolí Azure AD. Na základě názorů, ostatní může být zahrnuté v budoucnu. 

Pokud máte ve skriptech účty s oprávněním správce, měli byste použít [identita spravované služby (MSI)](managed-service-identity/overview.md) nebo [služby objekty zabezpečení (s certifikáty)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) místo. Jako dočasné řešení můžete vyloučit konkrétní uživatelské účty z vaší základní zásady. 

Zásady platí pro starší verze ověřování toky POP, IMAP, starší klienta pro stolní počítače Office. 

## <a name="next-steps"></a>Další postup

Pokud chcete vědět, jak konfigurovat zásadu podmíněného přístupu, najdete v článku [Začínáme s podmíněným přístupem v Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md). 
