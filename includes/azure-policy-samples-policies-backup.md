---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 4a6c0cad4a93d0d99573a348070823b909c75c7e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79299409"
---
|Name (Název) |Popis |Efekty |Version |GitHub |
|---|---|---|---|---|
|[Azure Backup by měla být povolená pro virtuální počítače](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Tato zásada pomáhá auditovat, pokud je služba Azure Backup povolená pro všechny virtuální počítače. Azure Backup je nákladově efektivní řešení zálohování jedním kliknutím zjednodušuje obnovu dat a je snadněji povolit než ostatní služby zálohování v cloudu. |AuditIfNotExists, zakázáno |1.0.0 |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Konfigurace zálohování na virtuálních počítačích umístění do existujícího centrálního úložiště ve stejném umístění](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Tato zásada konfiguruje ochranu azure backup na virtuálních počítačích v daném umístění do existujícího centrálního trezoru ve stejném umístění. Platí jenom pro virtuální počítače, které ještě nejsou nakonfigurované pro zálohování. Doporučujese, aby tato zásada byla přiřazena k maximálně 200 virtuálním mům. Pokud je zásada přiřazena pro více než 200 virtuálních počítače, může to mít za následek zálohování, které se aktivuje několik hodin nad rámec definovaného plánu. Tato zásada bude vylepšena tak, aby podporovala více iobrazek virtuálních her. |deployIfNotExists, auditIfNotExists, zakázáno |1.0.0 |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
