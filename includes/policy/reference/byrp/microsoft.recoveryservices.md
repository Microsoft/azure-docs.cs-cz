---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1b06c20e4c8cad1421bc328ff40570b3fa4a7572
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310715"
---
|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Azure Backup by měla být povolená Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Zajistěte ochranu Virtual Machines Azure tím, že povolíte Azure Backup. Azure Backup je bezpečné a nákladově efektivní řešení ochrany dat pro Azure. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Konfigurace zálohování na virtuálních počítačích umístění do existujícího centrálního trezoru ve stejném umístění](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Tato zásada konfiguruje Azure Backup ochranu virtuálních počítačů v daném umístění do existujícího centrálního trezoru ve stejném umístění. Platí jenom pro virtuální počítače, které ještě nejsou nakonfigurované pro zálohování. Doporučuje se, aby tato zásada byla přiřazena k více než 200 virtuálním počítačům. Pokud se zásada přiřadí více než 200 virtuálním počítačům, může to vést k tomu, že se zálohování spustí několik hodin nad definovaným plánem. Tato zásada bude vylepšena, aby podporovala více imagí virtuálních počítačů. |deployIfNotExists, auditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Nasaďte nastavení diagnostiky pro Recovery Services trezor pro Log Analytics pracovní prostor pro kategorie specifické pro prostředky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Nasaďte nastavení diagnostiky pro Recovery Services trezor do služby Stream do Log Analytics pracovního prostoru pro kategorie specifické pro prostředky. Pokud některá z kategorií konkrétního prostředku není povolená, vytvoří se nové nastavení diagnostiky. |deployIfNotExists |[1.0.1 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
