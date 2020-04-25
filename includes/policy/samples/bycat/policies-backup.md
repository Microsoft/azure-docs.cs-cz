---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/24/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1b0f218c6be405aee08596faede1005900275dd5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147439"
---
|Název |Popis |Vliv (s) |Version |GitHubu |
|---|---|---|---|---|
|[Azure Backup by měla být povolená Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Tato zásada pomáhá auditovat, pokud je služba Azure Backup povolená pro všechny virtuální počítače. Azure Backup je cenově výhodné řešení zálohování jedním kliknutím zjednodušuje obnovení dat a je snazší je povolit než jiné cloudové zálohovací služby. |AuditIfNotExists, zakázáno |1.0.0 |[Propojit](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Konfigurace zálohování na virtuálních počítačích umístění do existujícího centrálního trezoru ve stejném umístění](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Tato zásada konfiguruje Azure Backup ochranu virtuálních počítačů v daném umístění do existujícího centrálního trezoru ve stejném umístění. Platí jenom pro virtuální počítače, které ještě nejsou nakonfigurované pro zálohování. Doporučuje se, aby tato zásada byla přiřazena k více než 200 virtuálním počítačům. Pokud se zásada přiřadí více než 200 virtuálním počítačům, může to vést k tomu, že se zálohování spustí několik hodin nad definovaným plánem. Tato zásada bude vylepšena, aby podporovala více imagí virtuálních počítačů. |deployIfNotExists, auditIfNotExists, zakázáno |1.0.0 |[Propojit](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
