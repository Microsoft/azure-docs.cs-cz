---
title: VMware Monitoring řešení v Azure Monitor | Microsoft Docs
description: Přečtěte si, jak může řešení VMware Monitoring pomáhat spravovat protokoly a monitorovat hostitele ESXi.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: 9dc31cd4f492a4e95ce8232a8df28f07206e23b1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587173"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Řešení VMware Monitoring (nepoužívané) v Azure Monitor

![Symbol VMware](./media/vmware/vmware-symbol.png)

> [!NOTE]
> Řešení VMware Monitoring se už nepoužívá.  Zákazníci, kteří už toto řešení nainstalovali, ho můžou dál používat, ale VMware Monitoring nemůžou být přidané do žádných nových pracovních prostorů.

Řešení VMware Monitoring v Azure Monitor je řešení, které vám pomůže vytvořit centralizovaný protokol pro protokolování a monitorování pro velké protokoly VMware. Tento článek popisuje, jak můžete pomocí řešení řešit, zachytit a spravovat hostitele ESXi v jednom umístění. Pomocí tohoto řešení můžete zobrazit podrobná data všech hostitelů ESXi v jednom umístění. Můžete se podívat na hlavní počty událostí, stav a trendy hostitelů virtuálních počítačů a ESXi poskytovaných prostřednictvím protokolů hostitele ESXi. Problémy můžete řešit zobrazením a prohledáváním centralizovaných protokolů hostitele ESXi. A můžete vytvářet výstrahy na základě dotazů hledání v protokolu.

Řešení používá nativní funkce syslog hostitele ESXi k odesílání dat do cílového virtuálního počítače, který má agenta Log Analytics. Řešení ale nepíše soubory do protokolu syslog v cílovém virtuálním počítači. Agent Log Analytics otevře port 1514 a nanaslouchá tomuto. Jakmile data obdrží, agent Log Analytics data vloží do Azure Monitor.

## <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace.

* Přidejte řešení VMware Monitoring do svého předplatného pomocí postupu popsaného v tématu [instalace řešení monitorování](./solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Podporovaná VMware ESXi hostitelé
vSphere ESXi hostitele 5,5, 6,0 a 6,5

#### <a name="prepare-a-linux-server"></a>Příprava serveru Linux
Vytvořte virtuální počítač s operačním systémem Linux pro příjem všech dat syslog z hostitelů ESXi. [Agent Log Analytics Linux](../vm/quick-collect-linux-computer.md) je bod shromažďování pro všechna data syslog hostitele ESXi. Můžete použít více hostitelů ESXi pro přeposílání protokolů na jeden server Linux, jak je uvedeno v následujícím příkladu.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![tok syslog](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfigurovat shromažďování syslog
1. Nastavte předávání syslog pro VSphere. Podrobné informace, které vám pomůžou nastavit předávání syslog, najdete v tématu [Konfigurace protokolu syslog na ESXi 5,0 a vyšší (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Přejít na **konfiguraci hostitele ESXi**  >    >  **Rozšířené nastavení**  >  **syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. Do pole *syslog. Global. logHost* přidejte server Linux a číslo portu *1514*. Příkladem je `tcp://hostname:1514` nebo `tcp://123.456.789.101:1514`.
1. Otevřete hostitelskou bránu firewall ESXi pro syslog. **Konfigurace**  >  hostitele ESXi **Software**  >  **Profil zabezpečení**  >  **Brána firewall** a otevřené **vlastnosti**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Zkontrolujte konzolu vSphere a ověřte, jestli je syslog správně nastavený. Potvrďte u hostitele ESXI, že je nakonfigurovaný port **1514** .
1. Stáhněte a nainstalujte agenta Log Analytics pro Linux na server Linux. Další informace najdete v [dokumentaci ke službě Log Analytics Agent pro Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Po instalaci agenta Log Analytics pro Linux přejděte do adresáře/etc/opt/Microsoft/omsagent/sysconf/omsagent.d a zkopírujte soubor vmware_esxi. conf do adresáře/etc/opt/Microsoft/omsagent/conf/omsagent.d a změňte vlastníka/skupinu a oprávnění souboru. Příklad:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Restartujte agenta Log Analytics pro Linux spuštěním `sudo /opt/microsoft/omsagent/bin/service_control restart` .
1. Otestujte připojení mezi serverem Linux a hostitelem ESXi pomocí `nc` příkazu na hostiteli ESXi. Příklad:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. V Azure Portal proveďte dotaz protokolu pro `VMware_CL` . Když Azure Monitor shromažďuje data syslog, uchovává formát syslog. Na portálu jsou některá konkrétní pole zachycena, například *název hostitele* a název *procesu*.  

    ![Snímek obrazovky zobrazuje dotaz protokolu pro Type = VMware_CL s výsledkem časového razítka.](./media/vmware/type.png)  

    Pokud výsledky hledání protokolu zobrazení jsou podobné obrázku výše, je nastaveno použití řídicího panelu řešení VMware Monitoring.  

## <a name="vmware-data-collection-details"></a>Podrobnosti shromažďování dat VMware
Řešení VMware Monitoring shromažďuje různé metriky výkonu a data protokolů z hostitelů ESXi pomocí agentů Log Analytics pro Linux, které jste povolili.

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak se data shromažďují.

| platforma | Agent Log Analytics pro Linux | Agent System Center Operations Manager | Azure Storage | Operations Manager požadováno? | Data agenta Operations Manager odeslána prostřednictvím skupiny pro správu | frekvence shromažďování |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |každé 3 minuty |

V následující tabulce jsou uvedeny příklady datových polí shromažďovaných řešením VMware Monitoring:

| název pole | description |
| --- | --- |
| Device_s |Úložná zařízení VMware |
| ESXIFailure_s |typy selhání |
| EventTime_t |čas, kdy došlo k události |
| HostName_s |Název hostitele ESXi |
| Operation_s |Vytvoření virtuálního počítače nebo odstranění virtuálního počítače |
| ProcessName_s |název události |
| ResourceId_s |název hostitele VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Stav rozhraní SCSI VMware |
| SyslogMessage_s |Data syslogu |
| UserName_s |uživatel, který vytvořil nebo odstranil virtuální počítač |
| VMName_s |název virtuálního počítače |
| Počítač |hostitelský počítač |
| TimeGenerated |čas, kdy byla data vygenerována |
| DataCenter_s |Datové centrum VMware |
| StorageLatency_s |latence úložiště (MS) |

## <a name="vmware-monitoring-solution-overview"></a>Přehled řešení VMware Monitoring
Dlaždice VMware se zobrazí v pracovním prostoru Log Analytics. Poskytuje podrobný pohled na případné chyby. Když kliknete na dlaždici, přejdete do zobrazení řídicího panelu.

![Snímek obrazovky se zobrazí na dlaždici VMware a zobrazí devět selhání.](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navigace v zobrazení řídicího panelu
V zobrazení řídicího panelu **VMware** jsou okna uspořádána podle:

* Počet stavů selhání
* Hlavní hostitel podle počtu událostí
* Počty hlavních událostí
* Aktivity virtuálních počítačů
* Události disku hostitele ESXi

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Kliknutím na libovolné okno otevřete Log Analytics podokně hledání, které zobrazuje podrobné informace specifické pro okno.

Z tohoto místa můžete upravit dotaz protokolu a upravit ho pro konkrétní specifickou hodnotu. Podrobnosti o vytváření dotazů protokolu najdete v tématu [hledání dat pomocí dotazů protokolu v Azure monitor](../logs/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Najít události hostitele ESXi
Jeden hostitel ESXi generuje více protokolů na základě jejich procesů. VMware Monitoring řešení je centralizovaná a shrnuje počty událostí. Toto centralizované zobrazení vám pomůže pochopit, který hostitel ESXi má velký objem událostí a k jakým událostem dochází nejčastěji ve vašem prostředí.

![event](./media/vmware/events.png)

Další podrobnosti můžete zobrazit kliknutím na hostitele ESXi nebo na typ události.

Když kliknete na název hostitele ESXi, zobrazí se informace z tohoto hostitele ESXi. Pokud chcete zúžit výsledky s typem události, přidejte je `“ProcessName_s=EVENT TYPE”` do vyhledávacího dotazu. Ve vyhledávacím filtru můžete vybrat možnost **Process** . Tím se omezí informace za vás.

![Snímek obrazovky hostitele ESXi na počet událostí a rozpis na typ události v zobrazení řídicího panelu VMware Monitoring.](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Najít vysoké aktivity virtuálních počítačů
Virtuální počítač se dá vytvořit a odstranit na jakémkoli hostiteli ESXi. Správce může zjistit, kolik virtuálních počítačů vytvoří hostitel ESXi. To zase pomáhá pochopit plánování výkonu a kapacity. Sledování událostí aktivit virtuálních počítačů je klíčové při správě vašeho prostředí.

![Snímek obrazovky okna aktivity virtuálního počítače na řídicím panelu VMware Monitoring, ve kterém se zobrazuje graf vytváření a odstraňování virtuálních počítačů pomocí hostitele ESXi.](./media/vmware/vmactivities1.png)

Pokud se chcete podívat na další data o vytváření virtuálních počítačů hostitele ESXi, klikněte na název hostitele ESXi.

![Snímek obrazovky podokna z řídicího panelu VMware Monitoring zobrazuje tabulku s řádkem dat pro jednotlivé vytváření virtuálních počítačů pomocí hostitele ESXi.](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Běžné dotazy protokolu
Řešení zahrnuje další užitečné dotazy, které vám pomůžou spravovat hostitele ESXi, jako je třeba vysoký prostor úložiště, latence úložiště a selhání cesty.

![Snímek obrazovky obsahuje doporučená hledání, což jsou užitečné uložené dotazy.](./media/vmware/queries.png)


#### <a name="save-queries"></a>Uložení dotazů
Ukládání dotazů na protokol je standardní funkce v Azure Monitor a může pomoct s případnými dotazy, které byly užitečné. Po vytvoření dotazu, který je užitečný, ho uložte kliknutím na **Oblíbené položky**. Uložený dotaz vám umožní snadno ho znovu použít později ze stránky [Můj řídicí panel](../visualize/tutorial-logs-dashboards.md) , kde můžete vytvořit vlastní řídicí panely.

![Snímek obrazovky se zobrazí jako součást vlastního řídicího panelu s popisem vyhledávání v protokolu s ikonami pro vrácení zpět, export, upozorňování, ukládání, oblíbené položky a historii.](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Vytváření výstrah z dotazů
Po vytvoření dotazů můžete chtít použít dotazy, které vás upozorní, když dojde k určitým událostem. Informace o tom, jak vytvářet výstrahy, najdete [v tématu výstrahy v Log Analytics](../alerts/alerts-overview.md) . Příklady dotazů na dotazy a další příklady dotazů naleznete v příspěvku k [VMware pomocí Log Analytics](/archive/blogs/msoms/monitor-vmware-using-oms-log-analytics) Blogový příspěvek.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Co musím udělat v nastavení hostitele ESXi? Jaký dopad bude mít v mém aktuálním prostředí?
Řešení používá nativní mechanizmus předávání syslog hostitele ESXi. Pro zachycení protokolů není potřeba žádný další software Microsoftu na hostiteli ESXi. Mělo by mít nízký dopad na vaše stávající prostředí. Je ale potřeba nastavit předávání syslog, což je funkce ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Potřebuji restartovat hostitele ESXi?
No. Tento proces nevyžaduje restart. V některých případech vSphere nesprávně aktualizuje syslog. V takovém případě se přihlaste k hostiteli ESXi a znovu načtěte protokol syslog. Znovu nemusíte restartovat hostitele, takže tento proces nebude mít dopad na vaše prostředí.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Můžu zvýšit nebo snížit objem dat protokolu odesílaných do Log Analytics?
Ano, můžete. V vSphere můžete použít nastavení úrovně protokolu hostitele ESXi. Shromažďování protokolů je založeno na úrovni *informací* . Pokud tedy chcete auditovat vytváření nebo odstraňování virtuálních počítačů, je třeba zachovat úroveň *informací* na hostiteli. Další informace najdete v [článku znalostní báze VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Proč hostitel neposkytuje data Log Analytics? Nastavení moje protokol je nastaveno na informace.
Došlo k chybě hostitele ESXi pro časové razítko syslog. Další informace najdete v [článku znalostní báze VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Po použití alternativního řešení by měl hostitel fungovat normálně.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Můžu více hostitelů ESXi předávat data syslogu do jednoho virtuálního počítače pomocí omsagent?
Ano. K jednomu virtuálnímu počítači s omsagent může předávat víc hostitelů ESXi.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Proč se mi nezobrazuje tok dat do Log Analytics?
Může existovat několik důvodů:

* Hostitel ESXi nevkládá data do virtuálního počítače se systémem omsagent správně. Chcete-li provést test, proveďte následující kroky:

  1. Potvrďte to tak, že se přihlásíte k hostiteli ESXi pomocí SSH a spustíte následující příkaz: `nc -z ipaddressofVM 1514`

      Pokud to neproběhne úspěšně, nastavení vSphere v rozšířené konfiguraci nejspíš nejsou správná. Informace o tom, jak nastavit hostitele ESXi pro předávání syslog, najdete v tématu [Konfigurace kolekce SYSLOG](#configure-syslog-collection) .
  1. Pokud je připojení portu syslog úspěšné, ale pořád se nezobrazí žádná data, pak znovu načtěte na hostiteli ESXi pomocí SSH tento příkaz: `esxcli system syslog reload`
* Virtuální počítač s agentem Log Analytics není správně nastavený. Pokud to chcete otestovat, proveďte následující kroky:

  1. Log Analytics naslouchá na portu 1514. Pokud chcete ověřit, že je otevřený, spusťte následující příkaz: `netstat -a | grep 1514`
  1. Měl by se zobrazit `1514/tcp` otevřený port. Pokud to neuděláte, ověřte, že je omsagent správně nainstalovaný. Pokud nevidíte informace o portu, pak na virtuálním počítači není otevřený port syslog.

    a. Pomocí nástroje ověřte, zda je spuštěný agent Log Analytics `ps -ef | grep oms` . Pokud není spuštěný, spusťte proces spuštěním příkazu. `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Otevřete soubor `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Ověřte, jestli je správné nastavení uživatele a skupiny, podobně jako: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Pokud soubor neexistuje nebo se jedná o nesprávné nastavení uživatele a skupiny, proveďte nápravná opatření [přípravou serveru pro Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Další kroky
* K zobrazení podrobných dat hostitele VMware použijte [dotazy protokolu](../logs/log-query-overview.md) v Log Analytics.
* [Vytvořte si vlastní řídicí panely](../visualize/tutorial-logs-dashboards.md) zobrazující data hostitele VMware.
* [Vytváření výstrah](../alerts/alerts-overview.md) v případě, že dojde k určitým událostem hostitele VMware.

