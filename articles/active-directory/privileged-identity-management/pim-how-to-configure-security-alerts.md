---
title: Výstrahy zabezpečení pro role Azure AD v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Konfigurace výstrah zabezpečení pro role Azure AD Privileged Identity Management ve službě Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86dbcdc24c90ba8b161b041af96cbdd0665ad827
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253309"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Konfigurace výstrah zabezpečení pro role Azure AD ve správě privilegovaných identit

Privilegovaná správa identit (PIM) generuje výstrahy, když je podezřelá nebo nebezpečná aktivita ve vaší organizaci Azure Active Directory (Azure AD). Když se aktivuje výstraha, zobrazí se na řídicím panelu správy privilegovaných identit. Výběrem výstrahy zobrazíte sestavu se seznamem uživatelů nebo rolí, které výstrahu spustily.

## <a name="determine-your-version-of-pim"></a>Určení verze PIM

Počínaje listopadem 2019 se část rolí Azure AD privileged identity Management aktualizuje na novou verzi, která odpovídá prostředí pro role prostředků Azure. Tím se vytvoří další funkce, stejně jako [změny existující rozhraní API](azure-ad-roles-features.md#api-changes). Při zavádění nové verze, které postupy, které budete postupovat v tomto článku závisí na verzi privilegované správy identit, které aktuálně máte. Podle pokynů v této části určete, kterou verzi správy privilegovaných identit máte. Poté, co znáte verzi správy privilegovaných identit, můžete vybrat postupy v tomto článku, které odpovídají této verzi.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) s uživatelem, který je v roli [správce privilegované role.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Otevřete **správu privilegovaných identit Azure AD**. Pokud máte v horní části stránky přehledu nápis, postupujte podle pokynů na kartě **Nová verze** tohoto článku. V opačném případě postupujte podle pokynů na kartě **Předchozí verze.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Postupujte podle kroků v tomto článku prozkoumat výstrahy zabezpečení pro role Azure AD.

# <a name="new-version"></a>[Nová verze](#tab/new)

![Role Azure AD – výstrahy podokna výstrah a závažnost](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Výstrahy zabezpečení

V této části jsou uvedeny všechny výstrahy zabezpečení pro role Azure AD, spolu s jak opravit a jak zabránit. Závažnost má následující význam:

- **Vysoká**: Vyžaduje okamžitou akci z důvodu porušení zásad.
- **Střední**: Nevyžaduje okamžitou akci, ale signalizuje potenciální porušení zásad.
- **Nízká**: Nevyžaduje okamžitá opatření, ale navrhuje vhodnější změnu politiky.

### <a name="administrators-arent-using-their-privileged-roles"></a>Správci nepoužívají své privilegované role

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi toto upozornění zobrazí?** | Uživatelé, kterým byly přiřazeny privilegované role, které nepotřebují, zvyšují pravděpodobnost útoku. Je také jednodušší pro útočníky zůstat bez povšimnutí v účtech, které nejsou aktivně používány. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a odeberte je z privilegovaných rolí, které nepotřebují. |
| **Prevention (Prevence)** | Privilegované role přiřaďte pouze uživatelům, kteří mají obchodní odůvodnění. </br>Naplánujte pravidelné [kontroly přístupu](pim-how-to-start-security-review.md) a ověřte, zda uživatelé stále potřebují svůj přístup. |
| **Akce zmírnění na portálu** | Odebere účet z jejich privilegované role. |
| **Trigger** | Aktivuje se, pokud uživatel přejde přes zadaný počet dní bez aktivace role. |
| **Počet dní** | Toto nastavení určuje maximální počet dní od 0 do 100, které může uživatel přejít bez aktivace role.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Role nevyžadují vícefaktorové ověřování pro aktivaci

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi toto upozornění zobrazí?** | Bez vícefaktorového ověřování mohou ohrožení zabezpečení uživatelé aktivovat privilegované role. |
| **Jak opravit?** | Prohlédněte si seznam rolí a [vyžadovat vícefaktorové ověřování](pim-how-to-change-default-settings.md) pro každou roli. |
| **Prevention (Prevence)** | [Vyžadovat vícefaktorové řízení](pim-how-to-change-default-settings.md) pro každou roli.  |
| **Akce zmírnění na portálu** | Je pro aktivaci privilegované role vyžadováno vícefaktorové ověřování. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Organizace nemá Azure AD Premium P2

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi toto upozornění zobrazí?** | Aktuální organizace Azure AD nemá Azure AD Premium P2. |
| **Jak opravit?** | Zkontrolujte informace o [edicích Azure AD](../fundamentals/active-directory-whatis.md). Upgradujte na Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenciální zastaralé účty v privilegované roli

| | |
| --- | --- |
| **Závažnost** | Střednědobé používání |
| **Proč se mi toto upozornění zobrazí?** | Účty v privilegované roli nezměnily své heslo za posledních 90 dní. Tyto účty mohou být služby nebo sdílené účty, které nejsou udržovány a jsou zranitelné vůči útočníkům. |
| **Jak opravit?** | Zkontrolujte účty v seznamu. Pokud již nepotřebují přístup, odeberte je z jejich privilegovaných rolí. |
| **Prevention (Prevence)** | Ujistěte se, že účty, které jsou sdíleny rotující silná hesla, když dojde ke změně v uživatelé, kteří znají heslo. </br>Pravidelně kontrolujte účty s privilegovanými rolemi pomocí [kontrol přístupu](pim-how-to-start-security-review.md) a odeberte přiřazení rolí, která již nejsou potřebná. |
| **Akce zmírnění na portálu** | Odebere účet z jejich privilegované role. |
| **Osvědčené postupy** | Sdílené účty, účty pro sdílený, servisní a nouzový přístup, které se ověřují pomocí hesla a jsou přiřazeny k vysoce privilegovaným rolím správy, jako je globální správce nebo správce zabezpečení, by měly mít svá hesla otočená v následujících případech:<ul><li>Po bezpečnostním incidentu, který zahrnuje zneužití nebo ohrožení práv na přístup správce</li><li>Po změně oprávnění libovolného uživatele tak, aby již nebyly správcem (například poté, co zaměstnanec, který byl správcem, opustí IT nebo opustí organizaci)</li><li>V pravidelných intervalech (například čtvrtletně nebo ročně) i v případě, že nedošlo k žádnému</li></ul>Vzhledem k tomu, že k přihlašovacím údajům těchto účtů má přístup více osob, měla by být pověření otočena, aby bylo zajištěno, že uživatelé, kteří opustili své role, již nebudou mít přístup k účtům. [Další informace o zabezpečení účtů](../users-groups-roles/directory-admin-roles-secure.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Role jsou přiřazovány mimo správu privilegovaných identit.

| | |
| --- | --- |
| **Závažnost** | Vysoká |
| **Proč se mi toto upozornění zobrazí?** | Přiřazení privilegovaných rolí provedená mimo správu privilegovaných identit nejsou řádně sledována a mohou znamenat aktivní útok. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a odeberte je z privilegovaných rolí přiřazených mimo správu privilegovaných identit. |
| **Prevention (Prevence)** | Zjistěte, kde jsou uživatelům přiřazovány privilegované role mimo správu privilegovaných identit, a zakažte odtud budoucí přiřazení. |
| **Akce zmírnění na portálu** | Odebere uživatele z jeho privilegované role. |

### <a name="there-are-too-many-global-administrators"></a>Existuje příliš mnoho globálních správců.

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi toto upozornění zobrazí?** | Globální správce je nejvyšší privilegovanou rolí. Pokud dojde k ohrožení zabezpečení globálního správce, útočník získá přístup ke všem svým oprávněním, což ohrožuje celý systém. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a odeberte všechny, které absolutně nepotřebují roli globálního správce. </br>Místo toho těmto uživatelům přiřaďte nižší privilegované role. |
| **Prevention (Prevence)** | Přiřaďte uživatelům nejméně privilegovanou roli, kterou potřebují. |
| **Akce zmírnění na portálu** | Odebere účet z jejich privilegované role. |
| **Trigger** | Aktivuje se, pokud jsou splněna dvě různá kritéria a můžete je nakonfigurovat. Nejprve je třeba dosáhnout určité prahové hodnoty globálních správců. Za druhé, určité procento z celkového počtu přiřazení rolí musí být globální správci. Pokud se setkáte pouze s jedním z těchto měření, výstraha se nezobrazí. |
| **Minimální počet globálních správců** | Toto nastavení určuje počet globálních správců od 2 do 100, které považujete za příliš málo pro vaši organizaci Azure AD. |
| **Procento globálních správců** | Toto nastavení určuje minimální procento správců, kteří jsou globální správci, od 0 % do 100 %, pod kterou nechcete, aby vaše organizace Azure AD klesla. |

### <a name="roles-are-being-activated-too-frequently"></a>Role jsou aktivovány příliš často

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi toto upozornění zobrazí?** | Více aktivací stejné privilegované role stejným uživatelem je známkou útoku. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a ujistěte se, že [doba aktivace](pim-how-to-change-default-settings.md) pro jejich privilegovanou roli je nastavena dostatečně dlouho, aby mohly provádět své úkoly. |
| **Prevention (Prevence)** | Ujistěte se, že [doba trvání aktivace](pim-how-to-change-default-settings.md) privilegovaných rolí je nastavena dostatečně dlouho, aby uživatelé mohli provádět své úkoly.</br>[Vyžadovat vícefaktorové ověřování](pim-how-to-change-default-settings.md) pro privilegované role, které mají účty sdílené více správci. |
| **Akce zmírnění na portálu** | Není dostupné. |
| **Trigger** | Aktivuje se, pokud uživatel aktivuje stejnou privilegovanou roli vícekrát během zadaného období. Můžete nakonfigurovat časové období i počet aktivací. |
| **Časový rámec obnovení aktivace** | Toto nastavení určuje ve dnech, hodinách, minutách a po druhé časové období, které chcete použít ke sledování podezřelých obnovení. |
| **Počet obnovení aktivace** | Toto nastavení určuje počet aktivací od 2 do 100, ve kterých chcete být upozorněni v časovém rámci, který jste zvolili. Toto nastavení můžete změnit přesunutím jezdce nebo zadáním čísla do textového pole. |

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení

Na stránce Výstrahy přejděte na **Nastavení**.

![Stránka Upozornění se zvýrazněným nastavením](media/pim-how-to-configure-security-alerts/alert-settings.png)

Přizpůsobte si nastavení různých výstrah tak, aby fungovaly s vaším prostředím a cíli zabezpečení.

![Nastavení stránky pro výstrahu pro povolení a konfiguraci nastavení](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

![Role Azure AD – výstrahy podokna výstrah a závažnost](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Výstrahy zabezpečení

V této části jsou uvedeny všechny výstrahy zabezpečení pro role Azure AD, spolu s jak opravit a jak zabránit. Závažnost má následující význam:

- **Vysoká**: Vyžaduje okamžitou akci z důvodu porušení zásad.
- **Střední**: Nevyžaduje okamžitou akci, ale signalizuje potenciální porušení zásad.
- **Nízká**: Nevyžaduje okamžitá opatření, ale navrhuje vhodnější změnu politiky.

### <a name="administrators-arent-using-their-privileged-roles"></a>Správci nepoužívají své privilegované role

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi toto upozornění zobrazí?** | Uživatelé, kterým byly přiřazeny privilegované role, které nepotřebují, zvyšují pravděpodobnost útoku. Je také jednodušší pro útočníky zůstat bez povšimnutí v účtech, které nejsou aktivně používány. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a odeberte je z privilegovaných rolí, které nepotřebují. |
| **Prevention (Prevence)** | Privilegované role přiřaďte pouze uživatelům, kteří mají obchodní odůvodnění. </br>Naplánujte pravidelné [kontroly přístupu](pim-how-to-start-security-review.md) a ověřte, zda uživatelé stále potřebují svůj přístup. |
| **Akce zmírnění na portálu** | Odebere účet z jejich privilegované role. |
| **Trigger** | Aktivuje se, pokud uživatel přejde přes zadaný počet dní bez aktivace role. |
| **Počet dní** | Toto nastavení určuje maximální počet dní od 0 do 100, které může uživatel přejít bez aktivace role.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Role nevyžadují vícefaktorové ověřování pro aktivaci

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi toto upozornění zobrazí?** | Bez vícefaktorového ověřování mohou ohrožení zabezpečení uživatelé aktivovat privilegované role. |
| **Jak opravit?** | Prohlédněte si seznam rolí a [vyžadovat vícefaktorové ověřování](pim-how-to-change-default-settings.md) pro každou roli. |
| **Prevention (Prevence)** | [Vyžadovat vícefaktorové řízení](pim-how-to-change-default-settings.md) pro každou roli.  |
| **Akce zmírnění na portálu** | Je pro aktivaci privilegované role vyžadováno vícefaktorové ověřování. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Organizace nemá Azure AD Premium P2

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi toto upozornění zobrazí?** | Aktuální organizace Azure AD nemá Azure AD Premium P2. |
| **Jak opravit?** | Zkontrolujte informace o [edicích Azure AD](../fundamentals/active-directory-whatis.md). Upgradujte na Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenciální zastaralé účty v privilegované roli

| | |
| --- | --- |
| **Závažnost** | Střednědobé používání |
| **Proč se mi toto upozornění zobrazí?** | Účty v privilegované roli nezměnily své heslo za posledních 90 dní. Tyto účty mohou být služby nebo sdílené účty, které nejsou udržovány a jsou zranitelné vůči útočníkům. |
| **Jak opravit?** | Zkontrolujte účty v seznamu. Pokud již nepotřebují přístup, odeberte je z jejich privilegovaných rolí. |
| **Prevention (Prevence)** | Ujistěte se, že účty, které jsou sdíleny rotující silná hesla, když dojde ke změně v uživatelé, kteří znají heslo. </br>Pravidelně kontrolujte účty s privilegovanými rolemi pomocí [kontrol přístupu](pim-how-to-start-security-review.md) a odeberte přiřazení rolí, která již nejsou potřebná. |
| **Akce zmírnění na portálu** | Odebere účet z jejich privilegované role. |
| **Osvědčené postupy** | Sdílené účty, účty pro sdílený, servisní a nouzový přístup, které se ověřují pomocí hesla a jsou přiřazeny k vysoce privilegovaným rolím správy, jako je globální správce nebo správce zabezpečení, by měly mít svá hesla otočená v následujících případech:<ul><li>Po bezpečnostním incidentu, který zahrnuje zneužití nebo ohrožení práv na přístup správce</li><li>Po změně oprávnění libovolného uživatele tak, aby již nebyly správcem (například poté, co zaměstnanec, který byl správcem, opustí IT nebo opustí organizaci)</li><li>V pravidelných intervalech (například čtvrtletně nebo ročně) i v případě, že nedošlo k žádnému</li></ul>Vzhledem k tomu, že k přihlašovacím údajům těchto účtů má přístup více osob, měla by být pověření otočena, aby bylo zajištěno, že uživatelé, kteří opustili své role, již nebudou mít přístup k účtům. [Další informace](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Role jsou přiřazovány mimo správu privilegovaných identit.

| | |
| --- | --- |
| **Závažnost** | Vysoká |
| **Proč se mi toto upozornění zobrazí?** | Přiřazení privilegovaných rolí provedená mimo správu privilegovaných identit nejsou řádně sledována a mohou znamenat aktivní útok. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a odeberte je z privilegovaných rolí přiřazených mimo správu privilegovaných identit. |
| **Prevention (Prevence)** | Zjistěte, kde jsou uživatelům přiřazovány privilegované role mimo správu privilegovaných identit, a zakažte odtud budoucí přiřazení. |
| **Akce zmírnění na portálu** | Odebere uživatele z jeho privilegované role. |

### <a name="there-are-too-many-global-administrators"></a>Existuje příliš mnoho globálních správců.

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi toto upozornění zobrazí?** | Globální správce je nejvyšší privilegovanou rolí. Pokud dojde k ohrožení zabezpečení globálního správce, útočník získá přístup ke všem svým oprávněním, což ohrožuje celý systém. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a odeberte všechny, které absolutně nepotřebují roli globálního správce. </br>Místo toho těmto uživatelům přiřaďte nižší privilegované role. |
| **Prevention (Prevence)** | Přiřaďte uživatelům nejméně privilegovanou roli, kterou potřebují. |
| **Akce zmírnění na portálu** | Odebere účet z jejich privilegované role. |
| **Trigger** | Aktivuje se, pokud jsou splněna dvě různá kritéria a můžete je nakonfigurovat. Nejprve je třeba dosáhnout určité prahové hodnoty globálních správců. Za druhé, určité procento z celkového počtu přiřazení rolí musí být globální správci. Pokud se setkáte pouze s jedním z těchto měření, výstraha se nezobrazí. |
| **Minimální počet globálních správců** | Toto nastavení určuje počet globálních správců od 2 do 100, které považujete za příliš málo pro vaši organizaci Azure AD. |
| **Procento globálních správců** | Toto nastavení určuje minimální procento správců, kteří jsou globální správci, od 0 % do 100 %, pod kterou nechcete, aby vaše organizace Azure AD klesla. |

### <a name="roles-are-being-activated-too-frequently"></a>Role jsou aktivovány příliš často

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi toto upozornění zobrazí?** | Více aktivací stejné privilegované role stejným uživatelem je známkou útoku. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a ujistěte se, že [doba aktivace](pim-how-to-change-default-settings.md) pro jejich privilegovanou roli je nastavena dostatečně dlouho, aby mohly provádět své úkoly. |
| **Prevention (Prevence)** | Ujistěte se, že [doba trvání aktivace](pim-how-to-change-default-settings.md) privilegovaných rolí je nastavena dostatečně dlouho, aby uživatelé mohli provádět své úkoly.</br>[Vyžadovat vícefaktorové ověřování](pim-how-to-change-default-settings.md) pro privilegované role, které mají účty sdílené více správci. |
| **Akce zmírnění na portálu** | Není dostupné. |
| **Trigger** | Aktivuje se, pokud uživatel aktivuje stejnou privilegovanou roli vícekrát během zadaného období. Můžete nakonfigurovat časové období i počet aktivací. |
| **Časový rámec obnovení aktivace** | Toto nastavení určuje ve dnech, hodinách, minutách a po druhé časové období, které chcete použít ke sledování podezřelých obnovení. |
| **Počet obnovení aktivace** | Toto nastavení určuje počet aktivací od 2 do 100, ve kterých chcete být upozorněni v časovém rámci, který jste zvolili. Toto nastavení můžete změnit přesunutím jezdce nebo zadáním čísla do textového pole. |

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení

Některé výstrahy zabezpečení ve správě privilegovaných identit můžete přizpůsobit tak, aby fungovaly s potřebami a cíli zabezpečení vaší organizace. Chcete-li otevřít nastavení výstrah zabezpečení, postupujte takto:

1. Otevřete **správu privilegovaných identit** ve službě Azure AD.

1. Vyberte **role Azure AD**.

1. Vyberte **Nastavení** a potom **výstrahy**.

    ![Role Azure AD – nastavení s vybranými výstrahami](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Vyberte název výstrahy a nakonfigurujte nastavení pro tuto výstrahu.

    ![U vybrané výstrahy podokno nastavení výstrah zabezpečení](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role Azure AD ve správě privilegovaných identit](pim-how-to-change-default-settings.md)
