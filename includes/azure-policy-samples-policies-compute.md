---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 667d017e8febcf1abcc1cfe21ecfaa43ae75ea6d
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172748"
---
|Název |Popis |Vliv (s) |Verze |
|---|---|---|---|
|[Povolené SKU virtuálních počítačů](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |Tyto zásady umožňují zadat sadu skladových položek virtuálních počítačů, které může vaše organizace nasazovat. |Odepřít |1.0.0 |
|[Auditovat virtuální počítače bez nakonfigurovaného zotavení po havárii](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |Auditujte virtuální počítače, u kterých není nakonfigurované zotavení po havárii. Další informace o zotavení po havárii najdete v https://aka.ms/asr-doc. |auditIfNotExists |1.0.0 |
|[Auditovat virtuální počítače, které nepoužívají spravované disky](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |Tato zásada audituje virtuální počítače, které nevyužívají spravované disky. |audit |1.0.0 |
|[Nasadit výchozí rozšíření Microsoft IaaSAntimalware pro Windows Server](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |Tato zásada nasadí rozšíření Microsoft IaaSAntimalware s výchozí konfigurací, pokud není virtuální počítač nakonfigurovaný s antimalwarovým rozšířením. |deployIfNotExists |1.0.0 |
|[Měly by být povolené diagnostické protokoly v Virtual Machine Scale Sets.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |Doporučuje se povolit protokoly, aby se záznam aktivity mohl znovu vytvořit, když se v případě incidentu nebo ohrožení vyžaduje šetření. |AuditIfNotExists, zakázáno |1.0.0 |
|[Microsoft Antimalware pro Azure by měl být nakonfigurovaný tak, aby automaticky aktualizoval podpisy ochrany.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |Tato zásada Audituje všechny virtuální počítače s Windows, které nejsou nakonfigurované pomocí automatických aktualizací podpisů Microsoft antimalwarové ochrany. |AuditIfNotExists, zakázáno |1.0.0 |
|[Rozšíření Microsoft IaaSAntimalware Extension by mělo být nasazeno na serverech Windows](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |Tato zásada Audituje všechny virtuální počítače s Windows serverem bez nasazeného rozšíření Microsoft IaaSAntimalware. |AuditIfNotExists, zakázáno |1.0.0 |
|[Musí být nainstalovaná jenom schválená rozšíření virtuálních počítačů.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |Tato zásada řídí rozšíření virtuálních počítačů, která nejsou schválena. |Audit, zamítnutí, zakázáno |1.0.0 |
|[Vyžadovat automatické opravy imagí operačního systému na Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |Tato zásada vynutila povolení automatických oprav bitových kopií operačního systému na Virtual Machine Scale Sets, aby se vždy zajistilo Virtual Machines zabezpečení díky bezpečnému používání nejnovějších oprav zabezpečení každý měsíc. |odmítnout |1.0.0 |
|[Nepřipojené disky by měly být zašifrované](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |Tato zásada Audituje všechny nepřipojené disky bez povoleného šifrování. |Audit, zakázáno |1.0.0 |
|[Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manager.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |Pomocí nových Azure Resource Manager pro virtuální počítače poskytněte vylepšení zabezpečení, jako je: silnější řízení přístupu (RBAC), lepší audit, nasazení na bázi ARM a zásady správného řízení, přístup ke spravovaným identitám, přístup k trezoru klíčů pro tajné klíče, Azure Ověřování a podpora založené na AD pro značky a skupiny prostředků pro snadnější správu zabezpečení |Audit, zamítnutí, zakázáno |1.0.0 |
