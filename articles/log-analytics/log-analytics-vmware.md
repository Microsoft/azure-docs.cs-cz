---
title: Řešení VMware monitorování v Log Analytics | Microsoft Docs
description: Informace o jak řešení VMware monitorování vám mohou pomoci sledovat hostitelích ESXi a spravovat protokoly.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: magoedte
ms.openlocfilehash: c07cc3e434a178a5f6a1ea10f7dc630c3d0d548d
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019274"
---
# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>Řešení VMware monitorování (Preview) v analýzy protokolů

![VMware – symbol](./media/log-analytics-vmware/vmware-symbol.png)

> [!NOTE]
> Řešení VMware monitorování je zastaralá.  Zákazníci, kteří již nainstalovali řešení můžete nadále používat ji, ale VMware monitorování nelze přidat do žádné nové pracovní prostory.

Řešení VMware monitorování v analýzy protokolů je řešení, které vám pomůže vytvořit centralizované protokolování a monitorování přístup pro velké protokoly VMware. Tento článek popisuje, jak můžete řešení, zaznamenat a spravovat hostitele ESXi na jednom místě pomocí řešení. Pomocí tohoto řešení uvidíte podrobná data pro všechny hostitele ESXi na jednom místě. Se zobrazí počty nejvyšší událostí, stav a trendy hostitelů virtuálních počítačů a ESXi poskytované prostřednictvím protokolů hostitele ESXi. Řešení potíží s zobrazením a hledání centralizované protokoly hostitele ESXi. A můžete vytvářet výstrahy založené na protokolu vyhledávací dotazy.

Toto řešení využívá funkce nativní syslog hostitele ESXi a nabízí data na cíl, virtuální počítač, který má OMS Agent. Toto řešení však není zapisovat soubory do protokolu syslog v cílovém virtuálním počítači. OMS agent otevře port 1514 a naslouchá na to. Jakmile obdrží data, OMS agent doručí data do analýzy protokolů.

## <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace.

* Přidat řešení VMware monitorování k předplatnému pomocí procesu popsaného v tématu [přidat řešení pro správu](log-analytics-add-solutions.md#add-a-management-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Podporované hostitelé VMware ESXi
vSphere hostitele ESXi 5.5, 6.0 a verze 6.5

#### <a name="prepare-a-linux-server"></a>Připravte Linux server
Vytvořte operační systém Linux virtuálního počítače z hostitele ESXi přijímat všechna data syslog. [Agenta OMS Linux](log-analytics-linux-agents.md) je bod kolekce pro všechna data syslog hostitele ESXi. Několika hostitelích ESXi slouží k předávání protokolů do jednoho serveru Linux, jako v následujícím příkladu.  

   ![tok procesu Syslog](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfigurace sběru syslog
1. Nastavte syslog předávání VSphere. Podrobné informace, které pomohou nastavit předávání syslog, najdete v části [konfigurace syslog na ESXi 5.0 a vyšší (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Přejděte na **konfigurace hostitele ESXi** > **softwaru** > **upřesňující nastavení** > **Syslog**.
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
2. V *Syslog.global.logHost* pole, přidejte Linux server a číslo portu *1514*. Například `tcp://hostname:1514` nebo `tcp://123.456.789.101:1514`
3. Otevřete bránu firewall hostitele ESXi pro syslog. **Konfigurace hostitele ESXi** > **softwaru** > **profil zabezpečení** > **brány Firewall** a otevřete **Vlastnosti**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
4. Zkontrolujte vSphere konzoly ověřte, že je správně nastavený tento syslog. Potvrďte na hostiteli ESXI tento port **1514** je nakonfigurovaný.
5. Stáhněte a nainstalujte agenta OMS pro Linux na Linux server. Další informace najdete v tématu [dokumentace pro OMS agenta pro Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
6. Po instalaci agenta OMS pro Linux, přejděte do adresáře /etc/opt/microsoft/omsagent/sysconf/omsagent.d a zkopírujte soubor vmware_esxi.conf adresáři /etc/opt/microsoft/omsagent/conf/omsagent.d a změna vlastníka nebo skupiny a oprávnění souboru. Příklad:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
7. Restartujte agenta OMS pro Linux spuštěním `sudo /opt/microsoft/omsagent/bin/service_control restart`.
8. Testovací připojení mezi serverem pro Linux a hostitele ESXi pomocí `nc` příkazu na hostiteli ESXi. Příklad:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

9. V portálu Azure, provádět vyhledávání protokolu pro `VMware_CL`. Při analýze protokolů shromažďuje syslog data, je zachován formátu syslog. Na portálu, některé konkrétních polí zaznamenání, jako například *Hostname* a *název_procesu*.  

    ![type](./media/log-analytics-vmware/type.png)  

    Pokud výsledky zobrazení protokolu hledání podobně jako na obrázku výše, jste nastavena na řídicí panel řešení VMware monitorování.  

## <a name="vmware-data-collection-details"></a>Podrobnosti kolekce dat VMware
Řešení VMware monitorování shromažďuje různé metriky a protokolu údaje o výkonu z hostitele ESXi pomocí OMS agentů pro Linux, které jste povolili.

Následující tabulka uvádí metody shromažďování dat a další podrobnosti o tom, jak se data shromažďují.

| Platforma | OMS agenta pro Linux | Agenta nástroje SCOM | Azure Storage | SCOM vyžaduje? | Data agenta SCOM odeslána prostřednictvím skupiny pro správu | Frekvence kolekce |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |každé 3 minuty |

Následující tabulka obsahuje příklady datová pole shromážděných řešením pro monitorování VMware:

| název pole | description |
| --- | --- |
| Device_s |Zařízení úložiště VMware |
| ESXIFailure_s |selhání typy |
| EventTime_t |čas výskytu události |
| HostName_s |Název hostitele ESXi |
| Operation_s |Vytvoření virtuálního počítače nebo odstranění virtuálního počítače |
| ProcessName_s |Název události |
| ResourceId_s |název hostitele VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Stav VMware SCSI |
| SyslogMessage_s |Syslog data |
| UserName_s |uživatel, který vytvořené nebo odstraněné virtuálních počítačů |
| VMName_s |název virtuálního počítače |
| Počítač |hostitelský počítač |
| TimeGenerated |čas, který byl vygenerován data |
| DataCenter_s |Datové centrum VMware |
| StorageLatency_s |úložiště latence (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Přehled řešení VMware monitorování
V pracovním prostoru analýzy protokolů se zobrazí na dlaždici VMware. Poskytuje podrobný pohled případných selhání. Když kliknete na dlaždici, přejděte do zobrazení řídicího panelu.

![dlaždice](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Přejděte zobrazení řídicího panelu
V **VMware** zobrazení řídicího panelu okna jsou uspořádané podle:

* Počet selhání stav
* Počty nejvyšší hostitele událostí
* Počty nejvyšší událostí
* Aktivity virtuálního počítače
* Události hostitele ESXi disku

![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

Klikněte na libovolné okno otevřete podokno hledání analýzy protokolů, které jsou uvedeny podrobné informace specifické pro okno.

Zde můžete upravit vyhledávací dotaz upravit pro určitý objekt. Podrobnosti o vytvoření protokolu hledání najdete v tématu [najít data pomocí protokolu hledání v analýzy protokolů](log-analytics-log-searches.md).

#### <a name="find-esxi-host-events"></a>Najít události hostitele ESXi
Jednom hostiteli ESXi generuje více protokolů, na základě jejich procesů. Řešení VMware monitorování je centralizuje a shrnuje počtu událostí. Toto zobrazení centralizované vám pomůže pochopit hostitele ESXi, v němž má k velkému počtu událostí a událostech, které se vyskytují nejčastěji ve vašem prostředí.

![událost](./media/log-analytics-vmware/events.png)

Můžete zobrazit další podrobnosti Další kliknutím na hostiteli ESXi nebo typ události.

Když kliknete na název hostitele ESXi, zobrazí se informace z tohoto hostitele ESXi. Pokud chcete, chcete-li zúžit výsledky typu události, přidejte `“ProcessName_s=EVENT TYPE”` v dotazu hledání. Můžete vybrat **název_procesu** ve vyhledávací filtr. Informace pro vás, který zúží.

![Přejít k podrobnostem](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Najít vysoké aktivity virtuálních počítačů
Virtuální počítač můžete vytvořit a na každém hostiteli ESXi odstraněn. Je užitečné pro správce k identifikaci hostiteli ESXi vytvoří kolik virtuálních počítačů. Že naopak, pomáhá pochopit, výkonu a plánování kapacity. Udržování přehledu o události aktivity virtuálního počítače je důležitá při správě prostředí.

![Přejít k podrobnostem](./media/log-analytics-vmware/vmactivities1.png)

Pokud chcete zobrazit další data vytvoření virtuálního počítače hostitele ESXi, klikněte na název hostitele ESXi.

![Přejít k podrobnostem](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Běžné dotazy vyhledávání
Řešení obsahuje další užitečné dotazy, které vám může pomoci spravovat hostitele ESXi, jako je prostor úložiště s vysokou latencí úložiště a selhání cestu.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![dotazy](./media/log-analytics-vmware/queries.png)


#### <a name="save-queries"></a>Uložení dotazů
Ukládání Vyhledávací dotazy je standardní funkce analýzy protokolů a vám pomůže uchovávat žádné dotazy, které jste najít užitečné. Jakmile vytvoříte dotaz, který pro vás užitečné, uložte kliknutím **Oblíbené**. Uložený dotaz, můžete snadno znovu použít později z [vlastní řídicí panel](log-analytics-dashboards.md) stránku, kde můžete vytvořit vlastní řídicí panely.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Vytvářet výstrahy z dotazů
Po vytvoření můžete své dotazy, můžete chtít použít dotazy k upozornění, když konkrétní události. V tématu [výstrahy v analýzy protokolů](log-analytics-alerts.md) informace o tom, jak vytvářet výstrahy. Příkladem výstrahy dotazy a další příklady dotazů, najdete v článku [VMware monitorování pomocí analýzy protokolů](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) příspěvku na blogu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Co je potřeba na ESXi hostitelském nastavení? Jaký dopad bude mít na moje aktuální prostředí?
Toto řešení využívá nativní Syslog hostitele ESXi předávání mechanismus. Nepotřebujete žádný další software společnosti Microsoft na hostiteli ESXi k zachycení protokoly. Měl by mít nízkou vliv na vaše stávající prostředí. Však musíte nastavit předávání syslog, což je funkce ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Je nutné restartovat Moje hostitele ESXi?
Ne. Tento proces nevyžaduje restartování. V některých případech vSphere správně neaktualizuje syslog. V takovém případě Přihlaste se k hostiteli ESXi a znovu načtěte syslog. Nemáte znovu, restartujte hostitele, takže tento proces není rušivé pro vaše prostředí.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Můžete zvýšit nebo snížit objem protokolu dat odeslaných k analýze protokolů?
Ano, můžete. Můžete použít nastavení úroveň protokolu hostitele ESXi v vSphere. Protokol kolekce je založena na *informace* úroveň. Ano, pokud chcete auditovat vytvoření virtuálního počítače nebo odstranění, budete muset zachovat *informace* úrovni na Hostd. Další informace najdete v tématu [VMware znalostní báze](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Proč není Hostd poskytuje data k analýze protokolů? Moje nastavení protokolu nastavena na informace.
Došlo chyb hostitele ESXi pro časové razítko syslog. Další informace najdete v tématu [VMware znalostní báze](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Po použití alternativní řešení Hostd by měla fungovat normálně.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Může mít víc hostitelích ESXi předávající syslog data jeden virtuální počítač s omsagent?
Ano. Můžete mít víc hostitelích ESXi předávání jeden virtuální počítač s omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Proč nevidím dat odesílaných do analýzy protokolů
Může být z několika důvodů:

* Hostitele ESXi není správně předání dat do virtuálního počítače s omsagent. Chcete-li otestovat, proveďte následující kroky:

  1. Pokud chcete potvrdit, přihlaste se do hostitele ESXi pomocí ssh a spusťte následující příkaz: `nc -z ipaddressofVM 1514`

      Pokud neproběhne úspěšně, jsou nastavení vSphere v pokročilé konfiguraci pravděpodobně není opravte. V tématu [konfigurace sběru syslog](#configure-syslog-collection) informace o tom, jak nastavit ESXi hostitele pro předávání syslog.
  2. Pokud připojení port syslog je úspěšné, ale stále se nezobrazí žádná data, potom ho znovu načtěte syslog na hostiteli ESXi pomocí ssh spusťte následující příkaz: ` esxcli system syslog reload`
* Virtuální počítač s agentem OMS není správně nastaven. Abyste to mohli otestovat, proveďte následující kroky:

  1. Analýzy protokolů naslouchá na portu 1514. Pokud chcete ověřit, že je otevřen, spusťte následující příkaz: `netstat -a | grep 1514`
  2. Měli byste vidět port `1514/tcp` otevřete. Pokud ho použít nechcete, ověřte, zda je omsagent správně nainstalován. Pokud nevidíte informace o portu, není syslog port otevřete ve virtuálním počítači.

    a. Ověřte, zda je OMS Agent spuštěna pomocí `ps -ef | grep oms`. Pokud není spuštěná, spusťte proces spuštěním příkazu ` sudo /opt/microsoft/omsagent/bin/service_control start`

    b. Otevřete soubor `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

    c. Ověřte, že správné uživatele a skupiny nastavení je platný, podobně jako: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

    d. Pokud soubor neexistuje nebo není správný, uživatele a skupiny nastavení podniknout kroky podle [Příprava serveru Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Další postup
* Použití [protokolu hledání](log-analytics-log-searches.md) v analýzy protokolů, chcete-li zobrazit podrobné VMware hostují data.
* [Vytvořit vlastní řídicí panely](log-analytics-dashboards.md) zobrazující data hostitele VMware.
* [Vytvářet výstrahy](log-analytics-alerts.md) při výskytu určité události hostitele VMware.
