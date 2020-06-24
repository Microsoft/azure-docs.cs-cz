---
title: Správa pracovních prostorů Sentinel Azure ve velkém měřítku
description: Zjistěte, jak efektivně spravovat Sentinel Azure na delegovaných zákaznických zdrojích.
ms.date: 06/17/2020
ms.topic: how-to
ms.openlocfilehash: bca5b6fdc84fa5a7a5553fe64c0218c5f0b44aa6
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85268985"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Správa pracovních prostorů Sentinel Azure ve velkém měřítku

Jako poskytovatel služeb můžete mít k dispozici více zákazníků pro správu delegovaných prostředků Azure. Azure Lighthouse umožňuje poskytovatelům služeb provádět operace ve velkém měřítku napříč několika klienty Azure Active Directory (Azure AD) najednou a díky tomu se úlohy správy zefektivňují efektivněji.

Služba Azure Sentinel zajišťuje analýzy zabezpečení a analýzu hrozeb a poskytuje jedno řešení pro detekci výstrah, viditelnost hrozeb, proaktivní lov a reakci na hrozby. Pomocí Azure Lighthouse můžete spravovat víc pracovních prostorů služby Azure Sentinel napříč klienty ve velkém měřítku. To umožňuje scénářům, jako je spouštění dotazů napříč několika pracovními prostory, nebo vytváření sešitů pro vizualizaci a monitorování dat z připojených zdrojů dat, abyste získali přehledy. IP adresy, jako jsou dotazy a playbooky, zůstanou ve vašem spravovaném tenantovi, ale dají se použít k provádění správy zabezpečení v klientech zákazníka.

Toto téma poskytuje přehled o tom, jak pomocí funkce [Azure Sentinel](../../sentinel/overview.md) vytvořit škálovatelný způsob viditelnosti a spravovaných služeb zabezpečení mezi klienty.

## <a name="architectural-considerations"></a>Pokyny pro architekturu

Pro spravovaného poskytovatele služby zabezpečení (MSSP), který chce vytvořit nabídku zabezpečení jako služby pomocí Azure Sentinelu, může být potřeba jediné Centrum zabezpečení (SOC), které bude centrálně monitorovat, spravovat a konfigurovat víc pracovních prostorů služby Azure Sentinel nasazených v rámci jednotlivých zákaznických klientů. Podobně podniky s více klienty Azure AD můžou chtít centrálně spravovat víc pracovních prostorů Sentinel Azure nasazených ve svých klientech.

Tento centralizovaný model nasazení má následující výhody:

- Vlastnictví dat zůstává u každého spravovaného tenanta.
- Podporuje požadavky na ukládání dat v rámci geografických hranic.
- Zajišťuje izolaci dat, protože data pro více zákazníků nejsou uložená ve stejném pracovním prostoru. 
- Zabraňuje tomu, aby se data exfiltrace ze spravovaných tenantů, což pomáhá zajistit dodržování dat.
- Související náklady se účtují na každého spravovaného tenanta, nikoli na řízení tenanta.
- Data ze všech zdrojů dat a datových konektorů, které jsou integrovány pomocí služby Azure Sentinel (například protokoly aktivit Azure AD, protokoly Office 365 nebo výstrahy ochrany Microsoft Threat Protection), zůstanou v rámci každého tenanta zákazníka.
- Snižuje latenci sítě.
- Snadné přidání nebo odebrání nových poboček nebo zákazníků

## <a name="granular-role-based-access-control-rbac"></a>Podrobné řízení přístupu na základě role (RBAC)

Každé předplatné zákazníka, které bude spravovat MSSP, musí být zavedené [pro správu delegovaných prostředků Azure](onboard-customer.md). To umožňuje určeným uživatelům v tenantovi spravovat přístup a provádět operace správy v pracovních prostorech Azure Sentinel nasazených v klientech zákazníků.

Při vytváření autorizací můžete přiřadit předdefinované role služby Azure Sentinel uživatelům, skupinám nebo instančním objektům ve vašem tenantovi pro správu:

- [Čtečka Sentinel Azure](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Respondér služby Azure Sentinel](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Přispěvatel Sentinel Azure](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

K provádění dalších funkcí můžete také přiřadit další předdefinované role. Informace o konkrétních rolích, které se dají použít se službou Azure Sentinel, najdete v tématu [oprávnění v Azure Sentinel](../../sentinel/roles.md).

Po připojení svých zákazníků se určení uživatelé můžou přihlásit do svého správce a [získat přímý přístup k pracovnímu prostoru Azure Sentinel zákazníka](../../sentinel/multiple-tenants-service-providers.md) s přiřazenými rolemi.

## <a name="view-and-manage-incidents-across-workspaces"></a>Zobrazení a Správa incidentů napříč pracovními prostory

Pokud spravujete prostředky služby Azure Sentinel pro více zákazníků, můžete zobrazit a spravovat incidenty ve více pracovních prostorech v rámci několika tenantů najednou. Další informace najdete v tématu [práce s incidenty v mnoha pracovních prostorech](../../sentinel/multiple-workspace-view.md) najednou a [rozšířené použití Azure Sentinel napříč pracovními prostory a klienty](../../sentinel/extend-sentinel-across-workspaces-tenants.md).

> [!NOTE]
> Ujistěte se, že uživatelé ve vašem tenantovi pro správu mají přiřazená oprávnění ke čtení a zápisu ve všech pracovních prostorech, které jsou spravované. Pokud má uživatel oprávnění ke čtení v některých pracovních prostorech, mohou se zprávy s upozorněním zobrazit při výběru incidentů v těchto pracovních prostorech a uživatel nebude moci upravovat tyto incidenty ani jiné uživatele, které jste vybrali, a to ani v případě, že máte oprávnění pro ostatní.

## <a name="configure-playbooks-for-mitigation"></a>Konfigurace playbooky pro zmírnění rizik

[Playbooky](../../sentinel/tutorial-respond-threats-playbook.md) se dá použít k automatickému zmírnění rizik při aktivaci výstrahy. Tyto playbooky se dají spouštět ručně, nebo se můžou spouštět automaticky, když se aktivují konkrétní výstrahy. Playbooky se dá nasadit buď v tenantovi pro správu, nebo v tenantovi zákazníka s nakonfigurovanými postupy odpovědí na základě toho, kteří uživatelé tenanta budou muset v reakci na bezpečnostní hrozbu provést akci.

## <a name="create-cross-tenant-workbooks"></a>Vytváření sešitů mezi klienty

[Azure monitor sešitů v Azure Sentinel](../../sentinel/overview.md#workbooks) vám pomůžou vizualizovat a monitorovat data z připojených zdrojů dat a získat přehledy. Můžete použít předdefinované šablony sešitu ve službě Azure Sentinel nebo vytvářet vlastní sešity pro vaše scénáře.

Můžete nasazovat sešity ve vašem tenantovi pro správu a vytvářet na úrovni řídicích panelů pro monitorování a dotazování dat napříč klienty zákazníka. Další informace najdete v tématu [monitorování mezi pracovními prostory](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks). Všimněte si, že některé možnosti nejsou [podporovány napříč více pracovními prostory](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

Sešity můžete nasadit také přímo do samostatného tenanta, který spravujete pro scénáře specifické pro daného zákazníka.

## <a name="run-queries-across-azure-sentinel-workspaces"></a>Spouštění dotazů napříč pracovními prostory Azure Sentinel

Můžete vytvořit a uložit Log Analytics dotazy pro detekci hrozeb centrálně ve správě tenanta. Tyto dotazy se pak dají spouštět napříč všemi vašimi pracovními prostory Azure Sentinel ve vašich zákaznících pomocí operátoru Union a výrazu pracovního prostoru (). Další informace najdete v tématu [dotazování mezi jednotlivými pracovními prostory](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Použití automatizace pro správu mezi jednotlivými pracovními prostory

Službu Automation můžete použít ke správě několika pracovních prostorů služby Azure Sentinel a ke konfiguraci [loveckých dotazů](../../sentinel/hunting.md), playbooky a sešitů. Další informace najdete v tématu [Správa mezi jednotlivými pracovními prostory pomocí automatizace](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation).

Všimněte si, že některé možnosti se [v současné době nepodporují napříč několika pracovními prostory](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

## <a name="next-steps"></a>Další kroky

- Seznamte se s [Azure Sentinel](../../sentinel/overview.md).
- Přečtěte si [stránku ceny pro ověřovací služby Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/).
- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).

