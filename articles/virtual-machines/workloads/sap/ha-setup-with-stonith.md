---
title: Vysoká dostupnost nastaven STONITH pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: Vytvoření vysoké dostupnosti pro SAP HANA v Azure (velké instance) ve SUSE pomocí STONITH
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
ms.openlocfilehash: 982c6112a19654e268c9c50fec35d65fbc1766c2
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062016"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Nastavení v SUSE pomocí STONITH vysokou dostupnost
Tento dokument obsahuje podrobné pokyny krok za krokem nastavit vysokou dostupnost na SUSE operační systém pomocí STONITH zařízení.

**Právní omezení:** *Tato příručka se vypočítá testování instalace v prostředí Microsoft HANA velké instancí, které úspěšně funguje. Jako tým správy služby Microsoft pro velké instance HANA nepodporuje operační systém, budete muset kontaktovat SUSE pro všechny další informace o řešení a vysvětlení na vrstvě operačního systému. Tým správy služby Microsoft nastavit STONITH zařízení a plně podporuje a může být zahrnuté pro řešení potíží pro problémy s STONITH zařízení.*
## <a name="overview"></a>Přehled
Pokud chcete nastavit vysokou dostupnost použití clusteringu SUSE, musí splňovat následující požadavky.
### <a name="pre-requisites"></a>Požadavky
- Zřízení instance velké HANA
- Operační systém je zaregistrován.
- HANA velké instancí serverů připojeni k serveru SMT opravy nebo balíčky
- Nainstalovány nejnovější opravy operačního systému
- Nastavení protokolu NTP (čas serveru)
- Čtení a pochopení nejnovější verzi dokumentace SUSE na HA instalace

### <a name="setup-details"></a>Podrobnosti nastavení
Tato příručka používá následující nastavení:
- Operační systém: SLES 12 SP1 pro SAP
- Velké instancí HANA: 2xS192 (čtyři soketů, 2 TB)
- Verze HANA: HANA 2.0 SP1
- Názvy serverů: sapprdhdb95 (Uzel1) a sapprdhdb96 (Uzel2)
- STONITH zařízení: zařízení STONITH iSCSI na základě
- Nastavit na jednom z uzlů velké Instance HANA NTP

Při nastavování HANA velké instancí s HSR, můžete požádat tým správy služby Microsoft nastavit STONITH. Pokud jste již stávající zákazník, který má HANA velké instancí zřízený a nutnosti zařízení STONITH nastavení pro vaše stávající oken, budete muset zadejte následující informace týmu Microsoft Service Management v formulář žádosti o službu (SRF). Může požádat o SRF formuláře prostřednictvím Technical Account manažera nebo Contact vaší společnosti Microsoft pro velké Instance HANA registrace. Nové zákazníky může požádat o STONITH zařízení během zřizování. Vstupní hodnoty jsou k dispozici ve formuláři zřizování požadavku.

- Název serveru a serveru IP adresu (například myhanaserver1, 10.35.0.1)
- Umístění (například USA – východ)
- Jméno zákazníka (například Microsoft)
- Identifikátor SID - identifikátor HANA systému (například H11)

Po nakonfigurování zařízení STONITH tým správy služby Microsoft poskytují SBD název zařízení a IP adresu úložiště iSCSI, které můžete použít ke konfiguraci nastavení STONITH. 

Pokud chcete nastavit HA koncová pomocí STONITH, musí být sledována následující kroky:

1.  Identifikaci SBD zařízení
2.  Inicializace SBD zařízení
3.  Konfigurace clusteru
4.  Nastavení Softdog sledovacího zařízení
5.  Připojení k uzlu do clusteru
6.  Ověření clusteru
7.  Konfigurace prostředků do clusteru
8.  Otestujte proces převzetí služeb při selhání

## <a name="1---identify-the-sbd-device"></a>1.   Identifikaci SBD zařízení
Tato část popisuje, jak určit SBD zařízení pro vaše instalace po tým správy služby Microsoft byl nakonfigurován STONITH. **Tato část se týká pouze stávající zákazník**. Pokud jste zákazník s novou, tým správy služby Microsoft poskytuje SBD názvu zařízení vám a vy můžete tuto část přeskočit.

1.1 upravit */etc/iscsi/initiatorname.isci* na 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Správa služeb Microsoft, zadejte tento řetězec. Upravte soubor na **i** uzly, ale číslo uzlu se liší v každém uzlu.

![initiatorname.PNG](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 upravit */etc/iscsi/iscsid.conf*: nastavte *node.session.timeo.replacement_timeout=5* a *node.startup = automatické*. Upravte soubor na **i** uzly.

1.3 proveďte příkaz zjišťování, zobrazuje čtyři relací. Ho spusťte v obou uzlech.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 spustit příkaz k přihlášení do zařízení iSCSI, se zobrazí čtyři relací. Spusťte ho na **i** uzly.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 spuštění nového prohledání skriptu: *Prohledat znovu. scsi bus.sh*.  Tento skript je ukázkou, nové disky, které jsou vytvořené pro vás.  Ho spusťte v obou uzlech. Měli byste vidět číslo logické jednotky, je větší než nula (například: 1, 2 atd.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.PNG](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 se získat název zařízení, spusťte příkaz *fdisk – l*. Ho spusťte v obou uzlech. Vyberte zařízení s velikostí **178 MiB**.

```
  fdisk –l
```

![Fdisk l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Inicializace SBD zařízení

2.1 inicializovat zařízení SBD na **i** uzly

```
sbd -d <SBD Device Name> create
```
![sbdcreate.PNG](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Zkontrolujte, co byl zapsán do zařízení. Provést na **i** uzly

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   Konfigurace clusteru
Tato část popisuje postup nastavení clusteru SUSE HA.
### <a name="31-package-installation"></a>Verze 3.1 instalace balíčku
3.1.1 prosím zkontrolujte, zda jsou nainstalovány ha_sles a vzory SAPHanaSR dokumentů. Pokud nainstalovaná není, nainstalujte je. Nainstalujte na **i** uzly.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.PNG](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 nastavení clusteru
3.2.1, můžete použít *ha-cluster-init* příkazu, nebo pomocí Průvodce yast2 nastavte cluster. V takovém případě se používá Průvodce yast2. Provést tento krok **pouze na primárním uzlu**.

Postupujte podle yast2 > vysokou dostupnost > clusteru ![yast. ovládací prvek center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast. hawk install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Klikněte na tlačítko **zrušit** vzhledem k tomu, že halk2 balíček je již nainstalován.

![yast. hawk continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Klikněte na tlačítko **pokračovat**

Očekávaná hodnota = počet uzlů (v tomto případě 2) nasazený ![yast-Cluster-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) klikněte na tlačítko **Další**
![yast-cluster konfigurace csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) přidat názvy a pak klikněte na tlačítko "Přidat navrhované soubory"

Klikněte na tlačítko "Zapnout csync2"

Klikněte na tlačítko "Vygenerovat vedlejší Shared klíče", se zobrazí pod automaticky otevřeném okně.

![yast. klíč file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Klikněte na tlačítko **OK**.

Ověřování se provádí pomocí IP adresy a vedlejší shared klíče v Csync2. Soubor klíče se vygeneruje s csync2 -k /etc/csync2/key_hagroup. Key_hagroup soubor by měl být u všech členů clusteru ručně zkopírovat po jejím vytvoření. **Ujistěte se, při kopírování souboru z uzlu 1 na Uzel2**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Klikněte na tlačítko **Další**
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Výchozí možnost spuštění byl vypnutý, změňte jej na "on", který je kardiostimulátor spuštění na spouštěcí. Můžete nastavit možnost podle požadavků na instalaci.
Klikněte na tlačítko **Další** a konfigurace clusteru byla dokončena.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Nastavení Softdog sledovacího zařízení
Tato část popisuje konfiguraci sledovací zařízení (softdog).

4.1 přidejte následující řádek na */etc/init.d/boot.local* na **i** uzly.
```
modprobe softdog
```
![modprobe softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 aktualizovat soubor */etc/sysconfig/sbd* na **i** uzly jako následující:
```
SBD_DEVICE="<SBD Device Name>"
```
![SBD device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 načíst modul jádra na **i** uzly spuštěním následujícího příkazu
```
modprobe softdog
```
![modprobe. softdog command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 zkontrolujte a ujistěte se, že softdog běží jako následující na **i** uzly:
```
lsmod | grep dog
```
![lsmod. grep dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 spuštění zařízení SBD na **i** uzly
```
/usr/share/sbd/sbd.sh start
```
![SBD Zo – start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 testování démon SBD na **i** uzly. Zobrazí dvě položky po dokončení konfigurace na **i** uzly
```
sbd -d <SBD Device Name> list
```
![SBD list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 odeslat testovací zprávu a **jeden** uzly
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![SBD list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 na **druhý** uzlu (Uzel2) můžete zkontrolovat stav zprávy
```
sbd  -d <SBD Device Name> list
```
![SBD. seznam message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 konfigurace sbd přijmout, aktualizujte soubor */etc/sysconfig/sbd* jako následující. Aktualizovat soubor na **i** uzly
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 spusťte službu kardiostimulátor na **primárního uzlu** (Uzel1)
```
systemctl start pacemaker
```
![Počáteční pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Pokud službu kardiostimulátor *selže*, odkazovat na *scénář 5: kardiostimulátor služby selže*

## <a name="5---joining-the-cluster"></a>5.   Připojení clusteru
Tato část popisuje postup připojení k uzlu do clusteru.

### <a name="51-add-the-node"></a>5.1 přidat uzel
Spusťte následující příkaz **Uzel2** umožníte Uzel2 připojit ke clusteru.
```
ha-cluster-join
```
Pokud se zobrazí *chyba* během připojení clusteru, naleznete v *scénář 6: uzlu 2 nelze připojit ke clusteru*.

## <a name="6---validating-the-cluster"></a>6.   Ověření clusteru

### <a name="61-start-the-cluster-service"></a>6.1 spuštění služby clusteru se
Ke kontrole a volitelně start clusteru na prvním **i** uzlů.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl. stav pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 monitorování stavu
Spusťte příkaz *crm_mon* zajistit **i** uzly jsou online. Můžete ji spustit na **jakéhokoli z těchto uzlů** clusteru
```
crm_mon
```
![CRM mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) se může také přihlásit k hawk a zkontrolujte stav clusteru *https://<node IP>: 7630*. Výchozí uživatel se hacluster a heslo je linux. V případě potřeby můžete změnit heslo pomocí *hesel* příkaz.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Konfigurovat vlastnosti clusteru a prostředky 
Tato část popisuje postup pro konfiguraci prostředků clusteru.
V tomto příkladu, nastavte následující zdroj zbývající lze nastavit (v případě potřeby) pod položkou Průvodce SUSE HA. Provedení konfigurace v **jednoho z uzlů** pouze. Proveďte na primárním uzlu.

- Bootstrap clusteru
- STONITH zařízení
- Virtuální IP adresa


### <a name="71-cluster-bootstrap-and-more"></a>7.1 bootstrap clusteru a další
Přidejte bootstrap clusteru. Vytvoření souboru a přidejte text jako následující:
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

### <a name="72-stonith-device"></a>7.2 STONITH zařízení
Přidáte prostředek STONITH. Vytvoření souboru a přidejte text jako následující.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15" \
op monitor interval="15" timeout="15"
```
Přidání konfigurace do clusteru.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 virtuální IP adresy
Přidáte prostředek virtuální IP adresy. Vytvoření souboru a přidejte text jak je uvedeno níže.
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

Při spuštění příkazu *crm_mon*, zobrazí se tyto dva prostředky.
![crm_mon_command.PNG](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Navíc můžete zobrazit stav v *https://<node IP address>: 7630 nebo cib nebo za provozu nebo stavu*

![hawlk. stav page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Testování procesů převzetí služeb při selhání
Otestování procesu převzetí služeb při selhání, zastavte službu kardiostimulátor na Uzel1 a převzetí služeb při selhání prostředky k Uzel2.
```
Service pacemaker stop
```
Nyní, zastavte službu kardiostimulátor na **Uzel2** a prostředky převzal **UZEL1**

**Před převzetí služeb při selhání**
![před failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**po převzetí služeb při selhání**
![po failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
 ![ CRM mon po failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. Řešení potíží
Tato část popisuje několik scénářů selhání, které mohou být zjištěny během instalace. Nemusí mít nutně tyto potíže.

### <a name="scenario-1-cluster-node-not-online"></a>Scénář 1: Uzel clusteru není online
Pokud některé uzly nejsou zobrazeny ve Správci clusteru online, můžete zkusit následující uvést do režimu online.

Spuštění služby iSCSI
```
service iscsid start
```

A teď mělo by být možné se přihlásit k tomuto uzlu iSCSI
```
iscsiadm -m node -l
```
Očekávaný výstup vypadá podobně jako následující
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
Na obrazovce grafické yast2 se používá k nastavení vysokou dostupnost clusteru v tomto dokumentu. Pokud yast2 nepodporuje otevře se okno grafické jako na obrázku a throw RT chyby, proveďte kroky jako následující. Pokud se otevře s okno grafického rozhraní, můžete přeskočit kroky.

**Chyba**

![yast2 RT grafickým uživatelským rozhraním error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Očekávaný výstup**

![yast – ovládací prvek center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Pokud yast2 nelze otevřít v aplikaci grafické zobrazení, postupujte podle následujících kroků.

Nainstalujte požadované balíčky. Musíte být přihlášení jako uživatel "root" a mít SMT nastavit na stažení a instalaci balíčků.

Chcete-li instalaci balíčků, použijte yast > softwaru > Správa softwaru > závislosti > možnost "Instalace doporučená balíčky...". Následující snímek obrazovky ukazuje očekávané obrazovky.
>[!NOTE]
>Musíte provést kroky v obou uzlech, takže grafické zobrazení yast2 může získat přístup z obou uzlů.

![yast sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

V části závislosti, vyberte "Instalovat doporučené Packages" ![yast dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Zkontrolujte změny a klikněte na OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Balíček bude instalace pokračovat ![yast provádění installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Kliknutí na Další

![yast. instalace report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Klikněte na tlačítko Dokončit

Budete také muset nainstalovat balíčky libqt4 a libyui RT.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 by měla být schopni otevřít grafické zobrazení nyní jak je uvedené v tomto poli.
![yast2 – ovládací prvek center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scénář 3: yast2 nemá možnost vysoké dostupnosti
Pro možnost vysoké dostupnosti mají být zobrazeny na řídicí centrum yast2 musíte nainstalovat další balíčky.

Pomocí Yast2 > softwaru > Správa softwaru > vyberte následující vzorky

- Základní SAP HANA serveru
- Kompilátor C/C++ a nástroje
- Vysoká dostupnost
- Základní SAP aplikační server

Následující obrazovka ukazuje postup instalace vzory.

Pomocí yast2 > softwaru > Správa softwaru

![yast2 – ovládací prvek center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Vyberte vzory

![yast pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Klikněte na tlačítko **přijmout**

![yast změnit packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Klikněte na tlačítko **pokračovat**

![yast2 provádění installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Klikněte na tlačítko **Další** po dokončení instalace

![yast2. instalace report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scénář 4: HANA instalace se nezdaří s chybou RSZ sestavení
HANA instalace se nezdaří s následující chybou.

![Hana. instalace error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Pokud chcete vyřešit problém, je potřeba nainstalovat knihovny (libgcc_sl a libstdc ++ 6) jako následující.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scénář 5: Kardiostimulátor služby selže

Během spouštění služby kardiostimulátor došlo k následujícím potížím.

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

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scénář č. 6: Uzel 2 nelze připojit ke clusteru

Při připojení ke stávající Uzel2 clusteru pomocí *ha-cluster spojení* příkazu došlo k následující chybě.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster spojení error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Pokud chcete vyřešit, spusťte následující na obou uzlech

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![SSH-keygen Uzel1. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![SSH-keygen Uzel2. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Po předchozí opravu Uzel2 by se přidají do clusteru

![ha-cluster spojení fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Obecná dokumentace
Další informace o instalaci SUSE HA najdete v následujících článcích: 

- [SAP HANA SR výkonu optimalizované scénář](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Na základě úložiště vymezení](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog - pomocí kardiostimulátor clusteru pro SAP HANA – část 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog - pomocí kardiostimulátor clusteru pro SAP HANA - část 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)