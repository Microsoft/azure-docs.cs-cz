---
title: Řešení monitorování v oblasti VMware ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Zjistěte, jak může řešení VMware Monitoring pomoci spravovat protokoly a monitorovat hostitele ESXi.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: c1622ef16155206d779c6d703fc7da568d233e7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664775"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Řešení Monitorování VMware (zastaralé) ve službě Azure Monitor

![Symbol VMware](./media/vmware/vmware-symbol.png)

> [!NOTE]
> Řešení VMware Monitoring bylo zastaralé.  Zákazníci, kteří již nainstalovali řešení, jej mohou nadále používat, ale monitorování společnosti VMware nelze přidat do žádných nových pracovních prostorů.

Řešení Monitorování VMware ve službě Azure Monitor je řešení, které vám pomůže vytvořit centralizovaný přístup k protokolování a monitorování pro velké protokoly VMware. Tento článek popisuje, jak můžete řešit problémy, zachytit a spravovat hostitele ESXi na jednom místě pomocí řešení. S řešením můžete vidět podrobná data pro všechny hostitele ESXi na jednom místě. Můžete zobrazit nejvyšší počet událostí, stav a trendy hostitelů virtuálních vapů a ESXi, které jsou k dispozici prostřednictvím protokolů hostitelů ESXi. Řešení potíží můžete zobrazit a prohledat centralizované protokoly hostitelů ESXi. A můžete vytvořit výstrahy na základě vyhledávacích dotazů protokolu.

Řešení používá nativní funkce sysloghostitele ESXi k nabízení dat do cílového virtuálního virtuálního provozu, který má agenta Log Analytics. Řešení však nezapisuje soubory do syslogu v rámci cílového virtuálního virtuálního soudu. Agent Log Analytics otevře port 1514 a poslouchá to. Jakmile obdrží data, agent Analýzy protokolů odešle data do Azure Monitoru.

## <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace.

* Přidejte do předplatného řešení VMware Monitoring pomocí postupu popsaného v [části Instalace monitorovacího řešení](../insights/solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Podporovaní hostitelé vsystému VMware ESXi
vSphere ESXi Host 5.5, 6.0 a 6.5

#### <a name="prepare-a-linux-server"></a>Příprava linuxového serveru
Vytvořte virtuální počítač s operačním systémem Linux pro příjem všech dat syslogu z hostitelů ESXi. [Agent Linuxu Log Analytics](../learn/quick-collect-linux-computer.md) je sběrné místo pro všechna data syslogu hostitele ESXi. Můžete použít více hostitelů ESXi k předávání protokolů na jeden server Linux, jako v následujícím příkladu.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![tok syslogu](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfigurace kolekce syslogu
1. Nastavte předávání syslogu pro VSphere. Podrobné informace, které vám pomohou nastavit předávání syslogů, naleznete [v tématu Konfigurace syslogu na ESXi 5.0 a vyšší (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Přejděte na **web ESXi Host Configuration** > **Software** > **Advanced Settings** > **Syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. Do pole *Syslog.global.logHost* přidejte linuxový server a číslo portu *1514*. Příklad: `tcp://hostname:1514` nebo `tcp://123.456.789.101:1514`
1. Otevřete bránu firewall hostitele ESXi pro syslog. **Esxi Host Configuration** > **Software** > **Security Profile** > **Firewall** a open **Properties**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Zkontrolujte konzolu vSphere a ověřte, zda je syslog správně nastaven. Na hostiteli ESXI zkontrolujte, zda je port **1514** nakonfigurován.
1. Stáhněte a nainstalujte agenta Log Analytics pro Linux na linuxový server. Další informace naleznete [v dokumentaci pro agenta Log Analytics pro Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Po instalaci agenta Log Analytics pro Linux přejděte do adresáře /etc/opt/microsoft/omsagent/sysconf/omsagent.d a zkopírujte soubor vmware_esxi.conf do adresáře /etc/opt/microsoft/omsagent/conf/omsagent.d a změňte vlastníka/skupinu a oprávnění souboru. Například:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Restartujte agenta Log Analytics `sudo /opt/microsoft/omsagent/bin/service_control restart`pro Linux spuštěním .
1. Otestujte připojení mezi serverem Linux a `nc` hostitelem ESXi pomocí příkazu na hostiteli ESXi. Například:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. Na webu Azure Portal proveďte `VMware_CL`dotaz protokolu pro aplikaci . Když Azure Monitor shromažďuje data syslogu, zachová formát syslogu. Na portálu jsou zachycena některá konkrétní pole, například *Název hostitele* a *Název_procesu*.  

    ![type](./media/vmware/type.png)  

    Pokud jsou výsledky hledání protokolu zobrazení podobné obrázku výše, můžete použít řídicí panel řešení Monitorování vsystému VMware.  

## <a name="vmware-data-collection-details"></a>Podrobnosti o shromažďování dat společnosti VMware
Řešení VMware Monitoring shromažďuje různé metriky výkonu a data protokolu od hostitelů ESXi pomocí agentů Log Analytics pro Linux, které jste povolili.

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o způsobu shromažďování dat.

| platforma | Agent Log Analytics pro Linux | Agent SCOM | Azure Storage | SCOM požadované? | Data agenta SCOM odeslaná prostřednictvím skupiny pro správu | sběrová frekvence |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |každé 3 minuty |

V následující tabulce jsou uvedeny příklady datových polí shromážděných řešením Monitorování společnosti VMware:

| název pole | description |
| --- | --- |
| Device_s |Úložná zařízení VMware |
| ESXIFailure_s |typy selhání |
| EventTime_t |čas, kdy došlo k události |
| HostName_s |Název hostitele ESXi |
| Operation_s |vytvoření virtuálního virtuálního mísy nebo odstranění virtuálního mísy |
| ProcessName_s |název události |
| ResourceId_s |název hostitele společnosti VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Stav VMware SCSI |
| SyslogMessage_s |Data syslogu |
| UserName_s |uživatel, který vytvořil nebo odstranil virtuální hod |
| VMName_s |název virtuálního počítače |
| Počítač |hostitelský počítač |
| TimeGenerated |čas vygenerování dat |
| DataCenter_s |Datové centrum VMware |
| StorageLatency_s |latence úložiště (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Přehled řešení VMware Monitoring
Dlaždice VMware se zobrazí v pracovním prostoru Log Analytics. Poskytuje zobrazení na vysoké úrovni všech selhání. Po kliknutí na dlaždici přejdete do zobrazení řídicího panelu.

![tile (obkladač)](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navigace v zobrazení řídicího panelu
V zobrazení řídicího panelu **společnosti VMware** jsou listy uspořádány podle:

* Počet stavů selhání
* Nejlepší hostitel podle počtu událostí
* Nejlepší počty událostí
* Aktivity virtuálních strojů
* Události hostitelského disku ESXi

![řešení1](./media/vmware/solutionview1-1.png)

![řešení2](./media/vmware/solutionview1-2.png)

Kliknutím na libovolné okno otevřete podokno hledání Analýzy protokolů, které zobrazuje podrobné informace specifické pro okno.

Zde můžete upravit dotaz protokolu a upravit jej pro něco konkrétního. Podrobnosti o vytváření dotazů protokolu najdete v [tématu Hledání dat pomocí dotazů protokolu v Azure Monitor](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Najít události hostitele ESXi
Jeden hostitel ESXi generuje více protokolů na základě jejich procesů. Řešení VMware Monitoring je centralizuje a shrnuje počty událostí. Toto centralizované zobrazení vám pomůže pochopit, který hostitel ESXi má velký objem událostí a jaké události se vyskytují nejčastěji ve vašem prostředí.

![event](./media/vmware/events.png)

Můžete přejít k podrobnostem kliknutím na hostitele ESXi nebo typ události.

Po klepnutí na název hostitele ESXi zobrazíte informace z tohoto hostitele ESXi. Pokud chcete zúžit výsledky s typem události, přidejte `“ProcessName_s=EVENT TYPE”` do vyhledávacího dotazu. Ve vyhledávacím filtru můžete vybrat **Název_procesu.** To zužuje informace pro vás.

![Vrtačka](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Vyhledání vysokých aktivit virtuálních montovek
Virtuální počítač lze vytvořit a odstranit na libovolném hostiteli ESXi. Je užitečné pro správce určit, kolik virtuálních účtů hostitel ESXi vytvoří. To zase pomáhá pochopit výkon a plánování kapacity. Sledování událostí aktivit virtuálních počítačů je při správě vašeho prostředí klíčové.

![Vrtačka](./media/vmware/vmactivities1.png)

Pokud chcete zobrazit další data pro vytváření virtuálních virtuálních informací hostitele ESXi, klikněte na název hostitele ESXi.

![Vrtačka](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Běžné dotazy protokolu
Řešení obsahuje další užitečné dotazy, které vám pomohou spravovat hostitele ESXi, jako je vysoký úložný prostor, latence úložiště a selhání cesty.

![Dotazy](./media/vmware/queries.png)


#### <a name="save-queries"></a>Uložení dotazů
Ukládání dotazů protokolu je standardní funkce ve službě Azure Monitor a může vám pomoci zachovat všechny dotazy, které jste našli užitečné. Po vytvoření dotazu, který považujete za užitečný, jej uložte klepnutím na **položku Oblíbené položky**. Uložený dotaz umožňuje jeho pozdější použití na stránce [Můj řídicí panel,](../learn/tutorial-logs-dashboards.md) kde můžete vytvořit vlastní řídicí panely.

![Zobrazení řídicího panelu dockeru](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Vytváření výstrah z dotazů
Po vytvoření dotazů můžete použít dotazy k upozornění, když dojde k určité události. Informace o tom, jak vytvářet výstrahy, najdete v tématu [Výstrahy v Log Analytics.](../platform/alerts-overview.md) Příklady upozornění na dotazy a další příklady dotazů najdete v [tématu Monitor VMware pomocí blogu Log Analytics.](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Co musím udělat s nastavením hostitele ESXi? Jaký dopad to bude mít na mé současné prostředí?
Řešení používá nativní ESXi Host Syslog předávání mechanismus. K zachycení protokolů nepotřebujete žádný další software společnosti Microsoft na hostiteli ESXi. To by mělo mít malý dopad na vaše stávající prostředí. Je však nutné nastavit předávání syslogu, což je funkce ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Musím restartovat hostitele ESXi?
Ne. Tento proces nevyžaduje restartování. V některých případě vSphere není správně aktualizovat syslog. V takovém případě se přihlaste k hostiteli ESXi a znovu načtěte syslog. Opět není nutné restartovat hostitele, takže tento proces není rušivý pro vaše prostředí.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Můžu zvýšit nebo snížit objem dat protokolu odeslaných do Log Analytics?
Ano, můžete. Nastavení úrovně protokolu hostitele ESXi můžete použít ve vSphere. Kolekce protokolů je založena na úrovni *informací.* Takže pokud chcete auditovat vytvoření nebo odstranění virtuálního počítače, musíte zachovat úroveň *informací* na Hostd. Další informace naleznete ve [znalostní bázi VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Proč Hostd neposkytuje data do služby Log Analytics? Nastavení protokolu je nastaveno na informace.
Pro časové razítko syslogu došlo k chybě hostitele ESXi. Další informace naleznete ve [znalostní bázi VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Po použití řešení hostované by měl fungovat normálně.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Můžu mít více hostitelů ESXi přeposílání dat syslogu do jednoho virtuálního počítači s omsagentem?
Ano. Můžete mít více hostitelů ESXi předávání do jednoho virtuálního počítači s omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Proč nevidím data proudící do analýzy protokolů?
Důvodů může být několik důvodů:

* Hostitel ESXi není správně odesílání dat do virtuálního zařízení běží omsagent. Chcete-li provést testování, proveďte následující kroky:

  1. Chcete-li potvrdit, přihlaste se k hostiteli ESXi pomocí ssh a spusťte následující příkaz:`nc -z ipaddressofVM 1514`

      Pokud to není úspěšné, nastavení vSphere v rozšířené konfiguraci pravděpodobně nejsou správné. Informace o tom, jak nastavit hostitele ESXi pro předávání syslogů, najdete v tématu [Konfigurace kolekce syslogů.](#configure-syslog-collection)
  1. Pokud je připojení portu syslogu úspěšné, ale stále nevidíte žádná data, načtěte syslog na hostiteli ESXi opětovným použitím ssh a spusťte následující příkaz:`esxcli system syslog reload`
* Virtuální ms s agentem Log Analytics není správně nastavena. Chcete-li to otestovat, proveďte následující kroky:

  1. Log Analytics naslouchá portu 1514. Chcete-li ověřit, zda je otevřená, spusťte následující příkaz:`netstat -a | grep 1514`
  1. Měli byste `1514/tcp` vidět port otevřený. Pokud tak neučiníte, ověřte, zda je omsagent nainstalován správně. Pokud nevidíte informace o portu, pak port syslog není otevřen a na virtuálním počítači.

    a. Ověřte, zda je agent `ps -ef | grep oms`analýzy protokolů spuštěn pomocí aplikace . Pokud není spuštěn, spusťte proces spuštěním příkazu`sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Otevřete soubor `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Ověřte, zda je správné nastavení uživatele a skupiny platné, podobně jako:`-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Pokud soubor neexistuje nebo je nesprávné nastavení uživatele a skupiny, provádějte nápravná opatření [přípravou serveru Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Další kroky
* Pomocí [dotazů protokolu](../log-query/log-query-overview.md) v Log Analytics zobrazte podrobná data hostitele voblasti VMware.
* [Vytvořte si vlastní řídicí panely](../learn/tutorial-logs-dashboards.md) zobrazující data hostitele vMware.
* [Vytvořte výstrahy,](../platform/alerts-overview.md) když dojde k určitým událostem hostitele VMware.
