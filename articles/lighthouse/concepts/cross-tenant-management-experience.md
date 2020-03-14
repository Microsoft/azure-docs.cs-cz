---
title: Prostředí pro správu napříč tenanty
description: Správa delegovaných prostředků v Azure umožňuje prostředí pro správu mezi klienty.
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 0e55923e688d1062adc5838a88e8d3202864282a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218390"
---
# <a name="cross-tenant-management-experiences"></a>Prostředí pro správu napříč tenanty

Jako poskytovatel služeb můžete použít [správu delegovaných prostředků Azure](../concepts/azure-delegated-resource-management.md) ke správě prostředků Azure pro více zákazníků v rámci vašeho vlastního tenanta v [Azure Portal](https://portal.azure.com). Většinu úkolů a služeb je možné provádět u delegovaných prostředků Azure ve spravovaných klientech. Tento článek popisuje některé z rozšířených scénářů, ve kterých může být efektivní správa delegovaných prostředků Azure.

> [!NOTE]
> Správu delegovaných prostředků Azure je možné použít i [v rámci podniku, který má více tenantů Azure AD vlastní](enterprise.md) pro zjednodušení správy mezi klienty.

## <a name="understanding-customer-tenants"></a>Porozumění klientům zákazníka

Tenant Azure Active Directory (Azure AD) je zastoupení organizace. Jedná se o vyhrazenou instanci služby Azure AD, kterou organizace obdrží, když vytvoří relaci se společností Microsoft tím, že se přihlásí k Azure, Microsoft 365 nebo jiným službám. Každý tenant služby Azure AD je jedinečný a oddělený od ostatních tenantů Azure AD a má své vlastní ID tenanta (identifikátor GUID). Další informace najdete v tématu [co je Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

Aby bylo možné spravovat prostředky Azure pro zákazníka, poskytovatelé služeb by se museli přihlašovat k Azure Portal pomocí účtu přidruženého k tenantovi daného zákazníka a vyžadovat správce v tenantovi zákazníka k vytváření a správě uživatelských účtů. pro poskytovatele služeb.

Pomocí delegované správy prostředků Azure určí proces zprovoznění uživatele v tenantovi poskytovatele služeb, kteří budou mít přístup k předplatným, skupinám prostředků a prostředkům v tenantovi zákazníka. Tito uživatelé se pak mohou přihlašovat k Azure Portal pomocí vlastních přihlašovacích údajů. V rámci Azure Portal mohou spravovat prostředky patřící všem zákazníkům, ke kterým mají přístup. To se dá udělat tak, že navštívíte stránku [moji Customers](../how-to/view-manage-customers.md) v Azure Portal, nebo přímo v rámci svého předplatného daného zákazníka, a to buď v Azure Portal nebo prostřednictvím rozhraní API.

Správa delegovaných prostředků Azure umožňuje větší flexibilitu při správě prostředků pro více zákazníků bez nutnosti přihlašovat se k různým účtům v různých klientech. Poskytovatel služeb může například mít tři zákazníky s různými odpovědnostmi a úrovněmi přístupu, jak je znázorněno zde:

![Tři klienti zákazníka zobrazující odpovědnosti poskytovatele služeb](../media/azure-delegated-resource-management-customer-tenants.jpg)

Pomocí delegované správy prostředků Azure se oprávnění uživatelé můžou přihlásit k tenantovi poskytovatele služeb, aby měli přístup k těmto prostředkům, jak je vidět tady:

![Prostředky zákazníka spravované prostřednictvím jednoho tenanta poskytovatele služeb](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Podpora rozhraní API a nástrojů pro správu

Úlohy správy můžete u delegovaných prostředků provádět buď přímo na portálu, nebo pomocí rozhraní API a nástrojů pro správu (například Azure CLI a Azure PowerShell). Všechna existující rozhraní API se dají použít při práci s delegovanými prostředky, pokud je tato funkce podporovaná pro správu mezi klienty a uživatel má příslušná oprávnění.

[Rutina Azure PowerShell Get-AzSubscription](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0) zobrazuje **tenantID** pro každé předplatné a umožňuje určit, jestli vrácené předplatné patří do vašeho tenanta poskytovatele služeb nebo do spravovaného tenanta zákazníka.

Podobně příkazy rozhraní příkazového řádku Azure, jako [AZ Account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) , zobrazují atributy **homeTenantId** a **managedByTenants** .

> [!TIP]
> Pokud nevidíte tyto hodnoty při použití rozhraní příkazového řádku Azure, zkuste vymazat mezipaměť spuštěním `az account clear` následovaných `az login --identity`.

Poskytujeme také rozhraní API, která jsou specifická pro provádění úloh správy delegovaných prostředků Azure. Další informace najdete v části **reference** .

## <a name="enhanced-services-and-scenarios"></a>Rozšířené služby a scénáře

Většinu úkolů a služeb je možné provádět na delegovaných prostředcích ve spravovaných klientech. Níže jsou uvedeny některé z klíčových scénářů, ve kterých může být Správa mezi klienty efektivní.

[Azure ARC pro servery (Preview)](../../azure-arc/servers/overview.md):

- [Připojení počítačů se systémem Windows Server nebo Linux mimo Azure](../../azure-arc/servers/quickstart-onboard-portal.md) k delegovaným předplatným nebo skupinám prostředků v Azure
- Spravujte připojené počítače pomocí konstrukcí Azure, například Azure Policy a označování.

[Azure Automation](../../automation/index.yml):

- Použití účtů Automation pro přístup k prostředkům delegovaného zákazníka a práce s nimi

[Azure Backup](../../backup/index.yml):

- Zálohování a obnovení zákaznických dat v klientech zákazníků
- Pomocí [Průzkumníka zálohování](../../backup/monitor-azure-backup-with-backup-explorer.md) si můžete zobrazit provozní informace o zálohovaných položkách (včetně prostředků Azure, které ještě nejsou nakonfigurované pro zálohování), a informace o monitorování (úlohy a výstrahy) pro delegované předplatné. Průzkumník zálohování je momentálně dostupný jenom pro data virtuálních počítačů Azure.
- [Sestavy zálohování](../../backup/configure-reports.md) v rámci delegovaných předplatných vám umožní sledovat historické trendy, analyzovat spotřebu úložiště záloh a auditovat a obnovovat zálohy.

[Služba Azure Kubernetes (AKS)](../../aks/index.yml):

- Správa hostovaných prostředí Kubernetes a nasazení a Správa kontejnerových aplikací v rámci zákaznických tenantů

[Azure monitor](../../azure-monitor/index.yml):

- Zobrazit výstrahy pro delegovaná předplatná s možností zobrazovat výstrahy napříč všemi předplatnými
- Zobrazit podrobnosti protokolu aktivit pro delegované odběry
- Log Analytics: dotazování dat ze vzdálených pracovních prostorů zákazníka ve více klientech
- Vytváření výstrah v klientech zákazníka, kteří spouštějí automatizaci, například Azure Automation Runbooky nebo Azure Functions v tenantovi poskytovatele služeb prostřednictvím webhooků

[Azure Policy](../../governance/policy/index.yml):

- Snímky dodržování předpisů zobrazují podrobnosti o přiřazených zásadách v rámci delegovaných předplatných.
- Vytvoření a úprava definic zásad v rámci delegovaného předplatného
- Přiřazení definic zásad definovaných zákazníkem v rámci delegovaného předplatného
- Zákazníci uvidí zásady vytvořené poskytovatelem služeb společně se všemi zásadami, které sami vytvořily.
- Může [napravit deployIfNotExists nebo upravit přiřazení v rámci tenanta zákazníka](../how-to/deploy-policy-remediation.md) .

[Graf prostředků Azure](../../governance/resource-graph/index.yml):

- Teď obsahuje ID tenanta v vrácených výsledcích dotazu, které vám umožní určit, jestli předplatné patří klientovi zákazníka nebo poskytovateli služeb.

[Azure Security Center](../../security-center/index.yml):

- Viditelnost mezi klienty
  - Monitorování dodržování zásad zabezpečení a zajištění pokrytí zabezpečení napříč prostředky všech tenantů
  - Nepřetržité monitorování dodržování předpisů v rámci více zákazníků v jednom zobrazení
  - Monitorování, třídění a stanovení priorit s napadnutelnými doporučeními zabezpečení pomocí výpočtu zabezpečeného skóre
- Správa stav zabezpečení mezi klienty
  - Správa zásad zabezpečení
  - Provést akci s prostředky, které nedodržují předpisy s užitečnými doporučeními zabezpečení
  - Shromažďování a ukládání dat týkajících se zabezpečení
- Detekce a ochrana hrozeb mezi klienty
  - Zjišťování hrozeb napříč prostředky tenantů
  - Použití pokročilých ovládacích prvků ochrany před hrozbami, jako je například přístup k virtuálnímu počítači za běhu (JIT)
  - Posílení konfigurace skupiny zabezpečení sítě pomocí adaptivního posílení sítě
  - Zajistěte, aby na serverech běžely jenom aplikace a procesy, které by měly být s adaptivními řízeními aplikací.
  - Sledování změn důležitých souborů a položek registru pomocí monitorování integrity souborů (FIM)

[Sentinel Azure](../../sentinel/multiple-tenants-service-providers.md):

- Správa prostředků Azure Sentinel [v klientech zákazníků](../../sentinel/multiple-tenants-service-providers.md)
- [Sledování útoků a zobrazování výstrah zabezpečení napříč klienty s více zákazníky](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Azure Service Health](../../service-health/index.yml):

- Monitorujte stav zákaznických prostředků pomocí Azure Resource Health
- Sledujte stav služeb Azure používaných vašimi zákazníky.

[Azure Site Recovery](../../site-recovery/index.yml):

- Spravujte možnosti zotavení po havárii pro virtuální počítače Azure v klientech zákazníků (Všimněte si, že účty RunAs nemůžete použít ke kopírování rozšíření virtuálních počítačů).

[Virtual Machines Azure](../../virtual-machines/index.yml):

- Použití rozšíření virtuálních počítačů k zajištění konfigurace po nasazení a úloh automatizace na virtuálních počítačích Azure v klientech zákazníků
- Řešení potíží s virtuálními počítači Azure v klientech zákazníků pomocí diagnostiky spouštění
- Přístup k virtuálním počítačům pomocí sériové konzoly v klientech zákazníků
- Všimněte si, že nemůžete použít Azure Active Directory pro vzdálené přihlášení k virtuálnímu počítači a nemůžete integrovat virtuální počítač s Key Vaultem pro hesla, tajné klíče a kryptografické klíče pro šifrování disků.

[Virtual Network Azure](../../virtual-network/index.yml):

- Nasazení a správa virtuálních sítí a karet virtuální síťové adaptéry (Virtual Network Interface) v rámci zákaznických tenantů

Žádosti o podporu:

- Otevřete žádosti o podporu pro delegované prostředky v okně **help + support** v Azure Portal (výběr plánu podpory dostupného pro delegovaný obor).

## <a name="current-limitations"></a>Aktuální omezení
U všech scénářů Prosím mějte na paměti následující stávající omezení:

- Požadavky, které jsou zpracovávány Azure Resource Manager, lze provádět pomocí delegované správy prostředků Azure. Identifikátory URI operace pro tyto požadavky začínají na `https://management.azure.com`. Nicméně požadavky, které jsou zpracovávány instancí typu prostředku (například přístup k tajným klíčům klíčů nebo přístup k datům úložiště), se nepodporují se správou delegovaných prostředků Azure. Operace s identifikátory URI pro tyto požadavky obvykle začínají adresou, která je pro vaši instanci jedinečná, například `https://myaccount.blob.core.windows.net` nebo `https://mykeyvault.vault.azure.net/`. Druhá z nich také obvykle slouží k operacím s daty a nikoli k operacím správy. 
- Přiřazení rolí musí používat [předdefinované role](../../role-based-access-control/built-in-roles.md)řízení přístupu na základě role (RBAC). Všechny předdefinované role se v současné době podporují se správou delegovaných prostředků Azure s výjimkou vlastníka nebo jakýchkoli integrovaných rolí s oprávněním pro [Dataactions](../../role-based-access-control/role-definitions.md#dataactions) . Role správce přístupu uživatelů je podporovaná jenom pro omezené použití při [přiřazování rolí ke spravovaným identitám](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Vlastní role a [role správců pro klasický odběr](../../role-based-access-control/classic-administrators.md) nejsou podporovány.
- I když můžete připojit odběry, které používají Azure Databricks, uživatelé v tenantovi pro správu nemůžou v současné době spouštět Azure Databricks pracovní prostory na delegovaném předplatném.
- I když můžete připojit odběry a skupiny prostředků pro správu delegovaných prostředků Azure, které mají zámky prostředků, nebudou tyto zámky bránit provádění akcí uživatelům ve správě tenanta. [Zakažte přiřazení](../../role-based-access-control/deny-assignments.md) , která chrání systémem spravované prostředky, jako jsou ty, které vytvořily spravované aplikace Azure nebo plány Azure (přiřazení zamítnutí přiřazení systémem), zabrání uživatelům ve správě tenanta na těchto prostředcích. Nicméně v tomto okamžiku nemohou uživatelé v tenantovi zákazníka vytvořit vlastní přiřazení odepřít (přiřazení odepřít uživateli).

## <a name="next-steps"></a>Další kroky

- Připojte vaše zákazníky do správy delegovaných prostředků Azure, a to buď [pomocí šablon Azure Resource Manager](../how-to/onboard-customer.md) , nebo [publikováním nabídky privátních nebo veřejných spravovaných služeb, které Azure Marketplace](../how-to/publish-managed-services-offers.md).
- V **Azure Portal můžete** [Zobrazit a spravovat zákazníky](../how-to/view-manage-customers.md) .
