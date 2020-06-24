---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7baa1c030c8d70b76eb661d6a6fac7429a80a50e
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2020
ms.locfileid: "84709894"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Azure Backup by měla být povolená Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Tato zásada pomáhá auditovat, pokud je služba Azure Backup povolená pro všechny virtuální počítače. Azure Backup je cenově výhodné řešení zálohování jedním kliknutím zjednodušuje obnovení dat a je snazší je povolit než jiné cloudové zálohovací služby. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Konfigurace zálohování na virtuálních počítačích umístění do existujícího centrálního trezoru ve stejném umístění](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Tato zásada konfiguruje Azure Backup ochranu virtuálních počítačů v daném umístění do existujícího centrálního trezoru ve stejném umístění. Platí jenom pro virtuální počítače, které ještě nejsou nakonfigurované pro zálohování. Doporučuje se, aby tato zásada byla přiřazena k více než 200 virtuálním počítačům. Pokud se zásada přiřadí více než 200 virtuálním počítačům, může to vést k tomu, že se zálohování spustí několik hodin nad definovaným plánem. Tato zásada bude vylepšena, aby podporovala více imagí virtuálních počítačů. |deployIfNotExists, auditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Nasaďte nastavení diagnostiky pro Recovery Services trezor pro Log Analytics pracovní prostor pro kategorie specifické pro prostředky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Nasaďte nastavení diagnostiky pro Recovery Services trezor do služby Stream do Log Analytics pracovního prostoru pro kategorie specifické pro prostředky. Pokud některá z kategorií konkrétního prostředku není povolená, vytvoří se nové nastavení diagnostiky. |deployIfNotExists |[1.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
