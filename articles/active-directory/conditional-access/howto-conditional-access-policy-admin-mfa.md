---
title: Podmíněný přístup – vyžaduje MFA pro správce – Azure Active Directory
description: Vytvoření vlastních zásad podmíněného přístupu, které vyžadují, aby správci prováděli vícefaktorové ověřování
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9624a8c25a1e748f5cde8344b0200bb3c82bab88
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576664"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Podmíněný přístup: Vyžadovat MFA pro správce

Účtům, kterým jsou přiřazena práva pro správu, cílí útočníci. Vyžadování vícefaktorového ověřování (MFA) u těchto účtů představuje snadný způsob, jak snížit riziko narušení těchto účtů.

Společnost Microsoft doporučuje vyžadovat vícefaktorové ověřování na následujících rolích minimálně:

* Globální správce
* Správce SharePointu
* Správce Exchange
* Správce podmíněného přístupu
* Správce zabezpečení
* HelpDesk (heslo) – správce
* Správce hesel
* Správce fakturace
* Správce uživatelů

Organizace si můžou zvolit, že se mají zahrnout nebo vyloučit role podle potřeby.

## <a name="user-exclusions"></a>Vyloučení uživatelů

Zásady podmíněného přístupu jsou výkonné nástroje. doporučujeme, abyste z zásad vyloučili následující účty:

* **Nouzový přístup** nebo účty pro **přerušení** , které zabrání uzamknutí účtu na úrovni tenanta. V nepravděpodobném scénáři jsou všichni správci zamčeni z vašeho tenanta, účet pro správu pro nouzový přístup se dá použít k přihlášení k tenantovi a přijímá kroky pro obnovení přístupu.
   * Další informace najdete v článku [Správa účtů pro nouzový přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Účty služeb** a **zásady služby**, například účet Azure AD Connect Sync. Účty služeb jsou neinteraktivní účty, které nejsou vázány na konkrétního uživatele. Obvykle jsou používány back-end službami a umožňují programový přístup k aplikacím. Účty služby by měly být vyloučeny, protože MFA nelze dokončit programově.
   * Pokud má vaše organizace tyto účty používané ve skriptech nebo v kódu, zvažte jejich nahrazení pomocí [spravovaných identit](../managed-identities-azure-resources/overview.md). Jako dočasné řešení můžete tyto konkrétní účty z základní zásady vyloučit.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Následující kroky vám pomůžou vytvořit zásadu podmíněného přístupu, která bude vyžadovat, aby tyto přiřazené role správce prováděly službu Multi-Factor Authentication.

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na **Azure Active Directory** > **podmíněný přístup**.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **role adresáře (Preview)** a minimálně jednu z následujících rolí:
      * Globální správce
      * Správce SharePointu
      * Správce Exchange
      * Správce podmíněného přístupu
      * Správce zabezpečení
      * Správce helpdesku
      * Správce hesel
      * Správce fakturace
      * Správce uživatelů
   1. V části **vyloučit**vyberte **Uživatelé a skupiny** a zvolte účty pro nouzový přístup nebo rozklad vaší organizace. 
   1. Vyberte **Done** (Hotovo).
1. V části **cloudové aplikace nebo akce** > vyberte **všechny cloudové aplikace**a vyberte **Hotovo**.
1. V části **řízení** > přístupu**udělení**přístupu vyberte **udělit přístup**, **vyžadovat vícefaktorové ověřování**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

## <a name="next-steps"></a>Další postup

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
