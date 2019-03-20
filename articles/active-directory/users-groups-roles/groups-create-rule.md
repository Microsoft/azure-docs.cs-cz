---
title: Vytvoření dynamické skupiny a kontrola stavu – Azure Active Directory | Dokumentace Microsoftu
description: Jak vytvořit pravidla členství ve skupině na webu Azure Portal, zkontrolujte stav.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f828ff83e6b9c60eb08edef7f47e88185fb5aef8
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199677"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Vytvoření dynamické skupiny a zkontrolovat stav

Ve službě Azure Active Directory (Azure AD) můžete použít pravidla pro zjišťování členství ve skupinách na základě vlastností uživatele nebo zařízení. Tento článek vysvětluje, jak vytvořit pravidlo pro dynamické skupiny na webu Azure Portal.
Dynamické členství je podporovaná pro skupiny zabezpečení nebo skupiny Office 365. Při použití pravidla členství ve skupině atributů uživatelů a zařízení se vyhodnotí shod s využitím pravidel členství. Když atribut pro uživatele nebo zařízení, všechny dynamické skupiny v organizaci se zpracovávají změny členství. Uživatelé a zařízení přidávají a odebírají, pokud nebudou splňovat podmínky pro skupinu.

Příklady syntaxe, podporovaných vlastností, operátory a hodnoty pro pravidlo členství, najdete v článku [pravidla dynamického členství pro skupiny ve službě Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Chcete-li vytvořit pravidla členství ve skupině

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který se nachází v globální správce, Správce služby Intune nebo role uživatele správce v tenantovi.
2. Vyberte **skupiny**.
3. Vyberte **všechny skupiny**a vyberte **novou skupinu**.

   ![Vyberte příkaz pro přidání nové skupiny](./media/groups-create-rule/new-group-creation.png)

4. Na **skupiny** stránky, zadejte název a popis nové skupiny. Vyberte **typ členství** pro uživatele nebo zařízení a pak vyberte **Přidat dynamický dotaz**. Tvůrce pravidlo můžete použít k vytvoření jednoduché pravidlo, nebo [napsat pravidlo členství, sami](groups-dynamic-membership.md).

   ![Přidat pravidlo členství pro dynamickou skupinu](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Pokud chcete zobrazit vlastnosti vlastní rozšíření, která je k dispozici pro dotaz na členství
   1. Vyberte **získání rozšíření vlastních vlastností**
   2. Zadejte ID aplikace a pak vyberte **aktualizovat vlastnosti**. 
6. Po vytvoření pravidla, vyberte **přidat dotaz** v dolní části okna.
7. Vyberte **vytvořit** na **skupiny** okno pro vytvoření skupiny.

Pokud pravidlo, které jste zadali, není platná, zobrazí se vysvětlení, proč nebylo možné zpracovat pravidlo v pravém horním rohu portálu. Přečtěte si ho pečlivě, abyste pochopili, jak opravit pravidlo.

## <a name="turn-on-or-off-welcome-email"></a>Zapněte nebo vypněte Uvítacího e-mailu

Když se vytvoří nová skupina Office 365, úvodní oznámení se posílá uživatele, kteří jsou přidány do skupiny. Později Pokud se změní libovolné atributy uživatele nebo zařízení, všechny dynamické skupiny v organizaci se zpracovávají změny členství. Uživatelé, kteří jsou přidány také obdrží úvodní oznámení. Můžete ji vypnout toto chování v [Powershellu v Exchangi](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="check-processing-status-for-a-rule"></a>Kontrola stavu zpracování pro pravidlo

Zobrazí členství na zpracování stavu a datum poslední aktualizace **přehled** stránce pro skupinu.
  
  ![Zobrazení stavu dynamické skupiny](./media/groups-create-rule/group-status.png)

Tyto stavové zprávy lze zobrazit pro **členství zpracování** stavu:

* **Vyhodnocení**:  Byla přijata změnu skupiny a aktualizace se právě vyhodnocována.
* **Zpracování**: Aktualizace se právě zpracovává.
* **Dokončena aktualizace**: Bylo dokončeno zpracování a provedli všechny použitelné aktualizace.
* **Chyba zpracování**:  Zpracování nelze dokončit, protože došlo k chybě vyhodnocení pravidla členství.
* **Pozastavená aktualizace**: Aktualizace pravidla dynamického členství byla pozastavena správcem. MembershipRuleProcessingState nastavená na "Pozastaveno".

Tyto stavové zprávy lze zobrazit pro **členství poslední aktualizace** stavu:

* &lt;**Datum a čas**&gt;: Čas posledního členství byl aktualizován.
* **V průběhu**: Právě probíhají aktualizace.
* **Neznámý**: Čas poslední aktualizace nelze načíst. Skupina může být nový.

Pokud dojde k chybě při zpracování pravidla členství pro konkrétní skupinu, upozornění se zobrazí v horní **stránka s přehledem** pro skupinu. Pokud ne čekající členství v dynamické aktualizace lze zpracovat pro všechny skupiny v rámci tenanta pro další pak 24 hodin, upozornění se zobrazí v horní **všechny skupiny**.

![zpracování chybové zprávy oznámení](./media/groups-create-rule/processing-error.png)

Tyto články poskytují další informace o skupinách ve službě Azure Active Directory.

* [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Vytvoření nové skupiny a přidání členů](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
