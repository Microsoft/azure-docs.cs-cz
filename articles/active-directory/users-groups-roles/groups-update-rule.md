---
title: Aktualizace a Správa pravidla dynamické skupiny a řešení potíží s členstvím Azure Active Directory | Microsoft Docs
description: Postup vytvoření pravidla členství ve skupině v Azure Portal najdete v části stav.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/30/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c387e2d78adcaebc430073a2a45818c4a0928b9f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422353"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Aktualizace dynamické skupiny pro správu členství v Azure Active Directory

V Azure Active Directory (Azure AD) můžete pomocí pravidel určit členství ve skupině na základě vlastností uživatele nebo zařízení. V tomto článku se dozvíte, jak nastavit pravidlo pro dynamickou skupinu v Azure Portal.
Dynamické členství je podporováno pro skupiny zabezpečení nebo skupiny Office 365. Při použití pravidla členství ve skupině se vyhodnotí atributy uživatelů a zařízení pro shody s pravidlem členství. Když se změní atribut uživatele nebo zařízení, zpracují se všechna pravidla dynamické skupiny v organizaci pro změny členství. Uživatelé a zařízení se přidají nebo odeberou, pokud splňují podmínky pro skupinu.

## <a name="rule-builder-in-the-azure-portal"></a>Tvůrce pravidel v Azure Portal

Azure AD poskytuje tvůrci pravidel pro rychlejší vytváření a aktualizaci důležitých pravidel. Tvůrce pravidel podporuje vytváření až pěti výrazů. Tvůrce pravidel usnadňuje vytvoření pravidla s několika jednoduchými výrazy, ale nelze ho použít k reprodukování všech pravidel. Pokud tvůrce pravidel nepodporuje pravidlo, které chcete vytvořit, můžete použít textové pole.

Tady jsou některé příklady pokročilých pravidel nebo syntaxe, pro které doporučujeme sestavit pomocí textového pole:

- Pravidlo s více než pěti výrazy
- Pravidlo přímých sestav
- Nastavení [priority operátoru](groups-dynamic-membership.md#operator-precedence)
- [Pravidla se složitými výrazy](groups-dynamic-membership.md#rules-with-complex-expressions); například `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Tvůrce pravidel nemusí být schopný zobrazit některá pravidla vytvořená v textovém poli. Když tvůrce pravidel nemůže zobrazit pravidlo, může se zobrazit zpráva. Tvůrce pravidel nemění podporovanou syntaxi, ověřování ani zpracování pravidel dynamických skupin jakýmkoli způsobem.

![Přidat pravidlo členství pro dynamickou skupinu](./media/groups-update-rule/update-dynamic-group-rule.png)

Příklady syntaxe, podporovaných vlastností, operátorů a hodnot pravidla členství najdete v tématu [dynamická pravidla členství pro skupiny v Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>Aktualizace pravidla členství ve skupině

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který se nachází v roli globální správce, správce skupin, správce Intune nebo Správce uživatelů v tenantovi.
1. Vyberte **skupiny** > **všechny skupiny**.
1. Vyberte skupinu a otevřete její profil.
1. Na stránce profil pro skupinu vyberte **dynamická pravidla členství**. Tvůrce pravidel podporuje až pět výrazů. Chcete-li přidat více než pět výrazů, je nutné použít textové pole.

   ![Přidat pravidlo členství pro dynamickou skupinu](./media/groups-update-rule/update-dynamic-group-rule.png)

1. Chcete-li zobrazit vlastnosti vlastního rozšíření, které jsou k dispozici pro pravidlo členství:
   1. Vyberte **získat vlastnosti vlastního rozšíření** .
   1. Zadejte ID aplikace a pak vyberte **aktualizovat vlastnosti**.
1. Po aktualizaci pravidla vyberte **Uložit**.

Pokud pravidlo, které jste zadali, není platné, zobrazí se v oznámení Azure na portálu vysvětlení, proč se pravidlo nedalo zpracovat. Pečlivě si přečtěte, abyste zjistili, jak pravidlo opravit.

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

- **Datum a čas**: čas poslední aktualizace členství.
- **Probíhá: aktuálně**probíhá aktualizace.
- **Neznámé**: čas poslední aktualizace se nedá načíst. Tato skupina může být nová.

Pokud při zpracovávání pravidla členství pro určitou skupinu dojde k chybě, zobrazí se v horní části **stránky přehled** pro danou skupinu výstraha. Pokud není možné zpracovat žádné nedokončené dynamické aktualizace členství pro všechny skupiny v tenantovi za více než 24 hodin, zobrazí se v horní části **všech skupin**výstraha.

![zpracování upozornění na chybové zprávy](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>Další kroky

Tyto články poskytují další informace o práci s dynamickými skupinami v Azure AD.

- Úplný odkaz na strukturu dynamického pravidla najdete v tématu [syntaxe pravidla dynamického členství](groups-dynamic-membership.md).
- [Vytvořte skupinu statických členství a přidejte členy](../fundamentals/active-directory-groups-create-azure-portal.md).
