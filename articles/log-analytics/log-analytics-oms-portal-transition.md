---
title: Portál OMS do Azure | Dokumentace Microsoftu
description: Na portálu OMS se ještě sunsetted se všemi funkcemi přesouvá na portál Azure portal. Tento článek poskytuje podrobné informace o tomto přechodu.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: e24ad270996994a4061bbe7d71d79c7362e52406
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091218"
---
# <a name="oms-portal-moving-to-azure"></a>Portál OMS do Azure

> [!NOTE]
> Tento článek se týká veřejného cloudu Azure i v cloudu pro státní správu není uvedeno jinak.

Na webu Azure portal je Centrum pro všechny služby Azure a nabízí bohaté možnosti správy prostředí s funkcemi, jako jsou řídicí panely pro Připnutí prostředků, inteligentní vyhledávání, vyhledání prostředků a označí pro správu prostředků. Ke konsolidaci a zjednodušte pracovní postupy monitorování a správu, bodu jsme začali přidávat možnosti portálu OMS na web Azure Portal. Všechny funkce na portálu OMS jsou teď součástí na webu Azure portal. Ve skutečnosti některé nové funkce, jako je například analýza provozu jsou k dispozici pouze na webu Azure Portal. Budete moct provádět vše, co jste dělali v portálu OMS pomocí webu Azure portal a dalších. Pokud jste tak již neučinili, měli byste začít pomocí webu Azure portal ještě dnes!

**Na portálu OMS se oficiálně dostupné jenom do 15. ledna 2019.** Jsme nadšeni, přejděte na webu Azure portal a očekávají, že přechod na snadno. Ale chápeme změny je složité a můžou působit rušivě. Odesílat dotazy, názory a připomínky k **LAUpgradeFeedback@microsoft.com**. Zbývající část tohoto článku prochází přes klíčové scénáře a plán pro tento přechod.

## <a name="what-is-changing"></a>Co se mění? 
Tyto změny jsou právě oznámili vyřazení podpory na portálu OMS. Každá z těchto změn je popsána podrobněji v následujících částech.

- Můžete vytvořit nová [pouze pracovní prostory](#new-workspaces) na webu Azure Portal.
- Nové prostředí pro správu výstrah [nahradí řešení pro správu upozornění](#changes-to-alerts).
- [Správa přístupu uživatelů](#user-access-and-role-migration) teď provádí na webu Azure Portal pomocí řízení přístupu na základě rolí Azure.
- [Application Insights Connector se už nevyžaduje](#application-insights-connector-and-solution) od stejné funkce je povolená díky dotazy napříč pracovního prostoru.
- [Mobilní aplikaci OMS](#oms-mobile-app) je zastaralé. 
- [Řešení NSG se nahrazuje](#azure-network-security-group-analytics) s vylepšené funkce, které jsou k dispozici prostřednictvím řešení pro analýzu provozu.
- Nové připojení z nástroje System Center Operations Manager ke službě Log Analytics vyžadují [aktualizovat sady management Pack](#system-center-operations-manager).
- V tématu [proveďte migraci vašich nasazení aktualizace OMS do Azure](../automation/migrate-oms-update-deployments.md) pro podrobnosti o změnách [Update Management](../automation/automation-update-management.md).


## <a name="what-should-i-do-now"></a>Co mám teď?
Zatímco většina funkcí budou nadále fungovat bez provádění jakékoli migrace, musíte provést následující úkoly:

- Je potřeba [migrovat vaše uživatelská oprávnění](#user-access-and-role-migration) k webu Azure portal.
- Zobrazit [proveďte migraci vašich nasazení aktualizace OMS do Azure](../automation/migrate-oms-update-deployments.md) podrobnosti týkající se přechodu řešení Update Management.

Odkazovat na [běžné otázky týkající se přechodu z portálu OMS na webu Azure portal pro uživatele Log Analytics](../log-analytics/log-analytics-oms-portal-faq.md) informace o tom, jak přechod na webu Azure portal. Odeslat žádné zpětnou vazbu, otázky nebo připomínky k **LAUpgradeFeedback@microsoft.com**.

## <a name="user-access-and-role-migration"></a>Migrace přístup a role uživatele
Správa přístupu na portálu Azure je bohatší a výkonnější než správu přístupu na portálu OMS. Zobrazit [Správa pracovních prostorů](log-analytics-manage-access.md#manage-accounts-and-users) podrobné řízení přístupu ve službě Log Analytics.

> [!NOTE]
> Předchozí verze tohoto článku je uvedeno, že oprávnění by automaticky převést z portálu OMS na webu Azure portal. Tato automatická konverzace je již plánované a je třeba provést převod, sami.

Možná už máte správný přístup na webu Azure Portal v takovém případě nemusíte nic měnit. Existuje několik případů, kdy nemusí mít odpovídající přístup v takovém případě správce musíte přiřadit jste oprávnění.

- Máte oprávnění jen pro čtení na portálu OMS, ale žádná oprávnění na webu Azure Portal. 
- Máte oprávnění přispěvatele na portálu OMS, ale čtečky přístup jenom na webu Azure Portal.
 
V obou těchto případech musí správce ručně přiřadit vám příslušné role z následující tabulky. Doporučujeme, abyste přiřadili této role na úrovni skupiny nebo předplatného resource.  Podrobnější pokyny bude poskytována krátce obou těchto případech.

| Oprávnění na portálu OMS | Azure Role |
|:---|:---|
| ReadOnly | Čtenář Log Analytics |
| Přispěvatel | Přispěvatel Log Analytics |
| Správce | Vlastník | 
 

## <a name="new-workspaces"></a>Nové pracovní prostory
Jsou už nebude možné vytvořit nové pracovní prostory na portálu OMS. Postupujte podle pokynů v [vytvořit pracovní prostor Log Analytics na portálu Azure portal](log-analytics-quick-create-workspace.md) vytvořte nový pracovní prostor na webu Azure Portal.

## <a name="changes-to-alerts"></a>Změny výstrah

### <a name="alert-extension"></a>Oznámení rozšíření  

> [!NOTE]
> Výstrahy teď plně rozšířit do portálu Azure pro veřejný cloud. Stávající pravidla upozornění můžete zobrazit na portálu OMS, ale může být pouze spravované na portálu Azure portal. Rozšíření upozornění na web Azure Portal pro Azure government cloud začne v října 2018.

Byl [rozšíří do portálu Azure portal](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Po jejím dokončení akce správy na výstrahy budou k dispozici pouze na webu Azure portal. Existující výstrahy bude dál zobrazovat na portálu OMS. Pokud k upozorněním přistupujete programově s využitím Log Analytics výstrah REST API nebo šablonu oznámení prostředku Log Analytics, budete muset použít skupiny akcí místo akcí ve volání rozhraní API, šablon Azure Resource Manageru a příkazech Powershellu.

### <a name="alert-management-solution"></a>Řešení pro správu výstrah
Místo [řešení alert managementu](log-analytics-solution-alert-management.md), můžete použít [unified Azure Monitor výstrah rozhraní](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) vizualizovat a spravovat upozornění. Toto nové prostředí agreguje výstrahy z více zdrojů v rámci Azure včetně upozornění protokolů ze služby Log Analytics. Můžete naleznete v tématu distribuce upozornění, využít výhod automatizovaného seskupení souvisejících výstrah pomocí inteligentní skupin a zobrazit výstrahy napříč několika předplatnými při použití bohaté filtrů. Všechny tyto funkce jsou dostupné ve verzi preview od 4. června 2018. Správa výstrah řešení nebude k dispozici na webu Azure Portal. 

Údaje shromážděné tímto řešením Správa výstrah (záznamů s typem upozornění) i nadále v Log Analytics, dokud je řešení nainstalováno pro pracovní prostor. Od srpna 2018 se datové proudy upozornění neupozorňovala sjednocené do pracovních prostorů se povolí, nahraďte tuto funkci. Některé změny schématu se očekává, že a oznámíme později.

## <a name="oms-mobile-app"></a>Mobilní aplikaci OMS
Mobilní aplikaci OMS bude sunsetted spolu se na portálu OMS. Místo mobilní aplikaci OMS přístup k informacím o vašich IT infrastruktury, řídicí panely a uložené dotazy, můžete přistupovat na webu Azure portal přímo z prohlížeče v mobilním zařízení. Abyste mohli dostávat upozornění, měli byste nakonfigurovat [skupiny akcí Azure](../monitoring-and-diagnostics/monitoring-action-groups.md) pro příjem oznámení ve formě SMS nebo hlasový hovor

## <a name="application-insights-connector-and-solution"></a>Application Insights Connector a řešení
[Application Insights Connector](log-analytics-app-insights-connector.md) poskytuje způsob, jak přenést data Application Insights do pracovního prostoru Log Analytics. Tato duplikace dat nebyla nutná k zajištění viditelnost napříč dat infrastruktury a aplikací.

Díky podpoře [dotazy napříč prostředky](log-analytics-cross-workspace-search.md), už není potřebě duplicitní data. V důsledku toho se přestanou existující řešení Application Insights. Od října se nebudou moct propojit nové prostředky Application Insights do pracovních prostorů Log Analytics. Existující odkazy a řídicí panely, budou nadále fungovat až do 15. ledna 2019.


## <a name="azure-network-security-group-analytics"></a>Analýzy skupin zabezpečení sítě Azure
[Řešení analýzy skupin zabezpečení sítě Azure](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) nahradí s nedávno uvedená [analýzu provozu](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/) který poskytuje přehled o aktivitě uživatelů a aplikací v cloudových sítích. Analýza provozu umožňuje auditovat aktivitu sítě vaší organizace, zabezpečených aplikací a dat, optimalizovat výkon úloh a zajistěte dodržování předpisů. 

Toto řešení analyzuje protokoly toků NSG a poskytuje přehledy o následující.

- Provoz probíhá mezi vaší sítí mezi Azure a Internet, veřejných cloudových oblastech, virtuálních sítí a podsítí.
- Aplikace a protokoly ve vaší síti bez potřeby vyhrazených tok kolekce zařízení nebo zjištění je snazší.
- Hlavní vyjádření, přetížení aplikací, konverzace virtuálního počítače v cloudu, hotspotům provoz.
- Zdroje a cíle provozu mezi virtuálními sítěmi, vzájemných vztahů mezi nejdůležitější obchodní služby a aplikace.
- Zabezpečení, včetně škodlivý provoz, porty otevřené pro Internet, aplikace nebo virtuální počítače pokusu o přístup k Internetu.
- Využití kapacity, která pomáhá eliminovat problémy průběhu zřizování nebo nízkého využití.

Můžete nadále závisí na nastavení diagnostiky pro odesílání protokolů skupiny zabezpečení sítě ke službě Log Analytics, takže stávající uložit hledání, upozornění, řídicí panely se budou nadále fungovat. Zákazníci, kteří už jste nainstalovali řešení můžete ho nadále používat až do dalšího upozornění. Od 5. září se řešení analýzy skupin zabezpečení sítě budou odebrány z webu marketplace a k dispozici prostřednictvím komunity jako [šablonu pro rychlý start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Pokud jste [připojené skupině pro správu Operations Manageru k Log Analytics](log-analytics-om-agents.md), bude dál fungovat beze změny. Pro nová připojení, je nutné postupovat podle pokynů v [Microsoft System Center Operations Manager Management Pack nakonfigurovat Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>Další postup
- Zobrazit [běžné otázky týkající se přechodu z portálu OMS na webu Azure portal pro uživatele Log Analytics](log-analytics-oms-portal-faq.md) pro doprovodné materiály k přesunutí z portálu OMS na webu Azure portal.