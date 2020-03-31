---
title: Prostředí pro správu napříč tenanty
description: Správa delegovaných prostředků Azure umožňuje prostředí pro správu mezi tenanty.
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 0e55923e688d1062adc5838a88e8d3202864282a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218390"
---
# <a name="cross-tenant-management-experiences"></a>Prostředí pro správu napříč tenanty

Jako poskytovatel služeb můžete pomocí [azure delegované správy prostředků](../concepts/azure-delegated-resource-management.md) spravovat prostředky Azure pro více zákazníků z vlastního tenanta na webu Azure [Portal](https://portal.azure.com). Většinu úkolů a služeb lze provádět na delegovaných prostředcích Azure napříč spravovanými klienty. Tento článek popisuje některé rozšířené scénáře, kde azure delegované správy prostředků může být efektivní.

> [!NOTE]
> Azure delegované správy prostředků lze také použít [v rámci podniku, který má více klientů Azure AD vlastní](enterprise.md) zjednodušit správu mezi klienty.

## <a name="understanding-customer-tenants"></a>Principy klienta zákazníků

Tenant Azure Active Directory (Azure AD) je reprezentace organizace. Je to vyhrazená instance Azure AD, kterou organizace obdrží při vytváření vztahu s Microsoftem registrací do Azure, Microsoft 365 nebo jiných služeb. Každý klient Azure AD je odlišný a oddělený od ostatních klientů Azure AD a má své vlastní ID klienta (identifikátor GUID). Další informace najdete v tématu [Co je Služba Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

Obvykle, aby bylo možné spravovat prostředky Azure pro zákazníka, poskytovatelé služeb by se museli přihlásit k portálu Azure pomocí účtu přidruženého k tenantovi zákazníka, což vyžaduje, aby správce v tenantovi zákazníka vytvořil a spravoval uživatelské účty. pro poskytovatele služeb.

Díky správě delegovaných prostředků Azure určuje proces registrace uživatele v rámci tenanta poskytovatele služeb, kteří budou mít přístup k předplatným, skupinám prostředků a prostředkům v tenantovi zákazníka a spravovat je. Tito uživatelé se pak můžou přihlásit k portálu Azure pomocí vlastních přihlašovacích údajů. V rámci portálu Azure můžou spravovat prostředky patřící všem zákazníkům, ke kterým mají přístup. To lze provést na stránce [Moji zákazníci](../how-to/view-manage-customers.md) na webu Azure Portal nebo přímo v rámci předplatného tohoto zákazníka, a to buď na portálu Azure, nebo prostřednictvím api.

Správa delegovaných prostředků Azure umožňuje větší flexibilitu při správě prostředků pro více zákazníků, aniž byste se museli přihlašovat k různým účtům v různých klientech. Poskytovatel služeb může mít například tři zákazníky s různými povinnostmi a úrovněmi přístupu, jak je znázorněno zde:

![Tři klienti zákazníků zobrazující odpovědnosti poskytovatele služeb](../media/azure-delegated-resource-management-customer-tenants.jpg)

Pomocí správy delegovaných prostředků Azure se oprávnění uživatelé můžou přihlásit k tenantovi poskytovatele služeb a získat tak přístup k těmto prostředkům, jak je znázorněno tady:

![Prostředky zákazníků spravované prostřednictvím jednoho klienta poskytovatele služeb](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Podpora api a nástrojů pro správu

Úlohy správy můžete provádět na delegovaných prostředcích buď přímo na portálu, nebo pomocí api a nástrojů pro správu (jako je Azure CLI a Azure PowerShell). Všechna existující rozhraní API lze použít při práci s delegovanými prostředky, pokud je funkce podporována pro správu mezi klienty a uživatel má příslušná oprávnění.

Rutina Azure PowerShell [Get-AzSubscription](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0) zobrazuje **ID tenanta** pro každé předplatné, což vám umožní určit, jestli vrácené předplatné patří vašemu tenantovi poskytovatele služeb nebo spravovanému klientovi zákazníka.

Podobně příkazy Azure CLI, jako je [seznam účtů az](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) zobrazit **homeTenantId** a **managedByTenants** atributy.

> [!TIP]
> Pokud tyto hodnoty při používání azure cli nevidíte, zkuste `az account clear` vymazat `az login --identity`mezipaměť spuštěním následovaným .

Poskytujeme také api, která jsou specifická pro provádění úloh správy delegovaných prostředků Azure. Další informace najdete v části **Odkaz.**

## <a name="enhanced-services-and-scenarios"></a>Vylepšené služby a scénáře

Většinu úkolů a služeb lze provádět v delegovaných prostředcích napříč spravovanými klienty. Níže jsou uvedeny některé z klíčových scénářů, kde může být efektivní správa mezi tenanty.

[Azure Arc pro servery (preview)](../../azure-arc/servers/overview.md):

- [Připojení počítačů s Windows Serverem nebo Linuxem mimo Azure](../../azure-arc/servers/quickstart-onboard-portal.md) k delegovaným předplatným nebo skupinám prostředků v Azure
- Správa připojených počítačů pomocí konstrukcí Azure, jako jsou Zásady Azure a označování

[Azure Automation](../../automation/index.yml):

- Použití účtů automatizace pro přístup k delegovaným prostředkům zákazníků a práci s ním

[Zálohování Azure](../../backup/index.yml):

- Zálohování a obnovení zákaznických dat v klientech zákazníků
- Pomocí [Průzkumníka zálohování](../../backup/monitor-azure-backup-with-backup-explorer.md) můžete zobrazit provozní informace o položkách zálohování (včetně prostředků Azure, které ještě nejsou nakonfigurované pro zálohování) a informací o monitorování (úlohy a výstrahy) pro delegovaná předplatná. Průzkumník zálohování je momentálně dostupný jenom pro data virtuálních aplikací Azure.
- Pomocí [sestav zálohování](../../backup/configure-reports.md) v delegovaných předplatných můžete sledovat historické trendy, analyzovat spotřebu úložiště záloh a auditovat zálohování a obnovení.

[Služba Azure Kubernetes (AKS)](../../aks/index.yml):

- Správa hostovaných prostředí Kubernetes a nasazení a správa kontejnerizovaných aplikací v rámci klientských klientů zákazníků

[Azure Monitor](../../azure-monitor/index.yml):

- Zobrazení upozornění pro delegovaná předplatná s možností zobrazit výstrahy ve všech předplatných
- Zobrazit podrobnosti protokolu aktivit pro delegovaná předplatná
- Analýza protokolů: Dotazujte data ze vzdálených klientských prostorů ve více klientech
- Vytvářejte výstrahy v klientech zákazníků, které aktivují automatizaci, jako jsou runbooky Azure Automation nebo Azure Functions, v tenantovi poskytovatele služeb prostřednictvím webhooků

[Zásady Azure](../../governance/policy/index.yml):

- Snímky dodržování předpisů zobrazují podrobnosti o přiřazených zásadách v rámci delegovaných předplatných
- Vytváření a úpravy definic zásad v rámci delegovaného předplatného
- Přiřazení definic zásad definovaných zákazníkem v rámci delegovaného předplatného
- Zákazníkům se vedle zásad, které sami vytvořili, zobrazují zásady vytvořené poskytovatelem služeb
- Můžete [remediate deployIfNotExists nebo upravit přiřazení v rámci klienta zákazníka](../how-to/deploy-policy-remediation.md)

[Graf prostředků Azure](../../governance/resource-graph/index.yml):

- Nyní obsahuje ID klienta ve vrácených výsledcích dotazu, což umožňuje určit, zda předplatné patří klientovi zákazníka nebo tenantovi poskytovatele služeb

[Azure Security Center](../../security-center/index.yml):

- Viditelnost napříč tenanty
  - Monitorování dodržování zásad zabezpečení a zajištění pokrytí zabezpečení ve všech prostředcích klientů
  - Průběžné monitorování dodržování předpisů u více zákazníků v jednom zobrazení
  - Sledování, třídění a stanovení priorit doporučení zabezpečení s použitelné pro bezpečné
- Správa stavů zabezpečení napříč tenanty
  - Správa zásad zabezpečení
  - Provést akci týkající se prostředků, které nejsou v souladu s doporučeními zabezpečení, na kterou lze navléknout
  - Shromažďování a ukládání dat souvisejících se zabezpečením
- Detekce a ochrana hrozeb napříč tenanty
  - Detekce hrozeb napříč prostředky klientů
  - Použití pokročilých ovládacích prvků ochrany před hrozbami, jako je například přístup k virtuálním ms v rámci za čase (JIT)
  - Zpevnění konfigurace skupiny zabezpečení sítě pomocí adaptivního posílení sítě
  - Ujistěte se, že servery používají pouze aplikace a procesy, které by měly být s adaptivními ovládacími prvky aplikací
  - Sledování změn důležitých souborů a položek registru pomocí sledování integrity souborů (FIM)

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Správa prostředků Azure [Sentinelu v klientech zákazníků](../../sentinel/multiple-tenants-service-providers.md)
- [Sledování útoků a zobrazení výstrah zabezpečení mezi více klienty zákazníků](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Stav služby Azure](../../service-health/index.yml):

- Sledování stavu prostředků zákazníků pomocí Azure Resource Health
- Sledování stavu služeb Azure používaných vašimi zákazníky

[Obnovení webu Azure](../../site-recovery/index.yml):

- Správa možností zotavení po havárii pro virtuální počítače Azure v klientech zákazníků (všimněte si, že ke kopírování rozšíření virtuálních počítačů nelze použít účty RunAs).

[Virtuální počítače Azure:](../../virtual-machines/index.yml)

- Rozšíření virtuálních počítačů slouží k poskytování úloh konfigurace a automatizace po nasazení na virtuálních počítačích Azure v klientech zákazníků
- Řešení potíží s virtuálními počítači Azure v klientech zákazníků pomocí diagnostiky spouštění
- Přístup k virtuálním mům se sériovou konzolou v klientech zákazníků
- Všimněte si, že službu Azure Active Directory nelze použít pro vzdálené přihlášení k virtuálnímu počítači a nelze integrovat virtuální počítač s trezorem klíčů pro hesla, tajné klíče nebo kryptografické klíče pro šifrování disku.

[Virtuální síť Azure](../../virtual-network/index.yml):

- Nasazení a správa virtuálních sítí a karet virtuálních síťových rozhraní (vNICs) v rámci klientských klientů zákazníků

Žádosti o podporu:

- Otevřete žádosti o podporu pro delegované prostředky z okna **Nápověda + podpora** na webu Azure Portal (výběr plánu podpory, který je k dispozici pro delegovaný obor)

## <a name="current-limitations"></a>Aktuální omezení
U všech scénářů mějte na paměti následující aktuální omezení:

- Požadavky zpracovávané službou Azure Resource Manager lze provádět pomocí správy delegovaných prostředků Azure. Operace URI pro tyto požadavky `https://management.azure.com`začínají na . Požadavky, které jsou zpracovány instancí typu prostředku (například přístup k tajným klíčům nebo přístup k datům úložiště) však nejsou podporovány se správou delegovaných prostředků Azure. Identifikátory URI operace pro tyto požadavky obvykle začínají adresou, která `https://myaccount.blob.core.windows.net` je `https://mykeyvault.vault.azure.net/`jedinečná pro vaši instanci, například nebo . Ty jsou také obvykle datové operace spíše než operace správy. 
- Přiřazení rolí musí používat [integrované role](../../role-based-access-control/built-in-roles.md)řízení přístupu na základě rolí (RBAC). Všechny předdefinované role jsou aktuálně podporované se správou delegovaných prostředků Azure s výjimkou vlastníka nebo všech předdefinovaných rolí s [oprávněním DataActions.](../../role-based-access-control/role-definitions.md#dataactions) Role Správce přístupu uživatelů je podporována pouze pro omezené použití při [přiřazování rolí spravovaným identitám](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Vlastní role a [klasické role správce předplatného](../../role-based-access-control/classic-administrators.md) nejsou podporovány.
- Zatímco můžete napalubě předplatná, které používají Azure Databricks, uživatelé ve správě tenanta nelze spustit Azure Databricks pracovní prostory na delegované předplatné v tuto chvíli.
- Zatímco můžete na palubě předplatných a skupin prostředků pro azure delegované správy prostředků, které mají uzamčení prostředků, tyto zámky nezabrání akce provádí uživatelé ve správě tenanta. [Odepřít přiřazení,](../../role-based-access-control/deny-assignments.md) které chrání prostředky spravované systémem, jako jsou ty vytvořené spravovanými aplikacemi Azure nebo Azure Blueprints (systémem přiřazená přiřazení zamítnutí), zabránit uživatelům ve správě klienta z působení na tyto prostředky; v tuto chvíli však uživatelé v tenantovi zákazníka nemohou vytvořit vlastní přiřazení odepřít (uživatelem přiřazené odepřít přiřazení).

## <a name="next-steps"></a>Další kroky

- Založte své zákazníky do Azure delegované správy prostředků, a to buď [pomocí šablon Azure Resource Manager,](../how-to/onboard-customer.md) nebo [publikováním nabídky privátních nebo veřejných spravovaných služeb na Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Zobrazte a spravujte zákazníky](../how-to/view-manage-customers.md) tak, že přejdete na můj **zákazník** na webu Azure Portal.
