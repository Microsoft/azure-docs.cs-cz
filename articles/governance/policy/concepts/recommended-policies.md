---
title: Doporučené zásady pro služby Azure
description: Popisuje, jak najít a použít Doporučené zásady pro služby Azure, jako je například Azure Virtual Machines.
ms.date: 03/31/2021
ms.topic: conceptual
ms.customer: generated
ms.openlocfilehash: ef028dc61b10117509fdd27e815230ff27dbadfa
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089997"
---
# <a name="recommended-policies-for-azure-services"></a>Doporučené zásady pro služby Azure

Zákazníci, kteří se Azure Policy, často hledají společné definice zásad pro správu a řízení jejich prostředků. **Doporučené zásady** pro Azure Policy poskytují podrobný seznam běžných definic zásad, které je možné začít používat. **Doporučené možnosti zásad** pro podporované prostředky jsou vložené v rámci prostředí portálu pro daný prostředek.

Další Azure Policy integrovaných modulech najdete v tématu [Azure Policy předdefinované definice](../samples/built-in-policies.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

**Doporučené zásady** pro [Azure Virtual Machines](../../../virtual-machines/index.yml) najdete na stránce **Přehled** pro virtuální počítače a na kartě **Možnosti** . Na kartě _Azure Policy_ vyberte text "není nakonfigurováno" nebo "# přiřazený", chcete-li otevřít boční podokno s doporučenými zásadami. Všechny definice zásad, které jsou už přiřazené k oboru, ve kterém je virtuální počítač členem, jsou zobrazené šedě. Vyberte Doporučené zásady, které chcete použít pro tento virtuální počítač, a vyberte **přiřadit zásady** a vytvořte přiřazení pro každou z nich.

Vzhledem k tomu, že organizace dosáhne zralosti při [uspořádávání svých prostředků a hierarchie prostředků](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions), doporučujeme, abyste Tato přiřazení zásad převedli z jednoho na prostředek na úroveň předplatného nebo [skupiny pro správu](../../management-groups/index.yml) .

### <a name="azure-virtual-machines-recommended-policies"></a>Doporučené zásady pro Azure Virtual Machines

|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Auditovat virtuální počítače bez nakonfigurovaného zotavení po havárii](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Auditujte virtuální počítače, u kterých není nakonfigurované zotavení po havárii. Další informace o zotavení po havárii najdete v [https://aka.ms/asr-doc](../../../site-recovery/index.yml) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Auditovat virtuální počítače, které nepoužívají spravované disky](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Tato zásada Audituje virtuální počítače, které nepoužívají spravované disky. |ověřen |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[Azure Backup by měla být povolená Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Zajistěte ochranu Virtual Machines Azure tím, že povolíte Azure Backup. Azure Backup je bezpečné a nákladově efektivní řešení ochrany dat pro Azure. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [Vysvětlení efektů zásad](./effects.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).