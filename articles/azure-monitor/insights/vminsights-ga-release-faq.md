---
title: Nejčastější dotazy k Azure Monitor pro virtuální počítače (GA) | Microsoft Docs
description: Azure Monitor pro virtuální počítače je řešení v Azure, které kombinuje monitorování stavu a výkonu operačního systému virtuálního počítače Azure a automaticky zjišťuje součásti aplikací a závislosti s dalšími prostředky a mapuje komunikaci mezi ihned. Tento článek obsahuje odpovědi na běžné dotazy týkající se verze GA.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/07/2019
ms.openlocfilehash: 523fb2d3a3b148afc9219e666c2fbe7fa40d58ad
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553793"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Azure Monitor pro virtuální počítače všeobecně dostupné (GA) nejčastější dotazy

Nedávno jsme oznámili, že na blogu věnovaném [Azure Update](https://azure.microsoft.com/blog/) se naplánovaly plánované změny, ke kterým dojde v říjnu a listopadu 2019. Další podrobnosti o těchto změnách najdete v tomto obecném nejčastějších dotazech k dostupnosti.

## <a name="updates-for-azure-monitor-for-vms"></a>Aktualizace pro Azure Monitor pro virtuální počítače

Vydáním nové verze Azure Monitor pro virtuální počítače v listopadu. Zákazníci, kteří zajišťují, aby se pro virtuální počítače na virtuálních počítačích automaticky dostala nová verze, ale stávající zákazníci, kteří už používají Azure Monitor pro virtuální počítače, jsou vyzváni k upgradu.  Tyto nejčastější dotazy a naše dokumentace nabízí pokyny k provedení hromadného upgradu, pokud máte rozsáhlá nasazení napříč několika pracovními prostory.

V tomto upgradu jsou nyní Azure Monitor pro virtuální počítače datové sady výkonu uloženy ve stejné tabulce `InsightsMetrics` jako [Azure monitor pro kontejnery](container-insights-overview.md)a usnadňují dotazování obou datových sad. Můžete také uložit více různých datových sad, které jsme nemohli Uložit do tabulky, kterou jsme dřív použili.  Naše zobrazení výkonu budou také aktualizována, aby používala tuto novou tabulku.

Přesouváme nové datové typy pro naše datové sady pro připojení. Data, která jsou uložená v `ServiceMapComputer_CL` a `ServiceMapProcess_CL`, která používají vlastní tabulky protokolu, se přesunou na vyhrazené datové typy s názvem `VMComputer` a `VMProcess`.  Když přejdete na vyhrazené datové typy, můžeme těmto prioritám poskytnout příjem dat a schéma tabulky se bude standardizované napříč všemi zákazníky.

Uvědomujeme si, že požádáme o upgrade stávajících zákazníků na jejich pracovní postup. to je důvod, proč jsme se rozhodli tuto možnost udělat teď v Public Preview, ale ne později, až přijdeme v GA.

## <a name="what-will-change"></a>Co se změní?

V současné době po dokončení procesu připojování pro Azure Monitor pro virtuální počítače povolíte Service Map řešení v pracovním prostoru, který jste vybrali k uložení dat monitorování, a pak nakonfigurujete čítače výkonu pro data, která shromažďujeme z vašich virtuálních počítačů. V nadcházejících týdnech vydáte nové řešení s názvem **VMInsights**, které bude zahrnovat další možnosti shromažďování dat a nové umístění pro ukládání těchto dat v Log Analytics.

Náš aktuální proces použití čítačů výkonu v pracovním prostoru odesílá data do tabulky perf v Log Analytics.  Toto nové řešení odešle data do tabulky s názvem `InsightsMetrics`, která je také používána Azure Monitor pro kontejnery. Toto schéma tabulky nám umožňuje ukládat další metriky a sady dat služby, které nejsou kompatibilní s formátem tabulky perf.

## <a name="what-should-i-do-about-the-performance-counters-on-my-workspace-if-i-install-the-vminsights-solution"></a>Co mám dělat s čítači výkonu v pracovním prostoru při instalaci řešení VMInsights?

Aktuální metoda povolení Azure Monitor pro virtuální počítače používá čítače výkonu ve vašem pracovním prostoru. Nová metoda uloží tato data do nové tabulky s názvem `InsightsMetrics`.

Jakmile aktualizujeme naše uživatelské rozhraní tak, aby používalo data v InsightsMetrics, aktualizujeme naši dokumentaci a sdělíme toto oznámení prostřednictvím několika kanálů, včetně zobrazení banneru v Azure Portal. V tomto okamžiku se můžete rozhodnout zakázat tyto [čítače výkonu](vminsights-enable-overview.md#performance-counters-enabled) v pracovním prostoru, pokud je už nechcete používat. 

[!NOTE]
>Pokud máte pravidla upozornění, která odkazují na tyto čítače v tabulce perf, je nutné je aktualizovat, aby odkazovala na nová data v tabulce `InsightsMetrics`.  Příklady dotazů protokolu, které můžete použít, najdete v naší dokumentaci k této tabulce.

Pokud se rozhodnete zachovat čítače výkonu, budou se vám účtovat data ingestovaná a uchovávaná do tabulky perf na základě [Log Analytics ceny [(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>Jak bude tato změna mít vliv na pravidla upozornění?

Pokud jste vytvořili [výstrahy protokolu](../platform/alerts-unified-log.md) , které se dotazují na čítače výkonu v tabulce `Perf`, které byly povolené v pracovním prostoru, měli byste tato pravidla aktualizovat tak, aby místo toho odkazovaly na tabulku `InsightsMetrics`. Tyto pokyny se vztahují také na všechna pravidla prohledávání protokolu pomocí `ServiceMapComputer_CL` a `ServiceMapProcess_CL`, protože tyto sady dat přecházejí do `VMComputer` a `VMProcess` tabulek.

Tyto nejčastější dotazy a naši dokumentaci budeme aktualizovat tak, aby obsahovaly ukázková pravidla upozornění pro prohledávání protokolů pro sady dat, které shromažďujeme.

## <a name="will-there-be-any-changes-to-billing"></a>Budou se účtovat nějaké změny, které se účtují?

Fakturace se pořád vychází z dat, která se ingestují a uchovávají v pracovním prostoru Log Analytics.

Údaje o výkonu na úrovni počítače, které shromažďujeme, mají stejnou velikost jako data uložená v tabulce perf a budou mít náklady přibližně stejnou částku.

## <a name="what-if-i-only-want-to-use-service-map"></a>Co když chci použít jenom Service Map?

To je dobré.  Při zobrazení Azure Monitor pro virtuální počítače o nadcházející aktualizaci se zobrazí výzvy Azure Portal. Po vydání se zobrazí výzva požadující aktualizaci na novou verzi. Pokud dáváte přednost použití funkce [Maps](vminsights-maps.md) , můžete zvolit, že se nebudete upgradovat a nadále používat funkci Maps Azure monitor pro virtuální počítače a řešení Service map, ke kterému se přistupovalo z dlaždice pracovního prostoru nebo řídicího panelu.

Pokud jste se rozhodli ručně povolit čítače výkonu v pracovním prostoru, můžete zobrazit data v některých našich grafech výkonu zobrazených z Azure Monitor. Po vydání nového řešení aktualizujeme naše grafy výkonu a provedeme dotaz na data uložená v tabulce `InsightsMetrics`. Pokud chcete zobrazit data z této tabulky v těchto grafech, budete muset upgradovat na novou verzi Azure Monitor pro virtuální počítače.

Změny přesunu dat z `ServiceMapComputer_CL` a `ServiceMapProcess_CL` budou mít vliv na Service Map i Azure Monitor pro virtuální počítače, takže stále budete muset tuto aktualizaci naplánovat.

Pokud se rozhodnete neupgradovat na řešení **VMInsights** , budeme dál poskytovat starší verze našich sešitů výkonu, které odkazují na data v tabulce `Perf`.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Budou se sady dat Service Map také ukládat v InsightsMetrics?

Datové sady nebudou duplikovány, pokud použijete obě řešení. Obě nabídky sdílejí sady dat, které budou uložené v `VMComputer` (dříve ServiceMapComputer_CL), `VMProcess` (dříve ServiceMapProcess_CL), `VMConnection` a `VMBoundPort` tabulky pro uložení datových sad map, které shromažďujeme.  

Tabulka `InsightsMetrics` se použije k uložení sad dat pro virtuální počítače, procesy a služby, které shromažďujeme a bude naplněna, jenom pokud používáte Azure Monitor pro virtuální počítače.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-on-my-workspace"></a>Bude se mi něco účtovat, když mám Service Map a VMInsights řešení v mém pracovním prostoru?

Ne, tato dvě řešení sdílí sady dat map, které ukládáme v `VMComputer` (dříve ServiceMapComputer_CL), `VMProcess` (dřív ServiceMapProcess_CL), `VMConnection` a `VMBoundPort`.  V případě, že máte obě řešení ve vašem pracovním prostoru, nebudeme vám nic účtovat.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data-in-log-analytics"></a>Když odeberem řešení Service Map nebo VMInsights, odeberou se moje data v Log Analytics?

Ne, tato dvě řešení sdílí sady dat map, které ukládáme v `VMComputer` (dříve ServiceMapComputer_CL), `VMProcess` (dřív ServiceMapProcess_CL), `VMConnection` a `VMBoundPort`.  Pokud odeberete jedno z těchto řešení, tyto sady dat si všimněte, že je stále k dispozici řešení, které používá data a zůstává v Log Analytics.  Z pracovního prostoru musíte odebrat obě řešení, aby se data odebrala z pracovního prostoru Log Analytics.

## <a name="when-will-this-update-be-released"></a>Kdy bude tato aktualizace vydána?

Očekáváme vydání aktualizace pro Azure Monitor pro virtuální počítače v polovině listopadu. Až se dostanete k datu vydání, budeme aktualizace účtovat tady a při přechodu na Azure Monitor dostávat oznámení v Azure Portal.

## <a name="health-feature-to-enter-limited-public-preview"></a>Funkce Health k zadání omezené verze Public Preview

Od zákazníků jsme dostali spoustu skvělé zpětné vazby o sadě funkcí stavu virtuálních počítačů.  Tato funkce má spoustu zájmu a setkalo se na jejich potenciál, aby podporovaly monitorovací pracovní postupy. Plánujeme udělat řadu změn pro přidání funkcí a vyřešení zpětné vazby, kterou jsme dostali. Abychom minimalizovali dopad těchto změn na nové zákazníky, přesouváme tuto funkci do omezené verze Public Preview.

Tento přechod začne v rané říjnu a měl by být dokončený na konci měsíce.

Některé oblasti, na které se chceme zaměřit:

- Větší kontrola nad modelem stavu a jeho prahovou hodnotou
- Vytváření v rámci škálování modelů, které se vztahují na rozsah virtuálních počítačů
- Nativní použití platformy upozornění pro správu pravidel upozornění na základě stavu
- Možnost Zobrazit stav napříč širším rozsahem, jako je například jeden nebo více předplatných
- Omezená latence v propřechodechch stavu a oznámeních výstrah

## <a name="how-do-existing-customers-access-the-health-feature"></a>Jak existující zákazníci získají přístup k funkci stavu?

Stávající zákazníci, kteří používají funkci stavu, budou mít k tomuto přístupu i nadále přístup, ale nebudou jim nabízeni noví zákazníci.  

Pro přístup k této funkci můžete přidat následující příznak funkce `feature.vmhealth=true` do adresy URL portálu [https://portal.azure.com](https://portal.azure.com). Příklad `https://portal.azure.com/?feature.vmhealth=true`.

Můžete také použít tuto krátkou adresu URL, která automaticky nastaví příznak funkce: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Jako stávající zákazník můžete dál používat funkci stavu na virtuálních počítačích, které jsou připojené k existujícímu nastavení pracovního prostoru s funkcí stavu.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-for-a-new-environment"></a>Nyní používám stav virtuálních počítačů v jednom prostředí a chce ho nasadit pro nové prostředí

Pokud jste stávající zákazník, který používá funkci Health a chcete ho použít k novému uvedení, kontaktujte nás na adrese vminsights@microsoft.com a vyžádejte si pokyny.

## <a name="next-steps"></a>Další kroky

Pokud chcete pochopit požadavky a metody, které vám pomůžou monitorovat virtuální počítače, přečtěte si téma [nasazení Azure monitor pro virtuální počítače](vminsights-enable-overview.md).
