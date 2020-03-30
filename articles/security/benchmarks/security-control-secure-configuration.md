---
title: Azure Security Control – zabezpečená konfigurace
description: Konfigurace zabezpečení ovládacího prvku
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934300"
---
# <a name="security-control-secure-configuration"></a>Řízení zabezpečení: Zabezpečená konfigurace

Nastavte, implementujte a aktivně spravujte (sledujte, sestavují, opravujte) konfiguraci zabezpečení prostředků Azure, aby útočníci nezneužívali ohrožené služby a nastavení.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.1 | 5.1 | Zákazník |

Pomocí Zásad Azure nebo Azure Security Center můžete udržovat konfigurace zabezpečení pro všechny prostředky Azure.

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Vytvoření bezpečných konfigurací operačního systému

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.2 | 5.1 | Zákazník |

Pomocí doporučení &quot;Azure Security Center remediate Chyby zabezpečení&quot; v konfiguraci zabezpečení na virtuálních počítačích udržovat konfigurace zabezpečení na všech výpočetních prostředků.

Jak sledovat doporučení Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Jak napravit doporučení Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Udržování zabezpečených konfigurací prostředků Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.3 | 5.2 | Zákazník |

Pomocí zásad Azure [odepřít] a [nasadit, pokud neexistují] vynutit zabezpečené nastavení napříč prostředky Azure.

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Principy efektů zásad Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Udržovat bezpečné konfigurace operačního systému

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.4 | 5.2 | Sdílená |

Image základního operačního systému spravuje a spravuje společnost Microsoft.

Nastavení zabezpečení vyžadovaná vaší organizací však můžete použít pomocí šablon Azure Resource Manager a/nebo konfigurace požadovaného stavu.

Jak vytvořit virtuální počítač Azure ze šablony Azure Resource Manager:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Principy konfigurace požadovaného stavu pro virtuální počítače Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Konfigurace prostředků Azure bezpečně ukládá

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7,5 | 5.3 | Zákazník |

Pokud používáte vlastní definice zásad Azure, použijte Azure DevOps nebo Azure Repos bezpečně ukládat a spravovat svůj kód.

Jak ukládat kód v Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentace k azure repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpečné ukládání vlastních bitových kopií operačního systému

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.6 | 5.3 | Zákazník |

Pokud používáte vlastní obrázky, použijte RBAC, abyste zajistili, že k nim budou mít přístup pouze oprávnění uživatelé. Pro image kontejnerů je uložte do registru kontejnerů Azure a využijte RBAC k zajištění, že k bitovým kopiím mají přístup pouze oprávnění uživatelé.

Principy RBAC v Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Principy RBAC pro registr kontejnerů:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Jak nakonfigurovat RBAC v Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7: Nasazení nástrojů pro správu konfigurace systému

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.7 | 5.4 | Zákazník |

Zásady Azure slouží k výstrahám, auditování a vynucování konfigurací systému. Kromě toho vytvořte proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Nasazení nástrojů pro správu konfigurace systému pro operační systémy

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.8 | 5.4 | Zákazník |

Použijte výpočetní rozšíření Azure, jako je konfigurace požadovaného stavu PowerShellu pro windows compute nebo Linux Chef Extension for Linux.

Jak nainstalovat rozšíření virtuálních strojů v Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementace automatického monitorování konfigurace pro služby Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.9 | 5.5 | Zákazník |

Použití Centra zabezpečení Azure k provádění základních prohledávek pro vaše prostředky Azure

Jak napravit doporučení v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementace automatizovaného monitorování konfigurace operačních systémů

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.1 | 5.5 | Zákazník |

Azure Security Center slouží k provádění prohledávacích směrných plánů pro nastavení operačního systému a dockeru pro kontejnery.

Seznamte se s doporučeními kontejnerů Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpečná správa tajných kódů Azure 

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.11 | 13.1 | Zákazník |

Pomocí identity spravované služby ve spojení s Azure Key Vault zjednodušte a zabezpečte správu tajných klíčů pro vaše cloudové aplikace.

Jak integrovat s Azure Spravované identity:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak vytvořit trezor klíčů:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zajistit ověřování trezoru klíčů se spravovanou identitou:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpečná a automatická správa identit

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.12 | 4.1 | Zákazník |

Spravované identity slouží k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity umožňuje ověření na libovolnou službu, která podporuje ověřování Azure AD, včetně trezoru klíčů, bez pověření ve vašem kódu.

Jak nakonfigurovat spravované identity:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminujte nezamýšlenou expozici pověření

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 7.13 | 13.3 | Zákazník |

Implementujte skener pověření k identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů také podpoří přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je azure key vault. 

Jak nastavit skener pověření:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Další kroky

Podívejte se na další ovládací prvek zabezpečení: [Malware Defense](security-control-malware-defense.md)
