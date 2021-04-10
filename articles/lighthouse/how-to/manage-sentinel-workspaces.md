---
title: Správa pracovních prostorů Sentinel Azure ve velkém měřítku
description: Zjistěte, jak efektivně spravovat Sentinel Azure na delegovaných zákaznických zdrojích.
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 009edaefe021dedb5d9a40a8cc3bac2c2974ae10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702517"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Správa pracovních prostorů Sentinel Azure ve velkém měřítku

Jako poskytovatel služeb můžete mít k [Azure Lighthouse](../overview.md)k dispozici několik klientů pro zákazníky. Azure Lighthouse umožňuje poskytovatelům služeb provádět operace ve velkém měřítku napříč několika klienty Azure Active Directory (Azure AD) najednou a díky tomu se úlohy správy zefektivňují efektivněji.

Služba Azure Sentinel zajišťuje analýzy zabezpečení a analýzu hrozeb a poskytuje jedno řešení pro detekci výstrah, viditelnost hrozeb, proaktivní lov a reakci na hrozby. Pomocí Azure Lighthouse můžete spravovat víc pracovních prostorů služby Azure Sentinel napříč klienty ve velkém měřítku. To umožňuje scénářům, jako je spouštění dotazů napříč několika pracovními prostory, nebo vytváření sešitů pro vizualizaci a monitorování dat z připojených zdrojů dat, abyste získali přehledy. IP adresy, jako jsou dotazy a playbooky, zůstanou ve vašem spravovaném tenantovi, ale dají se použít k provádění správy zabezpečení v klientech zákazníka.

Toto téma poskytuje přehled o tom, jak pomocí funkce [Azure Sentinel](../../sentinel/overview.md) vytvořit škálovatelný způsob viditelnosti a spravovaných služeb zabezpečení mezi klienty.

> [!TIP]
> I když v tomto tématu odkazujeme na poskytovatele služeb a zákazníky, tyto doprovodné materiály se vztahují také na [podniky, které používají Azure Lighthouse ke správě více tenantů](../concepts/enterprise.md).

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

> [!NOTE]
> Můžete spravovat delegované prostředky, které se nacházejí v různých [oblastech](../../availability-zones/az-overview.md#regions). Nicméně delegování předplatných v rámci [národního cloudu](../../active-directory/develop/authentication-national-cloud.md) a veřejného cloudu Azure nebo ve dvou různých národních cloudech se nepodporuje.

## <a name="granular-azure-role-based-access-control-azure-rbac"></a>Podrobné řízení přístupu na základě role v Azure (Azure RBAC)

Každé předplatné zákazníka, které bude spravovat MSSP, musí být připojené [do Azure Lighthouse](onboard-customer.md). To umožňuje určeným uživatelům v tenantovi spravovat přístup a provádět operace správy v pracovních prostorech Azure Sentinel nasazených v klientech zákazníků.

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

Můžete nasazovat sešity ve vašem tenantovi pro správu a vytvářet na úrovni řídicích panelů pro monitorování a dotazování dat napříč klienty zákazníka. Další informace najdete v tématu [monitorování mezi pracovními prostory](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks). 

Sešity můžete nasadit také přímo do samostatného tenanta, který spravujete pro scénáře specifické pro daného zákazníka.

## <a name="run-log-analytics-and-hunting-queries-across-azure-sentinel-workspaces"></a>Spouštění Log Analytics a loveckých dotazů napříč pracovními prostory Azure Sentinel

Vytvářejte a neukládejte Log Analytics dotazy pro detekci hrozeb centrálně ve správě tenanta, včetně [loveckých dotazů](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-hunting). Tyto dotazy se pak dají spouštět napříč všemi vašimi pracovními prostory Azure Sentinel ve vašich zákaznících pomocí operátoru Union a výrazu pracovního prostoru (). Další informace najdete v tématu [dotazování mezi jednotlivými pracovními prostory](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Použití automatizace pro správu mezi jednotlivými pracovními prostory

Službu Automation můžete použít ke správě několika pracovních prostorů služby Azure Sentinel a ke konfiguraci [loveckých dotazů](../../sentinel/hunting.md), playbooky a sešitů. Další informace najdete v tématu [Správa mezi jednotlivými pracovními prostory pomocí automatizace](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation).

## <a name="monitor-security-of-office-365-environments"></a>Monitorování zabezpečení prostředí Office 365

Pomocí Azure Lighthouse ve spojení se službou Azure Sentinel můžete monitorovat zabezpečení prostředí Office 365 napříč klienty. Nejdřív je nutné, aby [byly ve spravovaném tenantovi zapnuté datové konektory Office 365](../../sentinel/connect-office-365.md) , aby se informace o aktivitách uživatelů a správců v Exchange a SharePointu (včetně OneDrivu) mohly ingestovat do pracovního prostoru Sentinel Azure v rámci spravovaného tenanta. Zahrnuje to i podrobnosti o akcích, jako jsou soubory ke stažení, odeslané požadavky na přístup, změny v událostech skupiny a operace poštovních schránek, a informace o uživatelích, kteří akce provedli. [Výstrahy ochrany před únikem informací office 365](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-office-365-dlp-events-into-azure-sentinel/ba-p/1031820) jsou také podporovány v rámci integrovaného konektoru sady Office 365.

[Konektor Microsoft Cloud App Security (MCAS)](../../sentinel/connect-cloud-app-security.md) můžete povolit pro streamování výstrah a Cloud Discovery přihlášení do Azure Sentinel. To vám umožní získat přehled o cloudových aplikacích, získávat sofistikované analýzy, které identifikují a bojovat proti týká kybernetických hrozeb a řídí, jak se data cestují. Protokoly aktivit pro MCAS se dají [spotřebovat pomocí formátu CEF (Common Event Format)](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-box-com-activity-events-via-microsoft-cloud-app-security/ba-p/1072849).

Po nastavení datových konektorů Office 365 můžete použít možnosti Azure Sentinel mezi klienty, jako je zobrazení a analýza dat v sešitech, používání dotazů k vytváření vlastních výstrah a konfigurace playbooky pro reakci na hrozby.

## <a name="next-steps"></a>Další kroky

- Seznamte se s [Azure Sentinel](../../sentinel/overview.md).
- Přečtěte si [stránku ceny pro ověřovací služby Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/).
- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).

