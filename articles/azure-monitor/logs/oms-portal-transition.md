---
title: Přesun portálu OMS do Azure | Microsoft Docs
description: Portál OMS se sunsetted se všemi funkcemi, které se přesunou do Azure Portal. Tento článek poskytuje podrobné informace o tomto přechodu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 0fc57b87e5ec1d7f47d9f9d74698af56172246ec
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610804"
---
# <a name="oms-portal-moving-to-azure"></a>Přesun portálu OMS do Azure

> [!NOTE]
> Tento článek se týká cloudového i veřejného cloudu Azure, s výjimkou případů, kdy je uvedeno jinak.

**Portál OMS pro veřejný cloud Azure byl oficiálně vyřazený. Portál OMS pro státní správu Azure USA byl oficiálně vyřazený z 15. května 2019.** Jsme rádi, že se přesunete do Azure Portal a očekáváme, že přechod bude snadný. Chápeme ale, že změny jsou obtížné a můžou být rušivé. Zbývající část tohoto článku se nachází v rámci klíčových scénářů a plánu pro tento přechod.

Azure Portal je centrum pro všechny služby Azure a nabízí bohatší prostředí pro správu s funkcemi, jako jsou řídicí panely pro připnutí prostředků, inteligentní hledání pro hledání prostředků a označování pro správu prostředků. Pokud chcete konsolidovat a zefektivnit pracovní postup monitorování a správy, začali jsme do Azure Portal přidávat možnosti portálu OMS. Všechny funkce portálu OMS jsou teď součástí Azure Portal. Některé nové funkce, například Analýza provozu, jsou k dispozici pouze v Azure Portal. Všechno, co jste na portálu OMS prováděli, budete moct provádět s Azure Portal a dalšími. Pokud jste to ještě neudělali, měli byste začít používat Azure Portal ještě dnes!

## <a name="what-is-changing"></a>Co se mění? 
Následující změny jsou oznámeny vyřazením portálu OMS. Každá z těchto změn je podrobněji popsána v níže uvedených částech.

- Nové [pracovní prostory](#new-workspaces) můžete vytvořit pouze v Azure Portal.
- Nové prostředí pro správu výstrah [nahrazuje Alert Management řešení](#changes-to-alerts).
- [Správa přístupu uživatele](#user-access-and-role-migration) se teď provádí v Azure Portal pomocí řízení přístupu na základě role v Azure.
- [Application Insights Connector už není potřeba](#application-insights-connector-and-solution) , protože stejné funkce jsou povolené prostřednictvím dotazů napříč pracovními prostory.
- [Mobilní aplikace OMS](#oms-mobile-app) je zastaralá. 
- [Řešení NSG se nahrazuje](#azure-network-security-group-analytics) vylepšenými funkcemi dostupnými prostřednictvím řešení Analýza provozu.
- Nová připojení z System Center Operations Manager pro Log Analytics vyžadují [aktualizované sady Management Pack](#system-center-operations-manager).
- Podrobnosti o změnách [Update Management](../../automation/update-management/overview.md)najdete v tématu [migrace nasazení aktualizace OMS do Azure](../../automation/migrate-oms-update-deployments.md) .


## <a name="what-should-i-do-now"></a>Co mám dělat?
I když většina funkcí bude i nadále fungovat bez provedení migrace, musíte provést následující úlohy:

- Musíte [migrovat vaše uživatelská oprávnění](#user-access-and-role-migration) do Azure Portal.
- Podrobnosti o přechodu Update Management řešení najdete v tématu [migrace nasazení aktualizace OMS do Azure](../../automation/migrate-oms-update-deployments.md) .

Přečtěte si [Nejčastější dotazy k přechodu z portálu OMS na Azure Portal pro Log Analytics uživatele](../overview.md) , kde najdete informace o tom, jak přejít na Azure Portal. 

## <a name="user-access-and-role-migration"></a>Přístup uživatelů a migrace rolí
Správa přístupu Azure Portal je bohatší a výkonnější než Správa přístupu na portálu OMS. Podrobnosti o správě přístupu v Log Analytics najdete v tématu věnovaném [navrhování Azure monitor v pracovním prostoru protokoly](../logs/design-logs-deployment.md) .

> [!NOTE]
> Předchozí verze tohoto článku uvedly, že oprávnění by se automaticky převedla z portálu OMS na Azure Portal. Tento automatický převod již není plánován a je nutné provést převod sami.

V Azure Portal už možná máte odpovídající přístup. v takovém případě nemusíte dělat žádné změny. K dispozici je několik případů, kdy nemáte odpovídající přístup v takovém případě, že vám správce musí přiřadit oprávnění.

- Na portálu OMS nemáte oprávnění uživatele jen pro čtení, ale v Azure Portal žádná oprávnění. 
- Máte oprávnění přispěvatele na portálu OMS, ale jenom přístup čtenář v Azure Portal.
 
V obou těchto případech musí správce ručně přiřadit příslušnou roli z následující tabulky. Tuto roli doporučujeme přiřadit na úrovni skupiny prostředků nebo předplatného.  V tuto chvíli budou k dispozici další doporučené doprovodné materiály pro obě tyto případy.

| Oprávnění portálu OMS | Role Azure |
|:---|:---|
| ReadOnly | Čtenář Log Analytics |
| Přispěvatel | Přispěvatel Log Analytics |
| Správce | Vlastník | 
 

## <a name="new-workspaces"></a>Nové pracovní prostory
Pomocí portálu OMS už nebudete moct vytvářet nové pracovní prostory. Podle pokynů v části [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../learn/quick-create-workspace.md) vytvořte nový pracovní prostor v Azure Portal.

## <a name="changes-to-alerts"></a>Změny výstrah

### <a name="alert-extension"></a>Rozšíření výstrahy  

Výstrahy se [rozšířily na Azure Portal](../alerts/alerts-unified-log.md) existující výstrahy budou dál uvedené na portálu OMS, ale můžete je spravovat jenom v Azure Portal. Pokud k upozorněním přistupujete programově pomocí Log Analytics výstrahy REST API nebo šablony prostředků výstrah Log Analytics, budete muset místo akcí v volání rozhraní API, šablonách Azure Resource Manager a příkazech PowerShellu používat skupiny akcí.

### <a name="alert-management-solution"></a>Řešení pro správu výstrah
V rámci změny z předchozího oznámení bude [řešení pro správu výstrah](../platform/alert-management-solution.md) dál dostupné a plně podporované v Azure Portal. Řešení můžete dál instalovat z Azure Marketplace.

I když je řešení pro správu výstrah stále k dispozici, doporučujeme vám používat [jednotné rozhraní pro upozorňování Azure monitor](../platform/alerts-overview.md) k vizualizaci a správě všech výstrah v Azure. Toto nové prostředí nativně agreguje výstrahy z více zdrojů v rámci Azure, včetně výstrah protokolu z Log Analytics. Pokud používáte jednotné rozhraní pro upozorňování Azure Monitor, řešení správy výstrah se vyžaduje jenom k tomu, aby se aktivovaly jenom integrace výstrah z nástroje System Center Operations Manager do Azure. V rámci sjednoceného rozhraní pro upozorňování Azure Monitor se můžete podívat na distribuce výstrah, využívat automatizované seskupení souvisejících výstrah prostřednictvím inteligentních skupin a zobrazovat výstrahy mezi několika předplatnými a přitom používat syté filtry. Z tohoto nového prostředí budou primárně dostupné budoucí zálohy ve správě výstrah. 

Data shromážděná řešením pro správu výstrah (záznamy s typem výstrahy) se nadále nacházejí v Log Analytics, pokud je řešení nainstalováno pro pracovní prostor. 

## <a name="oms-mobile-app"></a>Mobilní aplikace OMS
Mobilní aplikace OMS se sunsetted spolu s portálem OMS. Místo mobilní aplikace OMS pro přístup k informacím o vaší infrastruktuře IT, řídicím panelu a uložených dotazech můžete k Azure Portal přistupovat přímo z prohlížeče v mobilním zařízení. Pokud chcete dostávat upozornění, měli byste nakonfigurovat [skupiny akcí Azure](../alerts/action-groups.md) tak, aby přijímaly oznámení ve formě SMS nebo hlasového hovoru.

## <a name="application-insights-connector-and-solution"></a>Application Insights Connector a řešení
[Application Insights Connector](../logs/app-insights-connector.md) poskytuje způsob, jak zahrnout Application Insights data do log Analyticsho pracovního prostoru. Tato duplikace dat byla nutná k umožnění viditelnosti napříč daty infrastruktury a aplikací. Díky Application Insights podpoře rozšířeného uchovávání dat v březnu 2019 a možnost provádět dotazy na více [prostředků](../logs/cross-workspace-query.md) , aby bylo možné [zobrazit více prostředků Azure monitor Application Insights](../log-query/unify-app-resource-data.md), není nutné duplikovat data z prostředků Application Insights a odesílat je Log Analytics. Konektor navíc pošle podmnožinu vlastností aplikace do Log Analytics, zatímco dotazy na více prostředků poskytují lepší flexibilitu.  

V takovém případě je Application Insights Connector zastaralá a odebrána z Azure Marketplace společně s vyřazením portálu OMS do 30. března 2019. Stávající připojení budou fungovat i do 30. června 2019. V případě zastaralého portálu OMS neexistuje způsob, jak nakonfigurovat a odebrat existující připojení z portálu. Tato akce bude podporována pomocí REST API, která bude k dispozici v lednu 2019. oznámení budou odeslána na [Aktualizace Azure](https://azure.microsoft.com/updates/). 

## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
[Řešení Azure Network Security Group Analytics](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) bude nahrazeno nedávno spuštěným [Analýza provozu](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) , které poskytuje přehled o aktivitách uživatelů a aplikací v cloudových sítích. Analýza provozu vám pomůže auditovat síťové aktivity vaší organizace, zabezpečit aplikace a data, optimalizovat výkon úloh a zachovat vyhovující předpisy. 

Toto řešení analyzuje protokoly toků NSG a poskytuje přehledy o následujících položkách.

- Přenos toků mezi vašimi sítěmi mezi Azure a internetem, oblastmi veřejných cloudů, virtuální sítě a podsítěmi.
- Aplikace a protokoly ve vaší síti, aniž by to mělo být třeba pro nástroje pro sledování a zařízení pro sběr vyhrazených toků.
- Hlavní mluvčí, aplikace v konverzaci, konverzace virtuálních počítačů v cloudu, provozní hotspoty.
- Zdroje a cíle provozu mezi virtuální sítě a mezi důležitými obchodními službami a aplikacemi.
- Zabezpečení, včetně škodlivých přenosů, portů otevřených v Internetu, aplikací nebo virtuálních počítačů, které se pokoušejí o přístup k Internetu
- Využití kapacity, které pomáhá eliminovat problémy přes zřizování nebo nevyužití.

Můžete se i nadále spoléhat na nastavení diagnostiky a odesílat protokoly NSG, aby bylo možné Log Analytics, takže stávající uložená hledání, upozornění a řídicí panely budou fungovat i nadále. Zákazníci, kteří už toto řešení nainstalovali, ho můžou dál používat, dokud si nevšimnete. Od 5. září se řešení analýzy skupin zabezpečení sítě odebere z webu Marketplace a zpřístupní se prostřednictvím komunity jako [Šablona Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Pokud jste [Log Analytics skupinu pro správu Operations Manager k](../agents/om-agents.md), bude i nadále fungovat bez jakýchkoli změn. V případě nových připojení je potřeba při [konfiguraci Operations Management Suite](https://techcommunity.microsoft.com/t5/system-center-blog/bg-p/SystemCenterBlog)postupovat podle pokynů v sadě Microsoft System Center Operations Manager Management Pack.

## <a name="next-steps"></a>Další kroky
- V tématu [běžné otázky týkající se přechodu z portálu OMS na Azure Portal Log Analytics uživatele](../overview.md) , kde najdete pokyny k přesunu z portálu oms do Azure Portal.