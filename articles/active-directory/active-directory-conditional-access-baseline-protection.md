---
title: Co je směrný plán ochranu v Azure Active Directory podmíněného přístupu? -preview | Microsoft Docs
description: Zjistěte, jak směrný plán ochranu zajistí, že můžete mít aspoň základní úroveň zabezpečení povolené ve vašem prostředí Azure Active Directory.
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
ms.date: 06/21/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 86b57a82573760ac73975e851b2bb4caf769845b
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308556"
---
# <a name="what-is-baseline-protection---preview"></a>Co je ochrana směrného plánu? -preview  

V posledním roce identity útoky vzrůst 300 %. K ochraně před útoky stále rostoucí prostředí, Azure Active Directory (Azure AD) představuje novou funkci s názvem směrného plánu ochrany. Směrný plán ochranu je sada předdefinovaných [zásady podmíněného přístupu](active-directory-conditional-access-azure-portal.md). Cílem těchto zásad je zajistěte, abyste měli aspoň základní úroveň zabezpečení povolené ve všech edicích systémů Azure AD. 

Tento článek vám poskytne přehled směrný plán ochranu v Azure Active Directory.


 
## <a name="require-mfa-for-admins"></a>Vyžadovat vícefaktorové ověřování pro správce

Uživatelé s přístupem k privilegovaným účtům mají neomezený přístup k vašemu prostředí. Kvůli výkonu, které mají tyto účty by měly zpracovávat dát zvláštní pozor. Jeden běžnou metodu pro zvýšení ochrany proti privilegovaných účtů, je potřeba silnější formu ověření účtu, pokud se používá k přihlášení. V Azure Active Directory získáte silnější ověření účtu vyžadováním vícefaktorového ověřování (MFA).  

**Vícefaktorové ověřování vyžadovat pro admins** základní zásady, které vyžaduje vícefaktorové ověřování pro následující role adresáře: 

- Globální správce  

- Správce SharePointu  

- Správce Exchange  

- Správce podmíněného přístupu  

- Správce zabezpečení  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

Tato základní zásady vám poskytuje možnost pro vyloučení uživatelé a skupiny. Můžete chtít vyloučit jeden *[nutnosti přístupu k účtu správce](active-directory-admin-manage-emergency-access-accounts.md)* zajistit, abyste neměli zablokován přístup klienta.


## <a name="enable-a-baseline-policy"></a>Povolit základní zásady 

Základní zásady jsou ve verzi preview, jsou ve výchozím nastavení není aktivováno. Budete muset ručně povolte zásadu, pokud chcete aktivovat. Jakmile tato funkce dosáhl obecné dostupnosti, zásady se ve výchozím nastavení aktivované. Změna plánované chování je důvod, proč máte kromě aktivovat a deaktivovat třetí možnost pro nastavení stavu zásady: **automaticky povolit zásady v budoucnu**. Výběrem této možnosti umožníte Microsoft při rozhodování, kdy chcete aktivovat zásady.      


**Chcete-li povolit základní zásady:**  

1. Přihlaste se k [portál Azure](https://portal.azure.com) jako globální správce, správce zabezpečení nebo podmíněný přístup správce.

2. V **portál Azure**, v levém navigační panel, klikněte na tlačítko **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. Na **Azure Active Directory** stránky v **spravovat** klikněte na tlačítko **podmíněného přístupu**.

    ![Podmíněný přístup](./media/active-directory-conditional-access-baseline-protection/03.png)

4. V seznamu zásad, klikněte na zásadu, která začíná **základní zásady:**. 

5. Chcete-li povolit zásady, klikněte na tlačítko **použít zásady okamžitě**.

6. Klikněte na **Uložit**. 
 


  
 

## <a name="what-you-should-know"></a>Důležité informace 

Při správě zásad podmíněného přístupu vlastní vyžaduje licenci Azure AD Premium, jsou dostupné ve všech edicích systémů Azure AD základní zásady.     

Role adresáře, které jsou součástí základní zásady jsou nejvíce privilegovaných rolí Azure AD. 

Pokud máte privilegované účty, které se používají ve skriptech, měli byste je nahradit [identita spravované služby (MSI)](./managed-service-identity/overview.md) nebo [služby hlavních objektů s certifikáty](../azure-resource-manager/resource-group-authenticate-service-principal.md). Jako dočasné řešení můžete vyloučit konkrétní uživatelské účty ze základní zásady. 

Základní zásady platí pro starší verze ověřování toky POP, IMAP, starší klienta pro stolní počítače Office. 




## <a name="next-steps"></a>Další postup

Pokud chcete vědět, jak konfigurovat zásadu podmíněného přístupu, najdete v článku [Začínáme s podmíněným přístupem v Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md). 
