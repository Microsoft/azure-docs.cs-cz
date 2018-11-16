---
title: Správa záloh pomocí řízení přístupu na základě rolí Azure.
description: Použití řízení přístupu na základě rolí ke správě přístupu k operacím správy zálohování v trezoru služby Recovery Services.
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: trinadhk
ms.openlocfilehash: de7c00717349a1c814c5a13508adb11879aa10a5
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51704643"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Použití řízení přístupu na základě rolí ke správě body obnovení Azure Backup
Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí řízení přístupu na základě role můžete povinnosti v rámci týmu oddělit a udělit uživatelům jenom takový přístup, který potřebují k výkonu své práce.

> [!IMPORTANT]
> Role, které poskytuje Azure Backup jsou omezené na akce, které můžete provádět na webu Azure portal nebo rutin Powershellu pro trezor služby Recovery Services. Akce prováděné v Azure zálohování uživatelského rozhraní agenta klienta nebo systému System center Data Protection Manager uživatelského rozhraní nebo uživatelské rozhraní serveru služby zálohování Azure jsou mimo ovládací prvek z těchto rolí.

Azure Backup poskytuje 3 vestavěné role řízení operací správy zálohování. Další informace o [předdefinovaných rolích Azure RBAC](../role-based-access-control/built-in-roles.md)

* [Přispěvatel zálohování](../role-based-access-control/built-in-roles.md#backup-contributor) – tato role má všechna oprávnění k vytvoření a Správa zálohování s výjimkou vytvoření trezoru služby Recovery Services a udělování přístupu jiným uživatelům. Představte si tuto roli správce správy zálohování, kdo může provádět všechny operace správy zálohování.
* [Operátor zálohování](../role-based-access-control/built-in-roles.md#backup-operator) – tato role má oprávnění ke všemu, co přispěvatelé s výjimkou odebírání záloh a Správa zásad zálohování. Tato role je ekvivalentní k přispěvatelů s tím rozdílem, že nemůže provádět destruktivní operace, jako je zastavení zálohování s data odstranit nebo odebrat registraci místních prostředků.
* [Zálohování čtečky](../role-based-access-control/built-in-roles.md#backup-reader) – tato role má oprávnění k zobrazení všech operací správy zálohování. Představte si tuto roli monitorování osoba.

Pokud potřebujete definovat vlastní role pro ještě větší kontrolu, naleznete v tématu Jak [vytvářet vlastní role](../role-based-access-control/custom-roles.md) v Azure RBAC.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapování zálohování předdefinované role pro akce správy zálohování
Následující tabulka udává akce správy zálohování a odpovídající minimální role RBAC potřebná k provedení této operace.

| Operace správy | Vyžaduje minimální role RBAC | Rozsah, požadováno |
| --- | --- | --- |
| Vytvoření trezoru služby Recovery Services | Přispěvatel | Skupina prostředků obsahující trezor |
| Povolit zálohování virtuálních počítačů Azure | Operátor zálohování | Skupina prostředků obsahující trezor |
| | Přispěvatel virtuálních počítačů | Prostředek virtuálního počítače |
| Zálohování virtuálního počítače na vyžádání | Operátor zálohování | Prostředek trezoru pro obnovení |
| Obnovení virtuálního počítače | Operátor zálohování | Trezor služby Recovery Services |
| | Přispěvatel | Skupina prostředků, ve kterém bude nasazen virtuální počítač |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální počítač zálohoval |
| Obnovit zálohování nespravovaných disků virtuálních počítačů | Operátor zálohování | Prostředek trezoru pro obnovení |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální počítač zálohoval |
| | Přispěvatel účtů úložiště | Pokud se chystáte disky obnovit prostředek účtu úložiště |
| Obnovení spravovaných disků ze záloh virtuálních počítačů | Operátor zálohování | Prostředek trezoru pro obnovení |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální počítač zálohoval |
| | Přispěvatel účtů úložiště | Dočasné účtu úložiště jako součást obnovení vybraný pro uchovávání dat z trezoru před převodem na spravované disky |
| | Přispěvatel | Skupina prostředků, ke kterému se obnovit spravované disky |
| Obnovení jednotlivých souborů ze záloh virtuálních počítačů | Operátor zálohování | Prostředek trezoru pro obnovení |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální počítač zálohoval |
| Vytvoření zásady zálohování pro zálohování virtuálních počítačů Azure | Přispěvatel zálohování | Prostředek trezoru pro obnovení |
| Upravit zásady zálohování, zálohování virtuálních počítačů Azure | Přispěvatel zálohování | Prostředek trezoru pro obnovení |
| Odstraňování zásady zálohování, zálohování virtuálních počítačů Azure | Přispěvatel zálohování | Prostředek trezoru pro obnovení |
| Zastavit zálohování (při zachování dat nebo odstranit data) zálohování virtuálních počítačů | Přispěvatel zálohování | Prostředek trezoru pro obnovení |
| Registr systému Windows na místním serveru/klienta/SCDPM nebo Azure Backup serveru | Operátor zálohování | Prostředek trezoru pro obnovení |
| Odstranit registrovaný v místním systému Windows Server/klient/SCDPM nebo serveru Azure Backup | Přispěvatel zálohování | Prostředek trezoru pro obnovení |

> [!IMPORTANT]
> Pokud zadáte Přispěvatel virtuálních počítačů v oboru prostředků virtuálního počítače a klikněte na zálohu jako součást nastavení virtuálního počítače, otevře se obrazovka 'Povolit zálohování' i v případě, že virtuální počítač je již zálohovali jako volání zkontrolujte, jestli stav zálohování funguje pouze na úrovni předplatného. Abyste tomu předešli, buď přejděte do trezoru a otevřete zobrazení zálohovaná položka virtuálního počítače nebo zadejte role Přispěvatel virtuálních počítačů na úrovni předplatného. 

## <a name="next-steps"></a>Další postup
* [Řízení přístupu na základě rolí](../role-based-access-control/role-assignments-portal.md): Začínáme s RBAC na webu Azure Portal.
* Další informace o správě přístupu pomocí:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Na základě rolí řešení potíží s řízením přístupu](../role-based-access-control/troubleshooting.md): získat doporučení pro řešení běžných problémů.
