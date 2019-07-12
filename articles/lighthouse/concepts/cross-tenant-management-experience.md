---
title: Prostředí pro správu napříč tenanty s maják Azure
description: Správa Azure delegovanému prostředku umožňuje prostředí pro správu napříč tenanty.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 15454d4b3f0abad6166c4b163df6c8652669d649
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809901"
---
# <a name="cross-tenant-management-experiences"></a>Prostředí pro správu napříč tenanty

Tento článek popisuje scénáře, které, jako poskytovatel služeb, můžete použít s [delegovat správu prostředků Azure](../concepts/azure-delegated-resource-management.md) ke správě prostředků Azure pro více zákazníků ze svého vlastního tenanta v [webu Azure portal ](https://portal.azure.com).

> [!NOTE]
> Správa prostředků Azure delegované lze také v rámci organizace, která má více tenantů vlastní zjednodušovaly správu napříč tenanty.

## <a name="understanding-customer-tenants"></a>Principy zákazníka tenantů

Klient služby Azure Active Directory (Azure AD) je reprezentace organizace. Je vyhrazenou instanci služby Azure AD, kterou organizace obdrží po vytvoření vztahu se společností Microsoft registrací do Azure, Microsoft 365 nebo jiné služby. Každý tenant Azure AD je samostatný a oddělený od ostatních tenantů Azure AD a má své vlastní ID tenanta (GUID). Další informace najdete v tématu [co je Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

Abyste mohli spravovat prostředky Azure pro zákazníky, obvykle poskytovatelé služeb by mít pro přihlášení k webu Azure portal pomocí účtu tenanta daného zákazníka, které vyžadují oprávnění správce v tenantovi zákazníka můžete vytvořit a spravovat uživatelské účty přidružené pro poskytovatele služeb.

Se správou prostředků Azure delegované Určuje proces zařazení do systému uživatelů v rámci tenanta poskytovatele služeb, kteří se budou moct používat a spravovat předplatná, skupiny prostředků a prostředky v tenantovi zákazníka. Tito uživatelé mohou Přihlaste se k webu Azure portal pomocí vlastních přihlašovacích údajů. Na webu Azure portal můžou spravovat prostředky, které patří všem zákazníkům, ke kterým mají přístup. To můžete udělat přechodem [zákazníci](../how-to/view-manage-customers.md) stránky na webu Azure portal nebo z práce přímo v rámci předplatného tohoto zákazníka, na webu Azure Portal nebo prostřednictvím rozhraní API.

Správa delegovaného prostředků Azure umožňuje větší flexibilitu pro správu prostředků pro několik zákazníků bez nutnosti se na různé účty v různých tenantech. Poskytovatel služeb například může mít tři zákazníky s rozdílné povinnosti a úrovně přístupu, jak je znázorněno zde:

![Tři klienty zákazníků zobrazující odpovědnosti poskytovatele služby](../media/azure-delegated-resource-management-customer-tenants.jpg)

Pomocí správy prostředků Azure delegované oprávněným uživatelům můžete přihlásit k tenanta poskytovatele služeb pro přístup k těmto prostředkům, jak je znázorněno zde:

![Zákazník prostředky spravované přes poskytovatele tenanta jedné služby](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="supported-services-and-scenarios"></a>Podporované služby a scénáře

Prostředí pro správu napříč tenanty v současné době podporuje následující scénáře s prostředky delegované zákazníků:

[Azure Automation](https://docs.microsoft.com/azure/automation/):

- Účty automation použít pro přístup k a pracovat s prostředky delegované zákazníka

[Azure Backup](https://docs.microsoft.com/azure/backup/):

- Zálohování a obnovení dat zákazníků v tenantech zákazníka

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com//azure/aks/):

- Spravovat hostované prostředí Kubernetes a nasadit a spravovat kontejnerizované aplikace v rámci zákazníka tenantů

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/):

- Zobrazit výstrahy pro delegované předplatné na webu Azure Portal nebo prostřednictvím kódu programu prostřednictvím volání rozhraní REST API s možností zobrazení výstrah napříč všemi předplatnými
- Zobrazit podrobnosti o aktivitě protokolu pro delegované předplatné
- Log analytics: Dotaz na data z pracovních prostorů vzdálené zákazníků v několika tenantech

[Služba Azure Policy](https://docs.microsoft.com/azure/governance/policy/):

- Dodržování předpisů snímky zobrazit podrobnosti pro přiřazené zásady v rámci delegované předplatných
- Vytváření a úpravy definic zásad v rámci delegované předplatné
- Přiřazení definice zásady definované zákazníkem v rámci delegované předplatné
- Zákazníci, naleznete v tématu Zásady vytvořené poskytovatelem služeb společně s žádnými zásadami, které jsou jsme vytvořili sami
- Může napravit deployIfNotExists přiřazení v rámci tenantů zákazníka, pokud zákazník má nakonfigurovanou spravovanou identitu a *roleDefinitionIds* pro přiřazení zásad

[Prostředek Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/):

- Nyní obsahuje ID tenanta v vrácených výsledků, vám umožňuje identifikovat, jestli předplatné patří do tenanta zákazníka nebo poskytovatele služeb pro klienty

[Azure Security Center](https://docs.microsoft.com/azure/security-center/):

- Viditelnost napříč tenanty
  - Monitorování dodržování předpisů se zásadami zabezpečení a zajištění bezpečí napříč prostředky všech tenantů
  - Průběžné dodržování legislativních předpisů monitorování napříč více zákazníků na jednom místě
  - Monitorování, třídění a určit prioritu užitečné zabezpečení doporučení zabezpečení výpočtu skóre
- Správa stavu zabezpečení napříč tenanty
  - Správa zásad zabezpečení
  - Provádět akce s prostředky, které nedodržují předpisy s užitečná doporučení zabezpečení
  - Shromažďovat a ukládat data související se zabezpečením
- Detekce hrozeb napříč tenanty a ochrany
  - Detekce hrozeb napříč prostředky klientů
  - Použít ovládací prvky ochrany pokročilé hrozby jako je například přístup just-in-time (JIT) virtuálního počítače
  - Posílení konfigurace skupiny zabezpečení sítě s adaptivní posílení zabezpečení sítě
  - Ujistěte se, že servery používají aplikace a procesy, které by měla být s adaptivní řízení aplikací
  - Monitorování změní na důležité soubory a položky registru se monitorování Integrity souborů (FIM)

[Azure Service Health](https://docs.microsoft.com/azure/service-health/):

- Monitorování stavu prostředků zákazníků s Azure Resource Health
- Sledování stavu služeb Azure ho vaši zákazníci použijí

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/):

- Spravovat možnosti zotavení po havárii pro virtuální počítače Azure v tenantech zákazníka (Všimněte si, že nemůžete použít účty spustit jako ke zkopírování rozšíření virtuálních počítačů)

[Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/):

- Pomocí rozšíření virtuálního počítače zajistit konfiguraci a automatizaci úloh po nasazení na virtuální počítače Azure v tenantech zákazníka
- Používat diagnostiku spouštění k řešení potíží virtuální počítače Azure v tenantech zákazníka
- Virtuální počítače přístup pomocí konzoly sériového portu v tenantech zákazníka
- Všimněte si, že nemůžete použít Azure Active Directory pro vzdálené přihlášení k virtuálnímu počítači a virtuální počítač nejde integrovat se službou Key Vault pro hesla, tajné kódy nebo kryptografické klíče pro šifrování disku

[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/):

- Nasazení a správa virtuálních sítí a virtuální síťové karty (Vnic) v rámci zákazníka tenantů

Žádosti o podporu:

- Otevření žádosti o podporu pro delegované prostředky z **Nápověda a podpora** okna na webu Azure Portal (Vyberte plán podpory, který je k dispozici pro delegovaného rozsah)

Pomocí všech scénářích mějte prosím na následující aktuální omezení:

- Požadavků zpracovaných službou Azure Resource Manageru je možné provádět pomocí prostředků Azure delegovanou správu. Operace identifikátory URI pro tyto požadavky začínat `https://management.azure.com`. Se správou prostředků Azure delegované se však nepodporují požadavků, které jsou zpracovávány instance typu prostředku (například přístup tajné kódy KeyVault nebo přístup k datům úložiště). Operace identifikátory URI pro tyto žádosti obvykle začínáte s adresou, které je jedinečné pro vaše instance jako `https://myaccount.blob.core.windows.net` nebo `https://mykeyvault.vault.azure.net/`. Druhá možnost také jsou obvykle operace s daty, spíše než operace správy. 
- Přiřazení role musí používat řízení přístupu na základě role (RBAC) [předdefinované role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Všechny předdefinované role jsou aktuálně podporovány se správou prostředků Azure delegované s výjimkou vlastníka, správce uživatelských přístupů nebo žádné předdefinované role s [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) oprávnění. Vlastní role a [rolí správce v klasickém modelu předplatného](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) také nejsou podporovány.
- V současné době nemůžete připojit předplatné (nebo skupinu prostředků v rámci předplatného) pro Azure delegovat správu prostředků, pokud odběr využívá Azure Databricks. Podobně pokud odběr byl zaregistrován pro registraci se **Microsoft.ManagedServices** poskytovatele prostředků, nebude možné vytvořit pracovní prostor Databricks pro toto předplatné v tuto chvíli.

## <a name="using-apis-and-management-tools-with-cross-tenant-management"></a>Pomocí rozhraní API a Správa nástroje správy napříč tenanty

Podporované služby a scénáře uvedené výše můžete provádět úlohy správy přímo na portálu nebo pomocí rozhraní API a Správa nástroje (například rozhraní příkazového řádku Azure a Azure Powershellem). Všechna stávající rozhraní API je možné při práci s delegované prostředků (pro služby, které jsou podporovány).

Existují také rozhraní API specifická pro provádění úloh správy Azure delegovanému prostředku. Další informace najdete v tématu **odkaz** oddílu.


## <a name="next-steps"></a>Další postup

- Připojení zákazníky do Azure delegované správy prostředků, tak buď [pomocí šablon Azure Resource Manageru](../how-to/onboard-customer.md) nebo [publikování na webu Azure Marketplace nabídku spravovaných služeb pro privátní nebo veřejné](../how-to/publish-managed-services-offers.md).
- [Umožňuje zobrazit a spravovat zákazníky](../how-to/view-manage-customers.md) tak, že přejdete do **zákazníci** na webu Azure Portal.