---
title: Správa záloh pomocí Access Control založeného na rolích Azure
description: Pomocí Access Control na základě rolí můžete spravovat přístup k operacím správy zálohování v trezoru Recovery Services.
ms.reviewer: utraghuv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: dacurwin
ms.openlocfilehash: 8ba28829d3ee18b441227e537cb0a7ca97fb7638
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074040"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Pro správu Azure Backup bodů obnovení použít Access Control na základě rolí

Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí řízení přístupu na základě role můžete povinnosti v rámci týmu oddělit a udělit uživatelům jenom takový přístup, který potřebují k výkonu své práce.

> [!IMPORTANT]
> Role, které poskytuje Azure Backup, jsou omezené na akce, které se dají provádět v Azure Portal nebo REST API prostřednictvím rutiny PowerShellu nebo Recovery Servicesho prostředí PowerShellu nebo rozhraní příkazového řádku. Akce prováděné v uživatelském rozhraní klienta agenta Azure Backup nebo v uživatelském rozhraní nástroje System Center Data Protection Manager nebo v uživatelském rozhraní Azure Backup Server nejsou pod kontrolou těchto rolí.

Azure Backup poskytuje tři předdefinované role pro řízení operací správy zálohování. Další informace o [předdefinovaných rolích Azure RBAC](../role-based-access-control/built-in-roles.md)

* [Přispěvatel zálohování](../role-based-access-control/built-in-roles.md#backup-contributor) – tato role má všechna oprávnění k vytváření a správě zálohování s výjimkou odstranění Recovery Servicesho trezoru a udělení přístupu jiným uživatelům. Představte si tuto roli jako správce správy zálohování, který může provádět každou operaci správy zálohování.
* [Operátor zálohování](../role-based-access-control/built-in-roles.md#backup-operator) – tato role má oprávnění ke všem přispěvatelům s výjimkou odebrání zálohování a správy zásad zálohování. Tato role je rovnocenná přispěvateli, ale nemůže provádět destruktivní operace, jako je zastavení zálohování s odstraňováním dat, nebo odebrání místních prostředků.
* [Čtečka zálohování](../role-based-access-control/built-in-roles.md#backup-reader) – tato role má oprávnění k zobrazení všech operací správy zálohování. Představte si, že tato role je monitorovaná osoba.

Pokud chcete definovat vlastní role pro ještě více ovládacích prvků, přečtěte si téma Jak [vytvořit vlastní role](../role-based-access-control/custom-roles.md) v Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapování předdefinovaných rolí zálohování na akce správy zálohování

Následující tabulka zachycuje akce správy zálohování a odpovídající minimální roli RBAC potřebné k provedení této operace.

| Operace správy | Vyžaduje se minimální role RBAC. | Požadován rozsah |
| --- | --- | --- |
| Vytvoření trezoru služby Recovery Services | Přispěvatel zálohování | Skupina prostředků obsahující trezor |
| Povolení zálohování virtuálních počítačů Azure | Operátor zálohování | Skupina prostředků obsahující trezor |
| | Přispěvatel virtuálních počítačů | Prostředek virtuálního počítače |
| Zálohování virtuálního počítače na vyžádání | Operátor zálohování | Prostředek trezoru pro obnovení |
| Obnovit virtuální počítač | Operátor zálohování | Trezor služby Recovery Services |
| | Přispěvatel | Skupina prostředků, do které se virtuální počítač nasadí |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální počítač, který se zálohoval |
| Obnovení zálohy virtuálního počítače nespravovaných disků | Operátor zálohování | Prostředek trezoru pro obnovení |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální počítač, který se zálohoval |
| | Přispěvatel účtu úložiště | Prostředek účtu úložiště, kde se budou disky obnovovat |
| Obnovení spravovaných disků ze zálohy virtuálního počítače | Operátor zálohování | Prostředek trezoru pro obnovení |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální počítač, který se zálohoval |
| | Přispěvatel účtu úložiště | V rámci obnovení je vybraný dočasný účet úložiště, který bude obsahovat data z trezoru před jejich převodem na spravované disky. |
| | Přispěvatel | Skupina prostředků, do které budou obnoveny spravované disky |
| Obnovení jednotlivých souborů ze zálohy virtuálního počítače | Operátor zálohování | Prostředek trezoru pro obnovení |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální počítač, který se zálohoval |
| Vytvoření zásady zálohování pro zálohování virtuálních počítačů Azure | Přispěvatel zálohování | Prostředek trezoru pro obnovení |
| Úprava zásad zálohování pro zálohování virtuálních počítačů Azure | Přispěvatel zálohování | Prostředek trezoru pro obnovení |
| Odstranit zásady zálohování pro zálohování virtuálních počítačů Azure | Přispěvatel zálohování | Prostředek trezoru pro obnovení |
| Zastavit zálohování (se zachováním dat nebo odstraněním dat) v zálohování virtuálního počítače | Přispěvatel zálohování | Prostředek trezoru pro obnovení |
| Registrace místního Windows serveru/klienta/SCDPM nebo Azure Backup Server | Operátor zálohování | Prostředek trezoru pro obnovení |
| Odstranit registrovaný místní Windows Server/klienta/SCDPM nebo Azure Backup Server | Přispěvatel zálohování | Prostředek trezoru pro obnovení |

> [!IMPORTANT]
> Pokud zadáte Přispěvatel virtuálních počítačů do oboru prostředků virtuálních počítačů a kliknete na zálohovat jako součást nastavení virtuálního počítače, otevře se obrazovka povolit zálohování, i když je virtuální počítač už zálohovaný, protože volání pro ověření stavu zálohování funguje jenom na úrovni předplatného. Pokud tomu chcete předejít, buď si přečtěte do trezoru, otevřete zobrazení zálohovaná položka virtuálního počítače nebo zadejte roli Přispěvatel virtuálních počítačů na úrovni předplatného.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Minimální požadavky na role pro zálohu sdílené složky Azure

Následující tabulka zachycuje akce správy zálohování a odpovídající roli nutnou k provedení operace Azure File Share.

| Operace správy | Je vyžadována role. | Prostředky |
| --- | --- | --- |
| Povolení zálohování sdílených složek Azure | Přispěvatel zálohování |Trezor služby Recovery Services |
| |Účet úložiště | Prostředek účtu úložiště pro přispěvatele |
| Zálohování virtuálního počítače na vyžádání | Operátor zálohování | Trezor služby Recovery Services |
| Obnovit sdílenou složku | Operátor zálohování | Trezor služby Recovery Services |
| | Přispěvatel účtu úložiště | Prostředky účtu úložiště, ve kterých jsou přítomné obnovení zdrojových a cílových souborů sdílené složky |
| Obnovení jednotlivých souborů | Operátor zálohování | Trezor služby Recovery Services |
| |Přispěvatel účtu úložiště|Prostředky účtu úložiště, ve kterých jsou přítomné obnovení zdrojových a cílových souborů sdílené složky |
| Zastavení ochrany |Přispěvatel zálohování | Trezor služby Recovery Services |
| Zrušení registrace účtu úložiště z trezoru |Přispěvatel zálohování | Trezor služby Recovery Services |
| |Přispěvatel účtu úložiště | Prostředek účtu úložiště|

## <a name="next-steps"></a>Další kroky

* [Access Control založené na rolích](../role-based-access-control/role-assignments-portal.md): Začínáme s RBAC v Azure Portal.
* Naučte se spravovat přístup pomocí:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Řešení potíží s Access Control na základě rolí](../role-based-access-control/troubleshooting.md): Získejte návrhy pro řešení běžných problémů.
