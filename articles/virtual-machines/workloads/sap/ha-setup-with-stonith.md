---
title: Vysoká dostupnost pro SAP HANA v Azure (velké instance) nastavit s využitím techniky STONITH | Dokumentace Microsoftu
description: Vytvoření vysoké dostupnosti pro SAP HANA v Azure (velké instance) v SUSE pomocí využitím techniky STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c6d4ec767b4c566e6a390f37b97266916819a40c
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015156"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Vysoká dostupnost v SUSE pomocí využitím techniky STONITH
Tento dokument obsahuje podrobné pokyny krok za krokem nastavit vysokou dostupnost v SUSE operačního systému pomocí využitím techniky STONITH zařízení.

**Právní omezení:** *Tato příručka se vypočítá nastavení testování v prostředí velkých instancích HANA společnosti Microsoft, které úspěšně funguje. Když tým pro správu služby Microsoftu pro velké instance HANA nepodporuje operační systém, budete možná potřebovat požádat o žádné další informace o řešení nebo vysvětlení na vrstvě operačního systému SUSE. Tým pro správu služby Microsoft nastavit zařízení s využitím techniky STONITH a plně podporuje a může být zahrnuté pro řešení potíží s využitím techniky STONITH k zařízení.*
## <a name="overview"></a>Přehled
Pokud chcete nastavit vysokou dostupnost pomocí clusteringu s operačním systémem SUSE, musí splňovat následující předpoklady.
### <a name="pre-requisites"></a>Požadavky
- Velké instance HANA jsou zřízené
- Operační systém je zaregistrovaný.
- Velké instance HANA servery jsou připojeny ke SMT serveru opravy nebo balíčků
- Nainstalovány nejnovější opravy operačního systému
- Je nastavený NTP (čas serveru)
- Čtení a pochopení nejnovější verzi dokumentace SUSE na vysokou dostupnost nastavení

### <a name="setup-details"></a>Podrobnosti nastavení
Tato příručka používá následující nastavení:
- Operační systém: SLES 12 SP1 pro SAP
- Velké instance HANA: 2xS192 (čtyři soketů, 2 TB)
- Verze HANA: HANA 2.0 SP1
- Názvy serverů: sapprdhdb95 (node1) a sapprdhdb96 (node2)
- : Využitím techniky STONITH iSCSI na základě zařízení zařízení s využitím techniky STONITH
- Nastavení v jednom z uzlů velká Instance HANA NTP

Při nastavování velkých instancích HANA s HSR si můžete vyžádat týmu správy služeb Microsoft nastavit využitím techniky STONITH. Pokud již jste stávající zákazník, který má velké instance HANA zřízený a nutnosti využitím techniky STONITH zařízení nastavit pro existující oken, budete muset zadat následující informace, které tým pro správu služby Microsoft ve formuláři žádosti o službu (SRF). Můžete požádat o SRF formou pomocí Technical Account Manager nebo váš Contact Microsoftu pro velké Instance HANA registrace. Noví zákazníci můžou požádat o využitím techniky STONITH zařízení během zřizování. Vstupy, které nejsou k dispozici ve formuláři žádosti o zřízení.

- Název serveru a serveru IP adresu (například myhanaserver1, 10.35.0.1)
- Umístění (například USA – východ)
- Jméno zákazníka (například Microsoft)
- SID – identifikátor systému HANA (například H11)

Po nakonfigurování zařízení využitím techniky STONITH tým pro správu služby Microsoftu poskytují SBD název zařízení a IP adresu úložiště iSCSI, který můžete použít ke konfiguraci nastavení využitím techniky STONITH. 

Nastavení koncového HA pomocí využitím techniky STONITH, musí následovat následující kroky:

1.  Identifikace zařízení SBD
2.  Inicializovat SBD zařízení
3.  Konfigurace clusteru
4.  Nastavení Softdog sledovacího zařízení
5.  Připojte se k uzlu do clusteru
6.  Ověření clusteru
7.  Konfigurace prostředků do clusteru
8.  Otestovat proces převzetí služeb při selhání

## <a name="1---identify-the-sbd-device"></a>1.   Identifikace zařízení SBD
Tato část popisuje, jak zjistit, jestli zařízení SBD pro nastavení po tým správy společnosti Microsoft služby byl nakonfigurován využitím techniky STONITH. **Tato část platí jenom pro stávající zákazník**. Pokud jste nového zákazníka, týmu správy služby Microsoft poskytuje SBD názvu zařízení vám a vy můžete tuto část přeskočit.

1.1 upravit */etc/iscsi/initiatorname.isci* do 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Správa služeb společnosti Microsoft zadejte tento řetězec. Upravte soubor na **obě** uzly, ale číslo uzlu se liší v každém uzlu.

![initiatorname.PNG](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 upravit */etc/iscsi/iscsid.conf*: nastavte *node.session.timeo.replacement_timeout=5* a *node.startup = automatické*. Upravte soubor na **obě** uzly.

1.3 proveďte příkaz zjišťování, zobrazí čtyřech relacích. Běžel na obou uzlech.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 provést příkaz pro přihlášení k zařízení iSCSI, zobrazuje čtyřech relacích. Spustit na **obě** uzly.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 spuštění nového prohledání skriptu: *Prohledat znovu. scsi bus.sh*.  Tento skript ukazuje nové disky, které pro vás vytvořili.  Běžel na obou uzlech. Měli byste vidět počet LUN, která je větší než nula. (Příklad: 1, 2 atd.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.PNG](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 a získat tak název zařízení, spusťte příkaz *fdisk – l*. Běžel na obou uzlech. Vyberte zařízení s velikostí **178 MiB**.

```
  fdisk –l
```

![Fdisk l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Inicializovat SBD zařízení

2.1 inicializovat SBD zařízení na **obě** uzly

```
sbd -d <SBD Device Name> create
```
![sbdcreate.PNG](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Zkontrolujte, co byl zapsán do zařízení. To udělat na **obě** uzly

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   Konfigurace clusteru
Tato část popisuje postup nastavení clusteru SUSE HA.
### <a name="31-package-installation"></a>3.1 instalace balíčku
3.1.1 Zkontrolujte, že jsou nainstalované ha_sles a vzory SAPHanaSR dokumentu. Pokud není nainstalovaný, nainstalujte je. Nainstalujte ho na **obě** uzly.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.PNG](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 nastavení clusteru
3.2.1 můžete buď používat *ha clusteru init* příkaz, nebo pomocí Průvodce yast2 nastavení clusteru. V takovém případě se používá Průvodce yast2. Tento krok proveďte **pouze na primárním uzlu**.

Postupujte podle yast2 > vysoká dostupnost > clusteru ![yast. ovládací prvek center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast. hawk install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Klikněte na tlačítko **zrušit** od halk2 balíček je už nainstalovaný.

![yast. hawk continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Klikněte na tlačítko **pokračovat**

Očekávaná hodnota = počet uzlů nasazení (v tomto případě 2) ![yast clusteru-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) klikněte na tlačítko **Další**
![yast clusteru konfigurace csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) přidat názvy wwnn a pak klikněte na tlačítko "Přidat navrhované soubory"

Klikněte na tlačítko "Zapnout csync2"

Klikněte na tlačítko "Generovat klíče Předprodukční Shared", zobrazí pod automaticky otevíraného okna

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Klikněte na tlačítko **OK**.

Ověřování se provádí pomocí IP adresy a předprodukční shared klíče v Csync2. Soubor klíče se vygeneroval s csync2 -k /etc/csync2/key_hagroup. Soubor key_hagroup mají být zkopírovány do všech členů clusteru ručně po jeho vytvoření. **Nezapomeňte zkopírovat soubor z uzlu 1 k node2**.

![yast clusteru conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Klikněte na tlačítko **Další**
![yast clusteru service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Ve výchozí možnost spuštění byl vypnutý, změňte ho na "on", který je pacemaker spuštění při spuštění. Můžete nastavit možnost podle potřeby nastavení.
Klikněte na tlačítko **Další** a dokončení konfigurace clusteru.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Nastavení Softdog sledovacího zařízení
Tato část popisuje konfigurační sady sledovacího zařízení (softdog).

4.1 přidejte následující řádek, který */etc/init.d/boot.local* na **obě** uzly.
```
modprobe softdog
```
![modprobe softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 aktualizovat soubor */etc/sysconfig/sbd* na **obě** uzly následujícím způsobem:
```
SBD_DEVICE="<SBD Device Name>"
```
![SBD device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 načtení modulu jádra na **obě** uzly spuštěním následujícího příkazu
```
modprobe softdog
```
![modprobe. softdog command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 zkontrolujte a ověřte, že softdog běží jako následující **obě** uzly:
```
lsmod | grep dog
```
![lsmod. grep dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 SBD zařízení spustit na **obě** uzly
```
/usr/share/sbd/sbd.sh start
```
![SBD Zo – start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 testovat proces démon SBD na **obě** uzly. Dvě položky se zobrazí po dokončení konfigurace na **obě** uzly
```
sbd -d <SBD Device Name> list
```
![SBD list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 odeslat testovací zprávu a **jeden** uzly
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![SBD list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 na **druhý** uzlu (node2) můžete zkontrolovat stav zprávy
```
sbd  -d <SBD Device Name> list
```
![SBD. seznam message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 Pokud chcete přijmout sbd config, aktualizujte soubor */etc/sysconfig/sbd* následujícím způsobem. Aktualizace souboru na **obě** uzly
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 spusťte službu pacemaker na **primárního uzlu** (node1)
```
systemctl start pacemaker
```
![Start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Pokud službu pacemaker *selže*, odkazovat na *scénář 5: Pacemaker služba selže*

## <a name="5---joining-the-cluster"></a>5.   Připojení ke clusteru
Tato část popisuje, jak připojit k uzlu do clusteru.

### <a name="51-add-the-node"></a>5.1 Přidání uzlu
Spuštěním následujícího příkazu **node2** umožníte node2 připojit ke clusteru.
```
ha-cluster-join
```
Pokud se zobrazí *chyba* během připojující se ke clusteru, přečtěte si *scénář 6: uzel 2 nelze připojit ke clusteru*.

## <a name="6---validating-the-cluster"></a>6.   Ověření clusteru

### <a name="61-start-the-cluster-service"></a>6.1 spuštění služby clusteru se
Ke kontrole a volitelně spustí cluster na prvním **obě** uzly.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl. stav pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 sledování stavu
Spusťte příkaz *crm_mon* zajistit **obě** uzly jsou online. Můžete ho spustit na **některý z uzlů** clusteru
```
crm_mon
```
![CRM-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) také můžete přihlásit k hawk a zkontrolujte stav clusteru *https://<node IP>: 7630*. Výchozí uživatel se hacluster a heslo je linux. V případě potřeby můžete změnit pomocí hesla *hesel* příkazu.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Konfigurovat vlastnosti clusteru a prostředky 
Tato část popisuje postup pro konfiguraci prostředků clusteru.
V tomto příkladu, nastavte následující prostředek zbývající lze nastavit (v případě potřeby) pomocí odkazu na Průvodce SUSE HA. Proveďte konfiguraci v **jeden z uzlů** pouze. Proveďte na primárním uzlu.

- Spuštění clusteru
- Zařízení s využitím techniky STONITH
- Virtuální IP adresa


### <a name="71-cluster-bootstrap-and-more"></a>7.1 bootstrap clusteru a provádění dalších akcí
Přidání clusteru bootstrap. Vytvořit soubor a přidejte následující text:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
Přidání konfigurace do clusteru.
```
crm configure load update crm-bs.txt
```
![CRM konfigurace crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 zařízení s využitím techniky STONITH
Přidáte prostředek využitím techniky STONITH. Vytvořit soubor a přidejte následující text.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Přidání konfigurace do clusteru.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 virtuální IP adresy
Přidejte virtuální IP adresu prostředku. Vytvoření souboru a přidejte text, jak je uvedeno níže.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Přidání konfigurace do clusteru.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 ověření prostředky

Když spustíte příkaz *crm_mon*, zobrazí se tyto dva prostředky.
![crm_mon_command.PNG](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Kromě toho můžete zobrazit stav v *https://<node IP address>: 7630/cib/live/stav*

![hawlk. stav page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Testování procesu převzetí služeb při selhání
Otestovat proces převzetí služeb při selhání, zastavte službu pacemaker na Uzel1 a prostředky převzetí služeb při selhání do node2.
```
Service pacemaker stop
```
Nyní, zastavte službu pacemaker na **node2** a prostředky služeb při selhání **node1**

**Před převzetí služeb při selhání**
![před failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**po převzetí služeb při selhání**
![po failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
 ![ CRM pondělí po failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. Řešení potíží
Tato část popisuje několik scénáře selhání, které může dojít během instalace. Nemusí mít nutně tyto potíže s.

### <a name="scenario-1-cluster-node-not-online"></a>Scénář 1: Uzel clusteru není online
Pokud některý z uzlů není uveden ve Správci clusteru online, můžete zkusit opravit tímto ji do stavu online.

Spustit službu iSCSI
```
service iscsid start
```

A teď je třeba moct přihlásit do tohoto uzlu iSCSI
```
iscsiadm -m node -l
```
Očekávaný výstup vypadá jako následující
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Scénář 2: yast2 nezobrazuje grafické zobrazení
Grafické obrazovky yast2 slouží k nastavení clusteru vysokou dostupnost v tomto dokumentu. Pokud yast2 nepodporuje otevřete pomocí grafického okna, jak je znázorněno a Qt chybu, postupujte následujícím způsobem. Pokud se otevře s grafického okna, můžete přeskočit kroky.

**Chyba**

![yast2 qt grafickým uživatelským rozhraním error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Očekávaný výstup**

![yast. ovládací prvek center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Pokud yast2 s grafické zobrazení neotevře, postupujte podle následujících kroků.

Nainstalujte požadované balíčky. Musíte být přihlášení jako uživatel "root" a mít SMT nastavit na stahování a instalaci balíčků.

Chcete-li instalovat balíčky, použijte yast > softwaru > Správa softwaru > závislosti > možnost "Nainstalovat doporučené balíčky...". Následující snímek obrazovky ukazuje očekávané obrazovky.
>[!NOTE]
>Budete potřebovat k provedení kroků v obou uzlech, aby yast2 grafické zobrazení se dá dostat z obou uzlů.

![yast sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

V části závislosti, vyberte "Nainstalovat doporučené Packages" ![yast dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Zkontrolujte změny a klikněte na OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Balíček bude instalace pokračovat ![yast provádění installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Kliknutí na Další

![yast. instalace report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Klikněte na tlačítko Dokončit

Budete také muset nainstalovat balíčky libqt4 a libyui qt.
```
zypper -n install libqt4
```
![zypperu install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypperu install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypperu install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 měli být schopni otevřít grafické zobrazení nyní jak je vidět tady.
![yast2. ovládací prvek center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scénář 3: yast2 nemá možnost vysoké dostupnosti
Pro možnost vysoké dostupnosti, které mají být zobrazeny v control centeru yast2 budete muset nainstalovat další balíčky.

Pomocí Yast2 > softwaru > Správa softwaru > vyberte následující vzory

- Základní server SAP HANA
- Kompilátor C/C++ a nástroje
- Vysoká dostupnost
- Aplikační server SAP základní

Následující obrazovka ukazuje postup instalace vzory.

Pomocí yast2 > softwaru > Správa softwaru

![yast2. ovládací prvek center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Tyto vzory se dají vybrat

![yast pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Klikněte na tlačítko **přijmout**

![yast změnit packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Klikněte na tlačítko **pokračovat**

![yast2 provádění installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Klikněte na tlačítko **Další** po dokončení instalace

![yast2. instalace report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scénář 4: HANA instalace se nezdaří s chybou sestavení gcc
HANA instalace se nezdaří s následující chybou.

![Hana. instalace error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Pokud chcete problém vyřešit, je potřeba nainstalovat knihovny (libgcc_sl a libstdc ++ 6) následujícím způsobem.

![zypperu install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scénář 5: Pacemaker služba selže

Při spuštění služby pacemaker došlo k následující problémy.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Chcete-li problém odstranit, odstraňte následující řádek ze souboru */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistent.PNG](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scénář 6: Uzel nelze připojit ke clusteru 2

Při propojování node2 ke stávající clusteru s použitím *ha clusteru spojení* příkazu došlo k následující chybě.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha clusteru spojení error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Pokud chcete vyřešit, spusťte následující příkaz na oba uzly

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![SSH-keygen-node1. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![SSH-keygen Uzel2. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Po předchozím opravu Uzel2 přidají do clusteru

![ha clusteru spojení fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Obecná dokumentace
Další informace o instalaci operačního systému SUSE vysokou DOSTUPNOSTÍ najdete v následujících článcích: 

- [SAP HANA SR výkonu optimalizované scénář](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Na základě úložiště monitorování geografických zón](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog – pomocí Pacemaker clusteru pro SAP HANA – část 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog – pomocí Pacemaker clusteru pro SAP HANA – část 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
