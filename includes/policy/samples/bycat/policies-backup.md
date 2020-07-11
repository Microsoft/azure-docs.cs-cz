---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 345cc8d5c1bc362973c8fae0e1c5605faf6e6e2d
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277176"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Azure Backup by měla být povolená Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Tato zásada pomáhá auditovat, pokud je služba Azure Backup povolená pro všechny virtuální počítače. Azure Backup je cenově výhodné řešení zálohování jedním kliknutím zjednodušuje obnovení dat a je snazší je povolit než jiné cloudové zálohovací služby. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Konfigurace zálohování na virtuálních počítačích umístění do existujícího centrálního trezoru ve stejném umístění](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Tato zásada konfiguruje Azure Backup ochranu virtuálních počítačů v daném umístění do existujícího centrálního trezoru ve stejném umístění. Platí jenom pro virtuální počítače, které ještě nejsou nakonfigurované pro zálohování. Doporučuje se, aby tato zásada byla přiřazena k více než 200 virtuálním počítačům. Pokud se zásada přiřadí více než 200 virtuálním počítačům, může to vést k tomu, že se zálohování spustí několik hodin nad definovaným plánem. Tato zásada bude vylepšena, aby podporovala více imagí virtuálních počítačů. |deployIfNotExists, auditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
