---
title: Portál OMS se přesouvá do Azure | Dokumenty společnosti Microsoft
description: Portál OMS se zalahová se všemi funkcemi, které se přesouvají na portál Azure. Tento článek obsahuje podrobnosti o tomto přechodu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 680cd9b44cc447f9bdea38cb9d04fc661fba9c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659250"
---
# <a name="oms-portal-moving-to-azure"></a>Portál OMS se přesouvá do Azure

> [!NOTE]
> Tento článek se vztahuje jak na veřejný cloud Azure, tak pro vládní cloud, pokud není uvedeno jinak.

**Portál OMS pro veřejný cloud Azure byl oficiálně vyřazen. 15. května 2019 byl oficiálně vyřazen portál OMS pro cloud Azure US Government.** Jsme nadšeni, že přejdeme na portál Azure a očekáváme, že přechod bude snadný. Ale chápeme, že změny jsou obtížné a mohou být rušivé. Zbytek tohoto článku prochází klíčové scénáře a plán pro tento přechod.

Portál Azure je centrem pro všechny služby Azure a nabízí bohaté možnosti správy s funkcemi, jako jsou řídicí panely pro připnutí prostředků, inteligentní hledání pro hledání prostředků a označování pro správu prostředků. Abychom konsolidovali a zjednodušili pracovní postup monitorování a správy, začali jsme do portálu Azure přidávat možnosti portálu OMS. Všechny funkce portálu OMS jsou teď součástí portálu Azure. Ve skutečnosti některé nové funkce, jako je Analýza provozu, jsou dostupné jenom na webu Azure Portal. Se všemi možnostmi, které jste dělali na portálu OMS, budete moct dosáhnout všeho, co jste dělali na portálu OMS, s portálem Azure a dalšími. Pokud jste tak ještě neučinili, měli byste začít používat portál Azure ještě dnes!

## <a name="what-is-changing"></a>Co se mění? 
Následující změny jsou oznamovány s vyřazením portálu OMS. Každá z těchto změn je podrobněji popsána v následujících částech.

- Nové pracovní [prostory](#new-workspaces) můžete vytvořit pouze na webu Azure Portal.
- Nové prostředí pro správu výstrah [nahrazuje řešení správy výstrah](#changes-to-alerts).
- [Správa přístupu uživatelů](#user-access-and-role-migration) se teď provádí na webu Azure Portal pomocí řízení přístupu založeného na rolích Azure.
- [Konektor Application Insights již není vyžadován,](#application-insights-connector-and-solution) protože stejná funkce je povolena prostřednictvím dotazů mezi pracovními prostory.
- [Mobilní aplikace OMS](#oms-mobile-app) se zastaralá. 
- [Řešení NSG je nahrazováno](#azure-network-security-group-analytics) rozšířenými funkcemi dostupnými prostřednictvím řešení Traffic Analytics.
- Nová připojení od nástroje System Center Operations Manager k log analytics vyžadují [aktualizované sady Management Pack](#system-center-operations-manager).
- Podrobnosti o změnách [ve správě aktualizací](../../automation/automation-update-management.md)najdete v tématu [Migrace nasazení aktualizací OMS do Azure.](../../automation/migrate-oms-update-deployments.md)


## <a name="what-should-i-do-now"></a>Co mám dělat teď?
Zatímco většina funkcí bude nadále fungovat bez provedení migrace, je třeba provést následující úkoly:

- Musíte [migrovat uživatelská oprávnění](#user-access-and-role-migration) na portál Azure.
- Podrobnosti o přechodu řešení správy aktualizací najdete v tématu [Migrace nasazení aktualizací OMS do Azure.](../../automation/migrate-oms-update-deployments.md)

Informace o přechodu na portál Azure na portál Azure pro analýzu protokolů najdete v tématu Běžné otázky pro přechod z [portálu OMS na portál Azure pro službu Log Analytics.](oms-portal-faq.md) 

## <a name="user-access-and-role-migration"></a>Migrace přístupu uživatelů a role
Správa přístupu k portálu Azure portal je bohatší a výkonnější než správa přístupu na portálu OMS Portal. Podrobnosti o správě přístupu v Log Analytics najdete v [tématu Návrh pracovního prostoru protokolů Azure Monitor.](design-logs-deployment.md)

> [!NOTE]
> Předchozí verze tohoto článku uvádí, že oprávnění by se automaticky převést z portálu OMS na portál Azure. Tento automatický převod již není plánován a převod je nutné provést sami.

Už teď máte na webu Azure Portal odpovídající přístup, v takovém případě nemusíte provádět žádné změny. Existuje několik případů, kdy pravděpodobně nemáte odpovídající přístup, v takovém případě vám správce musí přiřadit oprávnění.

- Na portálu OMS máte oprávnění pouze pro čtení, ale na webu Azure Portal nemáte žádná oprávnění. 
- Máte oprávnění přispěvatele na portálu OMS, ale jenom přístup ke čtečce na webu Azure Portal.
 
V obou těchto případech musí správce ručně přiřadit příslušnou roli z následující tabulky. Doporučujeme přiřadit tuto roli na úrovni skupiny prostředků nebo předplatného.  Pro oba tyto případy budou v brzké době poskytnuty více normativních pokynů.

| Oprávnění portálu OMS | Azure Role |
|:---|:---|
| ReadOnly | Čtenář Log Analytics |
| Přispěvatel | Přispěvatel Log Analytics |
| Správce | Vlastník | 
 

## <a name="new-workspaces"></a>Nové pracovní prostory
Pomocí portálu OMS již nebudete moci vytvářet nové pracovní prostory. Postupujte podle pokynů v [části Vytvoření pracovního prostoru Analýzy protokolů](../learn/quick-create-workspace.md) na webu Azure Portal a vytvořte nový pracovní prostor na webu Azure Portal.

## <a name="changes-to-alerts"></a>Změny výstrah

### <a name="alert-extension"></a>Rozšíření výstrah  

Výstrahy byly [rozšířeny do portálu Azure](alerts-extend.md) Existující výstrahy budou nadále uvedené na portálu OMS, ale můžete je spravovat jenom na webu Azure Portal. Pokud přistupujete k výstrahám programově pomocí rozhraní API výstrahy log Analytics REST API nebo šablony prostředku výstrahy log analytics, budete muset použít skupiny akcí namísto akcí ve vašich voláních rozhraní API, šablonách Azure Resource Manageru a příkazech PowerShellu.

### <a name="alert-management-solution"></a>Řešení správy výstrah
Jako změna oproti předchozímu oznámení bude [řešení pro správu výstrah](alert-management-solution.md) nadále dostupné a plně podporované na webu Azure Portal. Můžete pokračovat v instalaci řešení z Azure Marketplace.

Zatímco řešení pro správu výstrah je stále k dispozici, doporučujeme vám používat [jednotné rozhraní pro výstrahy Azure Monitoru](alerts-overview.md) k vizualizaci a správě všech výstrah v Azure. Toto nové prostředí nativně agreguje výstrahy z více zdrojů v rámci Azure, včetně výstrah protokolu z Log Analytics. Pokud používáte jednotné rozhraní pro výstrahy Azure Monitor, pak je řešení pro správu výstrah nutné jenom k povolení integrace výstrah z Nástroje pro provoz systémového centra do Azure. V jednotném rozhraní pro upozorňování Azure Monitor uvidíte distribuce vašich výstrah, můžete využít výhod automatického seskupování souvisejících výstrah prostřednictvím inteligentních skupin a zobrazit výstrahy napříč několika předplatnými při použití rozšířených filtrů. Budoucí pokroky v řízení výstrah budou primárně k dispozici z této nové zkušenosti. 

Data shromážděná řešením pro správu výstrah (záznamy s typem výstrahy) jsou i nadále v Log Analytics, dokud je řešení nainstalováno pro pracovní prostor. 

## <a name="oms-mobile-app"></a>Mobilní aplikace OMS
Mobilní aplikace OMS bude sunsetted spolu s portálem OMS. Místo mobilní aplikace OMS, abyste měli přístup k informacím o vaší IT infrastruktuře, řídicích panelech a uložených dotazech, můžete k portálu Azure přistupovat přímo z vašeho prohlížeče v mobilním zařízení. Chcete-li dostávat upozornění, měli byste nakonfigurovat [skupiny akcí Azure](action-groups.md) tak, aby přijímaly oznámení ve formě SMS nebo hlasového hovoru.

## <a name="application-insights-connector-and-solution"></a>Konektor a řešení Application Insights
[Application Insights Connector](app-insights-connector.md) poskytuje způsob, jak zahrnout data Application Insights do pracovního prostoru Analýzy protokolů. Tato duplikace dat byla nutná k umožnění viditelnosti napříč daty infrastruktury a aplikací. S rozšířenou podporou uchovávání dat application insights v březnu 2019 a možnost provádět [dotazy mezi prostředky](../log-query/cross-workspace-query.md) kromě možnosti zobrazit více prostředků Azure Monitor Application [Insights](../log-query/unify-app-resource-data.md), není nutné duplikovat data z prostředků Application Insights a odesílat je do Log Analytics. Konektor navíc odešle podmnožinu vlastností aplikací do Log Analytics, zatímco dotazy mezi prostředky poskytuje zvýšenou flexibilitu.  

Jako takový Application Insights Connector byl zastaralé a odebrány z Azure Marketplace spolu s oms portálu vyřazení března 30, 2019. Stávající připojení budou fungovat až do 30.6.2019. S oms portálu vyřazení neexistuje žádný způsob, jak nakonfigurovat a odebrat existující připojení z portálu. To bude podporováno pomocí rozhraní REST API, které bude k dispozici v lednu 2019 a oznámení bude zveřejněno na [aktualizacích Azure](https://azure.microsoft.com/updates/). 

## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
Řešení [Azure Network Security Group Analytics](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) bude nahrazeno nedávno spuštěnou [analýzou provozu,](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) která poskytuje přehled o aktivitách uživatelů a aplikací v cloudových sítích. Traffic Analytics vám pomůže auditovat síťovou aktivitu vaší organizace, zabezpečit aplikace a data, optimalizovat výkon pracovního vytížení a zůstat v souladu s předpisy. 

Toto řešení analyzuje protokoly toku nsg a poskytuje přehled o následujících.

- Přenosy mezi vašimi sítěmi mezi Azure a Internetem, oblastmi veřejného cloudu, virtuálními sítěmi a podsítěmi.
- Aplikace a protokoly v síti bez nutnosti sledování nebo vyhrazených zařízení pro sběr toků.
- Nejlepší talkers, upovídaný aplikace, Virtuální konverzace v cloudu, dopravní hotspoty.
- Zdroje a cíle provozu napříč virtuálními sítěmi, vzájemné vztahy mezi kritickými obchodními službami a aplikacemi.
- Zabezpečení včetně škodlivého provozu, portů otevřených pro Internet, aplikací nebo virtuálních měn, které se pokoušejí o přístup k Internetu.
- Využití kapacity, které vám pomůže odstranit problémy nadměrného zřizování nebo nedostatečnévyužití.

Můžete se nadále spoléhat na nastavení diagnostiky k odeslání protokolů nsg do log Analytics, aby vaše stávající uložená hledání, výstrahy, řídicí panely budou i nadále fungovat. Zákazníci, kteří již řešení nainstalovali, jej mohou používat až do odvolání. září bude řešení Network Security Group Analytics odebráno z trhu a zpřístupněno prostřednictvím komunity jako [šablona Azure QuickStart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Pokud jste [svou skupinu pro správu nástroje Operations Manager připojili k nástroji Log Analytics](om-agents.md), bude i nadále fungovat bez evidenčních změn. U nových připojení však musíte ke [konfiguraci sady Operations Management Suite postupovat](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/)podle pokynů v sadě Microsoft System Center Operations Manager Management Pack.

## <a name="next-steps"></a>Další kroky
- Informace o přechodu z portálu [OMS na portál Azure pro službu Log Analytics](oms-portal-faq.md) najdete v tématu Běžné otázky pro přechod z portálu OMS na portál Azure.
