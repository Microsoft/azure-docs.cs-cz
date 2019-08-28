---
title: Monitorování a řešení potíží ze strany HANA na SAP HANA v Azure (velké instance) | Microsoft Docs
description: Monitorování a řešení potíží ze strany HANA na SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2c596a876817f0a501025c37e463a7eebb55cf2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099828"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Sledování a řešení potíží na straně HANA

Aby bylo možné efektivně analyzovat problémy související s SAP HANA v Azure (velké instance), je užitečné omezit hlavní příčinu problému. SAP publikovalo velkou část dokumentace, která vám může pomáhat.

Příslušné Nejčastější dotazy týkající se SAP HANA výkonu najdete v následujících komentářích ke službě SAP:

- [Poznámky SAP #2222200 – Nejčastější dotazy: SAP HANA sítě](https://launchpad.support.sap.com/#/notes/2222200)
- [Poznámky SAP #2100040 – Nejčastější dotazy: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [Poznámky SAP #199997 – Nejčastější dotazy: SAP HANA paměti](https://launchpad.support.sap.com/#/notes/2177064)
- [Poznámky SAP #200000 – Nejčastější dotazy: Optimalizace výkonu SAP HANA](https://launchpad.support.sap.com/#/notes/2000000)
- [Poznámky SAP #199930 – Nejčastější dotazy: SAP HANA v/v analýzy](https://launchpad.support.sap.com/#/notes/1999930)
- [Poznámky SAP #2177064 – Nejčastější dotazy: SAP HANA restartování služby a zhroucení](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>Výstrahy SAP HANA

V prvním kroku zkontrolujete aktuální protokoly výstrah SAP HANA. V SAP HANA studiu, otevřete **konzolu pro správu: Generoval Zobrazit: všechny výstrahy**. Tato karta zobrazí všechny výstrahy SAP HANA pro konkrétní hodnoty (volná fyzická paměť, využití procesoru atd.), které spadají mimo nastavený minimální a maximální prahovou hodnotu. Ve výchozím nastavení se kontroly automaticky aktualizují každých 15 minut.

![V SAP HANA studiu, otevřete konzolu pro správu: Generoval Zobrazit: všechny výstrahy](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>Procesor

V případě aktivované výstrahy z důvodu nesprávného nastavení prahové hodnoty je nastaveno obnovení na výchozí hodnotu nebo přiměřenou prahovou hodnotu.

![Obnovit výchozí hodnotu nebo přiměřenou prahovou hodnotu](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Následující výstrahy můžou indikovat problémy s prostředky procesoru:

- Využití CPU hostitele (výstraha 5)
- Poslední operace uložených bodů (výstraha 28)
- Doba trvání uloženého bodu (výstraha 54)

U SAP HANA databáze si můžete všimnout vysoké spotřeby procesoru z jedné z následujících možností:

- Výstraha 5 (využití CPU hostitele) je vyvolána pro aktuální nebo minulé využití procesoru.
- Zobrazené využití CPU na obrazovce Přehled

![Zobrazení využití CPU na obrazovce s přehledem](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Graf zatížení může zobrazit vysokou spotřebu procesoru nebo vysokou spotřebu v minulosti:

![Graf zatížení může zobrazit vysokou spotřebu procesoru nebo vysokou spotřebu v minulosti.](./media/troubleshooting-monitoring/image4-load-graph.png)

Výstraha aktivovaná z důvodu vysokého využití procesoru může být způsobena několika důvody, mimo jiné: provádění určitých transakcí, načítání dat, úlohy, které nereagují, dlouho běžící příkazy SQL a chybný výkon dotazu (například s ČERNOBÍLými datovými krychlemi HANA.

Přečtěte si téma řešení potíží s SAPHANA:[ V případě problémů souvisejících s](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) procesorem a řešeními najdete podrobný postup řešení potíží.

## <a name="operating-system"></a>Operační systém

Jednou z nejdůležitějších kontrol SAP HANA v systému Linux je ujistit se, že jsou transparentní velké stránky zakázané, viz téma [SAP Note #2131662 – průhledné velké stránky (THP) na SAP HANA serverech](https://launchpad.support.sap.com/#/notes/2131662).

- Můžete kontrolovat, jestli jsou transparentní obrovský stránky povolené pomocí následujícího příkazu Linux: **Cat/sys/kernel/mm/Transparent\_hugepage/Enabled** .
- Pokud je _vždycky_ uzavřený v závorkách jako dole, znamená to, že jsou povolené průhledné velké stránky: [Always] madvise nikdy; Pokud _nikdy_ není uzavřený v závorkách jako dole, znamená to, že průhledné obrovský stránky jsou zakázané: vždycky madvise [nikdy].

Následující příkaz pro Linux by neměl vracet hodnotu Nothing: **ot-QA | grep ulimit.** Pokud se zdá, že _ulimit_ je nainstalovaný, okamžitě ho odinstalujte.

## <a name="memory"></a>Memory (Paměť)

Můžete si všimnout, že velikost paměti přidělená SAP HANA databáze je vyšší, než se očekávalo. Následující výstrahy indikují problémy s vysokým využitím paměti:

- Využití fyzické paměti hostitele (výstraha 1)
- Využití paměti názvového serveru (výstraha 12)
- Celkové využití paměti v tabulkách úložiště sloupců (výstraha 40)
- Využití paměti službami (výstraha 43)
- Využití paměti pro hlavní úložiště tabulek úložiště sloupců (výstraha 45)
- Soubory výpisu paměti za běhu (výstraha 46)

Přečtěte si téma řešení potíží s SAPHANA:[ Na webu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) problémy s pamětí najdete podrobné kroky pro řešení potíží.

## <a name="network"></a>Síť

Přečtěte si informace v tématu [SAP note #2081065 – řešení potíží s SAP HANA sítě](https://launchpad.support.sap.com/#/notes/2081065) a postup řešení potíží se sítí v této poznámce SAP.

1. Analyzuje se doba odezvy mezi serverem a klientem.
  A. Spusťte [ _\_síťové\_klienty_](https://launchpad.support.sap.com/#/notes/1969700)SQL Script Hana _._
  
2. Analýza komunikace mezi uzly.
  A. Spusťte [ _\_síťové\_služby_](https://launchpad.support.sap.com/#/notes/1969700)SQL Script Hana _._

3. Spusťte příkaz Linux Command **ifconfig** (výstup ukazuje, zda dochází ke ztrátám paketů).
4. Spusťte příkaz **tcpdump**pro Linux.

Pomocí nástroje open source [IPERF](https://iperf.fr/) Tool (nebo podobné) můžete také měřit skutečný výkon sítě aplikace.

Přečtěte si téma řešení potíží s SAPHANA:[ Podrobnosti o výkonu sítě a](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) potížích s připojením pro účely podrobného postupu pro řešení potíží.

## <a name="storage"></a>Storage

V perspektivě pro koncové uživatele se aplikace (nebo systém jako celek) spouští sluggishly, neodpovídá nebo může dokonce reagovat, pokud dojde k problémům s výkonem vstupně-výstupních operací. Na kartě **svazky** v nástroji SAP HANA Studio můžete zobrazit připojené svazky a které svazky používají jednotlivé služby.

![Na kartě svazky v SAP HANA studiu vidíte připojené svazky a jaké svazky používají jednotlivé služby.](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Připojené svazky v dolní části obrazovky vám pomůžou zobrazit podrobnosti o svazcích, jako jsou soubory a statistiky I/O.

![Připojené svazky v dolní části obrazovky vidíte podrobnosti o svazcích, jako jsou soubory a statistiky I/O.](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Přečtěte si téma řešení potíží s SAPHANA:[ Hlavní příčiny a řešení](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) [související s vstupně-výstupními operacemi a SAP HANA řešení potíží: Hlavní příčiny pro řešení potíží související](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) s diskem a jeho řešení.

## <a name="diagnostic-tools"></a>Diagnostické nástroje

Proveďte SAP HANA kontrolu stavu prostřednictvím služby Hana\_Configuration\_Minichecks. Tento nástroj vrátí potenciálně kritické technické problémy, které by měly být v SAP HANA studiu již vyvolány jako výstrahy.

Informace o SAP HANA a stažení souboru. zip s příkazy SQL, které jsou připojené k této poznámce, najdete v tématu [SAP Note #1969700 – kolekce příkazů SQL](https://launchpad.support.sap.com/#/notes/1969700) . Uložte tento soubor. zip na místní pevný disk.

V SAP HANA studiu klikněte pravým tlačítkem na kartě **Systémové informace** na sloupec **název** a vyberte **importovat příkazy SQL**.

![V SAP HANA studiu klikněte pravým tlačítkem na kartě Systémové informace na sloupec název a vyberte Importovat příkazy SQL.](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Vyberte soubor. zip s příkazy jazyka SQL uložený místně a složka s odpovídajícími příkazy SQL se naimportuje. V tomto okamžiku lze pomocí těchto příkazů SQL spustit i mnoho různých diagnostických kontrol.

Pokud třeba chcete vyzkoušet SAP HANA požadavky na šířku pásma replikace systému, klikněte pravým tlačítkem na příkaz  ****Šířka pásma** v části replikace: Šířka** pásma a vyberte **otevřít** v konzole SQL.

Úplný příkaz SQL otevře a pak provede změnu vstupních parametrů (oddíl úprav).

![Úplný příkaz SQL otevře úpravu vstupních parametrů (oddíl úprav), který se má změnit a následně provést.](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Dalším příkladem je kliknutí pravým tlačítkem myši na **příkazy v části replikace: Přehled**. V místní nabídce vyberte **Execute (spustit** ):

![Dalším příkladem je kliknutí pravým tlačítkem myši na příkazy v části replikace: Přehled. V místní nabídce vyberte spustit.](./media/troubleshooting-monitoring/image9-import-statements-c.png)

To má za následek informace, které pomáhají při řešení potíží:

![Výsledkem bude informace, které vám pomůžou při řešení potíží.](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Proveďte stejnou konfiguraci pro Minichecks\_konfigurace\_Hana a ve sloupci _C_ (kritické) vyhledejte libovolné značky _X_ .

Ukázkové výstupy:

**Konfigurace\_HanaMiniChecks\_rev 102.01 + 1 pro obecné SAP HANA kontroly.\_**

![Konfigurace\_Hana\_MiniChecks\_rev 102.01 + 1 pro obecné kontroly SAP HANA](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**Přehled\_služeb\_pro službu Hana** , který nabízí přehled o tom, co jsou aktuálně spuštěné služby SAP HANA.

![Přehled\_služeb\_Hana a přehled o tom, co jsou aktuálně spuštěné služby SAP HANA](./media/troubleshooting-monitoring/image12-services-overview.png)

**Statistika\_služeb\_Hana** pro SAP HANA informace o službách (CPU, paměť atd.).

![Statistika\_služby\_Hana pro informace o službě SAP HANA](./media/troubleshooting-monitoring/image13-services-statistics.png)

**\_Přehled\_konfigurace Hana Rev110 + pro obecné informace o instanci SAP HANA.\_**

![Přehled\_konfigurace\_Hana Rev110 + pro obecné informace o instanci SAP HANA\_](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**\_Parametry\_konfigurace Hana Rev70 + pro kontrolu parametrů SAP HANA.\_**

![Parametry\_konfigurace\_Hana Rev70 + pro kontrolu parametrů SAP HANA\_](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Další postup**

- [Nastavení vysoké dostupnosti najdete v SUSE pomocí STONITH](ha-setup-with-stonith.md).