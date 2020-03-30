---
title: Správa záloh pomocí řízení přístupu na základě rolí
description: Pomocí řízení přístupu na základě rolí můžete spravovat přístup k operacím správy zálohování v trezoru služby Recovery Services.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: e2e32ac6981635e3b9885119fdf397783ac32cc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273199"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Správa bodů obnovení azure backup pomocí řízení přístupu na základě rolí

Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí řízení přístupu na základě role můžete povinnosti v rámci týmu oddělit a udělit uživatelům jenom takový přístup, který potřebují k výkonu své práce.

> [!IMPORTANT]
> Role poskytované službou Azure Backup jsou omezené na akce, které se můžou provádět na webu Azure Portal nebo prostřednictvím rutin PowerShellu nebo Rozhraní příkazového řádku služby REST API nebo služby Recovery Services. Akce prováděné v uzly klienta azure zálohování agenta nebo system center data protection manager ui nebo Azure Backup Server UI jsou mimo kontrolu těchto rolí.

Azure Backup poskytuje tři integrované role pro řízení operací správy zálohování. Další informace o [předdefinovaných rolích Azure RBAC](../role-based-access-control/built-in-roles.md)

* [Přispěvatel zálohování](../role-based-access-control/built-in-roles.md#backup-contributor) – Tato role má všechna oprávnění k vytváření a správě záloh y s výjimkou odstranění trezoru služby Recovery Services a poskytnutí přístupu ostatním. Představte si tuto roli jako správce správy zálohování, který může provést každou operaci správy zálohování.
* [Operátor zálohování](../role-based-access-control/built-in-roles.md#backup-operator) – tato role má oprávnění ke všemu, co přispěvatel dělá kromě odebrání zásad zálohování a správy zálohování. Tato role je ekvivalentní přispěvateli s tím rozdílem, že nemůže provádět destruktivní operace, jako je zastavení zálohování s odstraněním dat nebo odebrání registrace místních prostředků.
* [Backup Reader](../role-based-access-control/built-in-roles.md#backup-reader) - Tato role má oprávnění k zobrazení všech operací správy zálohování. Představte si, že tato role má být monitorovací osobou.

Pokud hledáte definovat vlastní role pro ještě větší kontrolu, podívejte se, jak [vytvořit vlastní role](../role-based-access-control/custom-roles.md) v Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapování předdefinovaných rolí zálohování na akce správy zálohování

V následující tabulce jsou zachyceny akce správy zálohování a odpovídající minimální role RBAC potřebná k provedení této operace.

| Operace řízení | Minimální požadovaná role RBAC | Požadovaný obor |
| --- | --- | --- |
| Vytvoření trezoru služby Recovery Services | Přispěvatel zálohy | Skupina prostředků obsahující trezor |
| Povolení zálohování virtuálních počítačů Azure | Operátor zálohování | Skupina prostředků obsahující trezor |
| | Přispěvatel virtuálních počítačů | Prostředek virtuálního ms |
| Zálohování virtuálního počítače na vyžádání | Operátor zálohování | Trezor služby Recovery Services |
| Obnovení virtuálního počítače | Operátor zálohování | Trezor služby Recovery Services |
| | Přispěvatel | Skupina prostředků, ve které se virtuální mísa nasadí |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální virtuální telefon, který se zálohoval |
| Obnovení zálohování virtuálních počítačů na nespravovaných discích | Operátor zálohování | Trezor služby Recovery Services |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální virtuální telefon, který se zálohoval |
| | Přispěvatel účtů úložiště | Prostředek účtu úložiště, kde budou obnoveny disky |
| Obnovení spravovaných disků ze zálohy virtuálního počítače | Operátor zálohování | Trezor služby Recovery Services |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální virtuální telefon, který se zálohoval |
| | Přispěvatel účtů úložiště | Účet dočasného úložiště vybraný jako součást obnovení pro uložení dat z úložiště před jejich převodem na spravované disky |
| | Přispěvatel | Skupina prostředků, do které budou obnoveny spravované disky |
| Obnovení jednotlivých souborů ze zálohy virtuálního počítače | Operátor zálohování | Trezor služby Recovery Services |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální virtuální telefon, který se zálohoval |
| Vytvoření zásad zálohování pro zálohování virtuálních počítačů Azure | Přispěvatel zálohy | Trezor služby Recovery Services |
| Úprava zásad zálohování zálohování virtuálních počítačů Azure | Přispěvatel zálohy | Trezor služby Recovery Services |
| Odstranění zásad zálohování záloh virtuálních počítačových virtuálních počítačů Azure | Přispěvatel zálohy | Trezor služby Recovery Services |
| Zastavení zálohování (s uchováním dat nebo odstraněním dat) při zálohování virtuálních počítačích | Přispěvatel zálohy | Trezor služby Recovery Services |
| Registrace místního Windows Serveru/klienta/SCDPM nebo Azure Backup Serveru | Operátor zálohování | Trezor služby Recovery Services |
| Odstranění registrovaného místního systému Windows Server/klient/SCDPM nebo Azure Backup Server | Přispěvatel zálohy | Trezor služby Recovery Services |

> [!IMPORTANT]
> Pokud zadáte přispěvatele virtuálního počítače v oboru prostředků virtuálního počítače a kliknete na zálohování jako součást nastavení virtuálního počítače, otevře se obrazovka Povolit zálohování, i když je virtuální počítač už zálohovaný, protože volání k ověření stavu zálohování funguje pouze na úrovni předplatného. Chcete-li tomu zabránit, přejděte do trezoru a otevřete zobrazení položky zálohování virtuálního počítače nebo zadejte roli přispěvatele virtuálního počítače na úrovni předplatného.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Minimální požadavky na roli pro zálohování sdílené ho souboru Azure

V následující tabulce jsou zachyceny akce správy zálohování a odpovídající role potřebná k provedení operace sdílení souborů Azure.

| Operace řízení | Je vyžadována role | Prostředky |
| --- | --- | --- |
| Povolení zálohování sdílených složek Azure | Přispěvatel zálohy |Trezor služby Recovery Services |
| |Účet úložiště | Prostředek účtu úložiště přispěvatele |
| Zálohování virtuálního počítače na vyžádání | Operátor zálohování | Trezor služby Recovery Services |
| Obnovit sdílenou složku | Operátor zálohování | Trezor služby Recovery Services |
| | Přispěvatel účtů úložiště | Prostředky účtu úložiště, kde jsou k dispozici sdílené položky zdroje obnovení a cílových souborů |
| Obnovit jednotlivé soubory | Operátor zálohování | Trezor služby Recovery Services |
| |Přispěvatel účtů úložiště|Prostředky účtu úložiště, kde jsou k dispozici sdílené položky zdroje obnovení a cílových souborů |
| Zastavení ochrany |Přispěvatel zálohy | Trezor služby Recovery Services |
| Zrušení registrace účtu úložiště z trezoru |Přispěvatel zálohy | Trezor služby Recovery Services |
| |Přispěvatel účtů úložiště | Prostředek účtu úložiště|

## <a name="next-steps"></a>Další kroky

* [Řízení přístupu na základě rolí:](../role-based-access-control/role-assignments-portal.md)Začínáme s RBAC na webu Azure Portal.
* Přečtěte si, jak spravovat přístup pomocí:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [ROZHRANÍ API PRO ODPOČINEK](../role-based-access-control/role-assignments-rest.md)
* [Řešení potíží s řízením přístupu na základě](../role-based-access-control/troubleshooting.md)rolí : Získejte návrhy na řešení běžných problémů.
