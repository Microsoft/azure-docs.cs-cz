---
title: Řízení zabezpečení Azure – zabezpečená konfigurace
description: Zabezpečená konfigurace řízení zabezpečení Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 347a63cc77c565d800328c19d1d543c2c9efafc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400088"
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

- [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Export jednoho a více prostředků do šablony v Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Doporučení zabezpečení – referenční příručka](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.2 | 5,1 | Zákazník |

Použijte Azure Security Center doporučení k údržbě konfigurací zabezpečení ve všech výpočetních prostředcích.  Kromě toho můžete k vytvoření konfigurace zabezpečení operačního systému vyžadovaného vaší organizací použít vlastní image operačního systému nebo konfiguraci stavu Azure Automation.

- [Jak monitorovat Azure Security Center doporučení](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Doporučení zabezpečení – referenční příručka](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Přehled konfigurace stavu Azure Automation](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Nahrání virtuálního pevného disku a jeho použití k vytvoření nových virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Vytvoření virtuálního počítače se systémem Linux z vlastního disku pomocí Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.3 | 5,2 | Zákazník |

Použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.  Kromě toho můžete použít šablony Azure Resource Manager k údržbě konfigurace zabezpečení vašich prostředků Azure, které vaše organizace vyžaduje. 

- [Pochopení Azure Policych efektů](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Přehled šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7,4 | 5,2 | Shared |

Dodržujte doporučení od Azure Security Center při provádění posouzení ohrožení zabezpečení výpočetních prostředků Azure.  Kromě toho můžete použít šablony Azure Resource Manager, vlastní image operačního systému nebo konfiguraci stavu Azure Automation k údržbě konfigurace zabezpečení operačního systému vyžadovaného vaší organizací.   Šablony virtuálních počítačů společnosti Microsoft kombinované s Azure Automation konfigurací požadovaného stavu mohou pomoci při schůzi a údržbě požadavků na zabezpečení. 

Všimněte si také, že Azure Marketplace image virtuálních počítačů publikované společností Microsoft jsou spravovány a spravovány společností Microsoft. 

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Jak vytvořit virtuální počítač Azure ze šablony Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Přehled konfigurace stavu Azure Automation](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Vytvoření virtuálního počítače s Windows v Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Informace o tom, jak stáhnout šablonu virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Ukázkový skript pro nahrání virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7,5 | 5.3 | Zákazník |

Využijte Azure DevOps k bezpečnému ukládání a správě kódu, jako jsou vlastní zásady Azure, Azure Resource Manager šablony a požadované konfigurační skripty stavu. Pokud chcete získat přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps, nebo Active Directory, pokud je integrovaná se sadou TFS.

- [Jak v Azure DevOps ukládat kód](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [O oprávněních a skupinách v Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.6 | 5.3 | Zákazník |

Pokud používáte vlastní image, použijte řízení přístupu na základě role Azure (Azure RBAC), abyste zajistili přístup k imagí jenom autorizovaným uživatelům. Pomocí Galerie sdílených imagí můžete své image sdílet s různými uživateli, instančními objekty nebo skupinami služby AD v rámci vaší organizace.  V případě imagí kontejnerů je uložte do Azure Container Registry a využijte Azure RBAC, aby bylo zajištěno, že přístup k obrázkům budou mít jenom autorizovaní uživatelé.  

- [Pochopení Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Vysvětlení služby Azure RBAC pro Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Jak nakonfigurovat službu Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Přehled Galerie sdílených imagí](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.7 | 5.4 | Zákazník |

Definování a implementace standardních konfigurací zabezpečení pro prostředky Azure pomocí Azure Policy. Pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich prostředků Azure. Můžete také využít integrované definice zásad související s vašimi konkrétními prostředky.  Kromě toho můžete k nasazení změn konfigurace použít Azure Automation.

- [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Jak používat aliasy](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7,8 | 5.4 | Zákazník |

Konfigurace stavu Azure Automation je služba správy konfigurace pro požadované uzly Konfigurace stavu (DSC) v jakémkoli cloudu nebo v místním datacentru. Můžete snadno připojit počítače, přiřazovat je k deklarativním konfiguracím a zobrazovat sestavy, které zobrazují kompatibilitu jednotlivých počítačů s požadovaným stavem, který jste zadali. 

- [Připojování počítačů pro správu podle konfigurace stavu Azure Automation](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.9 | 5,5 | Zákazník |

Použijte Azure Security Center k provádění kontrol standardních hodnot vašich prostředků Azure.  Kromě toho použijte Azure Policy k upozornění a auditování konfigurace prostředků Azure.

- [Jak opravit doporučení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.10 | 5,5 | Zákazník |

Pomocí Azure Security Center můžete provádět kontroly základní hodnoty pro kontejnery a nastavení Docker.

- [Porozumění doporučení týkajících se kontejnerů Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7.11 | 13,1 | Zákazník |

K zjednodušení a zabezpečení správy tajných kódů pro cloudové aplikace můžete použít Identita spravované služby ve spojení s Azure Key Vault.

- [Integrace se spravovanými identitami Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Vytvoření Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Ověření Key Vault](../../key-vault/general/authentication.md)

- [Postup přiřazení zásady přístupu Key Vault](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7,12 | 4.1 | Zákazník |

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

- [Postup konfigurace spravovaných identit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 7,13 | 18,1, 18,7 | Zákazník |

Implementujte kontrolu přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Další kroky

- Zobrazit další řízení zabezpečení:  [obrana proti malwaru](security-control-malware-defense.md)
