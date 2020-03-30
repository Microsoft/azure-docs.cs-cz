---
title: Monitorování aplikací
description: Zjistěte, jak monitorovat aplikace ve službě Azure App Service pomocí portálu Azure. Seznamte se s kvótami a metrikami, které jsou hlášeny.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: d84340730391abd7dba4d13202503d37941c09b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500434"
---
# <a name="monitor-apps-in-azure-app-service"></a>Monitorování aplikací ve službě Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) poskytuje integrované funkce monitorování pro webové aplikace, mobilní aplikace a aplikace API na [webu Azure Portal](https://portal.azure.com).

Na webu Azure Portal můžete zkontrolovat *kvóty* a *metriky* pro plán aplikace a služby App Service a nastavit *výstrahy* a metriky založené na pravidlech *automatického škálování.*

## <a name="understand-quotas"></a>Porozumět kvótaem

Aplikace, které jsou hostované ve službě App Service, podléhají určitým omezením prostředkům, které můžou používat. Omezení jsou definována plánem služby App Service, který je přidružený k aplikaci.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Pokud je aplikace hostovaná ve *volném* nebo *sdíleném* plánu, omezení prostředků, které aplikace může používat, jsou definována kvótami.

Pokud je aplikace hostovaná v plánu *Basic*, *Standard*nebo *Premium,* limity prostředků, které mohou používat, jsou nastaveny podle *velikosti* (malé, střední, velké) a *počtu instancí* (1, 2, 3 a tak dále) plánu služby App Service.

Kvóty pro bezplatné nebo sdílené aplikace jsou:

| Kvóta | Popis |
| --- | --- |
| **PROCESOR (krátký)** | Množství procesoru povolené pro tuto aplikaci v intervalu 5 minut. Tato kvóta se resetuje každých pět minut. |
| **CPU (den)** | Celková částka procesoru povolená pro tuto aplikaci za den. Tato kvóta se resetuje každých 24 hodin o půlnoci UTC. |
| **Paměti** | Celkové množství paměti povolené pro tuto aplikaci. |
| **Šířka pásma** | Celková velikost odchozí šířky pásma povolená pro tuto aplikaci za den. Tato kvóta se resetuje každých 24 hodin o půlnoci UTC. |
| **Filesystem** | Celková povolená velikost úložiště. |

Jedinou kvótou použitelnou pro aplikace, které jsou hostované v *basicu*, *standardu*a *premium,* je souborový systém.

Další informace o konkrétních kvótách, omezeních a funkcích dostupných pro různé sloky služby App Service najdete v [tématu Limity služby předplatného Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Vynucení kvót

Pokud aplikace překročí *kvótu procesoru (krátký),* *PROCESOR (Den)* nebo *šířky pásma,* aplikace se zastaví, dokud se kvóta neobnoví. Během této doby všechny příchozí požadavky za následek chybu HTTP 403.

![403 chybová zpráva][http403]

Pokud je překročena kvóta paměti aplikace, aplikace se restartuje.

Pokud je překročena kvóta souborového systému, všechny operace zápisu se nezdaří. Selhání operace zápisu zahrnují všechny zápisy do protokolů.

Upgradem plánu služby App Service můžete zvýšit nebo odebrat kvóty z aplikace.

## <a name="understand-metrics"></a>Principy metrik

> [!NOTE]
> **Využití systému souborů** je nová metrika, která se zavádí globálně, žádná data se neočekávají, pokud jste nebyli zařazeni na seznam povolených pro privátní náhled.
> 

> [!IMPORTANT]
> **Průměrná doba odezvy** bude zastaralá, aby nedošlo k záměně s agregacemi metrik. Použijte **dobu odezvy** jako náhradu.

Metriky poskytují informace o chování aplikace nebo plánu služby App Service.

Pro aplikaci jsou dostupné metriky:

| Metrika | Popis |
| --- | --- |
| **Doba odezvy** | Doba, kterou aplikace využila k zobrazování požadavků v sekundách. |
| **Průměrná doba odezvy (zastaralá)** | Průměrná doba, po kterou aplikace může zobrazovat žádosti, v sekundách. |
| **Průměrná pracovní sada paměti** | Průměrné množství paměti používané aplikací v megabajtech (MiB). |
| **Připojení** | Počet vázaných soketů existujících v izolovaném prostoru (w3wp.exe a jeho podřízené procesy).  Vázaná soket je vytvořena voláním rozhraní API bind()/connect() a zůstane, dokud není tato soket uzavřena s CloseHandle()/closesocket(). |
| **Čas procesoru** | Množství procesoru spotřebované ho aplikace, v sekundách. Další informace o této metrike naleznete [v tématu čas procesoru vs procento procesoru](#cpu-time-vs-cpu-percentage). |
| **Aktuální sestavení** | Aktuální počet sestavení načtených ve všech doménách aplikací v této aplikaci. |
| **Data v** | Množství příchozí šířky pásma spotřebované aplikací v MiB. |
| **Data Out** | Množství odchozí šířky pásma spotřebované aplikací v MiB. |
| **Využití systému souborů** | Procento kvóty souborového systému spotřebované aplikací. |
| **Gen 0 Uvolňování paměti** | Počet, kolikrát generace 0 objekty jsou uvolněny od začátku procesu aplikace. Vyšší generace G zahrnuje všechny gpc nižší generace.|
| **Gen 1 Uvolňování paměti** | Počet, kolikrát generace 1 objekty jsou uvolněny od začátku procesu aplikace. Vyšší generace G zahrnuje všechny gpc nižší generace.|
| **Gen 2 Uvolňování paměti** | Počet, kolikrát generace 2 objekty jsou uvolněny od začátku procesu aplikace.|
| **Počet popisovačů** | Celkový počet popisovačů aktuálně otevřených procesem aplikace.|
| **Http 2xx** | Počet požadavků, což má za následek stavový kód HTTP ≥ 200, ale < 300. |
| **Http 3xx** | Počet požadavků, což má za následek stavový kód HTTP ≥ 300, ale < 400. |
| **Http 401** | Počet požadavků, které vedou k pHTTP 401 stavový kód. |
| **Http 403** | Počet požadavků, které vedou k pHTTP 403 stavový kód. |
| **Http 404** | Počet požadavků, které vedou k pHTTP 404 stavový kód. |
| **Http 406** | Počet požadavků, které vedou k pHTTP 406 stavový kód. |
| **Http 4xx** | Počet požadavků, což má za následek stavový kód HTTP ≥ 400, ale < 500. |
| **Chyby serveru HTTP** | Počet požadavků, což má za následek stavový kód HTTP ≥ 500, ale < 600. |
| **IO ostatní bajty za sekundu** | Rychlost, s jakou proces aplikace vydává bajty do vstupně-v.i. operací, které nezahrnují data, jako jsou například operace řízení.|
| **Vi další operace za sekundu** | Rychlost, s jakou proces aplikace vydává vstupně-va operace, které nejsou operace čtení nebo zápisu.|
| **Čtení bajtů v iza sekundě** | Rychlost, jakou proces aplikace čte bajty z vstupně-vana operací.|
| **Operace čtení vi za sekundu** | Rychlost, s jakou proces aplikace vydává operace čtení vstupně-va.|
| **IO zápis bajtů za sekundu** | Rychlost, jakou proces aplikace zapisuje bajty do vstupně-vapových operací.|
| **Operace zápisu vi za sekundu** | Rychlost, s jakou proces aplikace vydává operace vstupně-vstupně-va zápisu.|
| **Pracovní sada paměti** | Aktuální množství paměti používané aplikací v MiB. |
| **Soukromé bajty** | Soukromé bajty je aktuální velikost paměti v bajtech, kterou proces aplikace přidělil a kterou nelze sdílet s jinými procesy.|
| **Žádosti** | Celkový počet požadavků bez ohledu na jejich výsledný stavový kód HTTP. |
| **Požadavky ve frontě aplikací** | Počet požadavků ve frontě žádostí o aplikaci.|
| **Počet vláken** | Počet podprocesů aktuálně aktivních v procesu aplikace.|
| **Celkový počet domén aplikací** | Aktuální počet AppDomains načten v této aplikaci.|
| **Celkový počet uvolněných domén aplikací** | Celkový počet AppDomains uvolněna od začátku aplikace.|


Pro plán služby App Service jsou k dispozici metriky:

> [!NOTE]
> Metriky plánu služby App Service jsou k dispozici pouze pro plány na úrovni *Basic*, *Standard*a *Premium.*
> 

| Metrika | Popis |
| --- | --- |
| **Procento procesoru** | Průměrný procesor používaný ve všech instancích plánu. |
| **Procento paměti** | Průměrná paměť použitá ve všech instancích plánu. |
| **Data v** | Průměrná příchozí šířka pásma použitá ve všech instancích plánu. |
| **Data Out** | Průměrná odchozí šířka pásma používaná ve všech instancích plánu. |
| **Délka fronty disku** | Průměrný počet požadavků na čtení i zápis, které byly zařazeny do fronty v úložišti. Vysoká délka fronty disku je označení aplikace, která může být zpomalení z důvodu nadměrné vstupně-videa disku. |
| **Délka fronty Http** | Průměrný počet požadavků HTTP, které musely sedět ve frontě před splněním. Vysoká nebo rostoucí délka fronty HTTP je příznakem plánu při velkém zatížení. |

### <a name="cpu-time-vs-cpu-percentage"></a>Čas procesoru vs procento procesoru
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Existují dvě metriky, které odrážejí využití procesoru:

**Čas procesoru**: Užitečné pro aplikace hostované ve volných nebo sdílených plánech, protože jedna z jejich kvót je definována v minutách procesoru používaných aplikací.

**Procento procesoru**: Užitečné pro aplikace hostované v základních, standardních a prémiových plánech, protože je lze škálovat. Procento procesoru je dobrým ukazatelem celkového využití ve všech instancích.

## <a name="metrics-granularity-and-retention-policy"></a>Zásady rozlišovací schopnost metrik a uchovávání informací
Metriky pro plán aplikace a služby aplikace jsou protokolovány a agregovány službou s následujícími podrobnými informacemi a zásadami uchovávání informací:

* **Minutové** metriky rozlišovací schopnost jsou uchovávány po dobu 30 hodin.
* **Metriky** rozlišovací schopnost hodin jsou uchovávány po dobu 30 dnů.
* **Metriky** rozlišovací schopnost den jsou uchovávány po dobu 30 dnů.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorování kvót a metrik na webu Azure Portal
Pokud chcete zkontrolovat stav různých kvót a metrik, které ovlivňují aplikaci, přejděte na [portál Azure](https://portal.azure.com).

![Graf kvót na webu Azure Portal][quotas]

Chcete-li najít kvóty, vyberte **možnost Nastavení** > **kvót**. V grafu si můžete prohlédnout: 
1. Název kvóty.
1. Jeho interval resetování.
1. Jeho současný limit.
1. Jeho aktuální hodnota.

![Metrikový graf na][metrics] portálu Azure: K metrikám máte přístup přímo na stránce **Přehled** prostředků. Zde uvidíte grafy představující některé metriky aplikací.

Kliknutím na některý z těchto grafů se dostanete do zobrazení metrik, kde můžete vytvářet vlastní grafy, dotazovat se na různé metriky a mnoho dalšího. 

Další informace o metrikách najdete v [tématu Sledování metrik služby](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Výstrahy a automatické škálování
Metriky pro aplikaci nebo plán služby App Service lze připojit k výstrahám. Další informace naleznete v tématu [Doručování oznámení o upozorněních](../monitoring-and-diagnostics/insights-alerts-portal.md).

Aplikace služby App Service hostované v základních nebo vyšších plánech služby App Service podporují automatické škálování. Pomocí automatického škálování můžete nakonfigurovat pravidla, která monitorují metriky plánu služby App Service. Pravidla můžete zvýšit nebo snížit počet instancí, které mohou poskytnout další prostředky podle potřeby. Pravidla vám taky můžou pomoct ušetřit, když je aplikace nadměrně zřizováná.

Další informace o automatickéškálování najdete v [tématu Jak škálovat](../monitoring-and-diagnostics/insights-how-to-scale.md) a [doporučené postupy pro automatické škálování Azure Monitoru](../azure-monitor/platform/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png