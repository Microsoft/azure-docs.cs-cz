---
title: Podmíněný přístup – vyžaduje MFA pro správu Azure – Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu, které budou vyžadovat vícefaktorové ověřování pro úlohy správy Azure
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad44cc83d65206ec1f199e57685b0dcc3cbb07a5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151168"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Podmíněný přístup: vyžadovat MFA pro správu Azure

Organizace využívají celou řadu služeb Azure a spravují je od Azure Resource Managerch nástrojů, jako jsou:

* Portál Azure
* Azure PowerShell
* Azure CLI

Tyto nástroje můžou poskytovat vysoce privilegovaný přístup k prostředkům, které můžou upravovat konfigurace, nastavení služeb a fakturace pro předplatné na úrovni předplatného. Z důvodu ochrany těchto privilegovaných prostředků doporučuje společnost Microsoft vyžadovat službu Multi-Factor Authentication pro všechny uživatele, kteří k těmto prostředkům přistupují.

## <a name="user-exclusions"></a>Vyloučení uživatelů

Zásady podmíněného přístupu jsou výkonné nástroje. doporučujeme, abyste z zásad vyloučili následující účty:

* **Nouzový přístup** nebo účty pro **přerušení** , které zabrání uzamknutí účtu na úrovni tenanta. V nepravděpodobném scénáři jsou všichni správci zamčeni z vašeho tenanta, účet pro správu pro nouzový přístup se dá použít k přihlášení k tenantovi a přijímá kroky pro obnovení přístupu.
   * Další informace najdete v článku [Správa účtů pro nouzový přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Účty služeb** a **zásady služby**, například účet Azure AD Connect Sync. Účty služeb jsou neinteraktivní účty, které nejsou vázány na konkrétního uživatele. Obvykle jsou používány back-end službami a umožňují programový přístup k aplikacím. Účty služby by měly být vyloučeny, protože MFA nelze dokončit programově.
   * Pokud má vaše organizace tyto účty používané ve skriptech nebo v kódu, zvažte jejich nahrazení pomocí [spravovaných identit](../managed-identities-azure-resources/overview.md). Jako dočasné řešení můžete tyto konkrétní účty z základní zásady vyloučit.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Následující kroky vám pomůžou vytvořit zásadu podmíněného přístupu, která bude vyžadovat, aby tyto přiřazené role správce prováděly službu Multi-Factor Authentication.

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na **Azure Active Directory** **podmíněný přístup** > .
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **Všichni uživatelé**.
   1. V části **vyloučit**vyberte **Uživatelé a skupiny** a zvolte účty pro nouzový přístup nebo rozklad vaší organizace. 
   1. Vyberte **Done** (Hotovo).
1. V části **cloudové aplikace nebo akce** > **Zahrnout**vyberte **vybrat aplikace**, zvolte **Microsoft Azure Správa**a pak vyberte **Vybrat** a **Hotovo**.
1. V části **řízení přístupu** > **udělení**vyberte **udělit přístup**, **vyžadovat službu Multi-Factor Authentication**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
