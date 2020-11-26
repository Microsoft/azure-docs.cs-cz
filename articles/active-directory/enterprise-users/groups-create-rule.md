---
title: Vytvoření nebo úprava dynamické skupiny a získání stavu – Azure AD | Microsoft Docs
description: Jak vytvořit nebo aktualizovat pravidlo členství ve skupině v Azure Portal a zjistit stav jeho zpracování.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343486286d45538b949de03604811bb2371701c1
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169014"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Vytvoření nebo aktualizace dynamické skupiny v Azure Active Directory

V Azure Active Directory (Azure AD) můžete pomocí pravidel určit členství ve skupině na základě vlastností uživatele nebo zařízení. V tomto článku se dozvíte, jak nastavit pravidlo pro dynamickou skupinu v Azure Portal.
Dynamické členství je podporováno pro skupiny zabezpečení nebo skupiny Microsoft 365. Při použití pravidla členství ve skupině se vyhodnotí atributy uživatelů a zařízení pro shody s pravidlem členství. Když se změní atribut uživatele nebo zařízení, zpracují se všechna pravidla dynamické skupiny v organizaci pro změny členství. Uživatelé a zařízení se přidají nebo odeberou, pokud splňují podmínky pro skupinu. Skupiny zabezpečení lze použít buď pro zařízení, nebo pro uživatele, ale skupiny Microsoft 365 mohou být pouze skupiny uživatelů. Používání dynamických skupin vyžaduje licenci Azure AD Premium P1. Další podrobnosti najdete v tématu [dynamická pravidla členství pro skupiny](./groups-dynamic-membership.md) . 

## <a name="rule-builder-in-the-azure-portal"></a>Tvůrce pravidel v Azure Portal

Azure AD poskytuje tvůrci pravidel pro rychlejší vytváření a aktualizaci důležitých pravidel. Tvůrce pravidel podporuje vytváření až pěti výrazů. Tvůrce pravidel usnadňuje vytvoření pravidla s několika jednoduchými výrazy, ale nelze ho použít k reprodukování všech pravidel. Pokud tvůrce pravidel nepodporuje pravidlo, které chcete vytvořit, můžete použít textové pole.

Tady jsou některé příklady pokročilých pravidel nebo syntaxe, pro které doporučujeme sestavit pomocí textového pole:

- Pravidlo s více než pěti výrazy
- Pravidlo přímých sestav
- Nastavení [priority operátoru](groups-dynamic-membership.md#operator-precedence)
- [Pravidla se složitými výrazy](groups-dynamic-membership.md#rules-with-complex-expressions); například `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Tvůrce pravidel nemusí být schopný zobrazit některá pravidla vytvořená v textovém poli. Když tvůrce pravidel nemůže zobrazit pravidlo, může se zobrazit zpráva. Tvůrce pravidel nemění podporovanou syntaxi, ověřování ani zpracování pravidel dynamických skupin jakýmkoli způsobem.

![Snímek obrazovky zobrazující stránku pravidla dynamického členství s akcí přidat výraz na kartě konfigurovat pravidla](./media/groups-create-rule/update-dynamic-group-rule.png)

Příklady syntaxe, podporovaných vlastností, operátorů a hodnot pravidla členství najdete v tématu [dynamická pravidla členství pro skupiny v Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Vytvoření pravidla členství ve skupině

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který se nachází v roli globální správce, Správce služby Intune nebo Správce uživatelů v organizaci Azure AD.
1. Vyhledejte a vyberte **skupiny**.
1. Vyberte možnost **všechny skupiny** a vyberte možnost **Nová skupina**.

   ![Vyberte příkaz pro přidání nové skupiny.](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. Na stránce **Skupina** zadejte název a popis nové skupiny. Vyberte **typ členství** buď pro uživatele, nebo pro zařízení a pak vyberte **Přidat dynamický dotaz**. Tvůrce pravidel podporuje až pět výrazů. Chcete-li přidat více než pět výrazů, je nutné použít textové pole.

   ![Snímek obrazovky zobrazující stránku všechny skupiny se zvolenou akcí nová skupina](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Chcete-li zobrazit vlastnosti vlastního rozšíření, které jsou k dispozici pro váš dotaz na členství:
   1. Vyberte **získat vlastnosti vlastního rozšíření** .
   1. Zadejte ID aplikace a pak vyberte **aktualizovat vlastnosti**.
1. Po vytvoření pravidla vyberte **Uložit**.
1. Vyberte **vytvořit** na stránce **Nová skupina** a vytvořte skupinu.

Pokud pravidlo, které jste zadali, není platné, zobrazí se v oznámení Azure na portálu vysvětlení, proč se pravidlo nedalo zpracovat. Pečlivě si přečtěte, abyste zjistili, jak pravidlo opravit.

## <a name="to-update-an-existing-rule"></a>Aktualizace existujícího pravidla

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který se nachází v roli globální správce, správce skupin, správce Intune nebo Správce uživatelů v organizaci Azure AD.
1. Vyberte **skupiny**  >  **všechny skupiny**.
1. Vyberte skupinu a otevřete její profil.
1. Na stránce profil pro skupinu vyberte **dynamická pravidla členství**. Tvůrce pravidel podporuje až pět výrazů. Chcete-li přidat více než pět výrazů, je nutné použít textové pole.

   ![Přidat pravidlo členství pro dynamickou skupinu](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Chcete-li zobrazit vlastnosti vlastního rozšíření, které jsou k dispozici pro pravidlo členství:
   1. Vyberte **získat vlastnosti vlastního rozšíření** .
   1. Zadejte ID aplikace a pak vyberte **aktualizovat vlastnosti**.
1. Po aktualizaci pravidla vyberte **Uložit**.

## <a name="turn-on-or-off-welcome-email"></a>Zapnutí nebo vypnutí uvítacího e-mailu

Když se vytvoří nová skupina Microsoft 365, uživatelům, kteří se do této skupiny přidají, se pošle uvítací e-mailové oznámení. Později platí, že pokud se libovolné atributy uživatele nebo zařízení změní, budou se pro změny členství zpracovávat všechna pravidla dynamické skupiny v organizaci. Přidaní uživatelé pak obdrží i uvítací oznámení. Toto chování můžete vypnout v [prostředí Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps&preserve-view=true).

## <a name="check-processing-status-for-a-rule"></a>Zkontroluje stav zpracování pravidla.

Stav zpracování členství a datum poslední aktualizace můžete zobrazit na stránce **Přehled** pro danou skupinu.
  
  ![zobrazení stavu dynamické skupiny](./media/groups-create-rule/group-status.png)

Pro stav **zpracování členství** lze zobrazit následující stavové zprávy:

- **Vyhodnocování**: byla přijata změna skupiny a probíhá vyhodnocení aktualizací.
- **Zpracování**: probíhá zpracování aktualizací.
- **Aktualizace byla dokončena**: zpracování bylo dokončeno a všechny použitelné aktualizace byly provedeny.
- **Chyba zpracování**: zpracování nebylo dokončeno z důvodu chyby při vyhodnocování pravidla členství.
- **Aktualizace pozastavena**: Správce pozastavil aktualizace pravidla dynamického členství. MembershipRuleProcessingState je nastavené na pozastaveno.

Pro stav **Poslední aktualizace členství** se můžou zobrazit tyto stavové zprávy:

- &lt;**Datum a čas** &gt; : čas poslední aktualizace členství.
- **Probíhá: aktuálně** probíhá aktualizace.
- **Neznámé**: čas poslední aktualizace se nedá načíst. Tato skupina může být nová.

Pokud při zpracovávání pravidla členství pro určitou skupinu dojde k chybě, zobrazí se v horní části **stránky přehled** pro danou skupinu výstraha. Pokud se pro všechny skupiny v organizaci po dobu delší než 24 hodin nedají zpracovat žádné nedokončené dynamické aktualizace členství, zobrazí se v horní části **všech skupin** výstraha.

![zpracování upozornění na chybové zprávy](./media/groups-create-rule/processing-error.png)

Tyto články poskytují další informace o skupinách v Azure Active Directory.

- [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Vytvoření nové skupiny a přidání členů](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)