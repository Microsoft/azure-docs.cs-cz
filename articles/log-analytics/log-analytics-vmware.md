---
title: Řešení VMware Monitoring v Log Analytics | Dokumentace Microsoftu
description: Zjistěte, jak řešení VMware Monitoring vám může pomoct spravovat protokoly a monitorovat hostitelích ESXi.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 6ba37a026a3c8f50fa47b0775a2ad49ee75f2769
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424642"
---
# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>Řešení VMware Monitoring (Preview) ve službě Log Analytics

![VMware symbol](./media/log-analytics-vmware/vmware-symbol.png)

> [!NOTE]
> Řešení VMware Monitoring je zastaralá.  Zákazníci, kteří už jste nainstalovali řešení můžete ho nadále používat, ale VMware Monitoring nelze přidat do jakékoli nové pracovní prostory.

Řešení VMware Monitoring v Log Analytics je řešení, které vám pomůže vytvořit centralizované protokolování a monitorování přístupu pro objemné protokoly VMware. Tento článek popisuje, jak můžete řešit, zachycení a spravovat hostitele ESXi na jednom místě pomocí řešení. Pomocí tohoto řešení můžete získat podrobné informace pro všechny hostitele ESXi na jednom místě. Můžete zobrazit počty hlavních událostí, stavu a trendech virtuálních počítačů a ESXi hostitelů, které jsou k dispozici prostřednictvím protokolů hostitelů ESXi. Řešení potíží s pomocí zobrazení a hledání centralizované protokoly hostitele ESXi. A můžete vytvářet upozornění na základě dotazů vyhledávání protokolu.

Toto řešení využívá nativní syslog funkce hostitele ESXi za účelem zápisu dat do cílového virtuálního počítače, který má OMS Agent. Toto řešení však není zapisovat soubory do protokolu syslog v cílovém virtuálním počítači. Agenta OMS otevře port 1514 a naslouchá na to. Jakmile přijme data, agenta OMS odešle data do Log Analytics.

## <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace.

* Přidejte ke svému předplatnému pomocí postupu popsaného v řešení VMware Monitoring [přidejte řešení pro správu](log-analytics-add-solutions.md#add-a-management-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Podporované hostitelé VMware ESXi
vSphere hostitele ESXi 5.5, 6.0 a 6.5

#### <a name="prepare-a-linux-server"></a>Příprava serveru s Linuxem
Vytvořte operační systém Linux virtuálního počítače pro příjem všechna data syslogu z hostitele ESXi. [Agenta OMS pro Linux](log-analytics-linux-agents.md) je bod kolekce pro všechna data syslogu hostitele ESXi. Více hostitelích ESXi můžete použít k předávání protokolů na jednom serveru systému Linux, jako v následujícím příkladu.  

   ![tok procesu Syslog](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfigurace shromažďování syslogu
1. Nastavení předávání syslog pro VSphere. Podrobné informace vám pomohou nastavit předávání syslog, naleznete v tématu [konfigurace syslog na ESXi 5.0 a novější (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Přejděte na **konfigurace hostitele ESXi** > **softwaru** > **upřesňující nastavení** > **Syslog**.
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
1. V *Syslog.global.logHost* pole, přidat Linux server a číslo portu *1514*. Například `tcp://hostname:1514` nebo `tcp://123.456.789.101:1514`
1. Otevření brány firewall hostitele ESXi pro syslog. **Konfigurace hostitele ESXi** > **softwaru** > **profil zabezpečení** > **brány Firewall** a otevřete **Vlastnosti**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
1. Zkontrolujte vSphere konzoly ověřte, že je správně nastavený tento syslog. Zkontrolujte na hostiteli ESXI tento port **1514** je nakonfigurované.
1. Stáhněte a nainstalujte agenta OMS pro Linux na serveru s Linuxem. Další informace najdete v tématu [dokumentaci pro agenta OMS pro Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Po instalaci agenta OMS pro Linux, přejděte do adresáře /etc/opt/microsoft/omsagent/sysconf/omsagent.d a zkopírujte soubor vmware_esxi.conf adresáři /etc/opt/microsoft/omsagent/conf/omsagent.d a změnu vlastníka nebo skupiny a oprávnění souboru. Příklad:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Restartujte agenta OMS pro Linux spuštěním `sudo /opt/microsoft/omsagent/bin/service_control restart`.
1. Testovací připojení mezi serverem pro Linux a k hostiteli ESXi pomocí `nc` příkazu na hostiteli ESXi. Příklad:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. Na webu Azure Portal, provádět vyhledávání protokolu `VMware_CL`. Log Analytics shromažďuje data syslogu, zůstane ve formátu syslog. Na portálu pro některé konkrétní pole jsou zachyceny, jako například *Hostname* a *ProcessName*.  

    ![type](./media/log-analytics-vmware/type.png)  

    Pokud výsledky hledání log zobrazení podobně jako na obrázku výše, nastavíte jste na řídicím panelu řešení VMware Monitoring.  

## <a name="vmware-data-collection-details"></a>Podrobné informace o shromažďování dat pro VMware
Řešení VMware Monitoring různých metrik a protokolů shromažďuje údaje o výkonu z hostitele ESXi pomocí agentů OMS pro Linux, které jste povolili.

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak se shromažďují data.

| Platforma | Agenta OMS pro Linux | Agent nástroje SCOM | Azure Storage | SCOM vyžaduje? | Data agenta nástroje SCOM odeslaná pomocí skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |každé 3 minuty |

Následující tabulka obsahuje příklady shromažďovaných řešení VMware Monitoring datová pole:

| název pole | description |
| --- | --- |
| Device_s |VMware úložných zařízení |
| ESXIFailure_s |typy selhání |
| EventTime_t |čas, kdy došlo k události |
| HostName_s |Název hostitele ESXi |
| Operation_s |Vytvoření virtuálního počítače nebo odstranění virtuálního počítače |
| ProcessName_s |Název události |
| ResourceId_s |název hostitele VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Stav VMware SCSI |
| SyslogMessage_s |Data Syslogu |
| UserName_s |uživatel, který vytvoří nebo odstraní virtuální počítač |
| VMName_s |název virtuálního počítače |
| Počítač |hostitelský počítač |
| TimeGenerated |vygenerování dat |
| DataCenter_s |Datové centrum VMware |
| StorageLatency_s |úložiště latence (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Přehled řešení VMware Monitoring
V pracovním prostoru Log Analytics se zobrazí na dlaždici VMware. Poskytuje podrobný pohled všechny chyby. Když kliknete na dlaždici, přejdete do zobrazení řídicího panelu.

![tile (obkladač)](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Přejděte zobrazení řídicího panelu
V **VMware** zobrazení řídicího panelu okna jsou uspořádané podle:

* Počet stavů selhání
* Začátek hostitele podle počtu událostí
* Počty hlavních událostí
* Aktivity virtuálního počítače
* Události disku hostitele ESXi

![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

Klikněte na libovolné okno otevřete podokno hledání Log Analytics, který zobrazuje detailní informace specifické pro okno.

Tady můžete upravit vyhledávací dotaz upravit pro určitý objekt. Podrobné informace o vytváření prohledávání protokolů, najdete v článku [našla data pomocí prohledávání protokolů v Log Analytics](log-analytics-log-searches.md).

#### <a name="find-esxi-host-events"></a>Najít akce hostitele ESXi
Jednoho hostitele ESXi generuje více protokolů na základě jejich procesů. Řešení VMware Monitoring centralizuje je a obsahuje souhrn počtu událostí. Toto zobrazení centralizované vám pomůže pochopit hostitele ESXi, který má k velkému počtu událostí a které události se nejčastěji vyskytují ve vašem prostředí.

![událost](./media/log-analytics-vmware/events.png)

Můžete přejít k dalším kliknutím na hostitele ESXi nebo typ události.

Když kliknete na název hostitele ESXi, zobrazení informací z tohoto hostitele ESXi. Pokud chcete, abychom omezili výsledky typu události, přidejte `“ProcessName_s=EVENT TYPE”` ve vyhledávacím dotazu. Můžete vybrat **ProcessName** ve vyhledávací filtr. Který způsobí zúžení informace za vás.

![Zobrazit podrobnosti](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Vyhledat vysoké aktivity virtuálního počítače
Virtuální počítač můžete vytvořit a odstranit na žádném hostiteli ESXi. Je užitečné pro správce k identifikaci kolik virtuálních počítačů, které jsou hostiteli ESXi, který vytvoří. Tento naopak, pomáhá pochopit výkon a plánování kapacity. Udržování přehledu o událostech aktivity virtuálního počítače je velmi důležité při správě prostředí.

![Zobrazit podrobnosti](./media/log-analytics-vmware/vmactivities1.png)

Pokud chcete zobrazit další data vytvoření virtuálního počítače hostitele ESXi, klikněte na název hostitele ESXi.

![Zobrazit podrobnosti](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Běžné dotazy na hledání
Toto řešení zahrnuje další užitečné dotazy, které vám může pomoci spravovat vaši hostitelé ESXi, jako je například vysoká úložný prostor, latence úložiště a cesta selhání.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![dotazy](./media/log-analytics-vmware/queries.png)


#### <a name="save-queries"></a>Ukládání dotazů
Ukládání vyhledávacích dotazů je standardní funkce v Log Analytics a můžete zachovat všechny dotazy, které připadají užitečné. Jakmile vytvoříte dotaz, který je pro vás užitečné, uložte kliknutím **Oblíbené**. Uložený dotaz umožňuje snadno opakovaně použít později [Můj řídicí panel](log-analytics-dashboards.md) stránku, kde můžete vytvořit vlastní řídicích panelů.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Vytvářet upozornění z dotazů
Po vytvoření dotazů, můžete využívat dotazy a zobrazí vám upozornění, když dojde k určité události. Zobrazit [upozornění v Log Analytics](log-analytics-alerts.md) informace o tom, jak vytvářet výstrahy. Příklady výstrah dotazy a další příklady dotazů, najdete v článku [VMware monitorování pomocí Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) blogový příspěvek.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Co je potřeba na ESXi hostovat nastavení? Jaký vliv bude mít na mé aktuální prostředí?
Toto řešení využívá nativní předávání mechanismus Syslog hostitele ESXi. Nepotřebujete žádný další software Microsoftu na hostiteli ESXi zachycení protokoly. Měl by mít nízký dopad na vaše stávající prostředí. Nicméně musíte nastavit předávání syslog, což je funkce ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Je nutné restartovat Můj hostitele ESXi?
Ne. Tento postup nevyžaduje restartování. V některých případech vSphere správně neaktualizuje syslog. V takovém případě se přihlaste se k hostiteli ESXi a znovu načtěte syslog. Nemusíte znovu, restartujte hostitele, abyste tento postup není rušivá pro vaše prostředí.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Můžete zvýšit nebo snížit objem dat protokolu odesílají do Log Analytics?
Ano, můžete. Nastavení protokolu na úrovni hostitele ESXi můžete použít v vSphere. Shromažďování protokolů je na základě *informace* úroveň. Ano, pokud chcete auditovat vytvoření virtuálního počítače nebo odstranění, je potřeba nechat *informace* úrovně na Hostd. Další informace najdete v tématu [VMware znalostní báze](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Proč není Hostd poskytuje data do Log Analytics? Moje nastavení protokolu je nastavené na informace.
Došlo chyby hostitele ESXi pro časové razítko syslog. Další informace najdete v tématu [VMware znalostní báze](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Po použití řešení Hostd by normálně fungovat.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Může mít více hostitelích ESXi předává data syslogu na jeden virtuální počítač s omsagent?
Ano. Můžete mít více hostitelů ESXi na jeden virtuální počítač s omsagent předávání.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Proč nevidím dat odesílaných do Log Analytics?
Může existovat několik důvodů:

* Hostitel ESXi není správně odesílání dat do virtuálního počítače s omsagent. Pokud chcete otestovat, postupujte následovně:

  1. Pokud chcete potvrdit, přihlaste se k hostiteli ESXi pomocí ssh a spusťte následující příkaz: `nc -z ipaddressofVM 1514`

      Pokud neproběhne úspěšně, nastavení vSphere v pokročilé konfiguraci se pravděpodobně není to správně. Zobrazit [Konfigurovat shromažďování syslogu](#configure-syslog-collection) informace o tom, jak vytvořit hostitele ESXi pro předávání protokolu syslog.
  1. Pokud je úspěšné připojení k portu syslog, ale stále nevidíte žádná data, pak znovu načtěte syslog na hostiteli ESXi pomocí ssh a spusťte následující příkaz: ` esxcli system syslog reload`
* Virtuální počítač s agentem OMS není nastavena správně. Abyste to mohli otestovat, proveďte následující kroky:

  1. Log Analytics naslouchá na portu 1514. Pokud chcete ověřit, že je otevřený, spusťte následující příkaz: `netstat -a | grep 1514`
  1. Měli byste vidět port `1514/tcp` otevřete. Pokud ho nevidíte, zkontrolujte, že je správně nainstalované omsagent. Pokud se nezobrazí údaje o portech, není syslog port otevřít na virtuálním počítači.

    a. Ověřte, zda je spuštěna agenta OMS s využitím `ps -ef | grep oms`. Pokud není spuštěná, proces spusťte pomocí příkazu ` sudo /opt/microsoft/omsagent/bin/service_control start`

    b. Otevřete soubor `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

    c. Ověřte, zda správné uživatele a skupiny nastavení je platný, podobně jako: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

    d. Pokud soubor neexistuje nebo uživatel a skupina nastavení se nepovedlo, provést nápravné opatření [Příprava serveru s Linuxem](#prepare-a-linux-server).

## <a name="next-steps"></a>Další postup
* Použití [prohledávání protokolů](log-analytics-log-searches.md) v Log Analytics, chcete-li zobrazit podrobné VMware hostovat data.
* [Vytvářejte vlastní řídicí panely](log-analytics-dashboards.md) zobrazující data hostitele VMware.
* [Vytvořit upozornění](log-analytics-alerts.md) Pokud dojde k určité událostem hostitele VMware.
