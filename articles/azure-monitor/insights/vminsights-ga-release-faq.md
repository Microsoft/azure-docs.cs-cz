---
title: Nejčastější dotazy k Azure Monitor pro virtuální počítače (GA) | Microsoft Docs
description: Azure Monitor pro virtuální počítače je řešení v Azure, které kombinuje monitorování stavu a výkonu operačního systému virtuálního počítače Azure a automaticky zjišťuje součásti aplikací a závislosti s dalšími prostředky a mapuje komunikaci mezi nimi. Tento článek obsahuje odpovědi na běžné dotazy týkající se verze GA.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: be192a5807c40ea65ea6533ec6244183e5a4b644
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88958792"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Azure Monitor pro virtuální počítače všeobecně dostupné (GA) nejčastější dotazy
V této obecné dostupnosti se Nejčastější dotazy týkají změn, které byly provedeny ve čtvrtletí 2019 a Q1 2020, jak jsme připraveni na GA.

## <a name="updates-for-azure-monitor-for-vms"></a>Aktualizace pro Azure Monitor pro virtuální počítače
Vydali jsme novou verzi Azure Monitor pro virtuální počítače v lednu 2020 před naší oznámením GA. Zákazníci, kteří zajišťují Azure Monitor pro virtuální počítače, teď obdrží verzi GA, ale stávající zákazníci, kteří používají verzi Azure Monitor pro virtuální počítače ze 4. čtvrtletí 2019 a starší, budou vyzváni k upgradu. Tato Nejčastější dotazy nabízí pokyny k provedení upgradu se škálováním v případě, že máte rozsáhlá nasazení napříč několika pracovními prostory.


S tímto upgradem Azure Monitor pro virtuální počítače údaje o výkonu jsou uloženy ve stejné tabulce *InsightsMetrics* jako [Azure monitor pro kontejnery](container-insights-overview.md), což usnadňuje dotazování obou datových sad. Můžete také uložit více různých datových sad, které jsme nedokázali uložit v tabulce, která se dřív použila. 

Naše zobrazení výkonu teď používají uložená data v tabulce *InsightsMetrics* .  Pokud jste ještě neupgradovali na použití nejnovějšího řešení VMInsights v pracovním prostoru, grafy už nebudou zobrazovat informace.  Upgrade můžete provést **na stránce Začínáme** , jak je popsáno níže.


## <a name="what-is-changing"></a>Co se mění?
Vydali jsme nové řešení s názvem VMInsights, které obsahuje další možnosti shromažďování dat a nové umístění pro ukládání těchto dat do pracovního prostoru Log Analytics. 

V minulosti jsme povolili řešení ServiceMap v pracovním prostoru a nastavili čítače výkonu v pracovním prostoru Log Analytics k odeslání dat do tabulky *perf* . Toto nové řešení odesílá data do tabulky s názvem *InsightsMetrics* , která je také používána Azure monitor pro kontejnery. Toto schéma tabulky nám umožňuje ukládat další metriky a sady dat služby, které nejsou kompatibilní s formátem tabulky *perf* .

Aktualizovali jsme naše grafy výkonu pro používání dat, která ukládáme do tabulky *InsightsMetrics* . Můžete upgradovat na použití tabulky *InsightsMetrics* **na naší stránce Začínáme,** jak je popsáno níže.


## <a name="how-do-i-upgrade"></a>Návody upgradovat?
Když se pracovní prostor Log Analytics upgraduje na nejnovější verzi Azure Monitor na virtuální počítače, upgraduje se agent závislostí na každém virtuálním počítači připojeném k tomuto pracovnímu prostoru. Každý virtuální počítač, který vyžaduje upgrade, se identifikuje na **kartě Začínáme v** Azure monitor pro virtuální počítače Azure Portal. Když se rozhodnete upgradovat virtuální počítač, upgraduje se pracovní prostor pro tento virtuální počítač společně s ostatními virtuálními počítači připojenými k tomuto pracovnímu prostoru. Můžete vybrat jeden nebo více virtuálních počítačů, skupiny prostředků nebo odběry. 

Pomocí následujícího příkazu upgradujte pracovní prostor pomocí prostředí PowerShell:

```PowerShell
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Co mám dělat s čítači výkonu v pracovním prostoru při instalaci řešení VMInsights?

Předchozí metoda povolení Azure Monitor pro virtuální počítače použití čítačů výkonu v pracovním prostoru. Aktuální verze uchovává tato data v tabulce s názvem `InsightsMetrics` . Tyto čítače výkonu můžete v pracovním prostoru zakázat, pokud je už nepotřebujete používat. 

>[!NOTE]
>Pokud máte pravidla výstrah, která odkazují na tyto čítače v `Perf` tabulce, je nutné je aktualizovat, aby odkazovala na nová data uložená v `InsightsMetrics` tabulce. Příklady dotazů protokolu, které můžete použít, najdete v naší dokumentaci k této tabulce.
>

Pokud se rozhodnete ponechat čítače výkonu povolené, budou se vám účtovat data ingestovaná a uložená v `Perf` tabulce na základě [Log Analytics ceny [( https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="how-will-this-change-affect-my-alert-rules"></a>Jak bude tato změna mít vliv na pravidla upozornění?

Pokud jste vytvořili [výstrahy protokolu](../platform/alerts-unified-log.md) , které se dotazují na `Perf` čítače výkonu, které byly povoleny v pracovním prostoru, měli byste tato pravidla aktualizovat, aby místo nich odkazovala na `InsightsMetrics` tabulku. Tyto doprovodné materiály platí také pro všechna pravidla prohledávání protokolu pomocí `ServiceMapComputer_CL` a `ServiceMapProcess_CL` , protože tyto datové sady se přesunou do `VMComputer` `VMProcess` tabulek a.

Tyto nejčastější dotazy a naši dokumentaci budeme aktualizovat tak, aby obsahovaly ukázková pravidla upozornění pro prohledávání protokolů pro sady dat, které shromažďujeme.

## <a name="how-will-this-affect-my-bill"></a>Jak to bude mít na vyúčtování vliv?

Fakturace se pořád vychází z dat, která se ingestují a uchovávají v pracovním prostoru Log Analytics.

Údaje o výkonu na úrovni počítače, které shromažďujeme, mají stejnou velikost jako data uložená v `Perf` tabulce a budou mít náklady přibližně stejnou částku.

## <a name="what-if-i-only-want-to-use-service-map"></a>Co když chci použít jenom Service Map?

To je dobré. Při zobrazení Azure Monitor pro virtuální počítače o nadcházející aktualizaci se zobrazí výzvy Azure Portal. Po vydání se zobrazí výzva požadující, abyste aktualizovali na novou verzi. Pokud dáváte přednost použití funkce [Maps](vminsights-maps.md) , můžete se rozhodnout, že nebudete upgradovat a nadále používat funkci maps v Azure Monitor pro virtuální počítače a Service map řešení dostupného z dlaždice pracovního prostoru nebo řídicího panelu.

Pokud jste se rozhodli ručně povolit čítače výkonu v pracovním prostoru, můžete zobrazit data v některých našich grafech výkonu zobrazených z Azure Monitor. Po vydání nového řešení aktualizujeme naše grafy výkonu a provedeme dotaz na data uložená v `InsightsMetrics` tabulce. Pokud chcete zobrazit data z této tabulky v těchto grafech, budete muset upgradovat na novou verzi Azure Monitor pro virtuální počítače.

Změny pro přesun dat z `ServiceMapComputer_CL` a `ServiceMapProcess_CL` budou mít vliv na Service Map i Azure monitor pro virtuální počítače, takže stále musíte tuto aktualizaci naplánovat.

Pokud se rozhodnete neupgradovat na řešení **VMInsights** , budeme dál poskytovat starší verze našich sešitů výkonu, které odkazují na data v `Perf` tabulce.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Budou se sady dat Service Map také ukládat v InsightsMetrics?

Datové sady nebudou duplikovány, pokud použijete obě řešení. Obě nabídky sdílejí sady dat, které budou uložené v `VMComputer` (dříve ServiceMapComputer_CL), `VMProcess` (dřív ServiceMapProcess_CL), `VMConnection` a `VMBoundPort` tabulky, do kterých se ukládají sady dat mapy, které shromažďujeme.  

`InsightsMetrics`Tabulka bude ukládat sady dat virtuálních počítačů, procesů a služeb, které shromažďujeme a bude naplněna, jenom pokud používáte Azure monitor pro virtuální počítače a řešení pro virtuální počítač Insights. Řešení Service Map nebude shromažďovat ani ukládat data v `InsightsMetrics` tabulce.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Bude se mi něco účtovat, když mám v pracovním prostoru řešení Service Map a VMInsights?

Ne, tato dvě řešení sdílejí sady dat mapy, které ukládáme `VMComputer` (dřív ServiceMapComputer_CL), `VMProcess` (dřív ServiceMapProcess_CL), `VMConnection` a `VMBoundPort` . V případě, že máte obě řešení ve vašem pracovním prostoru, nebudeme vám nic účtovat.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Když odeberem řešení Service Map nebo VMInsights, odeberou se moje data?

Ne, tato dvě řešení sdílejí sady dat mapy, které ukládáme `VMComputer` (dřív ServiceMapComputer_CL), `VMProcess` (dřív ServiceMapProcess_CL), `VMConnection` a `VMBoundPort` . Pokud odeberete jedno z těchto řešení, tyto sady dat si všimněte, že je stále k dispozici řešení, které používá data a zůstane v pracovním prostoru Log Analytics. Z pracovního prostoru musíte odebrat obě řešení, aby se z něho odstranila data.

## <a name="health-feature-is-in-limited-public-preview"></a>Funkce Health je ve verzi Public Preview omezená.

Od zákazníků jsme dostali spoustu skvělé zpětné vazby o sadě funkcí stavu virtuálních počítačů. Tato funkce má spoustu zájmu a setkalo se na jejich potenciál, aby podporovaly monitorovací pracovní postupy. Plánujeme udělat řadu změn pro přidání funkcí a vyřešení zpětné vazby, kterou jsme dostali. 

Abychom minimalizovali dopad těchto změn na nové zákazníky, přesunuli jsme tuto funkci do **omezené verze Public Preview**. Tato aktualizace se stala v říjnu 2019.

Plánujete znovu spustit tuto funkci stavu v 2020, po Azure Monitor pro virtuální počítače je v GA.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Jak existující zákazníci získají přístup k funkci stavu?

Stávající zákazníci, kteří používají funkci stavu, budou mít k tomuto přístupu i nadále přístup, ale nebudou jim nabízeni noví zákazníci.  

Pro přístup k této funkci můžete Azure Portal adresu URL přidat následující příznak funkce `feature.vmhealth=true` [https://portal.azure.com](https://portal.azure.com) . Příklad `https://portal.azure.com/?feature.vmhealth=true` :

Můžete také použít tuto krátkou adresu URL, která automaticky nastaví příznak funkce: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview) .

Jako stávající zákazník můžete dál používat funkci stavu na virtuálních počítačích, které jsou připojené k existujícímu nastavení pracovního prostoru s funkcí stavu.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Nyní používám stav virtuálního počítače v jednom prostředí a chce ho nasadit na nový

Pokud jste stávající zákazník, který používá funkci Health a chcete ho použít k novému uvedení, kontaktujte nás na stránce vminsights@microsoft.com s žádostí o pokyny.

## <a name="next-steps"></a>Další kroky

Pokud chcete pochopit požadavky a metody, které vám pomůžou monitorovat virtuální počítače, přečtěte si téma [nasazení Azure monitor pro virtuální počítače](vminsights-enable-overview.md).
