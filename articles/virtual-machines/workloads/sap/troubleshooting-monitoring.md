---
title: Řešení potíží a monitorování SAP HANA v Azure (velké instance) | Microsoft Docs
description: Řešení potíží a monitorovat SAP HANA na Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 41fbeb848d7d97e5ee41a2221b69cc88380dc1e1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657192"
---
# <a name="how-to-troubleshoot-and-monitor-sap-hana-large-instances-on-azure"></a>Postup řešení potíží a monitorovat SAP HANA (velké instance) na Azure


## <a name="monitoring-in-sap-hana-on-azure-large-instances"></a>Monitorování v SAP HANA v Azure (velké instance)

Se neliší od jiných IaaS nasazení SAP HANA v Azure (velké instance), je nutné sledovat operačním systémem a aplikace je činnosti a jak tyto využívat následující prostředky:

- Procesor
- Memory (Paměť)
- Šířka pásma sítě
- Místo na disku

Jak pomocí Azure Virtual Machines, je třeba zjistit, jestli jsou dostatečná třídy prostředků s názvem výše, nebo jestli tyto získat vyčerpané. Tady je podrobněji na každém z různých tříd:

**Využití prostředků procesoru:** poměr, která SAP definována pro určité úlohy proti HANA se nevynutí a ujistěte se, že by měla být k dispozici fungovat prostřednictvím data, která je uložená v paměti dostatek prostředků procesoru. Nicméně může být případech, kde HANA spotřebovává velké množství provádění dotazů procesoru z důvodu chybějící indexy nebo podobné problémy. To znamená, že je třeba sledovat využití prostředků procesoru HANA velké instance jednotky a také spotřebovávají určitým službám HANA prostředků procesoru.

**Využití paměti:** je důležité monitorovat z v rámci HANA i mimo HANA na jednotce. V rámci HANA sledujte, jak data spotřebovává přidělené paměti, aby bylo možné zůstat v požadované velikosti pokynů SAP HANA. Chcete monitorovat využití paměti na úrovni Instance velké a ujistěte se, že další nainstalovaný jiný HANA softwaru nepodporuje využívat příliš mnoho paměti a proto pokouší s HANA paměti.

**Šířka pásma sítě:** brány virtuální sítě Azure je omezena šířka pásma dat Přesun do virtuální sítě Azure, takže je vhodné pro monitorování dat přijatých všechny virtuální počítače pro Azure v rámci virtuální sítě a pokuste se zjistit, jak jste se k omezení Azure skladová položka brány samoobslužné braný. Na jednotce HANA velké Instance ho dává smysl pro příchozí a odchozí síťový provoz i monitorování a ke sledování svazků, které jsou zpracovány v čase.

**Místo na disku:** využívání místa na disku obvykle zvyšuje v čase. Existuje mnoho důvodů pro to, ale většina všech: datový svazek se zvyšuje, provádění zálohování transakčního protokolu, ukládání trasovacích souborů a provádění úložiště snímků. Proto je důležité monitorovat využití místa na disku a spravovat místo na disku spojené s jednotkou HANA velké Instance.

Pro **typu II SKU** velké instancí HANA, server se dodává s předem načtené systému diagnostické nástroje. Můžete využít tyto diagnostické nástroje k provedení kontroly stavu systému. Spusťte následující příkaz, který generuje stavu zkontrolujte soubor protokolu v /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Při práci s týmem Microsoft Support chcete vyřešit nějaký problém, může vám také požádáni o poskytnutí soubory protokolů pomocí tyto diagnostické nástroje. Soubor pomocí následujícího příkazu můžete zip.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```


## <a name="monitoring-and-troubleshooting-from-hana-side"></a>Monitorování a řešení potíží s ze strany HANA

Chcete-li efektivně analyzovat problémy související s SAP HANA v Azure (velké instance), je užitečné zúžit hlavní příčinu problému. SAP publikovali velké množství dokumentaci.

Použít nejčastější dotazy týkající se výkonu SAP HANA naleznete v následující poznámky k SAP:

- [Poznámka SAP #2222200 – nejčastější dotazy: SAP HANA sítě](https://launchpad.support.sap.com/#/notes/2222200)
- [Poznámka SAP #2100040 – nejčastější dotazy: SAP HANA procesoru](https://launchpad.support.sap.com/#/notes/0002100040)
- [Poznámka SAP #199997 – nejčastější dotazy: SAP HANA paměti](https://launchpad.support.sap.com/#/notes/2177064)
- [Poznámka SAP #200000 – nejčastější dotazy: Optimalizace výkonu SAP HANA](https://launchpad.support.sap.com/#/notes/2000000)
- [Poznámka SAP #199930 – nejčastější dotazy: SAP HANA vstupně-výstupních operací analýzy](https://launchpad.support.sap.com/#/notes/1999930)
- [Poznámka SAP #2177064 – nejčastější dotazy: SAP HANA službu restartovat a dojde k chybě](https://launchpad.support.sap.com/#/notes/2177064)

**Výstrahy SAP HANA**

Jako první krok zkontrolujte aktuální výstrahy protokoly SAP HANA. V systému SAP HANA Studio přejděte do **konzole pro správu: upozornění: Zobrazit: všechny výstrahy**. Na této kartě se zobrazí všechny výstrahy SAP HANA pro konkrétní hodnoty (Volná fyzická paměť, využití procesoru atd.), které spadají mimo sadu minimální a maximální prahové hodnoty. Ve výchozím nastavení kontroluje se automaticky aktualizují každých 15 minut.

![V systému SAP HANA Studio přejděte do konzoly pro správu: upozornění: zobrazení: všechny výstrahy](./media/troubleshooting-monitoring/image1-show-alerts.png)

**CPU**

Pro aktivaci z důvodu nesprávné prahová hodnota nastavení výstrahu řešení je resetovat na výchozí hodnotu nebo přijatelnější prahovou hodnotu.

![Obnoví na výchozí hodnotu nebo přijatelnější prahová hodnota](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Tyto výstrahy může znamenat problémy prostředků procesoru:

- Využití procesoru hostitele (výstrah 5)
- Poslední operace úložného bodu (výstrah 28)
- Doba trvání uloženého bodu (výstrah 54)

Můžete si všimnout vysoké spotřeby procesoru ve vaší databázi SAP HANA z jednoho z následujících akcí:

- Výstrahy 5 (využití procesoru hostitele) se vyvolá pro aktuální nebo posledních využití procesoru
- Zobrazit využití procesoru na obrazovce Přehled

![Zobrazit využití procesoru na obrazovce Přehled](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Grafu, pro zatížení může zobrazit vysoké využití procesoru, nebo vysoké spotřebu v minulosti:

![Grafu, pro zatížení může zobrazit vysoké využití procesoru, nebo vysoké spotřebu v minulosti.](./media/troubleshooting-monitoring/image4-load-graph.png)

Výstraha aktivuje z důvodu vysoké využití procesoru může být způsobeno několik důvodů, včetně, mimo jiné: provádění určitých transakcí, načítání dat, ukotvených úloh, dlouho běžící příkazů jazyka SQL a výkonu chybných dotazů (například s BW na HANA datové krychle).

Odkazovat [řešení potíží s SAP HANA: související způsobí, že využití procesoru a řešení](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) lokality podrobné kroky řešení problémů.

**Operační systém**

Jedním z nejdůležitějších kontroly pro SAP HANA v systému Linux se ujistěte, že transparentní obrovské stránky je zakázáno, najdete v článku [SAP Poznámka #2131662 – transparentní obrovské stránky (THP) na serverech SAP HANA](https://launchpad.support.sap.com/#/notes/2131662).

- Můžete zkontrolovat, zda jsou povoleny transparentní obrovské stránky pomocí následujícího příkazu Linux: **cat /sys/kernel/mm/transparent\_hugepage/povoleno**
- Pokud _vždy_ je uzavřený v závorkách, jak je uvedeno níže, znamená to povolení transparentní obrovské stránky: [vždy] madvise nikdy; Pokud _nikdy_ je uzavřený v závorkách, jak je uvedeno níže, znamená to, že transparentní velký Stránky jsou zakázány: vždy madvise [nikdy]

Příkaz Linux by měla vrátit nic: **ot. / min - qa | grep ulimit.** Pokud se zobrazí _ulimit_ je nainstalovaný, odinstalovat okamžitě.

**Paměť**

Může sledovat, zda je množství paměti přidělené databázi SAP HANA vyšší, než se očekávalo. Tyto výstrahy signalizují potíže s velké množství paměti:

- Využití hostitele fyzické paměti (výstrahy 1)
- Využití paměti název serveru (upozornění 12)
- Celkové využití paměti úložiště sloupec tabulky (výstrah 40)
- Využití paměti služby (výstrah 43)
- Využití paměti hlavní úložiště sloupec úložiště tabulek (45 výstrah)
- Soubory modulu runtime výpisu (výstrah 46)

Odkazovat [řešení potíží s SAP HANA: problémy s pamětí](http://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) lokality podrobné kroky řešení problémů.

**Síť**

Odkazovat na [SAP Poznámka #2081065 – řešení potíží s SAP HANA sítě](https://launchpad.support.sap.com/#/notes/2081065) a provádět sítě řešení potíží s kroky v této Poznámka SAP.

1. Analýza odezvy čas mezi serverem a klientem.
  A. Spusťte skript SQL [ _HANA\_sítě\_klienti_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analýza internodium komunikace.
  A. Spuštění skriptu SQL [ _HANA\_sítě\_služby_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Spusťte příkaz Linux **ifconfig** (výstup zobrazuje, pokud se vyskytnou ztráty paketů).
4. Spusťte příkaz Linux **tcpdump**.

Rovněž použijte open source [IPERF](https://iperf.fr/) nástroje (nebo podobnou) k měření výkonu sítě reálné aplikaci.

Odkazovat [řešení potíží s SAP HANA: výkon sítě a problémů s připojením k](http://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) lokality podrobné kroky řešení problémů.

**Úložiště**

Z hlediska koncový uživatel aplikace (nebo na systém jako celek) běží pomalu, neodpovídá nebo můžete dokonce se zdá, že zablokuje, pokud dochází k potížím s výkonem vstupně-výstupní operace. V **svazky** kartě v SAP HANA Studio uvidíte připojené svazky a svazky, které jsou používány jednotlivých služeb.

![Na kartě svazky v SAP HANA Studio uvidíte připojené svazky a svazky, které jsou používány každé služby](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Připojené svazky v dolní části obrazovky se zobrazí podrobnosti o na svazcích, jako jsou soubory a vstupně-výstupních operací statistiky.

![Připojené svazky v dolní části obrazovky se zobrazí podrobnosti o na svazcích, jako jsou soubory a vstupně-výstupních operací statistiky](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Odkazovat [řešení potíží s SAP HANA: vstupně-výstupních operací souvisejících hlavní příčiny a řešení](http://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) a [řešení potíží s SAP HANA: disku související hlavní příčiny a řešení](http://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) lokality podrobné kroky řešení problémů.

**Diagnostické nástroje**

Provést kontrolu stavu SAP HANA prostřednictvím HANA\_konfigurace\_Minichecks. Tento nástroj vrátí potenciálně kritickým technických problémů, které by měl mít bylo vyvoláno už jako výstrahy v SAP HANA Studio.

Odkazovat na [SAP Poznámka #1969700 – kolekce příkaz SQL pro SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) a stáhněte si soubor SQL Statements.zip poznámka připojena. Uložte tento soubor .zip na místní pevný disk.

V nástroji SAP HANA Studio na **informace o systému** kartě, klikněte pravým tlačítkem myši **název** sloupce a vyberte **příkazů SQL Import**.

![V systému SAP HANA Studio, na kartě informace o systému klikněte pravým tlačítkem na název sloupce a vyberte Import SQL příkazy](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Vyberte soubor SQL Statements.zip uložené místně, a budou importovány do složky s odpovídajících příkazů SQL. V tomto okamžiku mnoho různých diagnostiky kontroly lze spustit pomocí těchto příkazů SQL.

Například k testování replikace systému SAP HANA nároky na šířku pásma, klikněte pravým tlačítkem myši **šířky pásma** příkaz pod **replikace: šířky pásma** a vyberte **otevřete** v Konzola SQL.

Příkaz SQL otevře povolení vstupních parametrů (změna oddílu), změnit a potom spuštěn.

![Příkaz SQL otevře povolení vstupních parametrů (změna oddílu), změnit a potom spuštěn](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Dalším příkladem je klikněte pravým tlačítkem na v příkazech v části **replikace: Přehled**. Vyberte **Execute** v místní nabídce:

![Dalším příkladem je klikněte pravým tlačítkem na v příkazech v části replikace: Přehled. Vyberte možnost spustit z místní nabídky](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Výsledkem je informace, které pomáhají při řešení problémů:

![Tato akce způsobí informace, které pomáhají při řešení problémů](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Totéž proveďte pro HANA\_konfigurace\_Minichecks a zkontrolujte všechny _X_ označí v _C_ sloupce (kritické).

Ukázka výstupu:

**HANA\_konfigurace\_MiniChecks\_Rev102.01 + 1** pro kontroluje obecné SAP HANA.

![HANA\_konfigurace\_MiniChecks\_Rev102.01 + 1 pro obecné kontroly SAP HANA](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_služby\_přehled** přehled co SAP HANA aktuálně běží služby.

![HANA\_služby\_přehled přehled co SAP HANA aktuálně běží služby](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_služby\_statistiky** pro SAP HANA služby informace (procesor, paměť, atd.).

![HANA\_služby\_statistiku pro SAP HANA informace o služby ](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_konfigurace\_přehled\_Rev110 +** obecné informace o instanci SAP HANA.

![HANA\_konfigurace\_přehled\_Rev110 + obecné informace o instanci SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_konfigurace\_parametry\_Rev70 +** Zkontrolujte parametry SAP HANA.

![HANA\_konfigurace\_parametry\_Rev70 + Zkontrolujte parametry SAP HANA](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

