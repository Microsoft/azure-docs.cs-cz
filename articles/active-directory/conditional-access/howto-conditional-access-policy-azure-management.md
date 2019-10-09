---
title: Podmíněný přístup – vyžaduje MFA pro správu Azure – Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu, které budou vyžadovat vícefaktorové ověřování pro úlohy správy Azure
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
ms.openlocfilehash: dd2259dc715fb54122b721ce40a715c6987947d2
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170113"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Podmíněný přístup: vyžadovat MFA pro správu Azure

Organizace využívají celou řadu služeb Azure a spravují je od Azure Resource Managerch nástrojů, jako jsou:

* Azure Portal
* Azure PowerShell
* Rozhraní příkazového řádku Azure

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
   1. Vyberte **Hotovo**.
1. V **části cloudové aplikace nebo akce**@no__t **-1**vyberte **vybrat aplikace**, zvolte **Microsoft Azure Správa**a vyberte **Vybrat** a pak **Hotovo**.
1. V části **řízení přístupu**@no__t**udělení**přístupu vyberte **udělit přístup**, **vyžadovat službu Multi-Factor Authentication**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
