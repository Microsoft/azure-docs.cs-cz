---
title: Správa záloh pomocí řízení přístupu na základě rolí Azure.
description: Použití řízení přístupu na základě rolí ke správě přístupu k operacím správy zálohování v trezoru služby Recovery Services.
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/11/2018
ms.author: trinadhk
ms.openlocfilehash: 855b75652fca421df12766f7711152d1e3ca2aeb
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009249"
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

| Operace správy | Vyžaduje minimální role RBAC |
| --- | --- |
| Vytvoření trezoru služby Recovery Services | Přispěvatelem skupiny prostředků trezoru |
| Povolit zálohování virtuálních počítačů Azure | Operátor zálohování, které jsou definovány v oboru skupiny prostředků obsahující trezor, Přispěvatel virtuálních počítačů na virtuálních počítačích |
| Zálohování virtuálního počítače na vyžádání | Operátor zálohování |
| Obnovení virtuálního počítače | Backup Operators, Přispěvatel skupiny prostředků, ve kterém se bude virtuální počítač nasadí, přečtěte si o virtuální sítě a připojte se k vybrané podsíti |
| Obnovit disky jednotlivých souborů ze záloh virtuálních počítačů | Backup Operators, Přispěvatel virtuálních počítačů na virtuálních počítačích |
| Vytvoření zásady zálohování pro zálohování virtuálních počítačů Azure | Přispěvatel zálohování |
| Upravit zásady zálohování, zálohování virtuálních počítačů Azure | Přispěvatel zálohování |
| Odstraňování zásady zálohování, zálohování virtuálních počítačů Azure | Přispěvatel zálohování |
| Zastavit zálohování (při zachování dat nebo odstranit data) zálohování virtuálních počítačů | Přispěvatel zálohování |
| Registr systému Windows na místním serveru/klienta/SCDPM nebo Azure Backup serveru | Operátor zálohování |
| Odstranit registrovaný v místním systému Windows Server/klient/SCDPM nebo serveru Azure Backup | Přispěvatel zálohování |

## <a name="next-steps"></a>Další postup
* [Řízení přístupu na základě rolí](../role-based-access-control/role-assignments-portal.md): Začínáme s RBAC na webu Azure Portal.
* Další informace o správě přístupu pomocí:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Na základě rolí řešení potíží s řízením přístupu](../role-based-access-control/troubleshooting.md): získat doporučení pro řešení běžných problémů.
