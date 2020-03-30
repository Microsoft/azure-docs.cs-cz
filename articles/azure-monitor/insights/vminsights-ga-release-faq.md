---
title: Azure Monitor pro virtuální počítače (GA) nejčastější dotazy | Dokumenty společnosti Microsoft
description: Azure Monitor pro virtuální počítače je řešení v Azure, které kombinuje monitorování stavu a výkonu operačního systému Azure VM a také automaticky zjišťuje součásti a závislosti aplikací s jinými prostředky a mapuje komunikaci mezi Je. Tento článek odpovídá na běžné otázky týkající se vydání ga.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 3d250ef1aba979be04a44acaf31a3d685f162e37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283884"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Azure Monitor pro virtuální počítače obecně dostupné (GA) Nejčastější dotazy
Tato nejčastější dotazy týkající se obecné dostupnosti zahrnují změny, které byly provedeny v Q4 2019 a Q1 2020, jak jsme se připravovali pro GA.

## <a name="updates-for-azure-monitor-for-vms"></a>Aktualizace pro Azure Monitor pro virtuální počítače
Vydali jsme novou verzi Azure Monitor pro virtuální počítače v lednu 2020 před naší ga oznámení. Zákazníci, kteří povolí Azure Monitor pro virtuální počítače, teď dostanou verzi GA, ale stávající zákazníci používající verzi Azure Monitoru pro virtuální počítače z Q4 2019 a starší chtějí upgradovat. Tento častý dotaz nabízí pokyny k provedení upgradu ve velkém měřítku, pokud máte velké nasazení napříč více pracovními prostory.


Díky tomuto upgradu jsou data o výkonu Azure Monitor pro virtuální počítače uložená ve stejné tabulce *InsightsMetrics* jako [Azure Monitor pro kontejnery](container-insights-overview.md), což usnadňuje dotazování na dvě sady dat. Také můžete ukládat rozmanitější datové sady, které jsme nemohli uložit v tabulce dříve používané. 

Naše zobrazení výkonu teď používají data, která ukládáme v tabulce *InsightsMetrics.*  Pokud jste ještě neupgradovali na použití nejnovějšího řešení VMInsights v pracovním prostoru, vaše grafy už nebudou zobrazovat informace.  Můžete upgradovat z naší stránky **Začínáme,** jak je popsáno níže.


## <a name="what-is-changing"></a>Co se mění?
Vydali jsme nové řešení s názvem VMInsights, které obsahuje další funkce pro shromažďování dat spolu s novým umístěním pro ukládání těchto dat do pracovního prostoru Log Analytics. 

V minulosti jsme povolili servicemap řešení na vašem pracovním prostoru a nastavení čítače výkonu v pracovním prostoru Log Analytics k odeslání dat do tabulky *Perf.* Toto nové řešení odesílá data do tabulky s názvem *InsightsMetrics,* která se používá také Azure Monitor pro kontejnery. Toto schéma tabulky nám umožňuje ukládat další metriky a sady dat služby, které nejsou kompatibilní s formátem tabulky *Perf.*

Aktualizovali jsme naše grafy výkonu, abychom mohli používat data, která ukládáme v tabulce *InsightsMetrics.* Můžete upgradovat na použití tabulky *InsightsMetrics* z naší stránky **Začínáme,** jak je popsáno níže.


## <a name="how-do-i-upgrade"></a>Jak mohu provést upgrade?
Když se pracovní prostor Analýzy protokolů upgraduje na nejnovější verzi Azure Monitoru na virtuální počítače, upgraduje agenta závislostí na každém virtuálním počítači připojeném k tomuto pracovnímu prostoru. Každý virtuální počítač vyžadující upgrade se bude identifikovat na kartě **Začínáme** v Azure Monitoru pro virtuální počítače na webu Azure Portal. Když se rozhodnete upgradovat virtuální hod, upgraduje pracovní prostor pro tento virtuální virtuální ms spolu s dalšími virtuálními min připojenými k tomuto pracovnímu prostoru. Můžete vybrat jeden virtuální počítač nebo více virtuálních počítačů, skupin prostředků nebo předplatných. 

Pomocí následujícího příkazu můžete inovovat pracovní prostor pomocí PowerShellu:

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Co mám dělat s čítači výkonu v pracovním prostoru, když nainstaluji řešení VMInsights?

Předchozí metoda povolení Azure Monitor pro virtuální počítače používá čítače výkonu ve vašem pracovním prostoru. Aktuální verze ukládá tato data `InsightsMetrics`v tabulce s názvem . Můžete zakázat tyto čítače výkonu v pracovním prostoru, pokud již není nutné je používat. 

>[!NOTE]
>Pokud máte pravidla výstrah, které odkazují `Perf` na tyto čítače v tabulce, `InsightsMetrics` je třeba je aktualizovat tak, aby odkazovaly na nová data uložená v tabulce. Naleznete v naší dokumentaci, například dotazy protokolu, které můžete použít, které odkazují na tuto tabulku.
>

Pokud se rozhodnete ponechat čítače výkonu povolené, budou vám účtovány `Perf` údaje pořízené a uložené vhttps://azure.microsoft.com/pricing/details/monitor/)tabulce na základě [Log Analytics ceny[( .

## <a name="how-will-this-change-affect-my-alert-rules"></a>Jak tato změna ovlivní moje pravidla výstrah?

Pokud jste vytvořili [výstrahy protokolu,](../platform/alerts-unified-log.md) které dotaz na `Perf` tabulky cílení čítače výkonu, `InsightsMetrics` které byly povoleny v pracovním prostoru, měli byste aktualizovat tato pravidla odkazovat na tabulku místo. Tento návod platí také pro všechna `ServiceMapComputer_CL` `ServiceMapProcess_CL`pravidla hledání protokolu pomocí `VMComputer` a `VMProcess` , protože tyto sady dat se přesouvají do tabulky.

Tyto nejčastější dotazy a naši dokumentaci budeme aktualizovat tak, aby obsahovaly ukázková pravidla upozornění na vyhledávání protokolů pro shromažďovky dat.

## <a name="how-will-this-affect-my-bill"></a>Jak to ovlivní můj účet?

Fakturace je stále založena na datech požitých a uchovávaných v pracovním prostoru Log Analytics.

Údaje o výkonu na úrovni počítače, které shromažďujeme, jsou stejné, `Perf` mají podobnou velikost jako data uložená v tabulce a budou stát přibližně stejnou částku.

## <a name="what-if-i-only-want-to-use-service-map"></a>Co když chci používat pouze mapu služeb?

To je v pořádku. Při zobrazení Azure Monitoru pro virtuální počítače o nadcházející aktualizaci se na webu Azure Portal zobrazí výzvy. Po vydání se zobrazí výzva s žádostí o aktualizaci na novou verzi. Pokud dáváte přednost jenom funkci [Mapy,](vminsights-maps.md) můžete se rozhodnout, že nebudete upgradovat a nadále používat funkci Mapy v Azure Monitoru pro virtuální počítače a řešení Mapy služeb, které se přistupuje z vašeho pracovního prostoru nebo dlaždice řídicího panelu.

Pokud jste se rozhodli ručně povolit čítače výkonu ve vašem pracovním prostoru, pak můžete zobrazit data v některých našich grafech výkonu zobrazených z Azure Monitoru. Po vydání nového řešení aktualizujeme naše grafy výkonu, `InsightsMetrics` abychom se dotazovali na data uložená v tabulce. Pokud chcete zobrazit data z této tabulky v těchto grafech, budete muset upgradovat na novou verzi Azure Monitor pro virtuální počítače.

Změny přesunout data `ServiceMapComputer_CL` `ServiceMapProcess_CL` z a ovlivní mapu služeb a Azure Monitor pro virtuální počítače, takže stále potřebujete naplánovat pro tuto aktualizaci.

Pokud jste se rozhodli neupgradovat na řešení **VMInsights,** budeme i nadále poskytovat starší `Perf` verze našich sešitů výkonu, které odkazují na data v tabulce.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Budou sady dat mapy služeb také uloženy v InsightsMetrics?

Datové sady nebudou duplikovány, pokud použijete obě řešení. Obě nabídky sdílejí sady dat, které `VMComputer` budou uloženy `VMProcess` v (dříve `VMConnection`ServiceMapComputer_CL), (dříve ServiceMapProcess_CL) a `VMBoundPort` tabulky pro ukládání mapových datových sad, které shromažďujeme.  

Tabulka `InsightsMetrics` bude ukládat sady dat virtuálních počítače, procesů a služeb, které shromažďujeme, a bude naplněna jenom v případě, že používáte Azure Monitor pro virtuální počítače a řešení VM Insights. Řešení Mapa služeb nebude shromažďovat nebo ukládat data v `InsightsMetrics` tabulce.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Bude mi v pracovním prostoru účtovándvojnásobný poplatek, pokud mám v pracovním prostoru řešení Mapy služeb a VMInsights?

Ne, obě řešení sdílejí sady mapových `VMComputer` dat, které `VMProcess` ukládáme (dříve `VMConnection`ServiceMapComputer_CL), (dříve ServiceMapProcess_CL) a `VMBoundPort`. Tato data nebudou účtována dvakrát, pokud máte obě řešení v pracovním prostoru.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Pokud odeberu mapu služeb nebo řešení VMInsights, odebere moje data?

Ne, obě řešení sdílejí sady mapových `VMComputer` dat, které `VMProcess` ukládáme (dříve `VMConnection`ServiceMapComputer_CL), (dříve ServiceMapProcess_CL) a `VMBoundPort`. Pokud odeberete jedno z řešení, tyto sady dat oznámení, že je stále řešení na místě, které používá data a zůstane v pracovním prostoru Log Analytics. Chcete-li z pracovního prostoru odebrat obě řešení, je třeba je odebrat.

## <a name="health-feature-is-in-limited-public-preview"></a>Funkce stavu je v omezenéverzi Veřejné

Obdrželi jsme spoustu skvělé zpětné vazby od zákazníků o naší sadě funkcí VM Health. O tuto funkci je velký zájem a vzrušení nad jejím potenciálem pro podporu pracovních postupů monitorování. Plánujeme provést řadu změn, abychom přidali funkce a řešili zpětnou vazbu, kterou jsme obdrželi. 

Abychom minimalizovali dopad těchto změn na nové zákazníky, přesunuli jsme tuto funkci do **omezené verze Public Preview**. Tato aktualizace se stala v říjnu 2019.

Tuto funkci stavu plánujeme znovu spustit v roce 2020, po Azure Monitor u virtuálních počítačů je v GA.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Jak mají stávající zákazníci přístup k funkci Stav?

Stávající zákazníci, kteří používají funkci Stav, k ní budou mít i nadále přístup, ale nebudou nabídnuti novým zákazníkům.  

Chcete-li získat přístup k této `feature.vmhealth=true` funkci, můžete [https://portal.azure.com](https://portal.azure.com)přidat příznak následující funkce na adresu URL portálu Azure . Příklad `https://portal.azure.com/?feature.vmhealth=true`.

Můžete také použít tuto krátkou adresu URL, [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)která automaticky nastaví příznak funkce: .

Jako stávající zákazník můžete nadále používat funkci Stav na virtuálních počítačích, které jsou připojené k existujícímu nastavení pracovního prostoru s funkcí stavu.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Používám VM Health nyní s jedním prostředím a chtěl bych nasadit do nového

Pokud jste stávající zákazník, který používá funkci Stav a chcete ji použít pro nové vminsights@microsoft.com zavedení, kontaktujte nás prosím a vyžádejte si pokyny.

## <a name="next-steps"></a>Další kroky

Chcete-li porozumět požadavkům a metodám, které vám pomohou monitorovat vaše virtuální počítače, přečtěte [si seznam nasazení Azure Monitoru pro virtuální počítače](vminsights-enable-overview.md).
