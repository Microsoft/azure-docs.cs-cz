---
title: Nastavení vysoké dostupnosti s STONITH pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: Zajištění vysoké dostupnosti pro SAP HANA v Azure (velké instance) v SUSE pomocí STONITH
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
ms.openlocfilehash: 0967c5e354c3b0e433753cf89d830dc2101741af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91363116"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Nastavení vysoké dostupnosti v SUSE s využitím techniky STONITH
Tento dokument popisuje podrobné pokyny k nastavení vysoké dostupnosti v SUSE operačním systému pomocí zařízení STONITH.

**Právní omezení:** *Tato příručka je odvozená pomocí testování nastavení v prostředí velkých instancí Microsoft Hana, které úspěšně funguje. Protože tým Microsoft Service Management pro velké instance HANA nepodporuje operační systém, možná budete muset kontaktovat SUSE a vyřešit případné další řešení potíží nebo objasnění vrstvy operačního systému. Tým Microsoftu pro správu služeb nastavil zařízení STONITH a plně podporuje a může se zapojit do řešení potíží s STONITH problémy zařízení.*
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

## <a name="1---identify-the-sbd-device"></a>1. identifikace zařízení SBD
Tato část popisuje, jak určit zařízení SBD pro vaši instalaci poté, co tým Microsoftu pro správu služeb nakonfiguruje STONITH. **Tato část se vztahuje pouze na stávajícího zákazníka**. Pokud jste nový zákazník, tým Microsoftu pro správu služeb vám poskytne název zařízení SBD a tuto část můžete přeskočit.

1,1 upravit */etc/iSCSI/initiatorname.iSCI* na 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Služba Microsoft Service Management poskytuje tento řetězec. Upravte soubor na **obou** uzlech, ale číslo uzlu je v každém uzlu jiné.

![Snímek obrazovky ukazuje soubor iniciátoru s hodnotami hodnoty iniciátoru pro uzel.](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1,2 upravit */etc/iSCSI/iscsid.conf*: nastavte *Node.Session.Timeo.replacement_timeout = 5* a *Node. Startup = Automatic*. Upravte soubor na **obou** uzlech.

1,3 spuštěním příkazu pro zjišťování se zobrazí čtyři relace. Spusťte ji na obou uzlech.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![Snímek obrazovky se zobrazeným oknem konzoly s výsledky příkazu isciadm Discovery.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1,4 spuštěním příkazu pro přihlášení k zařízení iSCSI se zobrazí čtyři relace. Spusťte ji na **obou** uzlech.

```
iscsiadm -m node -l
```
![Snímek obrazovky se zobrazí okno konzoly s výsledky příkazu iscsiadm Node.](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1,5 spusťte skript opětovného prohledání: *rescan-SCSI-Bus.sh*.  Tento skript vám ukáže nové disky, které jste pro vás vytvořili.  Spusťte ji na obou uzlech. Mělo by se zobrazit číslo logické jednotky (LUN), které je větší než nula (například: 1, 2 atd.).

```
rescan-scsi-bus.sh
```
![Snímek obrazovky s výsledky skriptu zobrazuje okno konzoly.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1,6 Chcete-li získat název zařízení, spusťte příkaz *fdisk – l*. Spusťte ji na obou uzlech. Vyberte zařízení s velikostí **178 MIB**.

```
  fdisk –l
```

![Snímek obrazovky s výsledky příkazu f disku zobrazuje okno konzoly.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. inicializace zařízení SBD

2,1 inicializace zařízení **SBD na** uzlech

```
sbd -d <SBD Device Name> create
```
![Snímek obrazovky se zobrazí okno konzoly s výsledkem příkazu s b d Create.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2,2 Podívejte se, co bylo zapsáno do zařízení. Udělejte to na **obou** uzlech

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. konfigurace clusteru
Tato část popisuje kroky pro nastavení clusteru SUSE HA.
### <a name="31-package-installation"></a>instalace balíčku 3,1
3.1.1 Zkontrolujte prosím, jestli jsou nainstalované vzory ha_sles a SAPHanaSR-doc. Pokud není nainstalován, nainstalujte je. Nainstalujte ji do **obou** uzlů.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![Snímek obrazovky s výsledkem příkazu Pattern zobrazuje okno konzoly. ](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
 ![ Snímek obrazovky s výsledkem příkazu SAPHanaSR-doc zobrazuje okno konzoly.](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3,2 nastavení clusteru
3.2.1 můžete buď použít příkaz *ha-cluster-init* , nebo můžete použít Průvodce YaST2 k nastavení clusteru. V tomto případě se používá Průvodce YaST2. Tento krok provedete **pouze v primárním uzlu**.

Sledujte YaST2> vysoké dostupnosti > ![ snímku clusteru se zobrazuje centrum řízení YaST s vysokou dostupností a vybraným clusterem. ](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
 ![ Snímek obrazovky se zobrazí dialogové okno s možnostmi nainstalovat a zrušit.](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Klikněte na tlačítko **Storno** , protože balíček halk2 je již nainstalován.

![Snímek obrazovky se zobrazí zpráva o možnosti zrušení.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Klikněte na **pokračovat** .

Očekávaná hodnota = počet nasazených uzlů (v tomto případě 2) ![ snímek obrazovky zobrazuje zabezpečení clusteru pomocí zaškrtávacího políčka Povolit ověření zabezpečení.](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png)
Klikněte na **Další** 
 ![ snímek obrazovky zobrazit okno Konfigurace clusteru se seznamy synchronizace hostitele a synchronizovat soubory.](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
Přidejte názvy uzlů a pak klikněte na Přidat navrhované soubory.

Klikněte na zapnout csync2.

Klikněte na "generovat předsdílené klíče", zobrazí se pod místní nabídkou.

![Snímek obrazovky se zobrazí zpráva, že váš klíč byl vygenerován.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Klikněte na tlačítko **OK**.

Ověřování se provádí pomocí IP adres a předsdílených klíčů v Csync2. Soubor klíče se vygeneruje pomocí csync2-k/etc/csync2/key_hagroup. Soubor key_hagroup by měl být po vytvoření zkopírován do všech členů clusteru ručně. Nezapomeňte **zkopírovat soubor z uzlu 1 do Uzel2**.

![Snímek obrazovky se zobrazí dialogové okno konfigurovat cluster s možnostmi nezbytnými ke zkopírování klíče do všech členů clusteru.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Klikněte na **Další** 
 ![ snímek obrazovky a zobrazí se okno Clusterová služba.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Ve výchozí možnosti bylo spuštění vypnuto, změňte ho na zapnuto, aby se Pacemaker spustil při spuštění. Můžete vybrat podle požadavků na instalaci.
Klikněte na **Další** a konfigurace clusteru se dokončí.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. nastavení sledovacího zařízení Softdog
Tato část popisuje konfiguraci sledovacího zařízení (softdog).

4,1 přidejte následující řádek do */etc/init.d/Boot.Local* v **obou** uzlech.
```
modprobe softdog
```
![Snímek obrazovky zobrazuje spouštěcí soubor s přidaným řádkem softdog.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4,2 aktualizujte soubor */etc/sysconfig/SBD* na **obou** uzlech následujícím způsobem:
```
SBD_DEVICE="<SBD Device Name>"
```
![Snímek obrazovky se zobrazí v souboru s b d s přidaným D_DEVICE S hodnotou B.](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4,3 načtěte modul jádra na **obou** uzlech spuštěním následujícího příkazu.
```
modprobe softdog
```
![Snímek obrazovky ukazuje část okna konzoly s příkazem modprobe softdog.](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4,4 Zkontrolujte a zajistěte, **aby na** uzlech běžela softdog, a to následujícím způsobem:
```
lsmod | grep dog
```
![Snímek obrazovky ukazuje část okna konzoly s výsledkem spuštění příkazu mod. l s.](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4,5 spustit zařízení SBD v **obou** uzlech
```
/usr/share/sbd/sbd.sh start
```
![Snímek obrazovky ukazuje část okna konzoly pomocí příkazu Start.](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4,6 otestujte démon SBD na **obou** uzlech. Po nakonfigurování **na uzlech se zobrazí** dvě položky.
```
sbd -d <SBD Device Name> list
```
![Snímek obrazovky zobrazuje část okna konzoly, která zobrazuje dvě položky.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,7 odeslání zkušební zprávy na **jeden** z vašich uzlů
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![Snímek obrazovky zobrazuje část okna konzoly, která zobrazuje dvě položky.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,8 na **druhém** uzlu (Uzel2) můžete kontrolovat stav zprávy.
```
sbd  -d <SBD Device Name> list
```
![Snímek obrazovky ukazuje část okna konzoly s jedním z členů, kteří zobrazují hodnotu testu pro druhého člena.](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4,9 Chcete-li přijmout konfiguraci SBD, aktualizujte soubor */etc/sysconfig/SBD* následujícím způsobem. Aktualizujte soubor na **obou** uzlech.
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4,10 spuštění služby Pacemaker v **primárním uzlu** (Uzel1)
```
systemctl start pacemaker
```
![Snímek obrazovky zobrazuje okno konzoly zobrazující stav po spuštění Pacemaker.](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Pokud se služba Pacemaker *nepovede,* Přečtěte si *scénář 5: služba Pacemaker se nezdařila.*

## <a name="5---joining-the-cluster"></a>5. připojení ke clusteru
Tato část popisuje, jak připojit uzel ke clusteru.

### <a name="51-add-the-node"></a>5,1 přidat uzel
Spuštěním následujícího příkazu na **Uzel2** umožněte Uzel2 připojit ke clusteru.
```
ha-cluster-join
```
Pokud při připojování ke clusteru dojde k *chybě* , použijte *scénář 6: uzel 2 nelze připojit ke clusteru*.

## <a name="6---validating-the-cluster"></a>6. ověřování clusteru

### <a name="61-start-the-cluster-service"></a>6,1 spuštění Clusterové služby
Chcete-li kontrolovat a volitelně spustit cluster na **obou** uzlech poprvé.
```
systemctl status pacemaker
systemctl start pacemaker
```
![Snímek obrazovky se zobrazeným oknem konzoly se stavem Pacemaker.](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6,2 Sledujte stav
Spusťte *crm_mon* příkazu, aby se zajistilo, že **oba** uzly jsou online. Můžete ho spustit na **jakémkoli uzlu** clusteru.
```
crm_mon
```
![Snímek obrazovky zobrazuje okno konzoly s výsledky m_mon c r.](media/HowToHLI/HASetupWithStonith/crm-mon.png)
Můžete se také přihlásit k Hawk a ověřit stav clusteru *https:// \<node IP> : 7630*. Výchozí uživatel je hacluster a heslo je Linux. V případě potřeby můžete heslo změnit pomocí příkazu *passwd* .

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
![Snímek obrazovky ukazuje část okna konzoly, na které běží příkaz c r m.](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

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
![Snímek obrazovky se zobrazeným oknem konzoly se dvěma prostředky.](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Můžete také zobrazit stav v *https:// \<node IP address> : 7630/CIB/Live/State.*

![Snímek obrazovky zobrazuje stav těchto dvou prostředků.](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. testování procesu převzetí služeb při selhání
Chcete-li otestovat proces převzetí služeb při selhání, zastavte službu Pacemaker v Uzel1 a převzetí služeb při selhání v Uzel2.
```
Service pacemaker stop
```
Teď zastavte službu Pacemaker pro **Uzel2** a převzetí služeb při selhání službou **Uzel1**

**Před převzetím služeb při selhání**  
![Snímek obrazovky zobrazuje stav těchto dvou prostředků před převzetím služeb při selhání.](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Po převzetí služeb při selhání**  
![Snímek obrazovky zobrazuje stav těchto dvou prostředků po převzetí služeb při selhání.](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![Snímek obrazovky se stavem prostředků po převzetí služeb při selhání zobrazuje okno konzoly.](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. odstraňování potíží
Tato část popisuje několik scénářů selhání, které mohou být zjištěny během instalace. Tyto problémy nemusí být nutně vhodné.

### <a name="scenario-1-cluster-node-not-online"></a>Scénář 1: uzel clusteru není online
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

![Snímek obrazovky ukazuje část okna konzoly s chybovou zprávou.](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Očekávaný výstup**

![Snímek obrazovky se zobrazením řídicího centra YaST s vysokou dostupností a zvýrazněným clusterem.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Pokud se YaST2 pomocí grafického zobrazení neotevře, postupujte podle následujících kroků.

Nainstalujte požadované balíčky. Musíte být přihlášeni jako uživatel root a mít SMT nastavené na stažení nebo instalaci balíčků.

Pokud chcete balíčky nainstalovat, použijte YaST>software>software>závislosti> možnosti instalovat doporučené balíčky... Na následujícím snímku obrazovky vidíte očekávané obrazovky.
>[!NOTE]
>Je nutné provést kroky na obou uzlech, aby bylo možné získat přístup k YaST2 grafickému zobrazení z obou uzlů.

![Snímek obrazovky zobrazuje okno konzoly zobrazující YaST Control Center.](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

V části závislosti vyberte možnost nainstalovat Doporučené balíčky ![ . zobrazí se okno konzoly s vybranými možnostmi nainstalovat Doporučené balíčky.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Zkontrolujte změny a stiskněte OK.

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Instalace balíčku pokračuje ![ snímekem obrazovky okno konzoly znázorňující průběh instalace.](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Kliknutí na Další

![Snímek obrazovky se zobrazí okno konzoly se zprávou o úspěchu.](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Klikněte na Dokončit.

Také je potřeba nainstalovat balíčky libqt4 a libyui-QT.
```
zypper -n install libqt4
```
![Snímek obrazovky se zobrazí okno konzoly, ve kterém se instaluje balíček libqt4.](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![Snímek obrazovky s instalací balíčku libyui-QT zobrazuje okno konzoly. ](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
 ![ Snímek obrazovky zobrazující okno konzoly, které instaluje balíček libyui-QT, pokračuje.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
Yast2 by mělo být schopné nyní otevřít grafické zobrazení, jak je znázorněno zde.
![Snímek obrazovky s vybraným softwarem a online aktualizací zobrazuje YaST Control Center.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scénář 3: YaST2 nemá možnost vysoké dostupnosti
Aby bylo možné zobrazit možnost vysoké dostupnosti v řídicím centru YaST2, je nutné nainstalovat další balíčky.

Pomocí nástroje Yast2>software>software>vyberte následující vzory

- Základ serveru SAP HANA
- Kompilátor a nástroje jazyka C/C++
- Vysoká dostupnost
- Základ aplikačního serveru SAP

Následující obrazovka popisuje kroky pro instalaci vzorů.

Používání softwaru YaST2 > software > správu softwaru

![Snímek obrazovky se zobrazením řídicího centra YaST se softwarem a online aktualizací vybraným pro zahájení instalace.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Výběr vzorů

![Snímek obrazovky ukazuje výběr prvního vzoru v položce C/C++ Compiler a Tools. ](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
 ![ Snímek obrazovky ukazuje výběr druhého vzoru v položce C/C++ Compiler a Tools.](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Klikněte na **přijmout**

![Snímek obrazovky se zobrazí dialogové okno změněné balíčky, ve kterém se balíčky změnily tak, aby vyřešily závislosti.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Klikněte na **pokračovat** .

![Snímek obrazovky se zobrazí stránka stav provádění instalace.](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Po dokončení instalace klikněte na **Další** .

![Snímek obrazovky se zobrazí sestava instalace.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scénář 4: instalace HANA se nezdařila s chybou sestavení RSZ
Instalace HANA se nezdařila s následující chybou.

![Snímek obrazovky s chybovou zprávou, že operační systém není připravený k provádění sestavení g c c 5.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Chcete-li tento problém vyřešit, je třeba nainstalovat knihovny (libgcc_sl a libstdc + + 6) následujícím způsobem.

![Snímek obrazovky se zobrazí okno konzoly s instalací požadovaných knihoven.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scénář 5: služba Pacemaker se nezdařila

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

![Snímek obrazovky zobrazuje soubor střihu f s s hodnotou persistent = true, která se má odstranit.](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scénář 6: uzel 2 se nemůže připojit ke clusteru

Při připojení Uzel2 ke stávajícímu clusteru pomocí příkazu *ha-cluster-JOIN* došlo k následující chybě.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![Snímek obrazovky se zobrazeným oknem konzoly s chybovou zprávou se nepodařilo načíst klíče S hod. z adresy I P.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Chcete-li opravit, spusťte následující na uzlech

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![Snímek obrazovky se zobrazí v okně konzoly, ve kterém je spuštěný příkaz na prvním uzlu.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![Snímek obrazovky ukazuje část okna konzoly, která spouští příkaz na druhém uzlu.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Po předchozí opravě by se Uzel2 měl přidat do clusteru.

![Snímek obrazovky zobrazuje okno konzoly s úspěšným příkazem ha-cluster-JOIN.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Obecná dokumentace
Další informace o nastavení SUSE HA najdete v následujících článcích: 

- [Scénář optimalizovaného výkonu SAP HANA SR](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Ukládání na základě úložiště](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog – použití clusteru Pacemaker pro SAP HANA – část 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog – použití clusteru Pacemaker pro SAP HANA – část 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
