---
title: Zobrazit živá data (Preview) s Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje zobrazení Kubernetes protokolů, událostí a metrik v reálném čase bez použití kubectl v Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 12/17/2020
ms.custom: references_regions
ms.openlocfilehash: 3655ff8e5879aa4113753b5529c1e484fb079401
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609502"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Jak zobrazit protokoly Kubernetes, události a metriky pod v reálném čase

Azure Monitor for Containers zahrnuje funkci živá data (Preview), která je pokročilou diagnostickou, která umožňuje přímý přístup k vašim protokolům kontejnerů AKS (Azure Kubernetes Service), událostem a metrikám pod. Zpřístupňuje přímý přístup k `kubectl logs -c` `kubectl get` událostem a `kubectl top pods` . V podokně konzoly se zobrazují protokoly, události a metriky vygenerované modulem kontejnerů, které vám pomůžou při řešení problémů v reálném čase.

Tento článek poskytuje podrobný přehled a pomůže vám pochopit, jak tuto funkci používat.

Nápovědu k nastavení funkce živá data (Preview) najdete v naší [příručce k instalaci](container-insights-livedata-setup.md). Tato funkce přímo přistupuje k rozhraní Kubernetes API. Další informace o modelu ověřování najdete [tady](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

## <a name="view-deployment-live-logs-preview"></a>Zobrazit živé protokoly nasazení (Preview)
Následující postup slouží k zobrazení živých protokolů pro nasazení, která jsou součástí AKS clusterů, které nejsou monitorovány Azure Monitor for Containers. Pokud váš cluster používá Azure Monitor pro kontejnery, použijte následující postup k zobrazení živých dat pro uzly, řadiče, kontejnery a nasazení.

1. V Azure Portal přejděte do skupiny prostředků clusteru AKS a vyberte svůj prostředek AKS.

2. V části **Kubernetes Resources (prostředky** ) v nabídce vyberte **úlohy** .

3. Vyberte nasazení na kartě **nasazení** .

4. V nabídce nasazení vyberte **Live Logs (Preview)** .

5. Vyberte položku pod a spusťte shromažďování živých dat.

    [![Živé protokoly nasazení](./media/container-insights-livedata-overview/live-data-deployment.png)](./media/container-insights-livedata-overview/live-data-deployment.png#lightbox)

## <a name="view-logs"></a>Zobrazení protokolů

Data protokolu v reálném čase můžete zobrazit tak, jak jsou generována modulem kontejneru z **uzlů**, **řadičů** a zobrazení **kontejnerů** . Chcete-li zobrazit data protokolu, proveďte následující kroky.

1. V Azure Portal přejděte do skupiny prostředků clusteru AKS a vyberte svůj prostředek AKS.

2. Na řídicím panelu clusteru AKS v části **monitorování** na levé straně vyberte **přehledy**.

3. Vyberte kartu **uzly**, **řadiče** nebo **kontejnery** .

4. Vyberte objekt z mřížky výkonu a v podokně vlastnosti, které se nachází na pravé straně, vyberte možnost **Zobrazit živá data (Preview)** . Pokud je cluster AKS nakonfigurovaný s jednotným přihlašováním pomocí Azure AD, zobrazí se výzva k ověření při prvním použití během této relace prohlížeče. Vyberte svůj účet a dokončete ověřování pomocí Azure.

    >[!NOTE]
    >Když si z pracovního prostoru Log Analytics vyberete možnost **Zobrazit v analýze** v podokně vlastnosti, výsledky hledání v protokolu budou potenciálně zobrazovat **uzly**, **sady démonů**, **sady replik**, **úlohy**, **úlohy cron**, **lusky** a **kontejnery** , které už možná neexistují. Při pokusu o hledání protokolů pro kontejner, který není dostupný v, `kubectl` se tady zobrazí také chyba. Další informace o prohlížení historických protokolů, událostech a metrikách najdete v části [Přehled funkce Analýza](container-insights-log-search.md#search-logs-to-analyze-data) .

Po úspěšném ověření se podokno konzoly živá data (Preview) zobrazí pod datovou mřížkou výkonu, kde můžete zobrazit data protokolu v souvislém datovém proudu. Pokud se v indikátoru stavu načítání zobrazuje zelený symbol zaškrtnutí, který je na pravé straně podokna, znamená to, že se data dají načíst a zahájí streamování do konzoly.

![Možnost zobrazení dat v podokně vlastností uzlu](./media/container-insights-livedata-overview/node-properties-pane.png)

Název podokna zobrazuje název pod tím, kde je kontejner seskupen.

## <a name="view-events"></a>Zobrazení událostí

Data události v reálném čase můžete zobrazit tak, jak jsou generována modulem kontejneru z **uzlů**, **řadičů**, **kontejnerů** a **nasazení (Preview)** zobrazení, když je vybrána možnost kontejner, pod, uzel, ReplicaSet, DaemonSet, úloha, CronJob nebo nasazení. Chcete-li zobrazit události, proveďte následující kroky.

1. V Azure Portal přejděte do skupiny prostředků clusteru AKS a vyberte svůj prostředek AKS.

2. Na řídicím panelu clusteru AKS v části **monitorování** na levé straně vyberte **přehledy**.

3. Vyberte kartu **uzly**, **řadiče**, **kontejnery** nebo **nasazení (Preview)** .

4. Vyberte objekt z mřížky výkonu a v podokně vlastnosti, které se nachází na pravé straně, vyberte možnost **Zobrazit živá data (Preview)** . Pokud je cluster AKS nakonfigurovaný s jednotným přihlašováním pomocí Azure AD, zobrazí se výzva k ověření při prvním použití během této relace prohlížeče. Vyberte svůj účet a dokončete ověřování pomocí Azure.

    >[!NOTE]
    >Když si z pracovního prostoru Log Analytics vyberete možnost **Zobrazit v analýze** v podokně vlastnosti, výsledky hledání v protokolu budou potenciálně zobrazovat **uzly**, **sady démonů**, **sady replik**, **úlohy**, **úlohy cron**, **lusky** a **kontejnery** , které už možná neexistují. Při pokusu o hledání protokolů pro kontejner, který není dostupný v, `kubectl` se tady zobrazí také chyba. Další informace o prohlížení historických protokolů, událostech a metrikách najdete v části [Přehled funkce Analýza](container-insights-log-search.md#search-logs-to-analyze-data) .

Po úspěšném ověření se pod mřížkou dat výkonu zobrazí podokno konzoly živá data (Preview). Pokud se v indikátoru stavu načítání zobrazuje zelený symbol zaškrtnutí, který je na pravé straně podokna, znamená to, že se data dají načíst a zahájí streamování do konzoly.

Pokud je objekt, který jste vybrali, kontejner, vyberte v podokně možnost **události** . Pokud jste vybrali uzel, pod nebo kontroler, události se vyberou automaticky.

![Události zobrazení podokna vlastností kontroleru](./media/container-insights-livedata-overview/controller-properties-live-event.png)

Název podokna zobrazuje název pod tím, kde je kontejner seskupen.

### <a name="filter-events"></a>Události filtru

Při prohlížení událostí můžete kromě toho omezit výsledky pomocí **filtru** , který se nachází na pravé straně panelu hledání. V závislosti na tom, jaký prostředek jste vybrali, zobrazuje funkce pilla seznam pod, oborem názvů nebo clusteru, ze kterého se má vybrat.

## <a name="view-metrics"></a>Zobrazit metriky

Data metrik v reálném čase můžete zobrazit tak, jak jsou generována modulem kontejneru z **uzlů** nebo zobrazení **řadiče** pouze v případě, že je vybrána možnost **pod** . Chcete-li zobrazit metriky, proveďte následující kroky.

1. V Azure Portal přejděte do skupiny prostředků clusteru AKS a vyberte svůj prostředek AKS.

2. Na řídicím panelu clusteru AKS v části **monitorování** na levé straně vyberte **přehledy**.

3. Vyberte buď kartu **uzly** nebo **řadiče** .

4. V mřížce výkonu vyberte objekt **pod** a v podokně Vlastnosti klikněte na tlačítko **Zobrazit živá data (Preview)** . Pokud je cluster AKS nakonfigurovaný s jednotným přihlašováním pomocí Azure AD, zobrazí se výzva k ověření při prvním použití během této relace prohlížeče. Vyberte svůj účet a dokončete ověřování pomocí Azure.

    >[!NOTE]
    >Když si z pracovního prostoru Log Analytics vyberete možnost **Zobrazit v analýze** v podokně vlastnosti, výsledky hledání v protokolu budou potenciálně zobrazovat **uzly**, **sady démonů**, **sady replik**, **úlohy**, **úlohy cron**, **lusky** a **kontejnery** , které už možná neexistují. Při pokusu o hledání protokolů pro kontejner, který není dostupný v, `kubectl` se tady zobrazí také chyba. Další informace o prohlížení historických protokolů, událostech a metrikách najdete v části [Přehled funkce Analýza](container-insights-log-search.md#search-logs-to-analyze-data) .

Po úspěšném ověření se pod mřížkou dat výkonu zobrazí podokno konzoly živá data (Preview). Data metriky se načtou a zahájí streamování do konzoly pro prezentaci v těchto dvou grafech. Název podokna zobrazuje název pod tím, kde je kontejner seskupen.

![Příklad zobrazení metrik pod](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)

## <a name="using-live-data-views"></a>Používání zobrazení dynamických dat
Následující části popisují funkčnost, kterou můžete použít v různých zobrazeních dynamických dat.

### <a name="search"></a>Hledat
Funkce Live data (Preview) zahrnuje funkce hledání. V poli **hledání** můžete výsledky filtrovat zadáním klíčového slova nebo termínu. všechny vyhovující výsledky jsou zvýrazněny, aby umožňovaly rychlou kontrolu. Při prohlížení událostí můžete kromě toho omezit výsledky pomocí **filtru** , který se nachází na pravé straně panelu hledání. V závislosti na tom, jaký prostředek jste vybrali, zobrazuje funkce pilla seznam pod, oborem názvů nebo clusteru, ze kterého se má vybrat.

![Příklad filtru podokna konzoly živé dat](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

![Příklad filtru podokna konzoly živé dat pro nasazení](./media/container-insights-livedata-overview/live-data-deployment-search.png)

### <a name="scroll-lock-and-pause"></a>Posunout zámek a pozastavit

Chcete-li pozastavit automatické posouvání a ovládat chování podokna, což vám umožní ruční procházení nových čtených dat, můžete použít možnost **Scroll** . Chcete-li znovu povolit automatické posouvání, stačí znovu vybrat možnost **Scroll** . Můžete také pozastavit načítání dat protokolů nebo událostí výběrem možnosti **pozastavit** a až budete připraveni na obnovení, stačí vybrat možnost **Přehrát**.

![Živý pohled na pozastavit podokno konzoly živé dat](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

![Živý pohled na nasazení v podokně konzoly živá data](./media/container-insights-livedata-overview/live-data-deployment-pause.png)



>[!IMPORTANT]
>Při řešení problému doporučujeme pozastavit nebo pozastavit automatické posouvání po krátkou dobu. Tyto požadavky mohou mít vliv na dostupnost a omezování rozhraní Kubernetes API v clusteru.

>[!IMPORTANT]
>Během operace této funkce nejsou trvale uloženy žádná data. Všechny informace zaznamenané během relace se odstraní, když zavřete prohlížeč nebo z něj opustíte. Data zůstávají jenom pro vizualizaci v pěti minutách okna funkce metriky. odstraní se také všechny metriky starší než pět minut. Dynamická data (Preview) vyrovnávací paměti se dotazují v rozumném limitu využití paměti.

## <a name="next-steps"></a>Další kroky

- Pokud chcete pokračovat v učení, jak používat Azure Monitor a monitorovat další aspekty clusteru AKS, přečtěte si téma [zobrazení stavu služby Azure Kubernetes](container-insights-analyze.md).

- Podívejte se na [příklady dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) , kde najdete předdefinované dotazy a příklady pro vytváření výstrah, vizualizací nebo provádění dalších analýz vašich clusterů.
