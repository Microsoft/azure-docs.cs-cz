---
title: Řízení zabezpečení Azure – zabezpečená konfigurace
description: Zabezpečená konfigurace řízení zabezpečení Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f945699256b4de9bd87ee4ab7500268302e93089
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880165"
---
# <a name="security-control-secure-configuration"></a>Řízení zabezpečení: zabezpečená konfigurace

Navažte, implementujte a aktivně spravujte (sledujte, vykazovat, opravujte) konfiguraci zabezpečení prostředků Azure, aby útočníci nemohli zneužít ohrožené služby a nastavení.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.1 | 5,1 | Zákazník |

Pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace prostředků Azure. Můžete také použít předdefinované definice Azure Policy.

Azure Resource Manager taky umožňuje exportovat šablonu do JavaScript Object Notation (JSON), která by se měla zkontrolovat, aby se zajistilo, že konfigurace splňují nebo překračují požadavky zabezpečení vaší organizace.

Pro vaše prostředky Azure můžete také použít doporučení z Azure Security Center jako standardní hodnoty konfigurace zabezpečení.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../../azure-resource-manager/templates/export-template-portal.md)

- [Doporučení k zabezpečení – Referenční příručka](../../security-center/recommendations-reference.md)

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.2 | 5,1 | Zákazník |

Použijte Azure Security Center doporučení k údržbě konfigurací zabezpečení ve všech výpočetních prostředcích.  Kromě toho můžete k vytvoření konfigurace zabezpečení operačního systému vyžadovaného vaší organizací použít vlastní image operačního systému nebo konfiguraci stavu Azure Automation.

- [Jak monitorovat Azure Security Center doporučení](../../security-center/security-center-recommendations.md)

- [Doporučení k zabezpečení – Referenční příručka](../../security-center/recommendations-reference.md)

- [Přehled konfigurace stavu Azure Automation](../../automation/automation-dsc-overview.md)

- [Nahrání virtuálního pevného disku a jeho použití k vytvoření nových virtuálních počítačů s Windows v Azure](../../virtual-machines/windows/upload-generalized-managed.md)

- [Vytvoření virtuálního počítače se systémem Linux z vlastního disku pomocí Azure CLI](../../virtual-machines/linux/upload-vhd.md)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.3 | 5.2 | Zákazník |

Použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.  Kromě toho můžete použít šablony Azure Resource Manager k údržbě konfigurace zabezpečení vašich prostředků Azure, které vaše organizace vyžaduje. 

- [Pochopení Azure Policych efektů](../../governance/policy/concepts/effects.md)

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md)

- [Přehled šablon Azure Resource Manager](../../azure-resource-manager/templates/overview.md)

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7,4 | 5.2 | Shared |

Dodržujte doporučení od Azure Security Center při provádění posouzení ohrožení zabezpečení výpočetních prostředků Azure.  Kromě toho můžete použít šablony Azure Resource Manager, vlastní image operačního systému nebo konfiguraci stavu Azure Automation k údržbě konfigurace zabezpečení operačního systému vyžadovaného vaší organizací.   Šablony virtuálních počítačů společnosti Microsoft kombinované s Azure Automation konfigurací požadovaného stavu mohou pomoci při schůzi a údržbě požadavků na zabezpečení. 

Všimněte si také, že Azure Marketplace image virtuálních počítačů publikované společností Microsoft jsou spravovány a spravovány společností Microsoft. 

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](../../security-center/deploy-vulnerability-assessment-vm.md)

- [Jak vytvořit virtuální počítač Azure ze šablony Azure Resource Manager](../../virtual-machines/windows/ps-template.md)

- [Přehled konfigurace stavu Azure Automation](../../automation/automation-dsc-overview.md)

- [Vytvoření virtuálního počítače s Windows v Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

- [Informace o tom, jak stáhnout šablonu virtuálního počítače](/previous-versions/azure/virtual-machines/windows/download-template)

- [Ukázkový skript pro nahrání virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7,5 | 5.3 | Zákazník |

Využijte Azure DevOps k bezpečnému ukládání a správě kódu, jako jsou vlastní zásady Azure, Azure Resource Manager šablony a požadované konfigurační skripty stavu. Pokud chcete získat přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps, nebo Active Directory, pokud je integrovaná se sadou TFS.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.6 | 5.3 | Zákazník |

Pokud používáte vlastní image, použijte řízení přístupu na základě role Azure (Azure RBAC), abyste zajistili přístup k imagí jenom autorizovaným uživatelům. Pomocí Galerie sdílených imagí můžete své image sdílet s různými uživateli, instančními objekty nebo skupinami služby AD v rámci vaší organizace.  V případě imagí kontejnerů je uložte do Azure Container Registry a využijte Azure RBAC, aby bylo zajištěno, že přístup k obrázkům budou mít jenom autorizovaní uživatelé.  

- [Pochopení Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Vysvětlení služby Azure RBAC pro Container Registry](../../container-registry/container-registry-roles.md)

- [Jak nakonfigurovat službu Azure RBAC](../../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Přehled Galerie sdílených imagí](../../virtual-machines/shared-image-galleries.md)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.7 | 5.4 | Zákazník |

Definování a implementace standardních konfigurací zabezpečení pro prostředky Azure pomocí Azure Policy. Pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich prostředků Azure. Můžete také využít integrované definice zásad související s vašimi konkrétními prostředky.  Kromě toho můžete k nasazení změn konfigurace použít Azure Automation.

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Jak používat aliasy](../../governance/policy/concepts/definition-structure.md#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7,8 | 5.4 | Zákazník |

Konfigurace stavu Azure Automation je služba správy konfigurace pro požadované uzly Konfigurace stavu (DSC) v jakémkoli cloudu nebo v místním datacentru. Můžete snadno připojit počítače, přiřazovat je k deklarativním konfiguracím a zobrazovat sestavy, které zobrazují kompatibilitu jednotlivých počítačů s požadovaným stavem, který jste zadali. 

- [Připojování počítačů pro správu podle konfigurace stavu Azure Automation](../../automation/automation-dsc-onboarding.md)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.9 | 5.5 | Zákazník |

Použijte Azure Security Center k provádění kontrol standardních hodnot vašich prostředků Azure.  Kromě toho použijte Azure Policy k upozornění a auditování konfigurace prostředků Azure.

- [Jak opravit doporučení v Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.10 | 5.5 | Zákazník |

Pomocí Azure Security Center můžete provádět kontroly základní hodnoty pro kontejnery a nastavení Docker.

- [Porozumění doporučení týkajících se kontejnerů Azure Security Center](../../security-center/container-security.md)

## <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.11 | 13.1 | Zákazník |

K zjednodušení a zabezpečení správy tajných kódů pro cloudové aplikace můžete použít Identita spravované služby ve spojení s Azure Key Vault.

- [Integrace se spravovanými identitami Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Vytvoření Key Vault](../../key-vault/secrets/quick-create-portal.md)

- [Ověření Key Vault](../../key-vault/general/authentication.md)

- [Postup přiřazení zásady přístupu Key Vault](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7,12 | 4.1 | Zákazník |

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

- [Postup konfigurace spravovaných identit](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7,13 | 18,1, 18,7 | Zákazník |

Implementujte kontrolu přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Další kroky

- Zobrazit další řízení zabezpečení:  [obrana proti malwaru](security-control-malware-defense.md)