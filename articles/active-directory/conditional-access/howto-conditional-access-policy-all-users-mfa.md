---
title: Podmíněný přístup – vyžaduje MFA pro všechny uživatele – Azure Active Directory
description: Vytvoření vlastních zásad podmíněného přístupu, které budou vyžadovat, aby všichni uživatelé prováděli vícefaktorové ověřování
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc0d22e2e6478c265ba9219ae4df5d5ddb34d481
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803882"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Podmíněný přístup: vyžadovat MFA pro všechny uživatele

Alex Weinert je adresářem zabezpečení identity v Microsoftu, který zmiňuje svůj Blogový příspěvek, na který se [vaše PA $ $Word nezáleží](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> Vaše heslo nezáleží na tom, ale vícefaktorové ověřování. Na základě našich studií je váš účet o více než 99,9% méně pravděpodobný, pokud použijete MFA.

Pokyny v tomto článku vám pomůžou vaší organizaci vytvořit vyvážené zásady vícefaktorového ověřování pro vaše prostředí.

## <a name="user-exclusions"></a>Vyloučení uživatelů

Zásady podmíněného přístupu jsou výkonné nástroje. doporučujeme, abyste z zásad vyloučili následující účty:

* **Nouzový přístup** nebo účty pro **přerušení** , které zabrání uzamknutí účtu na úrovni tenanta. V nepravděpodobném scénáři jsou všichni správci zamčeni z vašeho tenanta, účet pro správu pro nouzový přístup se dá použít k přihlášení k tenantovi a přijímá kroky pro obnovení přístupu.
   * Další informace najdete v článku [Správa účtů pro nouzový přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Účty služeb** a **zásady služby**, například účet Azure AD Connect Sync. Účty služeb jsou neinteraktivní účty, které nejsou vázány na konkrétního uživatele. Obvykle jsou používány back-end službami a umožňují programový přístup k aplikacím. Účty služby by měly být vyloučeny, protože MFA nelze dokončit programově.
   * Pokud má vaše organizace tyto účty používané ve skriptech nebo v kódu, zvažte jejich nahrazení pomocí [spravovaných identit](../managed-identities-azure-resources/overview.md). Jako dočasné řešení můžete tyto konkrétní účty z základní zásady vyloučit.

## <a name="application-exclusions"></a>Vyloučení aplikací

Organizace můžou mít k dispozici mnoho cloudových aplikací. Ne všechny tyto aplikace mohou vyžadovat stejné zabezpečení. Například aplikace v rámci služby mzdy a docházky můžou vyžadovat MFA, ale stravování nejspíš ne. Správci se můžou rozhodnout vyloučit konkrétní aplikace ze svých zásad.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Následující kroky vám pomůžou vytvořit zásadu podmíněného přístupu, která bude vyžadovat, aby tyto přiřazené role správce prováděly službu Multi-Factor Authentication.

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na **Azure Active Directory** > **zabezpečení** > **podmíněný přístup**.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **Všichni uživatelé** .
   1. V části **vyloučit**vyberte **Uživatelé a skupiny** a zvolte účty pro nouzový přístup nebo rozklad vaší organizace. 
   1. Vyberte **Done** (Hotovo).
1. V části **cloudové aplikace nebo akce** > **Zahrnout**možnost **všechny cloudové aplikace**.
   1. V části **vyloučit**vyberte všechny aplikace, které nevyžadují vícefaktorové ověřování.
1. V části **řízení přístupu** > **udělení**vyberte **udělit přístup**, **vyžadovat službu Multi-Factor Authentication**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
