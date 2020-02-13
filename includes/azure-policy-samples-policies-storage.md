---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 7aa6abb1a5fa9c969ca5e6d0730bed3b461fe81b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172881"
---
|Název |Popis |Vliv (s) |Verze |
|---|---|---|---|
|[Povolené skladové položky účtu úložiště](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) |Tato zásada umožňuje zadat sadu SKU účtů úložiště, které může vaše organizace nasazovat. |Odepřít |1.0.0 |
|[Auditování neomezeného síťového přístupu k účtům úložiště](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |Auditujte neomezený přístup k síti v nastavení brány firewall účtu úložiště. Místo toho nakonfigurujte síťová pravidla, aby k účtu úložiště měly přístup jenom aplikace z povolených sítí. Aby bylo možné v připojeních z konkrétních internetových nebo místních klientů přistupovat k provozu z konkrétních virtuálních sítí Azure nebo do rozsahů veřejných IP adres sítě. |Audit, zakázáno |1.0.0 |
|[Nasazení rozšířené ochrany před internetovými útoky na účty úložiště](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |Tato zásada umožňuje rozšířenou ochranu před internetovými útoky na účty úložiště. |DeployIfNotExists, zakázáno |1.0.0 |
|[Pro účty úložiště by mělo být povoleno geograficky redundantní úložiště.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |Tato zásada Audituje libovolný účet úložiště s geograficky redundantním úložištěm, které není povolené. |Audit, zakázáno |1.0.0 |
|[Zabezpečený přenos do účtů úložiště by měl být povolený.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |Auditujte requirment zabezpečeného přenosu v účtu úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako je třeba prostředník, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |1.0.0 |
|[Účty úložiště by měly umožňovat přístup z důvěryhodných služeb Microsoftu.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |Některé služby společnosti Microsoft, které pracují s účty úložiště provoz ze sítě, které nelze udělit přístup pomocí pravidel sítě. Abychom tento typ služby práce tak, jak má, umožňují sadu důvěryhodným službám Microsoftu obejít pravidel sítě. Tyto služby pak bude používat silné ověřování pro přístup k účtu úložiště. |Audit, zamítnutí, zakázáno |1.0.0 |
|[Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |Použijte nové Azure Resource Manager pro účty úložiště k zajištění vylepšení zabezpečení, jako je: silnější řízení přístupu (RBAC), lepší auditování, Azure Resource Manager nasazení a zásady správného řízení, přístup ke spravovaným identitám, přístup k trezoru klíčů pro tajné kódy, ověřování založené na Azure AD a podpora značek a skupin prostředků pro snadnější správu zabezpečení |Audit, zamítnutí, zakázáno |1.0.0 |
