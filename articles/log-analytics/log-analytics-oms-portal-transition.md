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
ms.date: 07/27/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 1a8ccc818cafac4867cb533c83f297af61a21836
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39309098"
---
# <a name="oms-portal-moving-to-azure"></a>Portál OMS do Azure
Jedna složka zpětné vazby je opakovaně naslouchali zákazníkům Log Analytics je potřeba jednotné uživatelské prostředí můžete sledovat a spravovat místní a úloh v Azure. Pravděpodobně znáte na webu Azure portal je Centrum pro všechny služby Azure a nabízí bohaté možnosti správy prostředí s funkcemi, jako jsou řídicí panely pro Připnutí prostředků, inteligentní vyhledávání, vyhledání prostředků a označí pro správu prostředků. Ke konsolidaci a zjednodušte pracovní postupy monitorování a správu, bodu jsme začali přidávat možnosti portálu OMS na web Azure Portal. Je nám potěšením oznamujeme, že většinu funkcí na portálu OMS jsou teď součástí sady na webu Azure portal. Ve skutečnosti některé nové funkce, jako je například analýza provozu jsou k dispozici pouze na webu Azure Portal. Existuje pouze několik mezer zbývající včetně několik řešení, které jsou stále v procesu přesunout na webu Azure portal. Pokud tyto funkce nepoužíváte, budete moct provádět vše, co jste dělali v portálu OMS pomocí webu Azure portal a dalších. Pokud jste tak již neučinili, doporučujeme, abyste že mohli začít používat na webu Azure portal ještě dnes! 

Očekáváme, že chcete zavřít zbývající mezery mezi dva portály. srpna 2018. Na základě zpětné vazby od zákazníků, oznámíme časovou osu pro sunsetting na portálu OMS. Jsme nadšeni, přejděte na webu Azure portal a očekávají, že přechod na snadno. Ale chápeme změny je složité a můžou působit rušivě. Odesílat dotazy, názory a připomínky k **LAUpgradeFeedback@microsoft.com**. Zbývající část tohoto článku prochází přes klíčových scénářů, aktuální mezery a plán pro tento přechod. 

## <a name="progress"></a>Průběh
Toto jsou aktualizace, které byly dokončeny od předchozí verze tohoto článku.

### <a name="july-27"></a>27. července

- [DNS Analytics](log-analytics-dns.md) je teď plně funkční i na webu Azure Portal.
- [Správa aktualizací](../automation/automation-update-management.md) je aktualizovaná tak nebudou plně použitelné na webu Azure Portal. Zobrazit [proveďte migraci vašich nasazení aktualizace OMS do Azure](../automation/migrate-oms-update-deployments.md) podrobnosti.
- [Výstrahy](#changes-to-alerts) mají nyní plně rozšíří do portálu Azure portal.
- [Vlastní protokoly ve verzi preview funkce](log-analytics-data-sources-custom-logs.md) teď automaticky zapnutá pro všechny pracovní prostory.

## <a name="what-will-change"></a>Co se změní? 
Tyto změny jsou právě oznámili vyřazení podpory na portálu OMS. Každá z těchto změn je popsána podrobněji v následujících částech.

- Budete moct vytvořit nové pracovní prostory pouze na webu Azure portal.
- Nové prostředí pro správu výstrah nahradí řešení pro správu výstrahu.
- Správa přístupu uživatelů se provede na webu Azure Portal pomocí řízení přístupu na základě rolí Azure.
- Application Insights Connector se už nevyžaduje, protože stejné funkce můžete povolit prostřednictvím dotazů mezi pracovního prostoru.
- Mobilní aplikaci OMS se přestanou používat. 
- Skupina zabezpečení sítě řešení je nahrazen vylepšené funkce, které jsou k dispozici prostřednictvím řešení pro analýzu provozu.
- Nová připojení ze System Center Operations Manageru k Log Analytics vyžadovat aktualizované sady management Pack.


## <a name="current-known-gaps"></a>Aktuální známé mezery 
Momentálně nejsou některé funkce mezery, které vyžadují, abyste dál používat portál OMS. Tyto mezery se zavírá a odpovídajícím způsobem aktualizuje tento dokument. Také by měla odkazovat na [aktualizace Azure](https://azure.microsoft.com/updates/?product=log-analytics) pro oznámení probíhajících změn a rozšíření.

- Následující řešení nejsou plně funkční na webu Azure Portal. Měli byste pokračovat používat tato řešení na portálu classic, dokud se aktualizace.

    - Řešení pro analýzu Windows ([Upgrade Readiness](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started), [stav zařízení](https://docs.microsoft.com/windows/deployment/update/device-health-monitor), a [kompatibility aktualizací](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started))
    - [Surface Hub](log-analytics-surface-hubs.md)

-  Pro přístup k prostředku Log Analytics v Azure, uživatel musí mít udělen přístup prostřednictvím [přístupu na základě rolí Azure](#user-access-and-role-migration).


## <a name="what-should-i-do-now"></a>Co mám teď?  
By měla odkazovat na [běžné otázky týkající se přechodu z portálu OMS na webu Azure portal pro uživatele Log Analytics](../log-analytics/log-analytics-oms-portal-faq.md) informace o tom, jak přechod na webu Azure portal. Pokud [výše popsané mezery](#current-known-gaps) se nevztahují na vaše prostředí, měli byste uvažovat o spuštění pomocí webu Azure portal jako primární prostředí. Odeslat žádné zpětnou vazbu, otázky nebo připomínky k LAUpgradeFeedback@microsoft.com.

## <a name="new-workspaces"></a>Nové pracovní prostory
Od 29. července, už budete moct vytvořit nové pracovní prostory na portálu OMS. Postupujte podle pokynů v [vytvořit pracovní prostor Log Analytics na portálu Azure portal](log-analytics-quick-create-workspace.md) vytvořte nový pracovní prostor na webu Azure Portal.

## <a name="changes-to-alerts"></a>Změny výstrah 

### <a name="alert-extension"></a>Oznámení rozšíření  

> [!NOTE]
> Výstrahy teď plně rozšířit na web Azure Portal. Stávající pravidla upozornění můžete zobrazit na portálu OMS, ale může být pouze spravované na portálu Azure portal.

Výstrahy jsou právě probíhá [rozšíří do portálu Azure portal](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Po jejím dokončení akce správy na výstrahy budou k dispozici pouze na webu Azure portal. Existující výstrahy bude dál zobrazovat na portálu OMS. Pokud k upozorněním přistupujete programově s využitím Log Analytics výstrah REST API nebo šablonu oznámení prostředku Log Analytics, budete muset použít skupiny akcí místo akcí ve volání rozhraní API, šablon Azure Resource Manageru a příkazech Powershellu.

### <a name="alert-management-solution"></a>Řešení pro správu výstrah
Místo [řešení alert managementu](log-analytics-solution-alert-management.md), můžete použít [unified Azure Monitor výstrah rozhraní](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) vizualizovat a spravovat upozornění. Toto nové prostředí agreguje výstrahy z více zdrojů v rámci Azure včetně upozornění protokolů ze služby Log Analytics. Můžete naleznete v tématu distribuce upozornění, využít výhod automatizovaného seskupení souvisejících výstrah pomocí inteligentní skupin a zobrazit výstrahy napříč několika předplatnými při použití bohaté filtrů. Všechny tyto funkce jsou dostupné ve verzi preview od 4. června 2018. Správa výstrah řešení nebude k dispozici na webu Azure Portal. 

Údaje shromážděné tímto řešením Správa výstrah (záznamů s typem upozornění) i nadále v Log Analytics, dokud je řešení nainstalováno pro pracovní prostor. Od srpna 2018 se datové proudy upozornění neupozorňovala sjednocené do pracovních prostorů se povolí, nahraďte tuto funkci. Některé změny schématu se očekává, že a oznámíme později.

## <a name="user-access-and-role-migration"></a>Migrace přístup a role uživatele
Řízení přístupu na portálu Azure je bohatší a výkonnější než správu přístupu na portálu OMS, ale vyžaduje nějaké převody. Zobrazit [Správa pracovních prostorů](log-analytics-manage-access.md#manage-accounts-and-users) podrobné řízení přístupu ve službě Log Analytics.

Od července 30, automatického převodu oprávnění pro řízení přístupu z OMS se spustí portál Azure portal oprávnění. Po dokončení převodu bude v části o správě uživatelů portálu OMS směrovat uživatele do řízení přístupu (IAM) v Azure. 

Při převodu bude systém kontrolovat každý uživatel nebo skupina zabezpečení, který má oprávnění na portálu OMS a určení, zda má stejnou úroveň nebo oprávnění v Azure. Pokud chybí oprávnění, přiřadí následujících rolí pro příslušné pracovní prostory a řešení.

| Oprávnění na portálu OMS | Azure Role |
|:---|:---|
| ReadOnly | Čtenář Log Analytics |
| Přispěvatel | Přispěvatel Log Analytics |
| Správce | Vlastník |

Pokud chcete mít jistotu, že žádné nadměrná oprávnění se přiřazují uživatelům, systém nebude automaticky toto přiřazení oprávnění na úrovni skupiny prostředků. Správci pracovního prostoru musí v důsledku toho ručně přiřadit sami _vlastníka_ nebo _Přispěvatel_ role na úrovni skupiny nebo předplatného resource provádět následující akce.

- Přidat nebo odebrat řešení
- Definujte nové vlastní zobrazení
- Správa výstrah 

V některých případech automatický převod nemůže použít oprávnění a vyzve správce, chcete-li ručně přiřadit oprávnění.

## <a name="oms-mobile-app"></a>Mobilní aplikaci OMS
Mobilní aplikaci OMS bude sunsetted spolu se na portálu OMS. Místo mobilní aplikaci OMS přístup k informacím o vašich IT infrastruktury, řídicí panely a uložené dotazy, můžete přistupovat na webu Azure portal přímo z prohlížeče v mobilním zařízení. Abyste mohli dostávat upozornění, měli byste nakonfigurovat [skupiny akcí Azure](../monitoring-and-diagnostics/monitoring-action-groups.md) pro příjem oznámení ve formě SMS nebo hlasový hovor

## <a name="application-insights-connector-and-solution"></a>Application Insights Connector a řešení
[Application Insights Connector](log-analytics-app-insights-connector.md) poskytuje způsob, jak přenést data Application Insights do pracovního prostoru Log Analytics. Tato duplikace dat nebyla nutná k zajištění viditelnost napříč dat infrastruktury a aplikací.

Díky podpoře [dotazy napříč prostředky](log-analytics-cross-workspace-search.md), už není potřebě duplicitní data. V důsledku toho se přestanou existující řešení Application Insights. Od července se nebudou moct propojit nové prostředky Application Insights do pracovních prostorů Log Analytics. Existující odkazy a řídicí panely, budou nadále fungovat až do listopadu 2018.


## <a name="azure-network-security-group-analytics"></a>Analýzy skupin zabezpečení sítě Azure
[Řešení analýzy skupin zabezpečení sítě Azure](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) nahradí s nedávno uvedená [analýzu provozu](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/) který poskytuje přehled o aktivitě uživatelů a aplikací v cloudových sítích. Analýza provozu umožňuje auditovat aktivitu sítě vaší organizace, zabezpečených aplikací a dat, optimalizovat výkon úloh a zajistěte dodržování předpisů. 

Toto řešení analyzuje protokoly toků NSG a poskytuje přehledy o následující.

- Provoz probíhá mezi vaší sítí mezi Azure a Internet, veřejných cloudových oblastech, virtuálních sítí a podsítí.
- Aplikace a protokoly ve vaší síti bez potřeby vyhrazených tok kolekce zařízení nebo zjištění je snazší.
- Hlavní vyjádření, přetížení aplikací, konverzace virtuálního počítače v cloudu, hotspotům provoz.
- Zdroje a cíle provozu mezi virtuálními sítěmi, vzájemných vztahů mezi nejdůležitější obchodní služby a aplikace.
- Zabezpečení, včetně škodlivý provoz, porty otevřené pro Internet, aplikace nebo virtuální počítače pokusu o přístup k Internetu.
- Využití kapacity, která pomáhá eliminovat problémy průběhu zřizování nebo nízkého využití.

Můžete nadále závisí na nastavení diagnostiky pro odesílání protokolů skupiny zabezpečení sítě ke službě Log Analytics, takže stávající uložit hledání, upozornění, řídicí panely se budou nadále fungovat. Zákazníci, kteří už jste nainstalovali řešení můžete ho nadále používat až do dalšího upozornění. Od 15. srpna se řešení analýzy skupin zabezpečení sítě budou odebrány z webu marketplace a k dispozici prostřednictvím komunity jako [šablonu pro rychlý start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Pokud jste [připojené skupině pro správu Operations Manageru k Log Analytics](log-analytics-om-agents.md), bude dál fungovat beze změny. Pro nová připojení, je nutné postupovat podle pokynů v [Microsoft System Center Operations Manager Management Pack nakonfigurovat Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>Další postup
- Zobrazit [běžné otázky týkající se přechodu z portálu OMS na webu Azure portal pro uživatele Log Analytics](log-analytics-oms-portal-faq.md) pro doprovodné materiály k přesunutí z portálu OMS na webu Azure portal.