---
title: Správa záloh pomocí řízení přístupu na základě role v Azure
description: Řízení přístupu na základě role Azure slouží ke správě přístupu k operacím správy zálohování v trezoru Recovery Services.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 0b321a5f33bd75ce8615d6d2a90442a83d9fff67
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613438"
---
# <a name="use-azure-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Použití řízení přístupu na základě role Azure ke správě Azure Backup bodů obnovení

Řízení přístupu na základě role Azure (Azure RBAC) umožňuje jemně odstupňovanou správu přístupu pro Azure. Pomocí Azure RBAC můžete oddělit povinnosti v rámci týmu a udělit uživatelům jenom takový přístup, který potřebují k výkonu své práce.

> [!IMPORTANT]
> Role, které poskytuje Azure Backup, jsou omezené na akce, které se dají provádět v Azure Portal nebo REST API prostřednictvím rutiny PowerShellu nebo Recovery Servicesho prostředí PowerShellu nebo rozhraní příkazového řádku. Akce prováděné v uživatelském rozhraní klienta Azure Backup agenta nebo v uživatelském rozhraní nástroje System Center Data Protection Manager nebo Azure Backup Server uživatelském rozhraní nejsou pod kontrolou těchto rolí.

Azure Backup poskytuje tři předdefinované role pro řízení operací správy zálohování. Další informace o [předdefinovaných rolích Azure](../role-based-access-control/built-in-roles.md)

* [Přispěvatel zálohování](../role-based-access-control/built-in-roles.md#backup-contributor) – tato role má všechna oprávnění k vytváření a správě zálohování s výjimkou odstranění Recovery Servicesho trezoru a udělení přístupu jiným uživatelům. Představte si tuto roli jako správce správy zálohování, který může provádět každou operaci správy zálohování.
* [Operátor zálohování](../role-based-access-control/built-in-roles.md#backup-operator) – tato role má oprávnění ke všem přispěvatelům s výjimkou odebrání zálohování a správy zásad zálohování. Tato role je rovnocenná přispěvateli, ale nemůže provádět destruktivní operace, jako je zastavení zálohování s odstraňováním dat, nebo odebrání místních prostředků.
* [Čtečka zálohování](../role-based-access-control/built-in-roles.md#backup-reader) – tato role má oprávnění k zobrazení všech operací správy zálohování. Představte si, že tato role je monitorovaná osoba.

Pokud chcete definovat vlastní role pro ještě více ovládacích prvků, přečtěte si téma Jak [vytvořit vlastní role](../role-based-access-control/custom-roles.md) v Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapování předdefinovaných rolí zálohování na akce správy zálohování

### <a name="minimum-role-requirements-for-azure-vm-backup"></a>Minimální požadavky na role pro zálohování virtuálních počítačů Azure

Následující tabulka zachycuje akce správy zálohování a odpovídající minimální role Azure potřebné k provedení této operace.

| Operace správy | Vyžaduje se minimální role Azure. | Požadován rozsah | Jiné |
| --- | --- | --- | --- |
| Vytvoření trezoru služby Recovery Services | Přispěvatel zálohování | Skupina prostředků obsahující trezor |   |
| Povolení zálohování virtuálních počítačů Azure | Operátor záloh | Skupina prostředků obsahující trezor |   |
| | Přispěvatel virtuálních počítačů | Prostředek virtuálního počítače |  Alternativně můžete místo předdefinované role vzít v úvahu vlastní roli, která má následující oprávnění: Microsoft. COMPUTE/virtualMachines/Write. |
| Zálohování virtuálního počítače na vyžádání | Operátor záloh | Trezor služby Recovery Services |   |
| Obnovení virtuálního počítače | Operátor záloh | Trezor služby Recovery Services |   |
| | Přispěvatel | Skupina prostředků, do které se virtuální počítač nasadí |   Alternativně můžete místo předdefinované role zvážit vlastní roli, která má následující oprávnění: Microsoft. Resources/Subscriptions/resourceGroups/Write Microsoft. DomainRegistration/doméns/Write, Microsoft. COMPUTE/virtualMachines/Write Microsoft. Network/virtualNetworks/Read Microsoft. Network/virtualNetworks/subnets/JOIN/Action. |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální počítač, který se zálohoval |   Alternativně můžete místo předdefinované role vzít v úvahu vlastní roli, která má následující oprávnění: Microsoft. COMPUTE/virtualMachines/Write. |
| Obnovení zálohy virtuálního počítače nespravovaných disků | Operátor záloh | Trezor služby Recovery Services |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální počítač, který se zálohoval | Alternativně můžete místo předdefinované role vzít v úvahu vlastní roli, která má následující oprávnění: Microsoft. COMPUTE/virtualMachines/Write. |
| | Přispěvatel účtů úložiště | Prostředek účtu úložiště, kde se budou disky obnovovat |   Alternativně můžete místo předdefinované role vzít v úvahu vlastní roli, která má následující oprávnění: Microsoft. Storage/storageAccounts/Write. |
| Obnovení spravovaných disků ze zálohy virtuálního počítače | Operátor záloh | Trezor služby Recovery Services |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální počítač, který se zálohoval |    Alternativně můžete místo předdefinované role vzít v úvahu vlastní roli, která má následující oprávnění: Microsoft. COMPUTE/virtualMachines/Write. |
| | Přispěvatel účtů úložiště | V rámci obnovení je vybraný dočasný účet úložiště, který bude obsahovat data z trezoru před jejich převodem na spravované disky. |   Alternativně můžete místo předdefinované role vzít v úvahu vlastní roli, která má následující oprávnění: Microsoft. Storage/storageAccounts/Write. |
| | Přispěvatel | Skupina prostředků, do které budou obnoveny spravované disky | Alternativně můžete místo předdefinované role vzít v úvahu vlastní roli, která má následující oprávnění: Microsoft. Resources/Subscriptions/resourceGroups/Write.|
| Obnovení jednotlivých souborů ze zálohy virtuálního počítače | Operátor záloh | Trezor služby Recovery Services |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální počítač, který se zálohoval | Alternativně můžete místo předdefinované role vzít v úvahu vlastní roli, která má následující oprávnění: Microsoft. COMPUTE/virtualMachines/Write. |
| Obnovení mezi oblastmi | Operátor záloh | Předplatné trezoru Recovery Services | To je navíc k výše uvedeným oprávněním pro obnovení. Konkrétně pro CRR, a ne jako předdefinovanou roli, můžete zvážit vlastní roli, která má následující oprávnění: "Microsoft. RecoveryServices/Locations/backupAadProperties/Read" "Microsoft. RecoveryServices/Locations/backupCrrJobs/Action" "Microsoft. RecoveryServices/Locations/backupCrrJob/Action" "Microsoft. RecoveryServices/Locations/backupCrossRegionRestore/Action" "Microsoft. RecoveryServices/Locations/backupCrrOperationResults/Read" "Microsoft. RecoveryServices/Locations/backupCrrOperationsStatus/Read" |
| Vytvoření zásady zálohování pro zálohování virtuálních počítačů Azure | Přispěvatel zálohování | Trezor služby Recovery Services |
| Úprava zásad zálohování pro zálohování virtuálních počítačů Azure | Přispěvatel zálohování | Trezor služby Recovery Services |
| Odstranit zásady zálohování pro zálohování virtuálních počítačů Azure | Přispěvatel zálohování | Trezor služby Recovery Services |
| Zastavit zálohování (se zachováním dat nebo odstraněním dat) v zálohování virtuálního počítače | Přispěvatel zálohování | Trezor služby Recovery Services |
| Registrace místního Windows serveru/klienta/SCDPM nebo Azure Backup Server | Operátor záloh | Trezor služby Recovery Services |
| Odstranit registrovaný místní Windows Server/klienta/SCDPM nebo Azure Backup Server | Přispěvatel zálohování | Trezor služby Recovery Services |

> [!IMPORTANT]
> Pokud zadáte Přispěvatel virtuálních počítačů v oboru prostředků virtuálního počítače a v rámci nastavení virtuálního počítače vyberete **zálohovat** , otevře se obrazovka **Povolit zálohování** , i když je virtuální počítač už zálohovaný. Důvodem je to, že volání pro ověření stavu zálohování funguje pouze na úrovni předplatného. Pokud tomu chcete předejít, buď si přečtěte do trezoru, otevřete zobrazení zálohovaná položka virtuálního počítače nebo zadejte roli Přispěvatel virtuálních počítačů na úrovni předplatného.

### <a name="minimum-role-requirements-for-azure-workload-backups-sql-and-hana-db-backups"></a>Minimální požadavky na role pro zálohování úloh Azure (zálohy databáze SQL a HANA)

Následující tabulka zachycuje akce správy zálohování a odpovídající minimální role Azure potřebné k provedení této operace.

| Operace správy | Vyžaduje se minimální role Azure. | Požadován rozsah | Jiné |
| --- | --- | --- | --- |
| Vytvoření trezoru služby Recovery Services | Přispěvatel zálohování | Skupina prostředků obsahující trezor |   |
| Povolení zálohování databází SQL a/nebo HANA | Operátor záloh | Skupina prostředků obsahující trezor |   |
| | Přispěvatel virtuálních počítačů | Prostředek virtuálního počítače, na kterém je nainstalována databáze |  Alternativně můžete místo předdefinované role vzít v úvahu vlastní roli, která má následující oprávnění: Microsoft. COMPUTE/virtualMachines/Write. |
| Zálohování databáze na vyžádání | Operátor záloh | Trezor služby Recovery Services |   |
| Obnovení databáze nebo obnovení jako souborů | Operátor záloh | Trezor služby Recovery Services |   |
| | Přispěvatel virtuálních počítačů | Zdrojový virtuální počítač, který se zálohoval |   Alternativně můžete místo předdefinované role vzít v úvahu vlastní roli, která má následující oprávnění: Microsoft. COMPUTE/virtualMachines/Write. |
| | Přispěvatel virtuálních počítačů | Cílový virtuální počítač, ve kterém se databáze obnoví nebo se vytvoří soubory |   Alternativně můžete místo předdefinované role vzít v úvahu vlastní roli, která má následující oprávnění: Microsoft. COMPUTE/virtualMachines/Write. |
| Vytvoření zásady zálohování pro zálohování virtuálních počítačů Azure | Přispěvatel zálohování | Trezor služby Recovery Services |
| Úprava zásad zálohování pro zálohování virtuálních počítačů Azure | Přispěvatel zálohování | Trezor služby Recovery Services |
| Odstranit zásady zálohování pro zálohování virtuálních počítačů Azure | Přispěvatel zálohování | Trezor služby Recovery Services |
| Zastavit zálohování (se zachováním dat nebo odstraněním dat) v zálohování virtuálního počítače | Přispěvatel zálohování | Trezor služby Recovery Services |

### <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Minimální požadavky na role pro zálohu sdílené složky Azure

Následující tabulka zachycuje akce správy zálohování a odpovídající roli nutnou k provedení operace Azure File Share.

| Operace správy | Je vyžadována role. | Zdroje informací |
| --- | --- | --- |
| Povolení zálohování sdílených složek Azure | Přispěvatel zálohování |Trezor služby Recovery Services |
| |Účet úložiště | Prostředek účtu úložiště pro přispěvatele |
| Zálohování virtuálního počítače na vyžádání | Operátor záloh | Trezor služby Recovery Services |
| Obnovit sdílenou složku | Operátor záloh | Trezor služby Recovery Services |
| | Přispěvatel účtů úložiště | Prostředky účtu úložiště, ve kterých jsou přítomné obnovení zdrojových a cílových souborů sdílené složky |
| Obnovení jednotlivých souborů | Operátor záloh | Trezor služby Recovery Services |
| |Přispěvatel účtů úložiště|Prostředky účtu úložiště, ve kterých jsou přítomné obnovení zdrojových a cílových souborů sdílené složky |
| Zastavení ochrany |Přispěvatel zálohování | Trezor služby Recovery Services |
| Zrušení registrace účtu úložiště z trezoru |Přispěvatel zálohování | Trezor služby Recovery Services |
| |Přispěvatel účtů úložiště | Prostředek účtu úložiště|

## <a name="next-steps"></a>Další kroky

* [Řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): Začínáme s Azure RBAC v Azure Portal.
* Naučte se spravovat přístup pomocí:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Řešení potíží s řízením přístupu na základě role v Azure](../role-based-access-control/troubleshooting.md): Získejte návrhy na řešení běžných problémů.
