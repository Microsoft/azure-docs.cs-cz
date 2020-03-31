---
title: Vytvoření nebo úprava dynamické skupiny a získání stavu – Azure AD | Dokumenty společnosti Microsoft
description: Jak vytvořit nebo aktualizovat pravidlo členství ve skupině na webu Azure portal a zkontrolovat jeho stav zpracování.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/07/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ed7f27e2145f666f38eec5ddc6c985a4d32138
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266374"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Vytvoření nebo aktualizace dynamické skupiny ve službě Azure Active Directory

Ve službě Azure Active Directory (Azure AD) můžete pomocí pravidel určit členství ve skupinách na základě vlastností uživatele nebo zařízení. Tento článek popisuje, jak nastavit pravidlo pro dynamickou skupinu na webu Azure Portal.
Dynamické členství je podporováno pro skupiny zabezpečení nebo skupiny Office 365. Při použití pravidla členství ve skupině jsou atributy uživatele a zařízení vyhodnocovány pro shody s pravidlem členství. Při změně atributu pro uživatele nebo zařízení jsou pro změny členství zpracována všechna pravidla dynamické skupiny v organizaci. Uživatelé a zařízení jsou přidáni nebo odebráni, pokud splňují podmínky pro skupinu. Skupiny zabezpečení lze použít pro zařízení nebo uživatele, ale skupiny Office 365 mohou být jenom skupiny uživatelů.

## <a name="rule-builder-in-the-azure-portal"></a>Tvůrce pravidel na webu Azure Portal

Azure AD poskytuje tvůrce pravidel pro rychlejší vytváření a aktualizaci důležitých pravidel. Tvůrce pravidel podporuje konstrukci až pět výrazů. Tvůrce pravidel usnadňuje vytvoření pravidla s několika jednoduchými výrazy, ale nelze jej použít k reprodukci každého pravidla. Pokud tvůrce pravidel nepodporuje pravidlo, které chcete vytvořit, můžete použít textové pole.

Zde jsou některé příklady pokročilých pravidel nebo syntaxe, pro které doporučujeme vytvořit pomocí textového pole:

- Pravidlo s více než pěti výrazy
- Pravidlo Přímé sestavy
- Nastavení [priority operátora](groups-dynamic-membership.md#operator-precedence)
- [Pravidla se složitými výrazy](groups-dynamic-membership.md#rules-with-complex-expressions); například`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Tvůrce pravidel nemusí být schopen zobrazit některá pravidla vytvořená v textovém poli. Může se zobrazit zpráva, když tvůrce pravidel není schopen zobrazit pravidlo. Tvůrce pravidel žádným způsobem nezmění podporovanou syntaxi, ověření nebo zpracování pravidel dynamické skupiny.

![Přidání pravidla členství pro dynamickou skupinu](./media/groups-create-rule/update-dynamic-group-rule.png)

Příklady syntaxe, podporovaných vlastností, operátorů a hodnot pro pravidlo členství najdete v [tématu Dynamická pravidla členství pro skupiny ve službě Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Vytvoření pravidla členství ve skupině

1. Přihlaste se k [Centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je v roli globálního správce, správce Intune nebo správce uživatelů v tenantovi.
1. Vyhledejte a vyberte **skupiny**.
1. Vyberte **Všechny skupiny**a vyberte **Nová skupina**.

   ![Výběr příkazu pro přidání nové skupiny](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. Na stránce **Skupina** zadejte název a popis nové skupiny. Vyberte **typ členství** pro uživatele nebo zařízení a pak vyberte Přidat dynamický **dotaz**. Tvůrce pravidel podporuje až pět výrazů. Chcete-li přidat více než pět výrazů, musíte použít textové pole.

   ![Přidání pravidla členství pro dynamickou skupinu](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Zobrazení vlastních vlastností rozšíření, které jsou k dispozici pro váš členský dotaz:
   1. Vyberte **Získat vlastní vlastnosti rozšíření.**
   1. Zadejte ID aplikace a vyberte **Aktualizovat vlastnosti**.
1. Po vytvoření pravidla vyberte **Uložit**.
1. Chcete-li vytvořit skupinu, vyberte vytvořit na stránce **Nová skupina.** **Create**

Pokud zadané pravidlo není platné, zobrazí se v oznámení Azure na portálu vysvětlení, proč pravidlo nešlo zpracovat. Přečtěte si ji pozorně, abyste pochopili, jak opravit pravidlo.

## <a name="to-update-an-existing-rule"></a>Aktualizace existujícího pravidla

1. Přihlaste se k [Centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je v roli globálního správce, správce skupiny, správce Intune nebo správce uživatele v tenantovi.
1. Vyberte **Skupiny** > **Všechny skupiny**.
1. Vyberte skupinu, chcete-li otevřít její profil.
1. Na stránce profilu skupiny vyberte **Dynamická pravidla členství**. Tvůrce pravidel podporuje až pět výrazů. Chcete-li přidat více než pět výrazů, musíte použít textové pole.

   ![Přidání pravidla členství pro dynamickou skupinu](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Zobrazení vlastních vlastností rozšíření, které jsou k dispozici pro pravidlo členství:
   1. Vyberte **Získat vlastní vlastnosti rozšíření.**
   1. Zadejte ID aplikace a vyberte **Aktualizovat vlastnosti**.
1. Po aktualizaci pravidla vyberte **Uložit**.

## <a name="turn-on-or-off-welcome-email"></a>Zapnutí nebo vypnutí uvítacího e-mailu

Když se vytvoří nová skupina Office 365, odešle se uživatelům, kteří jsou do skupiny přidáni, uvítací e-mailové oznámení. Později, pokud se změní všechny atributy uživatele nebo zařízení, budou pro změny členství zpracována všechna pravidla dynamické skupiny v organizaci. Uživatelé, kteří jsou přidáni pak také obdrží uvítací oznámení. Toto chování můžete vypnout v [prostředí Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps).

## <a name="check-processing-status-for-a-rule"></a>Zkontrolovat stav zpracování pravidla

Stav zpracování členství a poslední aktualizované datum můžete zobrazit na stránce **Přehled** pro skupinu.
  
  ![zobrazení stavu dynamické skupiny](./media/groups-create-rule/group-status.png)

Pro stav **zpracování členství** lze zobrazit následující stavové zprávy:

- **Vyhodnocení**: Byla přijata změna skupiny a aktualizace jsou vyhodnocovány.
- **Zpracování**: Aktualizace jsou zpracovávány.
- **Aktualizace dokončena**: Zpracování bylo dokončeno a byly provedeny všechny příslušné aktualizace.
- **Chyba zpracování**: Zpracování nelze dokončit z důvodu chyby při vyhodnocování pravidla členství.
- **Aktualizace pozastavena**: Aktualizace pravidel dynamického členství byly pozastaveny správcem. MembershipRuleProcessingState je nastavena na "Pozastaveno".

Pro poslední aktualizovaný stav členství lze zobrazit následující **stavové** zprávy:

- &lt;**Datum a čas**&gt;: Čas poslední aktualizace členství.
- **Probíhá**: Právě probíhá aktualizace.
- **Neznámý:** Čas poslední aktualizace nelze načíst. Skupina může být nová.

Pokud dojde k chybě při zpracování pravidla členství pro určitou skupinu, zobrazí se v horní části **stránky Přehled** pro skupinu výstraha. Pokud nelze zpracovat žádné čekající dynamické aktualizace členství pro všechny skupiny v rámci klienta po dobu delší než 24 hodin, zobrazí se v horní části **všech skupin**výstraha .

![zpracování upozornění na chybové zprávy](./media/groups-create-rule/processing-error.png)

Tyto články obsahují další informace o skupinách ve službě Azure Active Directory.

- [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Vytvoření nové skupiny a přidání členů](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
