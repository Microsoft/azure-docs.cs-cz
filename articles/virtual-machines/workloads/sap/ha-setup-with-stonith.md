---
title: Nastavení vysoké dostupnosti s STONITH pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: Zajištění vysoké dostupnosti pro SAP HANA v Azure (velké instance) v SUSE pomocí STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d97130251f7ca56adaf77c5e70d6f08bd5cf514
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101514"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Nastavení vysoké dostupnosti v SUSE pomocí STONITH
Tento dokument popisuje podrobné pokyny k nastavení vysoké dostupnosti v SUSE operačním systému pomocí zařízení STONITH.

**Právní omezení** *Tato příručka je odvozená pomocí testování nastavení v prostředí velkých instancí Microsoft HANA, které úspěšně funguje. Protože tým Microsoft Service Management pro velké instance HANA nepodporuje operační systém, možná budete muset kontaktovat SUSE a vyřešit případné další řešení potíží nebo objasnění vrstvy operačního systému. Tým Microsoftu pro správu služeb nastavil zařízení STONITH a plně podporuje a může se zapojit do řešení potíží s STONITH problémy zařízení.*
## <a name="overview"></a>Přehled
K nastavení vysoké dostupnosti pomocí clusteringu SUSE musí splňovat tyto požadavky.
### <a name="pre-requisites"></a>Požadavky
- Velké instance HANA se zřídí
- Operační systém je zaregistrován.
- Servery velkých instancí HANA jsou připojené k serveru SMT, aby získali opravy a balíčky.
- Operační systém má nainstalované nejnovější opravy.
- Protokol NTP (časový server) je nastavený.
- Přečtěte si nejnovější verzi SUSE dokumentaci k instalaci HA.

### <a name="setup-details"></a>Podrobnosti o nastavení
Tato příručka používá následující nastavení:
- Operační systém: SLES 12 SP1 pro SAP
- Velké instance HANA: 2xS192 (čtyři sokety, 2 TB)
- Verze HANA: HANA 2,0 SP1
- Názvy serverů: sapprdhdb95 (Uzel1) a sapprdhdb96 (Uzel2)
- Zařízení STONITH: zařízení STONITH založené na iSCSI
- Protokol NTP nastavený na jednom z uzlů velkých instancí HANA

Když nakonfigurujete velké instance HANA pomocí HSR, můžete požádat tým Microsoftu pro správu služeb, aby nastavil STONITH. Pokud už jste stávající zákazník, který má zřízené velké instance HANA, a potřebujete zařízení STONITH nastavené pro vaše existující okna, musíte do formuláře žádosti o službu (SRF) ve formuláři žádosti o služby zadat následující informace. SRF formulář si můžete vyžádat prostřednictvím Správce technického účtu nebo svého kontaktu Microsoftu pro velké instance služby HANA. Noví zákazníci si můžou v době zřizování požádat o zařízení STONITH. Vstupy jsou k dispozici ve formuláři žádosti o zřízení.

- Název serveru a IP adresa serveru (například myhanaserver1, 10.35.0.1)
- Umístění (například USA – východ)
- Jméno zákazníka (například Microsoft)
- Identifikátor systému SID – HANA (například H11)

Po nakonfigurování zařízení STONITH vám tým Microsoftu pro správu služeb poskytne název zařízení SBD a IP adresu úložiště iSCSI, které můžete použít ke konfiguraci nastavení STONITH. 

Pro nastavení koncového HA pomocí STONITH je potřeba splnit následující kroky:

1.  Identifikace zařízení SBD
2.  Inicializace zařízení SBD
3.  Konfigurace clusteru
4.  Nastavení sledovacího zařízení Softdog
5.  Připojte uzel ke clusteru.
6.  Ověřit cluster
7.  Konfigurace prostředků do clusteru
8.  Test procesu převzetí služeb při selhání

## <a name="1---identify-the-sbd-device"></a>1.   Identifikace zařízení SBD
Tato část popisuje, jak určit zařízení SBD pro vaši instalaci poté, co tým Microsoftu pro správu služeb nakonfiguruje STONITH. **Tato část se vztahuje pouze na stávajícího zákazníka**. Pokud jste nový zákazník, tým Microsoftu pro správu služeb vám poskytne název zařízení SBD a tuto část můžete přeskočit.

1,1 upravit */etc/iSCSI/initiatorname.iSCI* na 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Služba Microsoft Service Management poskytuje tento řetězec. Upravte soubor na **obou** uzlech, ale číslo uzlu je v každém uzlu jiné.

![iniciátor. png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1,2 upravit */etc/iSCSI/iscsid.conf*: Nastavte *Node. Session. Timeo. replacement_timeout = 5* a *Node. Startup = Automatic*. Upravte soubor na **obou** uzlech.

1,3 spuštěním příkazu pro zjišťování se zobrazí čtyři relace. Spusťte ji na obou uzlech.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1,4 spuštěním příkazu pro přihlášení k zařízení iSCSI se zobrazí čtyři relace. Spusťte ji na **obou** uzlech.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1,5 spusťte skript opětovného prohledání: *rescan-SCSI-Bus.sh*.  Tento skript vám ukáže nové disky, které jste pro vás vytvořili.  Spusťte ji na obou uzlech. Mělo by se zobrazit číslo logické jednotky (LUN), které je větší než nula (například: 1, 2 atd.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1,6 Chcete-li získat název zařízení, spusťte příkaz *fdisk – l*. Spusťte ji na obou uzlech. Vyberte zařízení s velikostí **178 MIB**.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Inicializace zařízení SBD

2,1 inicializace zařízení SBD na uzlech

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2,2 Podívejte se, co bylo zapsáno do zařízení. Udělejte to na **obou** uzlech

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   Konfigurace clusteru
Tato část popisuje kroky pro nastavení clusteru SUSE HA.
### <a name="31-package-installation"></a>instalace balíčku 3,1
3.1.1 Zkontrolujte prosím, že jsou nainstalované vzory ha_sles a SAPHanaSR-doc. Pokud není nainstalován, nainstalujte je. Nainstalujte ji do **obou** uzlů.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3,2 nastavení clusteru
3.2.1 můžete buď použít příkaz *ha-cluster-init* , nebo můžete použít Průvodce YaST2 k nastavení clusteru. V tomto případě se používá Průvodce YaST2. Tento krok provedete **pouze v primárním uzlu**.

Sledujte YaST2 > vysoké dostupnosti > clusteru ![YaST-Control-Center. png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![YaST-Hawk-Install. png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Klikněte na tlačítko **Storno** , protože balíček halk2 je již nainstalován.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Klikněte na **pokračovat** .

Očekávaná hodnota = počet nasazených uzlů (v tomto případě 2) ![YaST-cluster-Security. png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) klikněte na **Další**
![YaST-cluster-Configure-csync2.](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) png přidat názvy uzlů a pak klikněte na Přidat. navrhované soubory "

Klikněte na zapnout csync2.

Klikněte na "generovat předsdílené klíče", zobrazí se pod místní nabídkou.

![YaST-Key-File. png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Klikněte na tlačítko **OK**.

Ověřování se provádí pomocí IP adres a předsdílených klíčů v Csync2. Soubor klíče se vygeneruje s csync2-k/etc/csync2/key_hagroup. Soubor key_hagroup by měl být po vytvoření zkopírován do všech členů clusteru ručně. Nezapomeňte **zkopírovat soubor z uzlu 1 do Uzel2**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Klikněte na **Další**
![YaST-Cluster-Service. png.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Ve výchozí možnosti bylo spuštění vypnuto, změňte ho na zapnuto, aby se Pacemaker spustil při spuštění. Můžete vybrat podle požadavků na instalaci.
Klikněte na **Další** a konfigurace clusteru se dokončí.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Nastavení sledovacího zařízení Softdog
Tato část popisuje konfiguraci sledovacího zařízení (softdog).

4,1 přidejte následující řádek do */etc/init.d/Boot.Local* v **obou** uzlech.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4,2 aktualizujte soubor */etc/sysconfig/SBD* na **obou** uzlech následujícím způsobem:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4,3 načtěte modul jádra na **obou** uzlech spuštěním následujícího příkazu.
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4,4 Zkontrolujte a zajistěte, aby na uzlech běžela softdog, a to následujícím způsobem:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4,5 spustit zařízení SBD v **obou** uzlech
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4,6 otestujte démon SBD na **obou** uzlech. Po nakonfigurování na uzlech se zobrazí dvě položky.
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,7 odeslání zkušební zprávy na **jeden** z vašich uzlů
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,8 na **druhém** uzlu (Uzel2) můžete kontrolovat stav zprávy.
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4,9 Chcete-li přijmout konfiguraci SBD, aktualizujte soubor */etc/sysconfig/SBD* následujícím způsobem. Aktualizujte soubor na **obou** uzlech.
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4,10 spuštění služby Pacemaker v primárním **uzlu** (Uzel1)
```
systemctl start pacemaker
```
![Start-Pacemaker. png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Pokud se služba Pacemakernezdařila, *Přečtěte si scénář 5: Služba Pacemaker se nezdařila*

## <a name="5---joining-the-cluster"></a>5.   Připojení ke clusteru
Tato část popisuje, jak připojit uzel ke clusteru.

### <a name="51-add-the-node"></a>5,1 přidat uzel
Spuštěním následujícího příkazu na **Uzel2** umožněte Uzel2 připojit ke clusteru.
```
ha-cluster-join
```
Pokud při připojování ke clusteru dojde k *chybě* , přečtěte *si scénář 6: Uzel 2 se nemůže připojit ke clusteru*.

## <a name="6---validating-the-cluster"></a>6.   Ověřování clusteru

### <a name="61-start-the-cluster-service"></a>6,1 spuštění Clusterové služby
Chcete-li kontrolovat a volitelně spustit cluster na **obou** uzlech poprvé.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6,2 Sledujte stav
Spusťte příkaz *crm_mon* , abyste zajistili, že **oba** uzly jsou online. Můžete ho spustit na **jakémkoli uzlu** clusteru.
```
crm_mon
```
![CRM-Mon. png](media/HowToHLI/HASetupWithStonith/crm-mon.png) můžete se také přihlásit k Hawk a ověřit tak stav clusteru *> IP\<adresa uzlu https://: 7630*. Výchozí uživatel je hacluster a heslo je Linux. V případě potřeby můžete heslo změnit pomocí příkazu *passwd* .

## <a name="7-configure-cluster-properties-and-resources"></a>7. Konfigurace vlastností a prostředků clusteru 
Tato část popisuje kroky pro konfiguraci prostředků clusteru.
V tomto příkladu nastavíte následující prostředek, zbytek se dá nakonfigurovat (v případě potřeby) odkazem na Průvodce SUSE HA. Proveďte konfiguraci pouze v **jednom z uzlů** . Provést na primárním uzlu.

- Zavedení clusteru
- Zařízení STONITH
- Virtuální IP adresa


### <a name="71-cluster-bootstrap-and-more"></a>7,1 cluster Bootstrap a další
Přidejte zavedení clusteru. Vytvořte soubor a přidejte text následujícím způsobem:
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

### <a name="72-stonith-device"></a>zařízení STONITH 7,2
Přidejte STONITH prostředku. Vytvořte soubor a přidejte text následujícím způsobem.
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

### <a name="73-the-virtual-ip-address"></a>7,3 virtuální IP adresa
Přidejte virtuální IP adresu prostředku. Vytvořte soubor a přidejte text následujícím způsobem.
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

### <a name="74-validate-the-resources"></a>7,4 ověřit prostředky

Po spuštění příkazu *crm_mon*můžete zobrazit tyto dva prostředky.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Můžete také zobrazit stav na adrese *> IP adresa\<uzlu https://: 7630/CIB/Live/State.*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Testování procesu převzetí služeb při selhání
Chcete-li otestovat proces převzetí služeb při selhání, zastavte službu Pacemaker v Uzel1 a převzetí služeb při selhání v Uzel2.
```
Service pacemaker stop
```
Teď zastavte službu Pacemaker pro **Uzel2** a převzetí služeb při selhání službou **Uzel1**

**Než převzetí služeb při**
![selhání before-Failover.](media/HowToHLI/HASetupWithStonith/Before-failover.png)
![
**PNG po převzetí služeb při selhání**After-Failover.PNGCRM-Mon-After-Failover.png![](media/HowToHLI/HASetupWithStonith/after-failover.png)
](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. Řešení potíží
Tato část popisuje několik scénářů selhání, které mohou být zjištěny během instalace. Tyto problémy nemusí být nutně vhodné.

### <a name="scenario-1-cluster-node-not-online"></a>Scénář 1: Uzel clusteru není online.
Pokud se některý z uzlů ve Správci clusteru nezobrazuje online, můžete zkusit převést ho do online režimu.

Spuštění služby iSCSI
```
service iscsid start
```

Nyní byste měli být schopni se přihlásit k tomuto uzlu iSCSI
```
iscsiadm -m node -l
```
Očekávaný výstup vypadá takto:
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
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Scénář 2: YaST2 nezobrazuje grafické zobrazení
Grafická obrazovka YaST2 slouží k nastavení clusteru s vysokou dostupností v tomto dokumentu. Pokud se YaST2 pomocí grafického okna neotevře, jak je znázorněno a vyvolá chybu QT, proveďte následující kroky. Pokud se otevře s grafickým oknem, můžete postup přeskočit.

**Chyba**

![YaST2-QT-GUI-Error. png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Očekávaný výstup**

![YaST-Control-Center. png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Pokud se YaST2 pomocí grafického zobrazení neotevře, postupujte podle následujících kroků.

Nainstalujte požadované balíčky. Musíte být přihlášeni jako uživatel root a mít SMT nastavené na stažení nebo instalaci balíčků.

Pokud chcete balíčky nainstalovat, použijte YaST > software > software > závislosti > možnosti instalovat doporučené balíčky... Na následujícím snímku obrazovky vidíte očekávané obrazovky.
>[!NOTE]
>Je nutné provést kroky na obou uzlech, aby bylo možné získat přístup k YaST2 grafickému zobrazení z obou uzlů.

![YaST-sofwaremanagement. png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

V části závislosti vyberte "instalovat doporučené balíčky" ![YaST-Dependencies. png.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Zkontrolujte změny a stiskněte OK.

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Instalace balíčku pokračuje ![YaST-Performing-Installation. png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Kliknutí na Další

![YaST-Installation-Report. png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Klikněte na Dokončit.

Také je potřeba nainstalovat balíčky libqt4 a libyui-QT.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![Zypper-Install-ligyui. png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![Zypper-Install-ligyui_part2. png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 by teď mělo být možné otevřít grafické zobrazení, jak je znázorněno zde.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scénář 3: YaST2 nemá možnost vysoké dostupnosti
Aby bylo možné zobrazit možnost vysoké dostupnosti v řídicím centru YaST2, je nutné nainstalovat další balíčky.

Pomocí nástroje Yast2 > software > software > vyberte následující vzory

- Základ serveru SAP HANA
- C/C++ kompilátor a nástroje
- Vysoká dostupnost
- Základ aplikačního serveru SAP

Následující obrazovka popisuje kroky pro instalaci vzorů.

Používání softwaru YaST2 > software > správu softwaru

![YaST2-Control-Center. png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Výběr vzorů

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Klikněte na **přijmout**

![YaST-Changed-Packages. png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Klikněte na **pokračovat** .

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Po dokončení instalace klikněte na **Další** .

![yast2-installation-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scénář 4: Instalace HANA se nezdařila s chybou sestavení RSZ
Instalace HANA se nezdařila s následující chybou.

![Hana-installation-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Chcete-li tento problém vyřešit, je třeba nainstalovat knihovny (libgcc_sl a libstdc + + 6) následujícím způsobem.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scénář 5: Služba Pacemaker se nezdařila

Během spouštění služby Pacemaker došlo k následující chybě.

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

Pokud ho chcete opravit, odstraňte následující řádek ze souboru */usr/lib/systemd/System/fstrim.Timer*

```
Persistent=true
```

![Persistent. png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scénář 6: Uzel 2 se nemůže připojit ke clusteru.

Při připojení Uzel2 ke stávajícímu clusteru pomocí příkazu *ha-cluster-JOIN* došlo k následující chybě.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-JOIN-Error. png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Chcete-li opravit, spusťte následující na uzlech

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-Uzel1. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-Uzel2. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Po předchozí opravě by se Uzel2 měl přidat do clusteru.

![ha-cluster-JOIN-Fix. png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Obecná dokumentace
Další informace o nastavení SUSE HA najdete v následujících článcích: 

- [Scénář optimalizovaného výkonu SAP HANA SR](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Ukládání na základě úložiště](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog – použití clusteru Pacemaker pro SAP HANA – část 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog – použití clusteru Pacemaker pro SAP HANA – část 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
