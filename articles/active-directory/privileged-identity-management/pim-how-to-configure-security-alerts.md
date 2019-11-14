---
title: Výstrahy zabezpečení pro role Azure AD v PIM – Azure AD | Microsoft Docs
description: Nakonfigurujte výstrahy zabezpečení pro role Azure AD Privileged Identity Management v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e05502b40f7f917526b2514d83dcc58636ac06f3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023047"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Konfigurace výstrah zabezpečení pro role Azure AD v Privileged Identity Management

Privileged Identity Management (PIM) generuje výstrahy v případě podezřelé nebo nebezpečné aktivity v organizaci Azure Active Directory (Azure AD). Když se aktivuje výstraha, zobrazí se na řídicím panelu Privileged Identity Management. Výběrem výstrahy zobrazíte sestavu se seznamem uživatelů nebo rolí, které výstrahu aktivovaly.

![Role Azure AD – podokno upozornění obsahující výstrahy a závažnost](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Výstrahy zabezpečení

V této části jsou uvedené všechny výstrahy zabezpečení pro role Azure AD společně s postupem opravy a způsobu prevence. Závažnost má následující význam:

- **Vysoká**: vyžaduje okamžitou akci kvůli porušení zásad.
- **Střední**: nevyžaduje okamžitou akci, ale signalizuje potenciální porušení zásad.
- **Nízká**: nevyžaduje okamžitou akci, ale navrhuje upřednostňovanou změnu zásad.

### <a name="administrators-arent-using-their-privileged-roles"></a>Správci nepoužívají své privilegované role.

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi tato výstraha zobrazí?** | Uživatelům, kterým se přiřadily privilegované role, se nepotřebují zvýšit šance na útok. Útočníkům je také snazší zůstat v účtech, které aktivně nejsou používány. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a odeberte je z privilegovaných rolí, které nepotřebují. |
| **Únikem** | Přiřaďte privilegované role pouze uživatelům, kteří mají obchodní odůvodnění. </br>Naplánujte pravidelné kontroly [přístupu](pim-how-to-start-security-review.md) , abyste ověřili, že uživatelé pořád potřebují přístup. |
| **Akce zmírňování na portálu** | Odebere účet z své privilegované role. |
| **Trigger** | Aktivuje se, pokud uživatel překročí zadaný počet dní bez aktivace role. |
| **Počet dní** | Toto nastavení určuje maximální počet dní od 0 do 100, které může uživatel přejít bez aktivace role.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Role nevyžadují službu Multi-Factor Authentication pro aktivaci

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi tato výstraha zobrazí?** | Bez ověřování Multi-Factor Authentication můžou ohrožení uživatelé aktivovat privilegované role. |
| **Jak opravit?** | Zkontrolujte seznam rolí a [vyžadovat vícefaktorové ověřování](pim-how-to-change-default-settings.md) pro každou roli. |
| **Únikem** | [Vyžadovat vícefaktorové ověřování](pim-how-to-change-default-settings.md) pro každou roli.  |
| **Akce zmírňování na portálu** | Pro aktivaci privilegované role je vyžadováno vícefaktorové ověřování. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Organizace nemá Azure AD Premium P2.

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi tato výstraha zobrazí?** | Aktuální organizace Azure AD nemá Azure AD Premium P2. |
| **Jak opravit?** | Přečtěte si informace o [edicích Azure AD](../fundamentals/active-directory-whatis.md). Upgradujte na Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenciální zastaralé účty v privilegované roli

| | |
| --- | --- |
| **Závažnost** | Střednědobé používání |
| **Proč se mi tato výstraha zobrazí?** | Účty v privilegované roli nezměnily heslo během posledních 90 dnů. Tyto účty můžou být služby nebo sdílené účty, které nejsou udržované a jsou zranitelné vůči útočníkům. |
| **Jak opravit?** | Zkontrolujte účty v seznamu. Pokud již přístup nepotřebují, odeberte je ze svých privilegovaných rolí. |
| **Únikem** | Ujistěte se, že účty, které jsou sdíleny, přecházejí silné heslo, když dojde ke změně v uživatelích, kteří znají heslo. </br>Pravidelně kontrolujte účty s privilegovanými rolemi pomocí kontrol [přístupu](pim-how-to-start-security-review.md) a odeberte přiřazení rolí, která už nepotřebujete. |
| **Akce zmírňování na portálu** | Odebere účet z své privilegované role. |
| **Osvědčené postupy** | Účty Shared, Service a Emergency Access, které se ověřují pomocí hesla a jsou přiřazené k vysoce privilegovaným rolím pro správu, jako je globální správce nebo správce zabezpečení, by měli mít svoje hesla otočená v následujících případech:<ul><li>Po bezpečnostním incidentu, který zahrnuje zneužití nebo narušení přístupových práv pro správu</li><li>Po změně oprávnění libovolného uživatele tak, aby již neexistovala jako správce (například po zaměstnanci, který ho správce opustí nebo opustí organizaci)</li><li>V pravidelných intervalech (například čtvrtletní nebo roční), a to i v případě, že nedošlo k žádnému nedodržení předpisů nebo ke změně pracovníků IT</li></ul>Vzhledem k tomu, že k přihlašovacím údajům těchto účtů mají přístup více lidí, by se měla otočit přihlašovací údaje, aby uživatelé, kteří si opustili své role, už nemuseli získat přístup k účtům. [Další informace](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Role se přiřazují mimo Privileged Identity Management

| | |
| --- | --- |
| **Závažnost** | Vysoká |
| **Proč se mi tato výstraha zobrazí?** | Přiřazení privilegovaných rolí, která se provedla mimo Privileged Identity Management, se nemonitorují správně a můžou indikovat aktivní útok. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a odeberte je z privilegovaných rolí přiřazených mimo Privileged Identity Management. |
| **Únikem** | Prozkoumejte, kde se uživatelům přiřazují privilegované role mimo Privileged Identity Management a z nich zakazují budoucí přiřazení. |
| **Akce zmírňování na portálu** | Odebere uživatele ze své privilegované role. |

### <a name="there-are-too-many-global-administrators"></a>Existuje příliš mnoho globálních správců.

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi tato výstraha zobrazí?** | Globální správce je nejvyšší privilegovaná role. Pokud dojde k ohrožení globálního správce, získá útočník přístup ke všem svým oprávněním, což zaznamená ohrožení celého systému. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a odeberte všechny, které nepotřebují roli globálního správce. </br>Přiřaďte těmto uživatelům méně privilegované role. |
| **Únikem** | Přiřaďte uživatelům nejnižší privilegovanou roli, kterou potřebují. |
| **Akce zmírňování na portálu** | Odebere účet z své privilegované role. |
| **Trigger** | Aktivované, pokud jsou splněné dvě různá kritéria, a můžete je nakonfigurovat. Nejprve je třeba dosáhnout určité prahové hodnoty globálních správců. Za druhé, určité procento vašich celkových přiřazení rolí musí být globální správci. Pokud odpovídáte jenom jednomu z těchto měření, výstraha se nezobrazí. |
| **Minimální počet globálních správců** | Toto nastavení určuje počet globálních správců od 2 do 100, které považujete za příliš málo pro vaši organizaci Azure AD. |
| **Procento globálních správců** | Toto nastavení určuje minimální procento správců, kteří jsou globálními správci, od 0 do 100%, pod kterými nechcete, aby vaše organizace Azure AD byla DIP. |

### <a name="roles-are-being-activated-too-frequently"></a>Role se aktivují příliš často.

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi tato výstraha zobrazí?** | Více aktivací stejné privilegované role stejným uživatelem je znaménkem útoku. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a ujistěte se, že [Doba trvání aktivace](pim-how-to-change-default-settings.md) pro příslušnou privilegovanou roli je nastavena dostatečně dlouho, aby mohla provádět jejich úkoly. |
| **Únikem** | Ujistěte se, že je [Doba aktivace](pim-how-to-change-default-settings.md) pro privilegované role nastavená na dostatečnou délku, aby uživatelé mohli provádět své úkoly.</br>[Vyžadovat vícefaktorové ověřování](pim-how-to-change-default-settings.md) pro privilegované role, které mají účty sdílené více správci. |
| **Akce zmírňování na portálu** | neuvedeno |
| **Trigger** | Aktivuje se, pokud uživatel v zadaném období několikrát aktivuje stejnou privilegovanou roli. Můžete nakonfigurovat jak časové období, tak počet aktivací. |
| **Časový rámec pro obnovení aktivace** | Toto nastavení určuje počet dnů, hodin, minut a druhý časový interval, který chcete použít ke sledování podezřelých obnovení. |
| **Počet obnovení aktivace** | Toto nastavení určuje počet aktivací od 2 do 100, na které chcete být upozorněni v rámci zvoleného časového rámce. Toto nastavení můžete změnit přesunutím posuvníku nebo zadáním čísla do textového pole. |

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení

V Privileged Identity Management můžete přizpůsobit některé výstrahy zabezpečení, které budou fungovat s potřebami vaší organizace a cíli zabezpečení. Pomocí těchto kroků otevřete nastavení výstrah zabezpečení:

1. Otevřete **Privileged Identity Management** ve službě Azure AD.

1. Vyberte **role Azure AD**.

1. Vyberte **Nastavení** a pak **výstrahy**.

    ![Role Azure AD – nastavení s vybranými výstrahami](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Vyberte název výstrahy a nakonfigurujte nastavení pro tuto výstrahu.

    ![Pro vybranou výstrahu podokno nastavení výstrah zabezpečení](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role Azure AD v Privileged Identity Management](pim-how-to-change-default-settings.md)
