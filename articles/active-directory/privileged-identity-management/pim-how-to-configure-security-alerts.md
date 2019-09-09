---
title: Konfigurace výstrah zabezpečení pro role Azure AD v PIM-Azure Active Directory | Microsoft Docs
description: Naučte se konfigurovat výstrahy zabezpečení pro role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3984d3276590357866c824d01ea8c51cf5b28fd7
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804385"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-pim"></a>Konfigurace výstrah zabezpečení pro role Azure AD v PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) generuje výstrahy v případě podezřelé nebo nebezpečné aktivity ve vašem prostředí. Když se aktivuje výstraha, zobrazí se na řídicím panelu PIM. Výběrem výstrahy zobrazíte sestavu se seznamem uživatelů nebo rolí, které výstrahu aktivovaly.

![Role Azure AD – podokno upozornění obsahující výstrahy a závažnost](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Výstrahy zabezpečení

V této části jsou uvedené všechny výstrahy zabezpečení pro role Azure AD společně s postupem opravy a způsobu prevence. Závažnost má následující význam:

* **Vysoká**: Vyžaduje okamžitou akci kvůli porušení zásad.
* **Střední**: Nevyžaduje okamžitou akci, ale signalizuje potenciální porušení zásad.
* **Nízká úroveň**: Nevyžaduje okamžitou akci, ale navrhuje upřednostňovanou změnu zásad.

### <a name="administrators-arent-using-their-privileged-roles"></a>Správci nepoužívají své privilegované role.

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi tato výstraha zobrazí?** | Uživatelům, kterým se přiřadily privilegované role, se nepotřebují zvýšit šance na útok. Útočníkům je také snazší zůstat v účtech, které aktivně nejsou používány. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a odeberte je z privilegovaných rolí, které nepotřebují. |
| **Únikem** | Přiřaďte privilegované role pouze uživatelům, kteří mají obchodní odůvodnění. </br>Naplánujte pravidelné kontroly [přístupu](pim-how-to-start-security-review.md) , abyste ověřili, že uživatelé pořád potřebují přístup. |
| **Akce zmírňování na portálu** | Odebere účet z své privilegované role. |
| **Trigger** | Aktivuje se, pokud uživatel přejde na určitou dobu bez aktivace role. |
| **Počet dní** | Toto nastavení určuje počet dní od 0 do 100, které může uživatel přejít bez aktivace role.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Role nevyžadují službu Multi-Factor Authentication pro aktivaci

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi tato výstraha zobrazí?** | Bez MFA můžou ohrožené uživatele aktivovat privilegované role. |
| **Jak opravit?** | Zkontrolujte seznam rolí a pro každou roli [vyžadovat MFA](pim-how-to-change-default-settings.md) . |
| **Únikem** | [Vyžadovat vícefaktorové ověřování](pim-how-to-change-default-settings.md) pro každou roli.  |
| **Akce zmírňování na portálu** | Provede MFA požadavek na aktivaci privilegované role. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>Tenant nemá Azure AD Premium P2.

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi tato výstraha zobrazí?** | Aktuální tenant nemá Azure AD Premium P2. |
| **Jak opravit?** | Přečtěte si informace o [edicích Azure AD](../fundamentals/active-directory-whatis.md). Upgradujte na Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenciální zastaralé účty v privilegované roli

| | |
| --- | --- |
| **Závažnost** | Střední |
| **Proč se mi tato výstraha zobrazí?** | Účty v privilegované roli, které v posledních 90 dnech nezměnily heslo. Tyto účty můžou být služby nebo sdílené účty, které nejsou udržované a jsou zranitelné vůči útočníkům. |
| **Jak opravit?** | Zkontrolujte účty v seznamu. Pokud již přístup nepotřebují, odeberte je ze svých privilegovaných rolí. |
| **Únikem** | Ujistěte se, že účty, které jsou sdíleny, přecházejí silné heslo, když dojde ke změně v uživatelích, kteří znají heslo. </br>Pravidelně kontrolujte účty s privilegovanými rolemi pomocí kontrol [přístupu](pim-how-to-start-security-review.md) a odeberte přiřazení rolí, která už nepotřebujete. |
| **Akce zmírňování na portálu** | Odebere účet z své privilegované role. |
| **Osvědčené postupy** | Účty Shared, Service a Emergency Access, které se ověřují pomocí hesla a jsou přiřazené k vysoce privilegovaným rolím pro správu, jako je globální správce nebo správce zabezpečení, by měli mít svoje hesla otočená v následujících případech:<ul><li>Po bezpečnostním incidentu, který zahrnuje zneužití nebo narušení přístupových práv pro správu</li><li>Po změně oprávnění libovolného uživatele tak, aby již neexistovala jako správce (například po zaměstnanci, který ho správce opustí nebo opustí organizaci)</li><li>V pravidelných intervalech (například čtvrtletní nebo roční), a to i v případě, že nedošlo k žádnému nedodržení předpisů nebo ke změně pracovníků IT</li></ul>Vzhledem k tomu, že k přihlašovacím údajům těchto účtů mají přístup více lidí, by se měla otočit přihlašovací údaje, aby uživatelé, kteří si opustili své role, už nemuseli získat přístup k účtům. [Víc se uč](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-pim"></a>Role se přiřazují mimo PIM.

| | |
| --- | --- |
| **Závažnost** | Vysoká |
| **Proč se mi tato výstraha zobrazí?** | Přiřazení privilegovaných rolí, která probíhají mimo PIM, nejsou správně monitorována a mohou indikovat aktivní útok. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a odeberte je z privilegovaných rolí přiřazených mimo PIM. |
| **Únikem** | Prozkoumejte, kde se uživatelům přiřazují privilegované role mimo PIM a zakazují budoucí přiřazení. |
| **Akce zmírňování na portálu** | Odebere účet z své privilegované role. |

### <a name="there-are-too-many-global-administrators"></a>Existuje příliš mnoho globálních správců.

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi tato výstraha zobrazí?** | Globální správce je nejvyšší privilegovaná role. Pokud dojde k ohrožení globálního správce, získá útočník přístup ke všem svým oprávněním, což zaznamená ohrožení celého systému. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a odeberte všechny, které nepotřebují roli globálního správce. </br>Přiřaďte těmto uživatelům nižší privilegované role. |
| **Únikem** | Přiřaďte uživatelům nejnižší privilegovanou roli, kterou potřebují. |
| **Akce zmírňování na portálu** | Odebere účet z své privilegované role. |
| **Trigger** | Aktivované, pokud jsou splněné dvě různá kritéria, a můžete je nakonfigurovat. Nejprve je třeba dosáhnout určité prahové hodnoty globálních správců. Za druhé, určité procento vašich celkových přiřazení rolí musí být globální správci. Pokud odpovídáte jenom jednomu z těchto měření, výstraha se nezobrazí. |
| **Minimální počet globálních správců** | Toto nastavení určuje počet globálních správců od 2 do 100, které považujete za nebezpečnou částku. |
| **Procento globálních správců** | Toto nastavení určuje minimální procento správců, kteří jsou globálními správci, od 0% po 100%, což není v prostředí bezpečné. |

### <a name="roles-are-being-activated-too-frequently"></a>Role se aktivují příliš často.

| | |
| --- | --- |
| **Závažnost** | Nízká |
| **Proč se mi tato výstraha zobrazí?** | Více aktivací stejné privilegované role stejným uživatelem je znaménkem útoku. |
| **Jak opravit?** | Zkontrolujte uživatele v seznamu a ujistěte se, že [Doba trvání aktivace](pim-how-to-change-default-settings.md) pro příslušnou privilegovanou roli je nastavena dostatečně dlouho, aby mohla provádět jejich úkoly. |
| **Únikem** | Ujistěte se, že je [Doba aktivace](pim-how-to-change-default-settings.md) pro privilegované role nastavená na dostatečnou délku, aby uživatelé mohli provádět své úkoly.</br>[Vyžadovat MFA](pim-how-to-change-default-settings.md) pro privilegované role, které mají účty sdílené více správci. |
| **Akce zmírňování na portálu** | Není k dispozici |
| **Trigger** | Aktivuje se, pokud uživatel v zadaném období několikrát aktivuje stejnou privilegovanou roli. Můžete nakonfigurovat jak časové období, tak počet aktivací. |
| **Časový rámec pro obnovení aktivace** | Toto nastavení určuje počet dnů, hodin, minut a druhý časový interval, který chcete použít ke sledování podezřelých obnovení. |
| **Počet obnovení aktivace** | Toto nastavení určuje počet aktivací od 2 do 100, které považujete za důvěryhodného výstrahy v rámci zvoleného časového rámce. Toto nastavení můžete změnit přesunutím posuvníku nebo zadáním čísla do textového pole. |

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení

Můžete přizpůsobit některé výstrahy zabezpečení v PIM, aby fungovaly s vaším prostředím a cíli zabezpečení. Pomocí těchto kroků otevřete nastavení výstrah zabezpečení:

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **role Azure AD**.

1. Klikněte na **Nastavení** a pak na **výstrahy**.

    ![Role Azure AD – nastavení s vybranými výstrahami](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Klikněte na název výstrahy a nakonfigurujte nastavení pro tuto výstrahu.

    ![Pro vybranou výstrahu podokno nastavení výstrah zabezpečení](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role Azure AD v PIM](pim-how-to-change-default-settings.md)
