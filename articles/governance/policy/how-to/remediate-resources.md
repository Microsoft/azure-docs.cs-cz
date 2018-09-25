---
title: Opravit nekompatibilní prostředky službou Azure Policy
description: Tento návod vás provede nápravné prostředky, které jsou nekompatibilní zásady ve službě Azure Policy.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 747650bc47644cdca07f705f42d063c995ebe9bf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980249"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Opravit nekompatibilní prostředky službou Azure Policy

Prostředky, které jsou pro nekompatibilní **deployIfNotExists** zásady můžou být přepnuté do vyhovujícího stavu prostřednictvím **nápravy**. Náprava dosahuje tím, že zásady spouštění **deployIfNotExists** vliv na stávající prostředky přiřazené zásady. Tento návod vás provede kroky potřebné k lze to provést.

## <a name="how-remediation-security-works"></a>Jak funguje opravy zabezpečení

Při spuštění zásad šablony **deployIfNotExists** definice zásad, dělá to pomocí [se identita spravované](../../../active-directory/managed-identities-azure-resources/overview.md).
Zásady spravované identity pro každé přiřazení pro vás vytvoří, ale musí být zadán podrobné informace o rolích udělit spravovaná identita. Pokud chybí role je spravovaná identita, zobrazuje se při přiřazování zásady nebo iniciativa obsahující zásadu. Během používání portálu, zásady automaticky udělí spravovanou identitu uvedené role po zahájení přiřazení.

![Spravovaná identita - chybějící role](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Pokud se prostředek změnil **deployIfNotExists** je mimo rozsah přiřazení zásad, spravovanou identitu tohoto přiřazení musí být programově udělen přístup nebo nápravy nasazení se nezdaří.

## <a name="configure-policy-definition"></a>Nakonfigurovat definici zásad

Prvním krokem je definování role, která **deployIfNotExists** potřebuje k úspěšnému nasazení obsahu zahrnuty šablony v definici zásad. V části **podrobnosti** vlastnost, přidejte **roleDefinitionIds** vlastnost. Toto je pole řetězců, které odpovídají role ve vašem prostředí.
Úplný příklad najdete v článku [deployIfNotExists příklad](../concepts/effects.md#deployifnotexists-example).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**roleDefinitionIds** používá identifikátor úplný zdroj a nepřijímá krátké **roleName** role. Chcete-li získat ID pro roli "Přispěvatel" ve vašem prostředí, použijte následující kód:

```azurecli-interactive
az role definition list --name 'Contributor'
```

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Contributor'
```

## <a name="create-a-remediation-task"></a>Vytvořte úlohu nápravy

Při vyhodnocování, přiřazení zásad s **deployIfNotExists** efekt Určuje, zda existují nekompatibilní prostředky. Po nalezení nekompatibilní prostředky jsou podrobné informace jsou k dispozici na **nápravy** stránky. Společně se seznamem zásad, které mají nekompatibilní prostředky je možnosti k aktivaci **úloha opravy**. Je to, co vytvoří nasazení z **deployIfNotExists** šablony.

Chcete-li vytvořit **úloha opravy**, postupujte podle těchto kroků:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

   ![Hledání zásad](../media/remediate-resources/search-policy.png)

1. Vyberte **nápravy** na levé straně na stránku služby Azure Policy.

   ![Vyberte možnost nápravy](../media/remediate-resources/select-remediation.png)

1. Všechny **deployIfNotExists** přiřazení zásad s nekompatibilní prostředky jsou k dispozici na **zásady k nápravě** kartu a data tabulky. Klikněte na příslušnou zásadu s prostředky, které jsou nekompatibilní. **Nová úloha opravy** otevře se stránka.

   > [!NOTE]
   > Alternativní způsob, jak otevírat **úloha opravy** stránky je najít a klikněte na zásadu z **dodržování předpisů** stránce a potom klikněte na **vytvořit úlohu nápravy** tlačítko.

1. Na **nová úloha opravy** stránce, filtrovat prostředky pomocí řešení **oboru** tři tečky a vyberte podřízené prostředky, ze které byla přiřazena zásady (včetně na jednotlivé prostředky objekty). Kromě toho pomocí **umístění** rozevíracího seznamu a dále filtrovat prostředky. Pouze prostředky uvedené v tabulce bude opraven.

   ![Napravit – výběr zdroje](../media/remediate-resources/select-resources.png)

1. Náprava úlohu spustit, jakmile prostředků se vyfiltrovaly kliknutím **napravit**. Otevře se stránka zásad dodržování předpisů pro **nápravy úlohy** kartu k zobrazení stavu průběh úlohy.

   ![Napravit – průběh úloh](../media/remediate-resources/task-progress.png)

1. Klikněte na **úloha opravy** ze stránky zásad dodržování předpisů se získat podrobnosti o průběhu. Filtrování používá pro úlohy se zobrazí spolu s seznam prostředků je opravit.

1. Z **remedation úloh** stránky, klikněte pravým tlačítkem na prostředek můžete zobrazit buď nasazení úloh opravy nebo prostředek. Na konci řádku, klikněte na **událostí souvisejících s** zobrazíte podrobnosti, jako je chybová zpráva.

   ![Napravit - prostředků úkolu kontextové nabídky](../media/remediate-resources/resource-task-context-menu.png)

Prostředky nasazené prostřednictvím **úloha opravy** budou přidány do **nasazené prostředky** karty na stránce zásady dodržování předpisů po krátké prodlevě.

## <a name="next-steps"></a>Další postup

- Projděte si příklady v [ukázek Azure Policy](../samples/index.md)
- Zkontrolujte [struktura definic zásad](../concepts/definition-structure.md)
- Kontrola [Principy účinky zásad](../concepts/effects.md)
- Pochopit postup [programové vytváření zásad](programmatically-create.md)
- Zjistěte, jak [získat data o dodržování předpisů](getting-compliance-data.md)
- Připomenutí skupin pro správu v článku [Uspořádání prostředků pomocí skupin pro správu Azure](../../management-groups/overview.md)