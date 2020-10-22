---
title: Podmíněný přístup – vyžaduje MFA pro všechny uživatele – Azure Active Directory
description: Vytvoření vlastních zásad podmíněného přístupu, které budou vyžadovat, aby všichni uživatelé prováděli vícefaktorové ověřování
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e6185c4bde71285fc163cae2af46f64ba052195
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366203"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Podmíněný přístup: vyžadovat MFA pro všechny uživatele

Alex Weinert je adresářem zabezpečení identity v Microsoftu, který zmiňuje svůj Blogový příspěvek, na který se [vaše PA $ $Word nezáleží](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> Vaše heslo nezáleží na tom, ale vícefaktorové ověřování. Na základě našich studií je váš účet o více než 99,9% méně pravděpodobný, pokud použijete MFA.

Pokyny v tomto článku vám pomůžou vaší organizaci vytvořit vyvážené zásady vícefaktorového ověřování pro vaše prostředí.

## <a name="user-exclusions"></a>Vyloučení uživatelů

Zásady podmíněného přístupu jsou výkonné nástroje. doporučujeme, abyste z zásad vyloučili následující účty:

* **Nouzový přístup** nebo účty pro **přerušení** , které zabrání uzamknutí účtu na úrovni tenanta. V nepravděpodobném scénáři jsou všichni správci zamčeni z vašeho tenanta, účet pro správu pro nouzový přístup se dá použít k přihlášení k tenantovi a přijímá kroky pro obnovení přístupu.
   * Další informace najdete v článku [Správa účtů pro nouzový přístup ve službě Azure AD](../roles/security-emergency-access.md).
* **Účty služeb** a **instanční objekty**, jako je například účet Azure AD Connect Sync. Účty služeb jsou neinteraktivní účty, které nejsou vázány na konkrétního uživatele. Obvykle jsou používány back-endové služby, které umožňují programový přístup k aplikacím, ale používají se také pro přihlášení k systémům pro účely správy. Účty služby by měly být vyloučené, protože MFA nelze dokončit programově. Podmíněný přístup neblokuje volání prováděná instančními objekty.
   * Pokud má vaše organizace tyto účty používané ve skriptech nebo v kódu, zvažte jejich nahrazení pomocí [spravovaných identit](../managed-identities-azure-resources/overview.md). Jako dočasné řešení můžete tyto konkrétní účty z základní zásady vyloučit.

## <a name="application-exclusions"></a>Vyloučení aplikací

Organizace můžou mít k dispozici mnoho cloudových aplikací. Ne všechny tyto aplikace mohou vyžadovat stejné zabezpečení. Například aplikace v rámci služby mzdy a docházky můžou vyžadovat MFA, ale stravování nejspíš ne. Správci se můžou rozhodnout vyloučit konkrétní aplikace ze svých zásad.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Následující kroky vám pomůžou vytvořit zásadu podmíněného přístupu, která bude vyžadovat, aby všichni uživatelé prováděli službu Multi-Factor Authentication.

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Vyhledejte **Azure Active Directory**  >  **Security**  >  **podmíněný přístup**zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **Všichni uživatelé** .
   1. V části **vyloučit**vyberte **Uživatelé a skupiny** a zvolte účty pro nouzový přístup nebo rozklad vaší organizace. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  **Include**vyberte **všechny cloudové aplikace**.
   1. V části **vyloučit**vyberte všechny aplikace, které nevyžadují vícefaktorové ověřování.
1. V části **podmínky**  >  **klientské aplikace (Preview)** v části **Vyberte klientské aplikace, na které se bude tato zásada vztahovat, aby** se vybrala všechna výchozí nastavení a vyberte **Hotovo**.
1. V části **řízení přístupu**  >  **udělení**přístupu vyberte **udělit přístup**, **vyžadovat vícefaktorové ověřování**a vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

### <a name="named-locations"></a>Pojmenovaná umístění

Organizace se můžou rozhodnout zahrnout známá síťová umístění známá jako **pojmenovaná umístění** do zásad podmíněného přístupu. Tato pojmenovaná umístění můžou zahrnovat důvěryhodné sítě IPv4, jako jsou třeba pro hlavní pobočku. Další informace o konfiguraci pojmenovaných umístění najdete v článku [co je podmínka umístění v Azure Active Directory podmíněný přístup?](location-condition.md)

V příkladech výše uvedených zásad se může organizace rozhodnout Nevyžadovat vícefaktorové ověřování, pokud přistupuje k cloudové aplikaci ze své podnikové sítě. V takovém případě by mohli do zásad přidat následující konfiguraci:

1. V části **přiřazení**vyberte **Conditions**  >  **umístění**podmínek.
   1. Nakonfigurujte **Ano**.
   1. Uveďte **libovolné umístění**.
   1. Vylučte **všechna důvěryhodná umístění**.
   1. Vyberte **Hotovo**.
1. Vyberte **Hotovo**.
1. **Uložte** změny zásad.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze sestavy podmíněného přístupu](howto-conditional-access-insights-reporting.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
