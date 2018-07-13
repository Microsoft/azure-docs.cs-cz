---
title: Konfigurace funkce MPIO na hostiteli s Linuxem StorSimple | Dokumentace Microsoftu
description: Konfigurace funkce MPIO na StorSimple připojené k hostiteli se systémem Linux CentOS 6.6 systémem
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: tysonn
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: ccd24e1498282cd2b627226df79af22e9647b64d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38681568"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Konfigurace funkce MPIO na hostiteli StorSimple se systémem CentOS
Tento článek vysvětluje kroky nutné ke konfiguraci cest vstupně-výstupních operací (MPIO) na serveru hostitele Centos 6.6. Hostitelský server je připojený ke svému zařízení Microsoft Azure StorSimple pro zajištění vysoké dostupnosti prostřednictvím iniciátory iSCSI. Popisuje podrobně automatické zjišťování zařízení více cest a specifické nastavení pouze pro svazky zařízení StorSimple.

Tento postup se vztahuje na všechny modely zařízení řady StorSimple 8000.

> [!NOTE]
> Tento postup nelze použít pro řešení StorSimple Cloud Appliance. Další informace najdete v tématu Postup konfigurace hostitelské servery pro cloudové zařízení.


## <a name="about-multipathing"></a>O více cest
Funkce více cest umožňuje konfigurovat více vstupně-výstupních cest mezi serverem pro hostitele a úložné zařízení. Tyto cesty k vstupně-výstupní operace jsou fyzické připojení SAN, které mohou obsahovat samostatné kabely, přepínače, síťová rozhraní a řadiče. Více cest agreguje vstupně-výstupních cest ke konfiguraci nového zařízení, která souvisí s všechny agregované cesty.

Účelem paměťovým má dvě fáze:

* **Vysoká dostupnost**: poskytuje alternativní cestu, pokud selže některý z cesty vstupně-výstupních operací (například kabel, přepínač, síťové rozhraní nebo řadič) prvků.
* **Vyrovnávání zatížení**: v závislosti na konfiguraci vašeho zařízení úložiště, můžete zvýšit výkon zjišťování zatížení vstupně-výstupních cest a dynamicky opětovné vyvážení těchto zatížení.

### <a name="about-multipathing-components"></a>O komponentách více cest
Více cest v Linuxu se skládá z komponenty jádra a uživatelskou komponenty, jako v následující tabulce.

* **Jádra**: hlavní součást se *zařízení Mapovač* , který přesměrovává vstupně-výstupní operace a podporuje pro cesty a cesty skupiny převzetí služeb při selhání.

* **Uživatelskou**: Jedná se o *multipath nástroje* , které spravují multipathed zařízení tím, že funkce multipath modul Mapovač zařízení, jak postupovat. Nástroje obsahovat:
   
   * **Funkce Multipath**: seznamy a nakonfiguruje multipathed zařízení.
   * **Multipathd**: proces démon, který provede multipath a monitoruje cesty.
   * **Název Devmap**: smysluplný název zařízení pro proces udev zajišťující devmaps.
   * **Kpartx**: lineární devmaps se mapuje na zařízení oddílů, aby rozdělený více cest map.
   * **Multipath.conf**: konfigurační soubor pro více cest démon, který se používá k přepsání integrovaná konfigurace tabulky.

### <a name="about-the-multipathconf-configuration-file"></a>O multipath.conf konfigurační soubor
Konfigurační soubor `/etc/multipath.conf` díky řadě funkcí více cest uživatelem konfigurovatelné. `multipath` Příkazu a proces daemon jádra `multipathd` použijte informace v tomto souboru. Soubor je konzultaci s pouze během konfigurace funkce multipath zařízení. Ujistěte se, že jsou všechny změny provedené před spuštěním `multipath` příkazu. Pokud později změníte soubor, je potřeba zastavit a spustit multipathd znovu pro změny projevily.

Multipath.conf má pět částí:

- **Výchozí nastavení na úrovni systému** *(výchozí nastavení)*: je možné přepsat výchozí nastavení na úrovni systému.
- **Zařízení na seznamu zakázaných adres** *(černý list)*: můžete zadat seznam zařízení, která by neměla být řízena Mapovač zařízení.
- **Seznam zakázaných výjimky** *(blacklist_exceptions)*: Chcete zjistit, konkrétní zařízení jsou považovány za funkce multipath zařízení i v případě, že uvedené v blacklist.
- **Nastavení konkrétní řadič úložiště** *(zařízení)*: můžete zadat nastavení konfigurace, které se použijí na zařízení, která mají dodavatele a informace o produktu.
- **Nastavení konkrétního zařízení** *(multipaths)*: v této části můžete použít k vyladění nastavení konfigurace pro jednotlivé logické jednotky.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Konfigurace více cest na StorSimple připojené k hostiteli s Linuxem
Zařízení StorSimple připojené k hostiteli se systémem Linux můžete nakonfigurovat pro vysokou dostupnost a vyrovnávání zatížení. Například pokud má dvě rozhraní připojené k síti SAN hostiteli s Linuxem a má dvě rozhraní připojené k síti SAN, tak, aby tato rozhraní jsou ve stejné podsíti, pak bude 4 cesty k dispozici. Nicméně pokud každé rozhraní DATA na zařízení a hostitele rozhraní jsou v jiné podsíti protokolu IP (a ne směrovatelné), pak pouze 2 cesty nebudou k dispozici. Můžete nakonfigurovat více cest automaticky zjistit všechny dostupné cesty, vyberte algoritmus Vyrovnávání zatížení pro tyto cesty, použít konkrétní konfiguraci nastavení pro svazky jen pro StorSimple a potom povolit a ověřit více cest.

Následující postup popisuje, jak nakonfigurovat více cest při připojení zařízení StorSimple se dvěma síťovými rozhraními na hostitele se dvěma síťovými rozhraními.

## <a name="prerequisites"></a>Požadavky
Tato část podrobně popisuje požadavky na konfiguraci pro CentOS server a zařízení StorSimple.

### <a name="on-centos-host"></a>Na hostiteli CentOS
1. Ujistěte se, že CentOS hostitele má 2 síťové rozhraní povolené. Zadejte:
   
    `ifconfig`
   
    Následující příklad ukazuje výstup, když dvě síťová rozhraní (`eth0` a `eth1`) jsou k dispozici na hostiteli.
   
        [root@centosSS ~]# ifconfig
        eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
          inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
         RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
   
        eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
          inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
          TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
   
        loLink encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
2. Nainstalujte *iSCSI. iniciátoru utils* na vašem serveru CentOS. Proveďte následující kroky k instalaci *iSCSI. iniciátoru utils*.
   
   1. Přihlaste se jako `root` do hostitele CentOS.
   2. Nainstalujte *iSCSI. iniciátoru utils*. Zadejte:
      
       `yum install iscsi-initiator-utils`
   3. Po *iSCSI. iniciátoru utils* je úspěšně nainstalována, spusťte službu iSCSI. Zadejte:
      
       `service iscsid start`
      
       V případech `iscsid` ve skutečnosti se nemusí spustit a `--force` možnost může být potřeba.
   4. K zajištění, že vaše iniciátor iSCSI je povoleno během spouštění, použijte `chkconfig` příkaz, který umožní službě.
      
       `chkconfig iscsi on`
   5. Pokud chcete ověřit, že se správně nastavila, spusťte příkaz:
      
       `chkconfig --list | grep iscsi`
      
       Ukázkový výstup najdete níž.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       V předchozím příkladu můžete zobrazit, že vaše prostředí iSCSI poběží na spuštění úrovně spuštění 2, 3, 4 a 5.
3. Nainstalujte *zařízení. Mapovač multipath*. Zadejte:
   
    `yum install device-mapper-multipath`
   
    Instalace bude zahájena. Typ **Y** pokračovat po zobrazení výzvy k potvrzení.

### <a name="on-storsimple-device"></a>Na zařízení StorSimple
Zařízení StorSimple by měl mít:

* Minimálně dvě rozhraní povolená pro iSCSI. Ověření, že dvě rozhraní iSCSI povolený v zařízení StorSimple, proveďte následující kroky na portálu Azure classic pro zařízení StorSimple:
  
  1. Přihlaste se na portálu classic pro zařízení StorSimple.
  2. Vyberte svoji službu StorSimple Manager, klikněte na tlačítko **zařízení** a zvolte konkrétní zařízení StorSimple. Klikněte na tlačítko **konfigurovat** a ověřte nastavení síťového rozhraní. Snímek obrazovky s dvě rozhraní sítě povolené iSCSI je uveden níže. Zde DATA 2 a DATA 3, obě 10 GbE pro iSCSI je povoleno rozhraní.
     
      ![Konfigurace funkce MPIO StorsSimple DATA 2](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![Funkce MPIO StorSimple DATA 3 Config](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      V **konfigurovat** stránky
     
     1. Ujistěte se, že obě síťová rozhraní jsou povolené iSCSI. **Povolené iSCSI** pole by mělo být nastavené **Ano**.
     2. Ujistěte se, že síťová rozhraní mají stejnou rychlostí, by měla být 1 GbE nebo 10 GbE.
     3. Poznamenejte si IPv4 adresy rozhraní iSCSI povolený a uložit pro pozdější použití v hostitelském počítači.
* Rozhraní iSCSI pro zařízení StorSimple by měl být dostupný ze serveru, CentOS.
      Chcete-li to ověřit, zadejte IP adresy povolené iSCSI síťových rozhraních StorSimple na hostitelském serveru. Příkazy a odpovídající výstup s DATA2 (10.126.162.25) a DATA3 (10.126.162.26) je uveden níže:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Konfigurace hardwaru
Doporučujeme vám, že se můžete připojit dvě síťová rozhraní iSCSI na samostatném cesty za účelem zajištění redundance. Následující obrázek znázorňuje doporučené hardwarové konfigurace pro vysokou dostupnost a vyrovnávání zatížení více cest pro váš CentOS server a zařízení StorSimple.

![Hardwarová konfigurace funkce MPIO pro StorSimple na hostiteli s Linuxem](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Jak je znázorněno na předchozím obrázku:

* Zařízení StorSimple je v konfiguraci aktivní pasivní pomocí dva řadiče.
* Dva přepínače sítě SAN jsou připojené k řadiči zařízení.
* Zařízení StorSimple jsou povolené dva iniciátory iSCSI.
* Dvě síťová rozhraní jsou povolené na hostiteli CentOS.

Konfiguraci uvedené výš předá 4 samostatné cesty mezi zařízením a hostitele, pokud jsou směrovatelné rozhraní hostitele a data.

> [!IMPORTANT]
> * Doporučujeme vám, že jste Nekombinujte 1 GbE a rozhraní sítí 10 GbE pro více cest. Pokud používáte dvě síťová rozhraní, jak rozhraní musí být identické typu.
> * Na zařízení StorSimple DATA0, DATA1, DATA4 a DATA5 jsou 1 GbE rozhraní zatímco DATA2 a DATA3 rozhraní sítí 10 GbE. |
> 
> 

## <a name="configuration-steps"></a>Postup konfigurace
Kroky konfigurace pro více cest zahrnují konfiguraci k dispozici cesty pro automatické zjišťování, určení algoritmu Vyrovnávání zatížení chcete použít, povolení více cest a nakonec ověření konfigurace. Každý z těchto kroků je podrobně popsány v následujících částech.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Krok 1: Konfigurace cest pro automatické zjišťování
Zařízení nepodporuje multipath lze automaticky zjistit a nakonfigurovaná.

1. Inicializovat `/etc/multipath.conf` souboru. Zadejte:
   
     `mpathconf --enable`
   
    Vytvoří výše uvedeného příkazu `sample/etc/multipath.conf` souboru.
2. Spusťte službu více cest. Zadejte:
   
    `service multipathd start`
   
    Zobrazí se následující výstup:
   
    `Starting multipathd daemon:`
3. Povolte automatické zjišťování multipaths. Zadejte:
   
    `mpathconf --find_multipaths y`
   
    To slouží k úpravě výchozí část vaší `multipath.conf` jak je znázorněno níže:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Krok 2: Konfigurace cest pro svazky zařízení StorSimple
Ve výchozím nastavení všechna zařízení jsou černá uvedené v souboru multipath.conf a bude možné obejít. Je potřeba vytvořit blacklist výjimky, které umožňují více cest svazků ze zařízení StorSimple.

1. Upravit `/etc/mulitpath.conf` souboru. Zadejte:
   
    `vi /etc/multipath.conf`
2. Vyhledejte v souboru multipath.conf blacklist_exceptions oddíl. Zařízení StorSimple, musí být uvedený jako blacklist výjimek v této části. Zrušením komentáře u příslušné řádky v tomto souboru a upravte ho podle následujícího obrázku (použijte pouze konkrétní model zařízení, které používáte):
   
        blacklist_exceptions {
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8100*"
            }
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8600*"
            }
           }

### <a name="step-3-configure-round-robin-multipathing"></a>Krok 3: Konfigurace kruhové dotazování více cest
Tento algoritmus Vyrovnávání zatížení používá všechny dostupné multipaths k aktivnímu řadiči způsobem vyvážené, kruhové dotazování.

1. Upravit `/etc/multipath.conf` souboru. Zadejte:
   
    `vi /etc/multipath.conf`
2. V části `defaults` nastavte `path_grouping_policy` k `multibus`. `path_grouping_policy` Určuje výchozí cestu k seskupování použít neurčené multipaths zásady. Výchozí nastavení části bude vypadat, jak je znázorněno níže.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> Nejběžnější hodnoty `path_grouping_policy` patří:
> 
> * převzetí služeb při selhání = 1 cesty na skupinu priority
> * multibus = všechny platné cesty ve skupině 1 priority
> 
> 

### <a name="step-4-enable-multipathing"></a>Krok 4: Povolit více cest
1. Restartujte `multipathd` démona. Zadejte:
   
    `service multipathd restart`
2. Výstup bude, jak je znázorněno níže:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>Krok 5: Ověření více cest
1. Nejprve se ujistěte, že iSCSI se naváže spojení s zařízení StorSimple následujícím způsobem:
   
   a. Zjišťování zařízení StorSimple. Zadejte:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    Výstup je IP adresa pro DATA0 10.126.162.25 a otevře port 3260 na zařízení StorSimple pro přenosy iSCSI odchozí je znázorněno níže:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Zkopírovat názvu IQN zařízení StorSimple `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, z předchozí výstupu.

   b. Připojte se k zařízení s využitím cílové IQN. Zařízení StorSimple je tady cíle iSCSI. Zadejte:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    Následující příklad ukazuje výstup s cílové IQN z `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. Výstup označuje, že právě jste úspěšně propojili na dvě rozhraní sítě povolené iSCSI na vašem zařízení.

    ```
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Pokud se zobrazí pouze jednoho hostitele rozhraní a tady dvě cesty, je potřeba povolit rozhraní na hostiteli pro iSCSI. Můžete postupovat podle [podrobné pokyny v dokumentaci k systému Linux](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

2. Svazek je přístupný na CentOS server ze zařízení StorSimple. Další informace najdete v tématu [krok 6: vytvoření svazku](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) prostřednictvím webu Azure portal v zařízení StorSimple.

3. Ověření cesty k dispozici. Zadejte:

      ```
      multipath –l
      ```

      Následující příklad zobrazí výstup pro dvě síťová rozhraní na zařízení StorSimple připojené k síťovému rozhraní jednoho hostitele se dvěma cestami k dispozici.

        ```
        mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 7:0:0:1 sdc 8:32 active undef running
        `- 6:0:0:1 sdd 8:48 active undef running
        ```

        The following example shows the output for two network interfaces on a StorSimple device connected to two host network interfaces with four available paths.

        ```
        mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 17:0:0:0 sdb 8:16 active undef running
        |- 15:0:0:0 sdd 8:48 active undef running
        |- 14:0:0:0 sdc 8:32 active undef running
        `- 16:0:0:0 sde 8:64 active undef running
        ```

        After the paths are configured, refer to the specific instructions on your host operating system (Centos 6.6) to mount and format this volume.

## <a name="troubleshoot-multipathing"></a>Řešení potíží s více cest
Tato část obsahuje několik užitečných tipů, pokud narazíte na nějaké problémy při konfiguraci cest.

Q. Nevidím změny v `multipath.conf` soubor neprojeví.

A. Pokud jste provedli všechny změny `multipath.conf` soubor, budete muset restartovat službu více cest. Zadejte následující příkaz:

    service multipathd restart

Q. Můžu povolili dvě síťová rozhraní na zařízení StorSimple a dvě síťová rozhraní na hostiteli. Při zobrazení seznamu cest k dispozici, zobrazí pouze dvě cesty. Můžu očekávali čtyři cesty k dispozici.

A. Ujistěte se, že tyto dvě cesty jsou ve stejné podsíti a směrovat. Pokud síťová rozhraní jsou v jiné sítě VLAN a není směrovatelný, zobrazí se pouze dvě cesty. Abyste měli jistotu, že můžete oslovit rozhraní hostitele ze síťového rozhraní na zařízení StorSimple je jeden způsob, jak to chcete ověřit. Budete muset [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) jako toto ověření můžete provést jen prostřednictvím relaci.

Q. Při zobrazení seznamu cest k dispozici, se mi nezobrazují žádný výstup.

A. Obvykle, že nevidíte všechny cesty multipathed by naznačoval problém s démonem více cest a je pravděpodobně, že jakýkoli problém s zde spočívá v `multipath.conf` souboru.

Také by bylo vhodné kontroluje se, že ve skutečnosti vidíte některé disky po připojení k cíli, protože žádná odpověď od seznamu více cest může také znamenat, že nemáte žádné disky.

* Znovu prohledejte sběrnice SCSI, použijte následující příkaz:
  
    `$ rescan-scsi-bus.sh `(součástí balíčku sg3_utils)
* Zadejte následující příkazy:
  
    `$ dmesg | grep sd*`
     
     Nebo
  
    `$ fdisk –l`
  
    Vrátí tyto podrobnosti naposledy přidané disky.
* Pokud chcete zjistit, zda se jedná o StorSimple disk, použijte následující příkazy:
  
    `cat /sys/block/<DISK>/device/model`
  
    Vrátí řetězec, který se zjistit, jestli je StorSimple disk.

A méně pravděpodobné, ale možná příčina může také být zastaralé iscsid pid. Použijte následující příkaz k odhlášení z relace iSCSI:

    iscsiadm -m node --logout -p <Target_IP>

Tento příkaz opakujte pro všechna připojená síťová rozhraní v cíli iSCSI, která je zařízení StorSimple. Po přihlášení od všechny relace iSCSI, znovu vytvořit relace iSCSI pomocí cíle iSCSI IQN. Zadejte následující příkaz:

    iscsiadm -m node --login -T <TARGET_IQN>


Q. Nejste jistí, jestli zařízení je na seznamu povolených.

A. Pokud chcete ověřit, jestli vaše zařízení je na seznamu povolených, použijte následující příkaz řešení problémů s interaktivní:

    multipathd –k
    multipathd> show devices
    available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


Další informace najdete v části [použít řešení potíží s interaktivního příkazu pro více cest](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## <a name="list-of-useful-commands"></a>Seznam užitečné příkazy
| Typ | Příkaz | Popis |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Spuštění služby iSCSI |
| &nbsp; |`service iscsid stop` |Zastavit službu iSCSI |
| &nbsp; |`service iscsid restart` |Restartujte službu iSCSI |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Zjištění dostupných cílů na zadané adrese |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Přihlaste se k cíli iSCSI |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Odhlaste se z cíle iSCSI |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Název iniciátoru iSCSI pro tisk |
| &nbsp; |`iscsiadm –m session –s <sessionid> -P 3` |Zkontrolujte stav relace iSCSI a objemu zjištěné na hostiteli |
| &nbsp; |`iscsi –m session` |Zobrazí všechny relace iSCSI, vytváří se mezi hostiteli a zařízení StorSimple |
|  | | |
| **Více cest** |`service multipathd start` |Spuštění démona více cest |
| &nbsp; |`service multipathd stop` |Zastavit proces démon více cest |
| &nbsp; |`service multipathd restart` |Restartujte démona více cest |
| &nbsp; |`chkconfig multipathd on` </br> NEBO </br> `mpathconf –with_chkconfig y` |Povolit více cest démon spustíte v době spuštění |
| &nbsp; |`multipathd –k` |Spustit interaktivní konzoly pro řešení potíží |
| &nbsp; |`multipath –l` |Seznam připojení více cest a zařízení |
| &nbsp; |`mpathconf --enable` |Vytvoření ukázkového souboru mulitpath.conf v `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Další postup
Když konfigurujete funkci MPIO na hostiteli s Linuxem, budete možná potřebovat, k odkazování na tyto dokumenty CentoS 6.6:

* [Nastavení funkce MPIO na CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
* [Průvodce školení Linux](http://linux-training.be/linuxsys.pdf)

