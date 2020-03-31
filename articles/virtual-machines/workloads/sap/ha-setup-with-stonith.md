---
title: Vysoká dostupnost nastavena s STONITH pro SAP HANA v Azure (velké instance)| Dokumenty společnosti Microsoft
description: Vytvoření vysoké dostupnosti pro SAP HANA v Azure (velké instance) v SUSE pomocí STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4060dbe936af8ff1f9dd8c958f64834cb06525de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77615089"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Nastavení vysoké dostupnosti v SUSE s využitím techniky STONITH
Tento dokument obsahuje podrobné pokyny krok za krokem k nastavení vysoké dostupnosti na operačním systému SUSE pomocí zařízení STONITH.

**Zřeknutí se odpovědnosti:** *Tato příručka je odvozena testováním instalace v prostředí Microsoft HANA Large Instances, které úspěšně funguje. Vzhledem k tomu, že tým správy služeb společnosti Microsoft pro velké instance HANA nepodporuje operační systém, bude pravděpodobně nutné kontaktovat suse pro další řešení potíží nebo objasnění vrstvy operačního systému. Tým pro správu služeb společnosti Microsoft nastavuje zařízení STONITH a plně podporuje a může se zapojit do řešení problémů se zařízením STONITH.*
## <a name="overview"></a>Přehled
Chcete-li nastavit vysokou dostupnost pomocí clusteringu SUSE, musí splňovat následující požadavky.
### <a name="pre-requisites"></a>Požadavky
- Velké instance HANA jsou zřízeny
- Operační systém je registrován
- Servery hana large instancejsou připojeny k serveru SMT, aby získaly opravy/balíčky
- Operační systém má nainstalovány nejnovější opravy
- NTP (časový server) je nastaven
- Přečtěte si a porozuměte nejnovější verzi dokumentace SUSE při nastavení HA

### <a name="setup-details"></a>Podrobnosti o nastavení
Tato příručka používá následující nastavení:
- Operační systém: SLES 12 SP1 pro SAP
- Velké instance HANA: 2xS192 (čtyři sokety, 2 TB)
- Hana verze: HANA 2.0 SP1
- Názvy serverů: sapprdhdb95 (node1) a sapprdhd196 (node2)
- ZAŘÍZENÍ STONITH: zařízení STONITH založené na iSCSI
- NTP nastaveno na jednom z uzlů velké instance HANA

Při nastavování velkých instancí HANA pomocí hsr můžete požádat tým správy služeb společnosti Microsoft o nastavení stonithu. Pokud jste již existující zákazník, který má hana velké instance zřízené a potřebují stonith zařízení nastavit pro vaše existující okna, je třeba poskytnout následující informace týmu Microsoft Service Management ve formuláři žádosti o služby (SRF). Formulář SRF můžete požádat prostřednictvím správce technického účtu nebo kontaktu společnosti Microsoft pro připojení velké instance HANA. Noví zákazníci mohou požádat o zařízení STONITH v době zřizování. Vstupy jsou k dispozici ve formuláři žádosti o zřízení.

- Název serveru a IP adresa serveru (například myhanaserver1, 10.35.0.1)
- Umístění (například USA – východ)
- Jméno zákazníka (například Microsoft)
- SID - Identifikátor systému HANA (například H11)

Jakmile je zařízení STONITH nakonfigurováno, tým Microsoft Service Management vám poskytne název zařízení SBD a IP adresu úložiště iSCSI, které můžete použít ke konfiguraci nastavení STONITH. 

Chcete-li nastavit komplexní ha pomocí STONITH, je třeba dodržovat následující kroky:

1.  Identifikace zařízení SBD
2.  Inicializovat zařízení SBD
3.  Konfigurace clusteru
4.  Nastavení Softdog Watchdog
5.  Připojení uzlu ke clusteru
6.  Ověření clusteru
7.  Konfigurace prostředků do clusteru
8.  Otestovat proces převzetí služeb při selhání

## <a name="1---identify-the-sbd-device"></a>1. Identifikujte zařízení SBD
Tato část popisuje, jak určit zařízení SBD pro vaše nastavení poté, co tým správy služeb společnosti Microsoft nakonfiguroval stonith. **Tato část se vztahuje pouze na stávajícího zákazníka**. Pokud jste nový zákazník, tým pro správu služeb společnosti Microsoft poskytuje název zařízení SBD a můžete tuto část přeskočit.

1.1 Upravte */etc/iscsi/iniciátorname.isci* na 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Správa služeb společnosti Microsoft poskytuje tento řetězec. Upravte soubor na **obou** uzlech, ale číslo uzlu se liší na každém uzlu.

![iniciátor.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 Změnit */etc/iscsi/iscsid.conf*: Nastavte *node.session.timeo.replacement_timeout=5* a *node.startup = automatické*. Upravte soubor v **obou** uzlech.

1.3 Spusťte příkaz zjišťování, zobrazuje čtyři relace. Spusťte jej na obou uzlech.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 Spusťte příkaz pro přihlášení k zařízení iSCSI, zobrazuje čtyři relace. Spusťte jej na **obou** uzlech.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 Spusťte skript opětovného prohledávaní: *rescan-scsi-bus.sh*.  Tento skript zobrazuje nové disky vytvořené pro vás.  Spusťte jej na obou uzlech. Měli byste vidět číslo LOGICKÉ JEDNOTKY, které je větší než nula (například: 1, 2 atd.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 Chcete-li získat název zařízení, spusťte příkaz *fdisk –l*. Spusťte jej na obou uzlech. Vyberte zařízení o velikosti **178 MiB**.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. Inicializovat zařízení SBD

2.1 Inicializovat zařízení SBD na **obou** uzlech

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Zkontrolujte, co bylo napsáno do zařízení. Udělejte to na **obou** uzlech

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. Konfigurace clusteru
Tato část popisuje kroky k nastavení clusteru SUSE HA.
### <a name="31-package-installation"></a>3.1 Instalace balíčku
3.1.1 Zkontrolujte, zda jsou nainstalovány vzory ha_sles a SAPHanaSR-doc. Pokud není nainstalován, nainstalujte je. Nainstalujte jej na **obou** uzlech.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 Nastavení clusteru
3.2.1 Můžete buď použít příkaz *ha-cluster-init,* nebo použít průvodce yast2 k nastavení clusteru. V tomto případě je použit průvodce yast2. Tento krok provedete **pouze v primárním uzlu**.

Sledujte yast2> ![vysoká dostupnost > Cluster](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-control-center.png yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Klepněte na tlačítko **Storno,** protože balíček halk2 je již nainstalován.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Klikněte na **Pokračovat.**

Očekávaná hodnota=Počet nasazených uzlů (v ![tomto případě 2)](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) yast-Cluster-Security.png Klepněte na tlačítko](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) **Další**
![yast-cluster-configure-csync2.png Přidat názvy uzlů a potom kliknout na tlačítko Přidat navrhované soubory.

Klikněte na "Turn csync2 ON"

Klikněte na "Generovat pre-shared-keys", to ukazuje níže popup

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Klikněte na tlačítko **OK**.

Ověřování se provádí pomocí IP adres a předsdílených klíčů v Csync2. Soubor klíče je generován pomocí csync2 -k /etc/csync2/key_hagroup. Soubor key_hagroup by měl být zkopírován do všech členů clusteru ručně po jeho vytvoření. **Ujistěte se, že zkopírovat soubor z uzlu 1 do uzlu2**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Klikněte na **další**
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Ve výchozí možnosti bylo zavedení vypnuto, změňte jej na "zapnuto", takže kardiostimulátor je spuštěn při startu. Můžete provést volbu na základě vašich požadavků na nastavení.
Klepněte na tlačítko **Další** a konfigurace clusteru je dokončena.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. Nastavení Softdog Watchdog
Tato část popisuje konfiguraci watchdog (softdog).

4.1 Přidejte následující řádek na */etc/init.d/boot.local* na **obou** uzlech.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 Aktualizujte soubor */etc/sysconfig/sbd* na **obou** uzlech takto:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 Načíst modul jádra na **obou** uzlech spuštěním následujícího příkazu
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 Zkontrolujte a ujistěte se, že softdog běží takto na **obou** uzlech:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 Spuštění zařízení SBD na **obou** uzlech
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 Otestujte daemon SBD na **obou** uzlech. Zobrazí se dvě položky po konfiguraci na **obou** uzlech
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 Odeslání testovací zprávy **do jednoho** z uzlů
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 Na **druhém** uzlu (node2) můžete zkontrolovat stav zprávy
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 Chcete-li přijmout konfiguraci sbd, aktualizujte soubor */etc/sysconfig/sbd* následujícím. Aktualizace souboru v **obou** uzlech
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 Spuštění služby kardiostimulátoru na **primárním uzlu** (uzel1)
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Pokud se služba kardiostimulátoru *nezdaří*, podívejte se na *scénář 5: Služba kardiostimulátoru se nezdaří*

## <a name="5---joining-the-cluster"></a>5. Připojení ke clusteru
Tato část popisuje, jak připojit uzel ke clusteru.

### <a name="51-add-the-node"></a>5.1 Přidání uzlu
Spusťte následující příkaz na **node2,** aby se node2 připojil ke clusteru.
```
ha-cluster-join
```
Pokud se během připojování ke clusteru zobrazí *chyba,* přečtěte si *scénář 6: Uzel 2, který nelze připojit ke clusteru*.

## <a name="6---validating-the-cluster"></a>6. Ověření clusteru

### <a name="61-start-the-cluster-service"></a>6.1 Spuštění clusterové služby
Chcete-li zkontrolovat a volitelně spustit cluster poprvé na **obou** uzlech.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 Sledování stavu
Spusťte příkaz *crm_mon* a ujistěte se, že **oba** uzly jsou online. Můžete jej spustit **na libovolném uzlu** clusteru
```
crm_mon
```
![crm-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) Můžete se také přihlásit do hawk u zkontrolovat stav clusteru *https://\<ip uzlu>:7630*. Výchozí uživatel je hacluster a heslo je linux. V případě potřeby můžete změnit heslo pomocí příkazu *passwd.*

## <a name="7-configure-cluster-properties-and-resources"></a>7. Konfigurace vlastností a prostředků clusteru 
Tato část popisuje kroky konfigurace prostředků clusteru.
V tomto příkladu nastavte následující prostředek, zbytek lze nakonfigurovat (v případě potřeby) odkazem na průvodce SUSE HA. Proveďte konfiguraci pouze v **jednom z uzlů.** Proveďte na primárníuzel.

- Zaváděcí past clusteru
- Zařízení STONITH
- Virtuální IP adresa


### <a name="71-cluster-bootstrap-and-more"></a>7.1 Zaváděcí past clusteru a další
Přidejte zaváděcí past clusteru. Vytvořte soubor a přidejte text takto:
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
Přidejte konfiguraci do clusteru.
```
crm configure load update crm-bs.txt
```
![crm-configure-crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 Zařízení STONITH
Přidejte prostředek STONITH. Vytvořte soubor a přidejte text takto.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Přidejte konfiguraci do clusteru.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 Virtuální IP adresa
Přidejte virtuální IP adresu prostředku. Vytvořte soubor a přidejte text, jak je uvedeno níže.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Přidejte konfiguraci do clusteru.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 Ověření zdrojů

Při spuštění příkazu *crm_mon*, můžete vidět dva prostředky tam.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Také můžete vidět stav na *adrese IP uzlu\<https://>:7630/cib/live/state*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Testování procesu převzetí služeb při selhání
Chcete-li otestovat proces převzetí služeb při selhání, zastavte službu kardiostimulátoru na node1 a prostředky převzetí služeb při selhání na node2.
```
Service pacemaker stop
```
Nyní zastavit službu kardiostimulátoru na **node2** a prostředky se nezdařilo **node1**

**Před převzetím služeb při selhání**  
![Před převzetím služeb při selhání.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Po převzetí služeb při selhání**  
![po převzetí služeb při selhání.png](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![crm-mon-after-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. Řešení potíží
Tato část popisuje několik scénářů selhání, které mohou narazit během instalace. Nemusíte nutně čelit těmto problémům.

### <a name="scenario-1-cluster-node-not-online"></a>Scénář 1: Uzel clusteru není online
Pokud některý z uzlů nezobrazuje online ve Správci clusteru, můžete zkusit následující převést do režimu online.

Spuštění služby iSCSI
```
service iscsid start
```

A nyní byste měli být schopni se přihlásit k tomuto uzlu iSCSI
```
iscsiadm -m node -l
```
Očekávaný výstup vypadá tak, že
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
Grafická obrazovka yast2 se používá k nastavení clusteru s vysokou dostupností v tomto dokumentu. Pokud yast2 neotevře s grafické okno, jak je znázorněno a vyvolat Chybu Qt, proveďte následující kroky. Pokud se otevře s grafickým oknem, můžete kroky přeskočit.

**Chyba**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Očekávaný výstup**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Pokud se yast2 neotevře s grafickým zobrazením, postupujte podle následujících kroků.

Nainstalujte požadované balíčky. Musíte být přihlášeni jako uživatel "root" a mít SMT nastavit ke stažení / instalaci balíčků.

Chcete-li nainstalovat balíčky, použijte yast>Software>Software Management>závislosti> možnost "Instalace doporučených balíčků...". Následující snímek obrazovky ilustruje očekávané obrazovky.
>[!NOTE]
>Je třeba provést kroky na obou uzlech, takže můžete přistupovat k grafickému zobrazení yast2 z obou uzlů.

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

V části Závislosti vyberte "Instalovat doporučené balíčky". ![](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Zkontrolujte změny a stiskněte tlačítko OK

![Yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Instalace balíčku ![pokračuje yast-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Kliknutí na Další

![yast-instalace-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Klikněte na Dokončit.

Musíte také nainstalovat balíčky libqt4 a libyui-qt.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 by měl být schopen otevřít grafické zobrazení nyní, jak je znázorněno zde.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scénář 3: yast2 není možnost vysoké dostupnosti
Aby byla možnost Vysoká dostupnost viditelná v řídicím centru yast2, je třeba nainstalovat další balíčky.

Použití>správy softwaru Yast2>Software>Vyberte následující vzory

- Základna serveru SAP HANA
- Kompilátor a nástroje c/c++
- Vysoká dostupnost
- Základ aplikačního serveru SAP

Následující obrazovka ukazuje kroky k instalaci vzorů.

Použití yast2 > Software > Software Management

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Výběr vzorků

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Klikněte na **Přijmout.**

![yast-změnil-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Klikněte na **Pokračovat.**

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Po dokončení instalace klepněte na tlačítko **Další.**

![yast2-instalace-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scénář 4: Instalace HANA se nezdaří s chybou sestavení gcc
Instalace HANA se nezdaří s následující chybou.

![Hana-instalace-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Chcete-li tento problém vyřešit, je třeba nainstalovat knihovny (libgcc_sl a libstdc ++6) takto.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scénář 5: Služba kardiostimulátoru se nezdaří

Při spuštění služby kardiostimulátoru došlo k následujícímu problému.

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
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
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

Chcete-li jej opravit, odstraňte následující řádek ze souboru */usr/lib/systemd/system/system/fstrim.timer*

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scénář 6: Uzel 2 se nemůže připojit ke clusteru

Při připojovat node2 k existujícímu clusteru pomocí příkazu *ha-cluster-join* došlo k následující chybě.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Chcete-li opravit, spusťte následující na obou uzlech

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-node1. Png](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-node2. Png](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Po předchozí opravě by měl být uzel2 přidán do clusteru.

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Obecná dokumentace
Další informace o nastavení SUSE HA naleznete v následujících článcích: 

- [Sap HANA SR optimalizovaný scénář výkonu](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Oplocení na bázi skladování](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog - Použití kardiostimulátoru clusteru pro SAP HANA- Část 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog - Použití kardiostimulátoru clusteru pro SAP HANA-část 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
