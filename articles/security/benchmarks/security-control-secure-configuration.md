---
title: Řízení zabezpečení Azure – zabezpečená konfigurace
description: Zabezpečená konfigurace řízení zabezpečení
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 08458777d8a8c5c74ac20a63245135ffaf0a5c04
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564111"
---
# <a name="security-control-secure-configuration"></a>Řízení zabezpečení: zabezpečená konfigurace

Navažte, implementujte a aktivně spravujte (sledujte, vykazovat, opravujte) konfiguraci zabezpečení prostředků Azure, aby útočníci nemohli zneužít ohrožené služby a nastavení.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 7.1 | 5.1 | Zákazník |

K údržbě konfigurací zabezpečení pro všechny prostředky Azure použijte Azure Policy nebo Azure Security Center.

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 7.2 | 5.1 | Zákazník |

Pomocí Azure Security Center doporučení &quot;napravit ohrožení zabezpečení v konfiguracích zabezpečení na Virtual Machines&quot; ke správě konfigurací zabezpečení ve všech výpočetních prostředcích.

Jak monitorovat Azure Security Center doporučení:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Jak opravit Azure Security Center doporučení:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 7.3 | 5.2 | Zákazník |

Použijte zásady Azure [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení napříč prostředky Azure.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Porozumění efektům Azure Policy:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 7.4 | 5.2 | Shared |

Základní image operačního systému se spravují a spravují od Microsoftu.

Můžete ale použít nastavení zabezpečení, které vaše organizace potřebuje, pomocí šablon Azure Resource Manager nebo konfigurace požadovaného stavu.

Jak vytvořit virtuální počítač Azure ze šablony Azure Resource Manager:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Vysvětlení konfigurace požadovaného stavu pro Azure Virtual Machines:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 7.5 | 5.3 | Zákazník |

Pokud používáte vlastní definice zásad Azure, použijte Azure Azure DevOps nebo Azure Repos k bezpečnému ukládání a správě kódu.

Jak ukládat kód v Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos dokumentaci:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 7.6 | 5.3 | Zákazník |

Pokud používáte vlastní image, zajistěte, aby k nim měli přístup jenom autorizovaní uživatelé. V případě imagí kontejnerů je uložte do Azure Container Registry a využijte RBAC, aby bylo zajištěno, že k imagí budou mít přístup jenom autorizovaní uživatelé.

Princip RBAC v Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Vysvětlení RBAC pro Container Registry:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Jak nakonfigurovat RBAC v Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 7.7 | 5.4 | Zákazník |

Pomocí Azure Policy můžete vyvarovat, auditovat a vymáhat konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 7.8 | 5.4 | Zákazník |

Použijte výpočetní rozšíření Azure, jako je třeba konfigurace požadovaného stavu prostředí PowerShell pro rozšíření Windows Compute nebo Linux pro Linux.

Jak nainstalovat rozšíření virtuálních počítačů v Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 7,9 | 5.5 | Zákazník |

Použití Azure Security Center k provádění kontrol standardních hodnot vašich prostředků Azure

Postup nápravy doporučení v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 7.1 | 5.5 | Zákazník |

Pomocí Azure Security Center můžete provádět kontroly základní hodnoty pro kontejnery a nastavení Docker.

Principy Azure Security Centerch doporučení ke kontejneru:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure 

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 7.11 | 13,1 | Zákazník |

K zjednodušení a zabezpečení správy tajných kódů pro cloudové aplikace můžete použít Identita spravované služby ve spojení s Azure Key Vault.

Jak integrovat se spravovanými identitami Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Vytvoření Key Vault: https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zajistit Key Vault ověřování pomocí spravované identity: https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 7,12 | 4.1 | Zákazník |

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

Postup konfigurace spravovaných identit: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 7,13 | 13,3 | Zákazník |

Implementujte kontrolu přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

Jak nastavit skener přihlašovacích údajů: https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Další kroky

Zobrazit další řízení zabezpečení: [obrana proti malwaru](security-control-malware-defense.md)
