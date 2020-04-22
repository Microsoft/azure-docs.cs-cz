---
title: Azure Security Control – zabezpečená konfigurace
description: Konfigurace zabezpečení ovládacího prvku Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: bfa16f59155e420209ab6370056a7c612b5327e4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759054"
---
# <a name="security-control-secure-configuration"></a>Řízení zabezpečení: Zabezpečená konfigurace

Nastavte, implementujte a aktivně spravujte (sledujte, sestavují, opravujte) konfiguraci zabezpečení prostředků Azure, aby útočníci nezneužívali ohrožené služby a nastavení.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.1 | 5.1 | Zákazník |

Pomocí aliasů zásad Azure můžete vytvářet vlastní zásady k auditování nebo vynucení konfigurace prostředků Azure. Můžete také použít předdefinované definice zásad Azure.

Azure Resource Manager má také možnost exportovat šablonu v JavaScriptu zápisu objektu (JSON), který by měl být přezkoumána, aby zajistily, že konfigurace splňují nebo překračují požadavky na zabezpečení pro vaši organizaci.

Můžete také použít doporučení z Azure Security Center jako výchozí hodnotu konfigurace pro vaše prostředky Azure.

- [Jak zobrazit dostupné aliasy zásad Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Kurz: Vytváření a správa zásad pro vynucení dodržování předpisů](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Export s jedním a více prostředky do šablony na webu Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Bezpečnostní doporučení – referenční příručka](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Vytvoření bezpečných konfigurací operačního systému

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.2 | 5.1 | Zákazník |

Pomocí doporučení Azure Security Center můžete udržovat konfigurace zabezpečení u všech výpočetních prostředků.  Kromě toho můžete použít vlastní bitové kopie operačního systému nebo konfigurace stavu Azure Automation k vytvoření konfigurace zabezpečení operačního systému vyžadovaného vaší organizací.

- [Jak sledovat doporučení Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Bezpečnostní doporučení – referenční příručka](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Přehled konfigurace stavu automatizace Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Nahrání virtuálního pevného disku a jeho použití k vytvoření nových virtuálních aplikací Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Vytvoření virtuálního počítače s Linuxem z vlastního disku pomocí azure cli](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Udržování zabezpečených konfigurací prostředků Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.3 | 5.2 | Zákazník |

Pomocí zásad Azure [odepřít] a [nasadit, pokud neexistují] vynutit zabezpečené nastavení napříč prostředky Azure.  Kromě toho můžete použít šablony Azure Resource Manager uudržovat konfiguraci zabezpečení prostředků Azure vyžadovaných vaší organizací. 

- [Principy efektů zásad Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Vytváření a správa zásad pro vynucení dodržování předpisů](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Přehled šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Udržovat bezpečné konfigurace operačního systému

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.4 | 5.2 | Sdílená |

Postupujte podle doporučení Centra zabezpečení Azure k provádění hodnocení ohrožení zabezpečení na výpočetních prostředcích Azure.  Kromě toho můžete použít šablony Azure Resource Manager, image vlastního operačního systému nebo konfiguraci stavu Azure Automation k udržení konfigurace zabezpečení operačního systému vyžadovaného vaší organizací.   Šablony virtuálních počítačů Microsoft v kombinaci s azure automatizace konfigurace požadovaného stavu může pomoci při plnění a udržování požadavků na zabezpečení. 

Všimněte si také, že image virtuálních strojů Azure Marketplace publikované společností Microsoft spravuje a spravuje Microsoft. 

- [Jak implementovat doporučení k vyhodnocení ohrožení zabezpečení Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Jak vytvořit virtuální počítač Azure ze šablony Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Přehled konfigurace stavu automatizace Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Vytvoření virtuálního počítače s Windows na webu Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Informace o stažení šablony virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Ukázkový skript pro nahrání virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Konfigurace prostředků Azure bezpečně ukládá

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7,5 | 5.3 | Zákazník |

Azure DevOps můžete bezpečně ukládat a spravovat kód, jako jsou vlastní zásady Azure, šablony Azure Resource Manageru a skripty konfigurace požadovaného stavu. Přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, integrovaným skupinám zabezpečení nebo skupinám definovaným ve službě Azure Active Directory (Azure AD), pokud jsou integrovány s Azure DevOps, nebo službou Active Directory, pokud jsou integrovány s TFS.

- [Jak ukládat kód v Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Oprávnění a skupiny v Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpečné ukládání vlastních bitových kopií operačního systému

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.6 | 5.3 | Zákazník |

Pokud používáte vlastní image, použijte řízení přístupu na základě rolí (RBAC) zajistit pouze oprávněné uživatele přístup k bitovým kopiím. Pomocí Galerie sdílených obrázků můžete sdílet obrázky s různými uživateli, instančními objekty nebo skupinami služby AD v rámci organizace.  Pro image kontejnerů je uložte do registru kontejnerů Azure a využijte RBAC k zajištění, že k bitovým kopiím mají přístup pouze oprávnění uživatelé.  

- [Principy RBAC v Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Principy RBAC pro registru kontejnerů](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Jak nakonfigurovat RBAC v Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Sdílená galerie obrázků – přehled](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Nasazení nástrojů pro správu konfigurace pro prostředky Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.7 | 5.4 | Zákazník |

Definujte a implementujte standardní konfigurace zabezpečení pro prostředky Azure pomocí zásad Azure. Pomocí aliasů zásad Azure můžete vytvářet vlastní zásady k auditování nebo vynucení síťové konfigurace prostředků Azure. Můžete také použít předdefinované definice zásad související s konkrétními zdroji.  Kromě toho můžete použít Azure Automation k nasazení změny konfigurace.

- [Jak nakonfigurovat a spravovat zásady Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Jak používat aliasy](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Nasazení nástrojů pro správu konfigurace pro operační systémy

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.8 | 5.4 | Zákazník |

Azure Automation State Configuration je služba správy konfigurace pro uzly konfigurace požadovaného stavu (DSC) v libovolném cloudu nebo místním datovém centru. Můžete snadno napalubě počítačů, přiřadit jim deklarativní konfigurace a zobrazit sestavy zobrazující dodržování předpisů každého počítače do zadaného stavu. 

- [Onboardingové počítače pro správu pomocí konfigurace stavu azure automatizace](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementace automatického monitorování konfigurace pro prostředky Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.9 | 5.5 | Zákazník |

Azure Security Center slouží k provádění základních prohledávese prostředků Azure.  Kromě toho použijte zásady Azure k upozornění a auditování konfigurací prostředků Azure.

- [Jak napravit doporučení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementace automatizovaného monitorování konfigurace operačních systémů

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.10 | 5.5 | Zákazník |

Azure Security Center slouží k provádění prohledávacích směrných plánů pro nastavení operačního systému a dockeru pro kontejnery.

- [Porozumění doporučení týkajících se kontejnerů Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpečná správa tajných kódů Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.11 | 13.1 | Zákazník |

Pomocí identity spravované služby ve spojení s Azure Key Vault zjednodušte a zabezpečte správu tajných klíčů pro vaše cloudové aplikace.

- [Jak integrovat s Azure Spravované identity](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Jak vytvořit trezor klíčů](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Jak zajistit ověřování trezoru klíčů se spravovanou identitou](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpečná a automatická správa identit

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.12 | 4.1 | Zákazník |

Spravované identity slouží k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity umožňuje ověření na libovolnou službu, která podporuje ověřování Azure AD, včetně trezoru klíčů, bez pověření ve vašem kódu.

- [Konfigurace spravovaných identit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminujte nezamýšlenou expozici pověření

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.13 | 18.1, 18.7 | Zákazník |

Implementujte skener pověření k identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů také podpoří přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je azure key vault. 

- [Jak nastavit skener pověření](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Další kroky

- Podívejte se na další ovládací prvek zabezpečení: [Malware Defense](security-control-malware-defense.md)