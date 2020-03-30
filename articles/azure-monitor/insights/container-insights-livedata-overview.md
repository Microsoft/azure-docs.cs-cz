---
title: Zobrazení živých dat (preview) pomocí Azure Monitoru pro kontejnery | Dokumenty společnosti Microsoft
description: Tento článek popisuje zobrazení v reálném čase kubernetes protokoly, události a pod metriky bez použití kubectl v Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9e7c7a7b7bf276b3451cee1d289b8b07ac0f40ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216540"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Jak zobrazit kubernetes protokoly, události a pod metriky v reálném čase

Azure Monitor pro kontejnery zahrnuje funkci Živých dat (preview), což je pokročilá diagnostická funkce, která umožňuje přímý přístup k protokolům kontejnerů služby Azure Kubernetes (AKS) (stdout/stderror), událostem a metrikám podů. Poskytuje přímý přístup `kubectl logs -c`k `kubectl get` , `kubectl top pods`události a . Podokno konzoly zobrazuje protokoly, události a metriky generované modulem kontejneru, které dále pomáhají při řešení problémů v reálném čase.

Tento článek obsahuje podrobný přehled a pomůže vám pochopit, jak tuto funkci používat. 

>[!NOTE]
>Clustery AKS povolené jako [privátní clustery](https://azure.microsoft.com/updates/aks-private-cluster/) nejsou touto funkcí podporovány. Tato funkce závisí na přímém přístupu k rozhraní API Kubernetes prostřednictvím proxy serveru z vašeho prohlížeče. Povolení zabezpečení sítě blokovat Rozhraní API Kubernetes z tohoto proxy serveru bude blokovat tento provoz. 

>[!NOTE]
>Tato funkce je dostupná ve všech oblastech Azure, včetně Azure China. V současné době není k dispozici v Azure US Government.

Nápovědu k nastavení nebo řešení potíží s funkcí Živá data (preview) naleznete v našem [průvodci nastavením](container-insights-livedata-setup.md). Tato funkce přímo přístup Kubernetes API a další informace o modelu ověřování lze nalézt [zde](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

## <a name="live-data-preview-functionality-overview"></a>Přehled funkcí živých dat (preview)

### <a name="search"></a>Search

![Příklad filtru podokna konzoly živých dat](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

Funkce Živá data (náhled) zahrnuje funkce vyhledávání. V poli **Hledat** můžete filtrovat výsledky zadáním klíčového slova nebo výrazu a všechny odpovídající výsledky jsou zvýrazněny, aby bylo možné provést rychlou kontrolu. Při prohlížení událostí můžete navíc omezit výsledky pomocí **filtrační** pilulky nalezené napravo od vyhledávacího panelu. V závislosti na tom, jaký prostředek jste vybrali, pilulka uvádí Pod, Obor názvů nebo cluster, ze kterých chcete vybrat.  

### <a name="scroll-lock-and-pause"></a>Posunout zámek a pozastavit 

Chcete-li pozastavit automatické posouvání a řídit chování podokna, což vám umožní ručně procházet nová data číst, můžete použít **možnost Posunout.** Chcete-li znovu povolit automatické posouvání, jednoduše znovu vyberte možnost **Posunout.** Načítání dat protokolu nebo událostí můžete také pozastavit výběrem možnosti **Pozastavit** a až budete připraveni pokračovat, jednoduše vyberte **přehrát**.  

![Podokno živé datové konzoly pozastaví živé zobrazení](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>Doporučujeme pouze pozastavení nebo pozastavení automatického posouvání na krátkou dobu při řešení problému. Tyto požadavky mohou mít vliv na dostupnost a omezení rozhraní API Kubernetes ve vašem clusteru. 

>[!IMPORTANT]
>Během provozu této funkce nejsou trvale uložena žádná data. Všechny informace zachycené během relace budou odstraněny, když zavřete prohlížeč nebo od něj odejdete. Data zůstávají přítomna pouze pro vizualizaci uvnitř pětiminutového okna funkce metriky; všechny metriky starší než pět minut jsou také odstraněny. Živé datové (preview) vyrovnávací paměti dotazy v rámci přiměřené limity využití paměti (musí být konkrétnější zde, co je rozumné?). 

## <a name="view-logs"></a>Zobrazení protokolů

Můžete zobrazit data protokolu v reálném čase tak, jak jsou generována modulu kontejneru ze zobrazení **Uzly**, **Řadiče**a **Kontejnery.** Chcete-li zobrazit data protokolu, proveďte následující kroky.

1. Na webu Azure Portal přejděte do skupiny prostředků clusteru AKS a vyberte prostředek AKS.

2. Na řídicím panelu clusteru AKS v části **Monitorování** na levé straně zvolte **Přehledy**. 

3. Vyberte kartu **Uzly**, **Řadiče**nebo **Kontejnery.**

4. Vyberte objekt z mřížky výkonu a v podokně vlastností, které se nachází na pravé straně, vyberte možnost **Zobrazit živá data (náhled).** Pokud je cluster AKS nakonfigurován s jedním přihlášením pomocí služby Azure AD, budete vyzváni k ověření při prvním použití během této relace prohlížeče. Vyberte svůj účet a dokončete ověřování pomocí Azure.  

    >[!NOTE]
    >Při zobrazení dat z pracovního prostoru Log Analytics výběrem **možnosti Zobrazit v analytice** z podokna vlastností budou výsledky hledání protokolu potenciálně zobrazovat **uzly**, **sady démonů**, **sady replik**, **úlohy**, **úlohy Cron**, **pody**a **kontejnery,** které již nemusí existovat. Pokus o hledání v protokolech pro kontejner, `kubectl` který není k dispozici v také nezdaří zde. Další informace o zobrazení historických protokolů, událostí a metrik se dozvíte v části [Zobrazení v analytice.](container-insights-log-search.md#search-logs-to-analyze-data)  

Po úspěšném ověření se podokno konzoly Živá data (náhled) zobrazí pod mřížkou dat výkonu, kde můžete zobrazit data protokolu v nepřetržitém datovém proudu. Pokud indikátor stavu načítání zobrazuje zelenou značku zaškrtnutí, která je zcela vpravo od podokna, znamená to, že data lze načíst a začne streamovat do konzole.  

![Možnost zobrazení zobrazení podokna vlastností uzlu](./media/container-insights-livedata-overview/node-properties-pane.png)  

V názvu podokna se zobrazí název podu, do které je kontejner seskupen.

## <a name="view-events"></a>Zobrazení událostí

Data událostí v reálném čase můžete zobrazit tak, jak jsou generována modulem kontejneru z zobrazení **Uzly**, **Řadiče**, **Kontejnery**a **Nasazení (náhled),** když je vybrán kontejner, pod, uzel, ReplicaSet, DaemonSet, úloha, CronJob nebo nasazení. Chcete-li zobrazit události, proveďte následující kroky.

1. Na webu Azure Portal přejděte do skupiny prostředků clusteru AKS a vyberte prostředek AKS.

2. Na řídicím panelu clusteru AKS v části **Monitorování** na levé straně zvolte **Přehledy**. 

3. Vyberte kartu **Uzly**, **Řadiče**, **Kontejnery**nebo **Nasazení (náhled).**

4. Vyberte objekt z mřížky výkonu a v podokně vlastností, které se nachází na pravé straně, vyberte možnost **Zobrazit živá data (náhled).** Pokud je cluster AKS nakonfigurován s jedním přihlášením pomocí služby Azure AD, budete vyzváni k ověření při prvním použití během této relace prohlížeče. Vyberte svůj účet a dokončete ověřování pomocí Azure.  

    >[!NOTE]
    >Při zobrazení dat z pracovního prostoru Log Analytics výběrem **možnosti Zobrazit v analytice** z podokna vlastností budou výsledky hledání protokolu potenciálně zobrazovat **uzly**, **sady démonů**, **sady replik**, **úlohy**, **úlohy Cron**, **pody**a **kontejnery,** které již nemusí existovat. Pokus o hledání v protokolech pro kontejner, `kubectl` který není k dispozici v také nezdaří zde. Další informace o zobrazení historických protokolů, událostí a metrik se dozvíte v části [Zobrazení v analytice.](container-insights-log-search.md#search-logs-to-analyze-data)  

Po úspěšném ověření se pod mřížkou dat výkonu zobrazí podokno konzoly Živá data (náhled). Pokud indikátor stavu načítání zobrazuje zelenou značku zaškrtnutí, která je zcela vpravo od podokna, znamená to, že data lze načíst a začne streamovat do konzole. 
    
Pokud byl vybraný objekt kontejner, vyberte možnost **Události** v podokně. Pokud jste vybrali uzel, pod nebo ovladač, je automaticky vybráno zobrazení událostí. 

![Události zobrazení podokna vlastností řadiče](./media/container-insights-livedata-overview/controller-properties-live-event.png)  

V názvu podokna se zobrazí název podu, se kterým je kontejner seskupen.

### <a name="filter-events"></a>Události filtru 

Při prohlížení událostí můžete navíc omezit výsledky pomocí **filtrační** pilulky nalezené napravo od vyhledávacího panelu. V závislosti na tom, jaký prostředek jste vybrali, pilulka uvádí Pod, Obor názvů nebo cluster, ze kterých chcete vybrat.  

## <a name="view-metrics"></a>Zobrazit metriky 

Data metrik v reálném čase můžete zobrazit tak, jak jsou generována modulem kontejneru ze zobrazení **uzlů** nebo **řadičů,** pouze v případě, že je vybrán **pod.** Chcete-li zobrazit metriky, proveďte následující kroky.

1. Na webu Azure Portal přejděte do skupiny prostředků clusteru AKS a vyberte prostředek AKS.

2. Na řídicím panelu clusteru AKS v části **Monitorování** na levé straně zvolte **Přehledy**. 

3. Vyberte kartu **Uzly** nebo **Řadiče.**

4. Vyberte objekt **Pod** z mřížky výkonu a v podokně vlastností, které se nachází na pravé straně, vyberte možnost **Zobrazit živá data (náhled).** Pokud je cluster AKS nakonfigurován s jedním přihlášením pomocí služby Azure AD, budete vyzváni k ověření při prvním použití během této relace prohlížeče. Vyberte svůj účet a dokončete ověřování pomocí Azure.  

    >[!NOTE]
    >Při zobrazení dat z pracovního prostoru Log Analytics výběrem **možnosti Zobrazit v analytice** z podokna vlastností budou výsledky hledání protokolu potenciálně zobrazovat **uzly**, **sady démonů**, **sady replik**, **úlohy**, **úlohy Cron**, **pody**a **kontejnery,** které již nemusí existovat. Pokus o hledání v protokolech pro kontejner, `kubectl` který není k dispozici v také nezdaří zde. Další informace o zobrazení historických protokolů, událostí a metrik se dozvíte v části [Zobrazení v analytice.](container-insights-log-search.md#search-logs-to-analyze-data)  

Po úspěšném ověření se pod mřížkou dat výkonu zobrazí podokno konzoly Živá data (náhled). Metrická data se načtou a začnou streamovat do konzole pro prezentaci ve dvou grafech. V názvu podokna se zobrazí název podu, do které je kontejner seskupen.

![Příklad metriky Pod zobrazit](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>Další kroky

- Informace o používání Služby Azure Monitor a monitorování dalších aspektů clusteru AKS najdete v [tématu Zobrazení stavu služby Azure Kubernetes](container-insights-analyze.md).

- Chcete-li zobrazit předdefinované dotazy a příklady pro vytvoření výstrah, vizualizací nebo provedení další analýzy clusterů, zobrazte [příklady dotazů](container-insights-log-search.md#search-logs-to-analyze-data) protokolu.
