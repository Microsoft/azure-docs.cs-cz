---
title: Nejčastější dotazy k virtuálnímu počítači Insights (GA) | Microsoft Docs
description: Cloud Insights je řešení v Azure, které kombinuje monitorování stavu a výkonu operačního systému virtuálního počítače Azure a automaticky zjišťuje součásti aplikací a závislosti s dalšími prostředky a mapuje komunikaci mezi nimi. Tento článek obsahuje odpovědi na běžné dotazy týkající se verze GA.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: fbef73bfe8058110277b200b8c4091fcde110c04
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031860"
---
# <a name="vm-insights-generally-available-ga-frequently-asked-questions"></a>Nejčastější dotazy k VIRTUÁLNÍm počítačům jsou všeobecně dostupné (GA)
V této obecné dostupnosti se Nejčastější dotazy týkají změn, které byly provedeny ve čtvrtletí 2019 a Q1 2020, jak jsme připraveni na GA.

## <a name="updates-for-vm-insights"></a>Aktualizace pro službu VM Insights
Vydali jsme novou verzi sady VM Insights v lednu 2020 před naší oznámením GA. Zákazníci, kteří si můžou povolit službu VM Insights, teď obdrží verzi GA, ale stávající zákazníci, kteří používají verzi služby VM Insights ze 4. čtvrtletí 2019 a starší, budou vyzváni k upgradu. Tato Nejčastější dotazy nabízí pokyny k provedení upgradu se škálováním v případě, že máte rozsáhlá nasazení napříč několika pracovními prostory.


S tímto upgradem se data o výkonu pro virtuální počítače v nástroji Insights ukládají do stejné tabulky *InsightsMetrics* jako služby [Container Insights](../containers/container-insights-overview.md), což usnadňuje dotazování obou datových sad. Můžete také uložit více různých datových sad, které jsme nedokázali uložit v tabulce, která se dřív použila. 

Naše zobrazení výkonu teď používají uložená data v tabulce *InsightsMetrics* .  Pokud jste ještě neupgradovali na použití nejnovějšího řešení VMInsights v pracovním prostoru, grafy už nebudou zobrazovat informace.  Upgrade můžete provést **na stránce Začínáme** , jak je popsáno níže.


## <a name="what-is-changing"></a>Co se mění?
Vydali jsme nové řešení s názvem VMInsights, které obsahuje více možností shromažďování dat a nové umístění pro ukládání těchto dat do pracovního prostoru Log Analytics. 

V minulosti jsme povolili řešení ServiceMap v pracovním prostoru a nastavili čítače výkonu v pracovním prostoru Log Analytics k odeslání dat do tabulky *perf* . Toto nové řešení odesílá data do tabulky s názvem *InsightsMetrics* , která je také používána službou Container Insights. Toto schéma tabulky nám umožňuje ukládat další metriky a sady dat služby, které nejsou kompatibilní s formátem tabulky *perf* .

Aktualizovali jsme naše grafy výkonu pro používání dat, která ukládáme do tabulky *InsightsMetrics* . Můžete upgradovat na použití tabulky *InsightsMetrics* **na naší stránce Začínáme,** jak je popsáno níže.


## <a name="how-do-i-upgrade"></a>Návody upgradovat?
Když se pracovní prostor Log Analytics upgraduje na nejnovější verzi Azure Monitor na virtuální počítače, upgraduje se agent závislostí na každém virtuálním počítači připojeném k tomuto pracovnímu prostoru. Každý virtuální počítač, který vyžaduje upgrade, se identifikuje na **kartě Začínáme ve** službě VM insights v Azure Portal. Když se rozhodnete upgradovat virtuální počítač, upgraduje se pracovní prostor pro tento virtuální počítač společně s ostatními virtuálními počítači připojenými k tomuto pracovnímu prostoru. Můžete vybrat jeden nebo více virtuálních počítačů, skupiny prostředků nebo odběry. 

Pomocí následujícího příkazu upgradujte pracovní prostor pomocí prostředí PowerShell:

```PowerShell
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Co mám dělat s čítači výkonu v pracovním prostoru při instalaci řešení VMInsights?

Předchozí metoda povolení čítačů výkonu pro použití v rámci virtuálního počítače v pracovním prostoru. Aktuální verze uchovává tato data v tabulce s názvem `InsightsMetrics` . Tyto čítače výkonu můžete v pracovním prostoru zakázat, pokud je už nepotřebujete používat. 

>[!NOTE]
>Pokud máte pravidla výstrah, která odkazují na tyto čítače v `Perf` tabulce, je nutné je aktualizovat, aby odkazovala na nová data uložená v `InsightsMetrics` tabulce. Příklady dotazů protokolu, které můžete použít, najdete v naší dokumentaci k této tabulce.
>

Pokud se rozhodnete ponechat čítače výkonu povolené, budou se vám účtovat data ingestovaná a uložená v `Perf` tabulce na základě [Log Analytics ceny [( https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="how-will-this-change-affect-my-alert-rules"></a>Jak bude tato změna mít vliv na pravidla upozornění?

Pokud jste vytvořili [výstrahy protokolu](../alerts/alerts-unified-log.md) , které se dotazují na `Perf` čítače výkonu, které byly povoleny v pracovním prostoru, měli byste tato pravidla aktualizovat, aby místo nich odkazovala na `InsightsMetrics` tabulku. Tyto doprovodné materiály platí také pro všechna pravidla prohledávání protokolu pomocí `ServiceMapComputer_CL` a `ServiceMapProcess_CL` , protože tyto datové sady se přesunou do `VMComputer` `VMProcess` tabulek a.

Tyto nejčastější dotazy a naši dokumentaci budeme aktualizovat tak, aby obsahovaly ukázková pravidla upozornění pro prohledávání protokolů pro sady dat, které shromažďujeme.

## <a name="how-will-this-change-affect-my-bill"></a>Jak bude tato změna mít na vyúčtování vliv?

Fakturace se pořád vychází z dat, která se ingestují a uchovávají v pracovním prostoru Log Analytics.

Údaje o výkonu na úrovni počítače, které shromažďujeme, mají stejnou velikost jako data uložená v `Perf` tabulce a budou mít náklady přibližně stejnou částku.

## <a name="what-if-i-only-want-to-use-service-map"></a>Co když chci použít jenom Service Map?

To je dobré. V Azure Portal se zobrazí výzvy k zobrazení informací o nadcházející aktualizaci pro virtuální počítač. Po vydání se zobrazí výzva požadující, abyste aktualizovali na novou verzi. Pokud dáváte přednost použití funkce [Maps](vminsights-maps.md) , můžete se rozhodnout, že nebudete upgradovat a nadále používat funkci Maps v nástroji VM Insights a k řešení Service map přistupované z dlaždice pracovního prostoru nebo řídicího panelu.

Pokud jste se rozhodli ručně povolit čítače výkonu v pracovním prostoru, můžete zobrazit data v některých našich grafech výkonu zobrazených z Azure Monitor. Po vydání nového řešení aktualizujeme naše grafy výkonu a provedeme dotaz na data uložená v `InsightsMetrics` tabulce. Pokud byste chtěli zobrazit data z této tabulky v těchto grafech, budete muset upgradovat na novou verzi služby VM Insights.

Změny pro přesun dat z `ServiceMapComputer_CL` a `ServiceMapProcess_CL` budou mít vliv na Service map i na službu VM Insights, takže stále musíte tuto aktualizaci naplánovat.

Pokud se rozhodnete neupgradovat na řešení **VMInsights** , budeme dál poskytovat starší verze našich sešitů výkonu, které odkazují na data v `Perf` tabulce.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Budou se sady dat Service Map také ukládat v InsightsMetrics?

Datové sady nebudou duplikovány, pokud použijete obě řešení. Obě nabídky sdílejí sady dat, které budou uložené v `VMComputer` (dříve ServiceMapComputer_CL), `VMProcess` (dřív ServiceMapProcess_CL), `VMConnection` a `VMBoundPort` tabulky, do kterých se ukládají sady dat mapy, které shromažďujeme.  

`InsightsMetrics`Tabulka bude ukládat sady dat o virtuálních počítačích, procesech a službách, které shromažďujeme a bude naplnit, jenom pokud používáte virtuální počítač a řešení pro virtuální počítače Insights. Řešení Service Map nebude shromažďovat ani ukládat data v `InsightsMetrics` tabulce.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Bude se mi něco účtovat, když mám v pracovním prostoru řešení Service Map a VMInsights?

Ne, tato dvě řešení sdílejí sady dat mapy, které ukládáme `VMComputer` (dřív ServiceMapComputer_CL), `VMProcess` (dřív ServiceMapProcess_CL), `VMConnection` a `VMBoundPort` . V případě, že máte obě řešení ve vašem pracovním prostoru, nebudeme vám nic účtovat.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Když odeberem řešení Service Map nebo VMInsights, odebere se moje data?

Ne, tato dvě řešení sdílejí sady dat mapy, které ukládáme `VMComputer` (dřív ServiceMapComputer_CL), `VMProcess` (dřív ServiceMapProcess_CL), `VMConnection` a `VMBoundPort` . Pokud odeberete jedno z těchto řešení, tyto sady dat si všimněte, že je stále k dispozici řešení, které používá data a zůstane v pracovním prostoru Log Analytics. Z pracovního prostoru musíte odebrat obě řešení, aby se z něho odstranila data.

## <a name="health-feature-is-in-limited-public-preview"></a>Funkce Health je ve verzi Public Preview omezená.

Od zákazníků jsme dostali spoustu skvělé zpětné vazby o sadě funkcí stavu virtuálních počítačů. Tato funkce má asignificant zájmu a setkalo se na její potenciál, aby podporovaly monitorovací pracovní postupy. Plánujeme udělat řadu změn pro přidání funkcí a vyřešení zpětné vazby, kterou jsme dostali. 

Abychom minimalizovali dopad těchto změn na nové zákazníky, přesunuli jsme tuto funkci do **omezené verze Public Preview**. Tato aktualizace se stala v říjnu 2019.

Tuto funkci stavu v 2020 plánujete znovu spustit, až bude přehledy VM v oblasti GA.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Jak existující zákazníci získají přístup k funkci stavu?

Stávající zákazníci, kteří používají funkci stavu, budou mít k tomuto přístupu i nadále přístup, ale nebudou jim nabízeni noví zákazníci.  

Pro přístup k této funkci můžete Azure Portal adresu URL přidat následující příznak funkce `feature.vmhealth=true` [https://portal.azure.com](https://portal.azure.com) . Příklad `https://portal.azure.com/?feature.vmhealth=true` :

Můžete také použít tuto krátkou adresu URL, která automaticky nastaví příznak funkce: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview) .

Jako stávající zákazník můžete dál používat funkci stavu na virtuálních počítačích, které jsou připojené k existujícímu nastavení pracovního prostoru s funkcí stavu.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Nyní používám stav virtuálního počítače v jednom prostředí a chce ho nasadit na nový

Pokud jste stávající zákazník, který používá funkci stavu a chcete ho použít k novému uvedení, kontaktujte nás na stránce vminsights@microsoft.com s žádostí o pokyny.

## <a name="next-steps"></a>Další kroky

Pokud chcete pochopit požadavky a metody, které vám pomůžou monitorovat virtuální počítače, přečtěte si téma [nasazení přehledů virtuálních počítačů](./vminsights-enable-overview.md).
