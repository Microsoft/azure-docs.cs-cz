---
title: Konfigurace výstrah zabezpečení pro role adresáře Azure AD v PIM | Dokumentace Microsoftu
description: Zjistěte, jak konfigurovat výstrahy zabezpečení pro role adresáře Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/04/2019
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7b419be7327403d3ee0f6fad38da58fab72ddc10
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156390"
---
# <a name="configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>Konfigurace výstrah zabezpečení pro role adresáře Azure AD v PIM

Azure AD Privileged Identity Management (PIM) generuje výstrahy, když je aktivita podezřelého nebo nebezpečné ve vašem prostředí. Když se aktivuje upozornění, zobrazí na řídicím panelu PIM. Vyberte výstrahu, pokud chcete zobrazit sestavu obsahující seznam uživatelů nebo rolí, které aktivuje výstrahu.

![Upozornění zabezpečení PIM – snímek obrazovky](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Výstrahy zabezpečení

Tato část obsahuje seznam všech výstrah zabezpečení pro role adresáře, jak vyřešit a jak zabránit. Závažnost má následující význam:

* **Vysoká**: Vyžaduje okamžitý zásah kvůli porušení zásad.
* **Střední**: Není potřeba reagovat okamžitě, ale signály potenciální porušení zásad.
* **Nízká**: Není potřeba reagovat okamžitě, ale navrhuje změna vhodnější zásad.

### <a name="administrators-arent-using-their-privileged-roles"></a>Nepoužívají správci svoje privilegované role

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se zobrazí toto upozornění?** | Uživatelé, kteří mají přiřazený privilegované role, které nepotřebují se zvyšuje riziko útoku. Je také jednodušší pro útočníky zůstanou v účtech, které nejsou aktivně používá bez povšimnutí. |
| **Jak vyřešit?** | Projděte si uživatelé ze seznamu a odebere je z privilegované role, které nejsou potřeba. |
| **Ochrany před únikem informací** | Privilegované role přiřadíte jenom uživatelé, kteří mají obchodní odůvodnění. </br>Plánování pravidelně [kontrol přístupu](pim-how-to-start-security-review.md) k ověření, že uživatelé stále potřebují přístup. |
| **Na portálu zmírňující opatření** | Odebere účet z svou privilegovanou roli. |
| **Trigger** | Aktivuje, pokud uživatel přejde určitou dobu bez aktivace role. |
| **Počet dnů** | Toto nastavení určuje počet dní od 0 do 100, který uživatel přejít bez aktivace role.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Role nevyžadují ověřování službou Multi-Factor Authentication pro aktivaci

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se zobrazí toto upozornění?** | Bez MFA ohrožených uživatelů můžete aktivovat privilegované role. |
| **Jak vyřešit?** | Projděte si seznam rolí a [vyžadovat vícefaktorové ověřování](pim-how-to-change-default-settings.md) pro každou roli. |
| **Ochrany před únikem informací** | [Vyžadovat vícefaktorové ověřování](pim-how-to-change-default-settings.md) pro každou roli.  |
| **Na portálu zmírňující opatření** | Díky aktivace privilegovaných rolí vyžaduje se MFA. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>Tenant nemá Azure AD Premium P2

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se zobrazí toto upozornění?** | Aktuálního tenanta Azure AD Premium P2 nemá. |
| **Jak vyřešit?** | Přečtěte si informace o [edice Azure AD](../fundamentals/active-directory-whatis.md). Upgrade na Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenciální zastaralé účty v privilegovaných rolí

| | |
| --- | --- |
| **Závažnost** | Střednědobé používání |
| **Proč se zobrazí toto upozornění?** | Účty v privilegovaných rolí, které nedošlo ke změně hesla v posledních 90 dnů. Tyto účty mohou být služby nebo sdílené účty, které nejsou dodržovány a můžou se stát terčem útočníků. |
| **Jak vyřešit?** | Projděte si účty v seznamu. Pokud už potřebují přístup, odeberte je ze svoje privilegované role. |
| **Ochrany před únikem informací** | Ujistěte se, že účty, které jsou sdíleny jsou otáčení silná hesla, když dojde ke změně v uživatele, kteří znát heslo. </br>Pravidelně revize účty s privilegované role pomocí [kontrol přístupu](pim-how-to-start-security-review.md) a odeberte přiřazení rolí, které už nejsou potřeba. |
| **Na portálu zmírňující opatření** | Odebere účet z svou privilegovanou roli. |
| **Osvědčené postupy** | Sdílené služby, a které ověřování pomocí hesla a jsou přiřazeny k vysoce privilegované role pro správu, jako je například globální správce nebo správce zabezpečení účtů pro nouzový přístup by měl mít svá hesla otočen v následujících případech:<ul><li>Po incidentu zabezpečení zahrnující zneužití nebo ohrožení zabezpečení pro správu přístupových práv</li><li>Po každý uživatel oprávnění se změní tak, aby už nejsou správce (například po zaměstnanec, který byl listy správce IT nebo opustí organizaci)</li><li>V pravidelných intervalech (pro příklad, čtvrtletně nebo ročně), i když došlo žádné známé porušení zabezpečení nebo změna IT pracovníky</li></ul>Od několika lidí přístup k přihlašovacím údajům tyto účty, by měl zajistit, že osoby, které ještě zbývá jejich role už mít přístup k účtům otočit přihlašovací údaje. [Další informace](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-pim"></a>Role se přiřazení mimo PIM

| | |
| --- | --- |
| **Závažnost** | Vysoký |
| **Proč se zobrazí toto upozornění?** | Přiřazení privilegovaných rolí, které jsou mimo PIM nemonitoruje správně a může znamenat útok aktivní. |
| **Jak vyřešit?** | Projděte si uživatelé ze seznamu a odebere je z privilegované role přiřazené mimo PIM. |
| **Ochrany před únikem informací** | Prozkoumejte, kde uživatelé jsou přiřazení privilegovaných rolí mimo PIM a zakázat budoucí přiřazení z něj. |
| **Na portálu zmírňující opatření** | Odebere účet z svou privilegovanou roli. |

### <a name="there-are-too-many-global-administrators"></a>Existuje příliš mnoho globálních správců

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se zobrazí toto upozornění?** | Globální správce je nejvyšší privilegovaných rolí. Pokud dojde k narušení globální správce útočník získá přístup ke všem jejich oprávnění, která vloží celý systém ohrožení. |
| **Jak vyřešit?** | Projděte si uživatelé ze seznamu a odeberte všechny, není nezbytně nutné roli globálního správce. </br>Přiřadíte těmto uživatelům nižší privilegovaných rolí. |
| **Ochrany před únikem informací** | Přiřazení uživatelů nejméně privilegovaných rolí, které potřebují. |
| **Na portálu zmírňující opatření** | Odebere účet z svou privilegovanou roli. |
| **Trigger** | Aktivuje, pokud jsou splněny dva různých kritérií a můžete nakonfigurovat obě z nich. Nejprve budete muset dosáhne určité prahové hodnoty globální správci. Za druhé procento celkové role přiřazení musí být globální správce. Pokud splňujete pouze jeden z těchto měření, výstraha se nezobrazí. |
| **Minimální počet globálních správců** | Toto nastavení určuje počet globálních správců, od 2 do 100, vezměte v úvahu nebezpečné částku. |
| **Procento globálních správců** | Toto nastavení určuje minimální procento správce, kteří jsou globální správci, od 0 % do 100 %, který nebezpečné ve vašem prostředí. |

### <a name="roles-are-being-activated-too-frequently"></a>Role se aktivuje příliš často

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se zobrazí toto upozornění?** | Více aktivací na stejný atribut privilegované role stejným uživatelem je signalizovat útok. |
| **Jak vyřešit?** | Projděte si uživatelé ze seznamu a ujistěte se, že [doba trvání aktivace](pim-how-to-change-default-settings.md) svou privilegovanou roli je nastavený dostatečně dlouhé pro ně k provádění svých úloh. |
| **Ochrany před únikem informací** | Ujistěte se, [doba trvání aktivace](pim-how-to-change-default-settings.md) pro privilegované role je nastavena dostatečně dlouhé pro uživatele k provádění svých úloh.</br>[Vyžadovat vícefaktorové ověřování](pim-how-to-change-default-settings.md) pro privilegované role, které mají účty, které jsou sdíleny více správců. |
| **Na portálu zmírňující opatření** | neuvedeno |
| **Trigger** | Aktivuje, pokud uživatel aktivuje stejné privilegovaných rolí více než jednou v zadaném období. Můžete nakonfigurovat časové období a počet aktivací. |
| **Časový rámec prodloužení aktivace** | Toto nastavení určuje dny, hodiny, minuty a sekundy časové období chcete použít ke sledování podezřelých obnovení. |
| **Počet prodloužení aktivace** | Toto nastavení určuje počet aktivací, od 2 do 100, které považujete za albertových upozornění v časovém rámci, kterou jste zvolili. Je toto nastavení můžete změnit přesunutím posuvníku nebo zadáním čísla do textového pole. |

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení

Můžete přizpůsobit některé z výstrah zabezpečení ve službě PIM pro práci s vaším prostředím a cíle zabezpečení. Postupujte podle těchto kroků a otevřete nastavení výstrah zabezpečení:

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **role Azure AD**.

1. Klikněte na tlačítko **nastavení** a potom **výstrahy**.

    ![Přejděte do nastavení výstrah zabezpečení](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Klikněte na název výstrahy. ke konfiguraci nastavení pro toto upozornění.

    ![Nastavení výstrah zabezpečení](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role adresáře Azure AD v PIM](pim-how-to-change-default-settings.md)
