---
title: Monitorování aplikací
description: Naučte se monitorovat aplikace v Azure App Service pomocí Azure Portal. Pochopení kvót a metrik, které jsou hlášeny.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 04/23/2020
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: bf230032afe80680dc392c2a74da2a5aef381983
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586090"
---
# <a name="monitor-apps-in-azure-app-service"></a>Monitorování aplikací v Azure App Service
[Azure App Service](./overview.md) poskytuje integrované funkce monitorování pro webové aplikace, mobilní aplikace a aplikace API v [Azure Portal](https://portal.azure.com).

V Azure Portal můžete zkontrolovat *kvóty* a *metriky* pro plán aplikací a App Service a nastavit *výstrahy* a pravidla automatického *škálování* na základě metrik.

## <a name="understand-quotas"></a>Principy kvót

Pro aplikace, které jsou hostované v App Service, se vztahují určitá omezení na prostředky, které můžou používat. Omezení jsou definovaná plánem App Service, který je přidružený k aplikaci.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Pokud je aplikace hostovaná v *bezplatném* nebo *sdíleném* plánu, omezení prostředků, které může aplikace používat, jsou definovaná pomocí kvót.

Pokud je aplikace hostována v plánu *Basic*, *Standard* nebo *Premium* , omezení prostředků, které mohou používat, jsou nastavena podle *velikosti* (malá, střední, Velká) a *počtu instancí* (1, 2, 3 atd.) v plánu App Service.

Kvóty pro bezplatné nebo sdílené aplikace jsou:

| Kvóta | Description |
| --- | --- |
| **PROCESOR (krátký)** | Počet PROCESORů povolených pro tuto aplikaci v intervalu 5 minut. Tato kvóta se resetuje každých pět minut. |
| **CPU (den)** | Celková velikost procesoru povoleného pro tuto aplikaci za den. Tato kvóta se resetuje každých 24 hodin v půlnoci UTC. |
| **Memory (Paměť)** | Celková velikost paměti, která je pro tuto aplikaci povolena. |
| **Šířka pásma** | Celková velikost odchozí šířky pásma, která je pro tuto aplikaci povolená za den. Tato kvóta se resetuje každých 24 hodin v půlnoci UTC. |
| **Filesystem** | Celková velikost povoleného úložiště. |

Jediná kvóta platí pro aplikace, které jsou hostované v systému souborů *Basic*, *Standard* a *Premium* .

Další informace o konkrétních kvótách, omezeních a funkcích, které jsou dostupné pro různé App Service SKU, najdete v tématu [omezení služby předplatného Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Vynucení kvót

Pokud aplikace překročí kvótu *CPU (krátký)*, *CPU (den)* nebo *šířky pásma* , aplikace se zastaví až do obnovení kvóty. Během této doby všechny příchozí žádosti vyplývají z důvodu chyby HTTP 403.

![chybová zpráva 403][http403]

Pokud dojde k překročení kvóty paměti aplikace, aplikace se dočasně zastaví.

Pokud je překročena kvóta systému souborů, jakákoli operace zápisu se nezdařila. Selhání operací zápisu zahrnuje všechny zápisy do protokolů.

Můžete zvýšit nebo odebrat kvóty z aplikace tím, že upgradujete plán App Service.

## <a name="understand-metrics"></a>Principy metrik

> [!NOTE]
> **Využití systému souborů** je nová metrika, která je globálně nasazená. neočekávají se žádná data, pokud vaše aplikace není hostovaná v App Service Environment.
> 

> [!IMPORTANT]
> **Průměrná doba odezvy** bude zastaralá, aby nedocházelo k záměně s agregacemi metrik. Použijte **dobu odezvy** jako náhradu.

> [!NOTE]
> Metriky pro aplikaci zahrnují požadavky na web SCM aplikace (Kudu).  To zahrnuje požadavky na zobrazení logstream webu pomocí Kudu.  Žádosti Logstream můžou zahrnovat několik minut, což bude mít vliv na metriky času požadavku.  Uživatelé by měli být vědomi tohoto vztahu při použití těchto metrik s logikou automatického škálování.
> 

Metriky poskytují informace o chování aplikace nebo naplánování App Service.

V případě aplikace jsou dostupné metriky:

| Metric | Popis |
| --- | --- |
| **Doba odezvy** | Doba, kterou aplikace bude obsluhovat požadavky (v sekundách). |
| **Průměrná doba odezvy (nepoužívané)** | Průměrná doba, jakou trvalo, aby aplikace sloužila požadavkům (v sekundách) |
| **Průměrná pracovní sada paměti** | Průměrná velikost paměti, kterou aplikace používá, v megabajtech (MiB). |
| **Připojení** | Počet vázaných soketů existujících v izolovaném prostoru (w3wp.exe a jeho podřízených procesech).  Vázaný soket se vytvoří voláním rozhraní API BIND ()/Connect () a zůstane až do chvíle, kdy je tento soket uzavřený pomocí CloseHandle ()/closesocket (). |
| **Čas procesoru** | Množství procesoru spotřebovaného aplikací v řádu sekund Další informace o této metrikě najdete v části [čas procesoru vs. procento využití procesoru](#cpu-time-vs-cpu-percentage). |
| **Aktuální sestavení** | Aktuální počet sestavení načtených napříč všemi doménami AppDomain v této aplikaci. |
| **Data v** | Množství příchozí šířky pásma spotřebované aplikací v souboru MiB. |
| **Výstupní data** | Velikost odchozí šířky pásma spotřebované aplikací v souboru MiB. |
| **Využití systému souborů** | Množství využití v bajtech podle sdílené složky úložiště |
| **Generace paměti gen 0** | Počet, kolikrát jsou objekty generace 0 od spuštění procesu aplikace shromažďovány z paměti. GC vyšší generace zahrnuje všechny GC nižší generace.|
| **Generace paměti 1. generace** | Kolikrát jsou objekty generace 1 od spuštění procesu aplikace uvolněny z paměti. GC vyšší generace zahrnuje všechny GC nižší generace.|
| **Uvolňování paměti 2. generace** | Kolikrát jsou objekty generace 2 od spuštění procesu aplikace uvolněny z paměti.|
| **Počet popisovačů** | Celkový počet popisovačů aktuálně otevřených procesem aplikace.|
| **Stav kontroly stavu** | Průměrný stav mezi instancemi aplikace v plánu App Service.|
| **2xx http** | Počet požadavků, které mají za následek stavový kód HTTP ≥ 200, ale < 300. |
| **3xx http** | Počet požadavků, které mají za následek stavový kód HTTP ≥ 300, ale < 400. |
| **HTTP 401** | Počet požadavků, které mají za následek stavový kód HTTP 401 |
| **HTTP 403** | Počet požadavků, které mají za následek stavový kód HTTP 403 |
| **HTTP 404** | Počet požadavků, které mají za následek stavový kód HTTP 404 |
| **Http 406** | Počet požadavků, které mají za následek stavový kód HTTP 406 |
| **4xx http** | Počet požadavků, které mají za následek stavový kód HTTP ≥ 400, ale < 500. |
| **Chyby serveru http** | Počet požadavků, které mají za následek stavový kód HTTP ≥ 500, ale < 600. |
| **IO – ostatní bajty za sekundu** | Rychlost, s jakou proces aplikace vydává bajty, do vstupně-výstupních operací, které neobsahují data, jako jsou například operace řízení.|
| **V/v – ostatní operace za sekundu** | Frekvence, s jakou proces aplikace vystavuje vstupně-výstupní operace, které nejsou operacemi čtení nebo zápisu.|
| **Bajty čtení v/v za sekundu** | Rychlost, s jakou proces aplikace čte bajty z vstupně-výstupních operací.|
| **Vstupně-výstupní operace čtení za sekundu** | Rychlost, s jakou proces aplikace vydává vstupně-výstupní operace čtení|
| **Vstupně-výstupní bajty zápisu za sekundu** | Rychlost, kterou proces aplikace zapisuje bajty na vstupně-výstupní operace.|
| **Vstupně-výstupní operace zápisu za sekundu** | Frekvence, s jakou proces aplikace vydává vstupně-výstupní operace zápisu.|
| **Pracovní sada paměti** | Aktuální velikost paměti, kterou aplikace používá, v souboru MiB. |
| **Soukromé bajty** | Soukromé bajty jsou aktuální velikost (v bajtech) paměti, kterou proces aplikace přidělen, který nelze sdílet s jinými procesy.|
| **Žádosti** | Celkový počet požadavků bez ohledu na výsledný kód stavu HTTP. |
| **Žádosti ve frontě aplikací** | Počet požadavků ve frontě žádostí aplikace.|
| **Počet vláken** | Počet vláken, která jsou aktuálně aktivní v procesu aplikace.|
| **Celkový počet domén aplikace** | Aktuální počet objektů třídy AppDomains zavedených v této aplikaci.|
| **Celkový počet uvolněných domén aplikace** | Celkový počet objektů třídy AppDomains uvolněných od spuštění aplikace.|


V případě plánu App Service jsou dostupné metriky:

> [!NOTE]
> Metriky plánu App Service jsou dostupné jenom pro plány v úrovních *Basic*, *Standard* a *Premium* .
> 

| Metric | Popis |
| --- | --- |
| **Procento využití procesoru** | Průměrně využitý procesor napříč všemi instancemi plánu. |
| **Procento paměti** | Průměrná paměť využitá ve všech instancích plánu. |
| **Data v** | Průměrná příchozí šířka pásma využitá ve všech instancích plánu. |
| **Výstupní data** | Průměrná odchozí šířka pásma využitá ve všech instancích plánu. |
| **Délka fronty disku** | Průměrný počet požadavků na čtení i zápis, které byly zařazeny do fronty úložiště. Maximální délka fronty disku je označením aplikace, která může být zpomalena z důvodu nadměrného vstupně-výstupních operací disku. |
| **Délka fronty http** | Průměrný počet požadavků HTTP, které měly být zařazeny do fronty před jejich splněním. Vysoká nebo rostoucí délka fronty HTTP je příznakem plánu v oblasti vysokého zatížení. |

### <a name="cpu-time-vs-cpu-percentage"></a>Čas procesoru vs. procento využití procesoru
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

K dispozici jsou dvě metriky, které odrážejí využití CPU:

**Čas procesoru**: hodí se pro aplikace hostované v bezplatných nebo sdílených plánech, protože jedna z jejich kvót je definována v minutách procesoru, který používá aplikace.

**Procento využití procesoru**: užitečné pro aplikace hostované v plánech Basic, Standard a Premium, protože je možné je škálovat. Procento využití procesoru je dobrým označením celkového využití napříč všemi instancemi.

## <a name="metrics-granularity-and-retention-policy"></a>Členitost metrik a zásady uchovávání informací
Metriky pro aplikaci a plán služby App Service jsou protokolovány a agregovány službou a uchovávají se v [souladu s těmito pravidly](../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics).

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorování kvót a metrik v Azure Portal
Pokud chcete zkontrolovat stav různých kvót a metrik, které mají vliv na aplikaci, použijte [Azure Portal](https://portal.azure.com).

![Graf kvót v Azure Portal][quotas]

Pokud chcete najít kvóty, vyberte **Nastavení**  >  **kvóty**. V grafu můžete zkontrolovat: 
1. Název kvóty
1. Interval jeho resetování.
1. Jeho aktuální limit.
1. Její aktuální hodnota.

![Graf metriky v Azure Portal ][metrics] můžete získat přístup k metrikám přímo ze stránky **Přehled** prostředků. Tady uvidíte grafy reprezentující některé metriky aplikací.

Kliknutím na některý z těchto grafů přejdete k zobrazení metrik, kde můžete vytvářet vlastní grafy, dotazovat se na různé metriky a mnohem víc. 

Další informace o metrikách najdete v tématu [monitorování metrik služby](../azure-monitor/data-platform.md).

## <a name="alerts-and-autoscale"></a>Výstrahy a automatické škálování
Metriky pro aplikaci nebo plán App Service můžete připojit k výstrahám. Další informace naleznete v tématu [Doručování oznámení o upozorněních](../azure-monitor/alerts/alerts-classic-portal.md).

App Service aplikace hostované v plánech Basic a vyšší App Service podporují automatické škálování. Díky automatickému škálování můžete nakonfigurovat pravidla, která monitorují metriky App Service plánu. Pravidla mohou zvýšit nebo snížit počet instancí, což může poskytovat další prostředky podle potřeby. Pravidla vám také pomůžou ušetřit peníze při zřizování aplikace.

Další informace o automatickém škálování najdete v tématu [Jak škálovat](../azure-monitor/autoscale/autoscale-get-started.md) a [osvědčené postupy pro Azure monitor automatického škálování](../azure-monitor/autoscale/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
