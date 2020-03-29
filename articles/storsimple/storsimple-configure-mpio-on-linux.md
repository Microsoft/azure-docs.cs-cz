---
title: Konfigurace mpio na hostiteli StorSimple Linux
description: Konfigurace mpio na StorSimple připojen k hostiteli Linuxu se systémem CentOS 6.6
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 5dadd231335e93839e947077168f32dbfe96eb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278367"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Konfigurace příkazu MPIO na hostiteli StorSimple se systémem CentOS
Tento článek vysvětluje kroky potřebné ke konfiguraci multipathing IO (MPIO) na hostitelském serveru Centos 6.6. Hostitelský server je připojený k vašemu zařízení Microsoft Azure StorSimple pro vysokou dostupnost prostřednictvím iniciátorů iSCSI. Podrobně popisuje automatické zjišťování vícecestných zařízení a specifické nastavení pouze pro svazky StorSimple.

Tento postup je použitelný pro všechny modely zařízení řady StorSimple 8000.

> [!NOTE]
> Tento postup nelze použít pro StorSimple Cloud Appliance. Další informace naleznete v tématu konfigurace hostitelských serverů pro cloudové zařízení.


## <a name="about-multipathing"></a>O multipathingu
Funkce vícecestní umožňuje konfigurovat více vstupně-va/o cest mezi hostitelským serverem a úložným zařízením. Tyto vstupně-o cesty jsou fyzické připojení sítě SAN, které mohou zahrnovat samostatné kabely, přepínače, síťová rozhraní a řadiče. Multipathing agreguje vstupně-vod cesty, chcete-li nakonfigurovat nové zařízení, které je přidruženo ke všem agregované cesty.

Účelem multipathingu je dvojí:

* **Vysoká dostupnost**: Poskytuje alternativní cestu, pokud selže libovolný prvek vstupně-o cesty (například kabel, přepínač, síťové rozhraní nebo řadič).
* **Vyrovnávání zatížení**: V závislosti na konfiguraci paměťového zařízení může zvýšit výkon tím, že detekuje zatížení na cestách V/O a dynamicky vyvažuje tato zatížení.

### <a name="about-multipathing-components"></a>O součástech s více cestami
Multipathing v Linuxu se skládá ze součástí jádra a uživatelských prostorových komponent, jak jsou uvedeny níže.

* **Jádro**: Hlavní komponentou je *mapovač zařízení,* který přesměruje vstupně-výstupní chod a podporuje převzetí služeb při selhání pro cesty a skupiny cest.

* **User-space**: Jedná se *o multipath-nástroje,* které spravují vícecestné zařízení tím, že instruuje zařízení mapovat multipath modul, co má dělat. Nástroje se skládají z:
   
   * **Multipath**: uvádí a konfiguruje vícecestná zařízení.
   * **Multipathd**: daemon, který provádí multipath a sleduje cesty.
   * **Devmap-name**: poskytuje smysluplný název zařízení pro udev pro devmaps.
   * **Kpartx**: mapuje lineární devmapy na oddíly zařízení, aby se multipath mapy rozdělitelné.
   * **Multipath.conf**: konfigurační soubor pro vícecestný daemon, který se používá k přepsání předdefinované konfigurační tabulky.

### <a name="about-the-multipathconf-configuration-file"></a>O konfiguračním souboru multipath.conf
Konfigurační soubor `/etc/multipath.conf` umožňuje mnoho funkcí vícecestných uživatelem konfigurovatelné. Příkaz `multipath` a daemon `multipathd` jádra používají informace nalezené v tomto souboru. Soubor je konzultován pouze během konfigurace vícecestných zařízení. Před spuštěním příkazu `multipath` se ujistěte, že jsou provedeny všechny změny. Pokud soubor později upravíte, budete muset změny znovu zastavit a spustit s více cestami.

Multipath.conf má pět částí:

- **Výchozí hodnoty systémové úrovně** *(výchozí hodnoty):* Výchozí hodnoty na úrovni systému můžete přepsat.
- **Zařízení na černé listině** *(blacklist)*: Můžete zadat seznam zařízení, která by neměla být řízena mapovačem zařízení.
- **Blacklist výjimky** *(blacklist_exceptions)*: Můžete identifikovat konkrétní zařízení, které mají být považovány za multipath zařízení, i když jsou uvedeny v černé listině.
- **Nastavení specifická** *(zařízení)* řadiče úložiště : Můžete určit nastavení konfigurace, která budou použita u zařízení, která mají informace o dodavateli a produktu.
- **Nastavení specifická pro zařízení** *(vícecest)*: V této části můžete doladit nastavení konfigurace pro jednotlivé luny.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Konfigurace multipathing u StorSimple připojen k hostiteli Linuxu
Zařízení StorSimple připojené k hostiteli Linuxu lze nakonfigurovat pro vysokou dostupnost a vyrovnávání zatížení. Například pokud hostitel Linuxu má dvě rozhraní připojená k síti SAN a zařízení má dvě rozhraní připojená k síti SAN tak, aby tato rozhraní byla ve stejné podsíti, budou k dispozici 4 cesty. Pokud jsou však každé datové rozhraní v rozhraní zařízení a hostitele v jiné podsíti IP (a nikoli směrovatelné), budou k dispozici pouze 2 cesty. Multipathing můžete nakonfigurovat tak, aby automaticky zjišťoval všechny dostupné cesty, zvolte algoritmus vyrovnávání zatížení pro tyto cesty, použít konkrétní nastavení konfigurace pro svazky pouze StorSimple a poté povolit a ověřit multipathing.

Následující postup popisuje, jak nakonfigurovat multipathing, když je zařízení StorSimple se dvěma síťovými rozhraními připojeno k hostiteli se dvěma síťovými rozhraními.

## <a name="prerequisites"></a>Požadavky
Tato část podrobně popisuje požadavky konfigurace pro server CentOS a vaše zařízení StorSimple.

### <a name="on-centos-host"></a>Na hostiteli CentOS
1. Ujistěte se, že váš hostitel CentOS má povolena 2 síťová rozhraní. Zadejte:
   
    `ifconfig`
   
    Následující příklad ukazuje výstup, když jsou`eth0` `eth1`na hostiteli přítomna dvě síťová rozhraní ( a ) .
   
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
1. Nainstalujte *iniciátory iSCSI na* server CentOS. Chcete-li nainstalovat *iniciátor iniciátor iSCSI-utils, proveďte*následující kroky .
   
   1. Přihlaste `root` se jako do svého hostitele CentOS.
   1. Nainstalujte *iniciátor iniciátor iSCSI - utilita*. Zadejte:
      
       `yum install iscsi-initiator-utils`
   1. Po úspěšné instalaci *iscsi-iniciátoru-utils* spusťte službu iSCSI. Zadejte:
      
       `service iscsid start`
      
       V některých `iscsid` případech nemusí ve `--force` skutečnosti začít a možnost může být nutná
   1. Chcete-li zajistit, aby byl iniciátor iSCSI povolen během spuštění, povolte službu pomocí příkazu. `chkconfig`
      
       `chkconfig iscsi on`
   1. Chcete-li ověřit, zda bylo správně nastaveno, spusťte příkaz:
      
       `chkconfig --list | grep iscsi`
      
       Ukázkový výstup najdete níž.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       Z výše uvedeného příkladu uvidíte, že vaše prostředí iSCSI bude spuštěno při spuštění při spuštění úrovní 2, 3, 4 a 5.
1. Nainstalujte *zařízení-mapper-multipath*. Zadejte:
   
    `yum install device-mapper-multipath`
   
    Instalace se spustí. Po zobrazení výzvy k potvrzení pokračujte zadáním příkazu **Y.**

### <a name="on-storsimple-device"></a>Na zařízení StorSimple
Vaše zařízení StorSimple by mělo mít:

* Pro iSCSI jsou povolena minimálně dvě rozhraní. Chcete-li ověřit, že dvě rozhraní jsou na vašem zařízení StorSimple povolena iSCSI, proveďte na klasickém portálu Azure pro vaše zařízení StorSimple následující kroky:
  
  1. Přihlaste se ke klasickému portálu pro vaše zařízení StorSimple.
  1. Vyberte službu StorSimple Manager, klikněte na **zařízení** a zvolte konkrétní zařízení StorSimple. Klikněte na **Konfigurovat** a ověřte nastavení síťového rozhraní. Snímek obrazovky se dvěma síťovými rozhraními s podporou iSCSI je uveden níže. Zde data 2 a data 3, obě rozhraní 10 GbE jsou povoleny pro iSCSI.
     
      ![MPIO StorsJednoduchá data 2 konfigurace](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorJednoduchá data 3 Konfigurace](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      Na stránce **Konfigurovat**
     
     1. Ujistěte se, že obě síťová rozhraní jsou povolena protokolem iSCSI. Pole **s povoleným protokolem iSCSI** by mělo být nastaveno na **ano**.
     1. Ujistěte se, že síťová rozhraní mají stejnou rychlost, obě by měla být 1 GbE nebo 10 GbE.
     1. Poznamenejte si adresy IPv4 rozhraní s podporou iSCSI a uložte je pro pozdější použití na hostiteli.
* Rozhraní iSCSI na vašem zařízení StorSimple by měla být dostupná ze serveru CentOS.
      Chcete-li to ověřit, je třeba zadat adresy IP síťových rozhraní s podporou protokolu StorSimple iSCSI na hostitelském serveru. Použité příkazy a odpovídající výstup s DATA2 (10.126.162.25) a DATA3 (10.126.162.26) jsou uvedeny níže:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Hardwarová konfigurace
Doporučujeme připojit dvě síťová rozhraní iSCSI na samostatné cesty pro redundanci. Na obrázku níže je uvedena doporučená konfigurace hardwaru pro vysokou dostupnost a multipathing vyrovnávání zatížení pro server CentOS a zařízení StorSimple.

![MPIO hardware config pro Hostitele StorSimple linuxu](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Jak je znázorněno na předchozím obrázku:

* Vaše zařízení StorSimple je v konfiguraci aktivní pasivní se dvěma řadiči.
* K řadičům zařízení jsou připojeny dva přepínače SAN.
* Na zařízení StorSimple jsou povoleny dva iniciátory iSCSI.
* Na vašem hostiteli CentOS jsou povolena dvě síťová rozhraní.

Výše uvedená konfigurace přinese 4 samostatné cesty mezi zařízením a hostitelem, pokud jsou hostitelská a datová rozhraní směrovatelná.

> [!IMPORTANT]
> * Doporučujeme nekombinovat síťová rozhraní 1 GbE a 10 GbE pro multipathing. Při použití dvou síťových rozhraní by obě rozhraní měla být stejného typu.
> * Na vašem zařízení StorSimple jsou DATA0, DATA1, DATA4 a DATA5 1 GbE rozhraní, zatímco DATA2 a DATA3 jsou 10 GbE síťová rozhraní.|
> 
> 

## <a name="configuration-steps"></a>Postup konfigurace
Konfigurační kroky pro vícecestní zahrnují konfiguraci dostupných cest pro automatické zjišťování, určení algoritmu vyrovnávání zatížení, který se má použít, povolení vícecestní a nakonec ověření konfigurace. Každý z těchto kroků je podrobně popsán v následujících částech.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Krok 1: Konfigurace multipathingu pro automatické zjišťování
Zařízení podporovaná více cestami lze automaticky zjistit a nakonfigurovat.

1. Inicializovat `/etc/multipath.conf` soubor. Zadejte:
   
     `mpathconf --enable`
   
    Výše uvedený příkaz `sample/etc/multipath.conf` vytvoří soubor.
1. Spusťte službu s více cestami. Zadejte:
   
    `service multipathd start`
   
    Zobrazí se následující výstup:
   
    `Starting multipathd daemon:`
1. Povolte automatické zjišťování vícecestných cest. Zadejte:
   
    `mpathconf --find_multipaths y`
   
    Tím se změní výchozí část `multipath.conf` vaší části, jak je znázorněno níže:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Krok 2: Konfigurace multipathingu pro svazky StorSimple
Ve výchozím nastavení jsou všechna zařízení uvedena na černé listině v souboru multipath.conf a budou vynechána. Budete muset vytvořit výjimky blacklist povolit multipathing pro svazky ze zařízení StorSimple.

1. Upravte `/etc/mulitpath.conf` soubor. Zadejte:
   
    `vi /etc/multipath.conf`
1. Vyhledejte oddíl blacklist_exceptions v souboru multipath.conf. Vaše zařízení StorSimple musí být uvedeno jako výjimka blacklistu v této části. Můžete odkomentovat příslušné řádky v tomto souboru a upravit je, jak je znázorněno níže (použijte pouze konkrétní model zařízení, které používáte):
   
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

### <a name="step-3-configure-round-robin-multipathing"></a>Krok 3: Konfigurace multipathingu kruhového dotazování
Tento algoritmus vyrovnávání zatížení používá všechny dostupné vícecestníky k aktivnímu řadiči vyváženým způsobem kruhového dotazování.

1. Upravte `/etc/multipath.conf` soubor. Zadejte:
   
    `vi /etc/multipath.conf`
1. Pod `defaults` oddílem nastavte `path_grouping_policy` `multibus`na . Určuje `path_grouping_policy` výchozí zásadu seskupení cest, která se použije na neurčené vícecestníky. Výchozí část bude vypadat tak, jak je znázorněno níže.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> Mezi nejběžnější hodnoty `path_grouping_policy` patří:
> 
> * převzetí služeb při selhání = 1 cesta na prioritní skupinu
> * multibus = všechny platné cesty v 1 prioritní skupině
> 
> 

### <a name="step-4-enable-multipathing"></a>Krok 4: Povolení vícecestní
1. Restartujte `multipathd` daemon. Zadejte:
   
    `service multipathd restart`
1. Výstup bude uveden níže:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>Krok 5: Ověření vícecestní
1. Nejprve se ujistěte, že je připojení iSCSI navázáno se zařízením StorSimple následujícím způsobem:
   
   a. Objevte své zařízení StorSimple. Zadejte:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    Výstup při IP adrese DATA0 je 10.126.162.25 a port 3260 je otevřen na zařízení StorSimple pro odchozí provoz iSCSI je, jak je znázorněno níže:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Zkopírujte IQN zařízení StorSimple `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`z předchozího výstupu.

   b. Připojte se k zařízení pomocí cílového IQN. Zařízení StorSimple je zde cílem iSCSI. Zadejte:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    Následující příklad ukazuje výstup s cílovým IQN . `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` Výstup znamená, že jste se úspěšně připojili ke dvěma síťovým rozhraním s podporou iSCSI v zařízení.

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

    Pokud zde vidíte pouze jedno hostitelské rozhraní a dvě cesty, musíte povolit obě rozhraní na hostiteli pro iSCSI. Můžete postupovat podle [podrobných pokynů v dokumentaci k Linuxu](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

1. Svazek je vystaven serveru CentOS ze zařízení StorSimple. Další informace najdete [v tématu Krok 6: Vytvoření svazku](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) prostřednictvím portálu Azure na zařízení StorSimple.

1. Ověřte dostupné cesty. Zadejte:

      ```
      multipath -l
      ```

      Následující příklad ukazuje výstup pro dvě síťová rozhraní na zařízení StorSimple připojeném k jednomu hostitelskému síťovému rozhraní se dvěma dostupnými cestami.

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

## <a name="troubleshoot-multipathing"></a>Poradce při potížích s více cestami
Tato část obsahuje několik užitečných tipů, pokud narazíte na nějaké problémy během konfigurace vícecestných.

Otázka: Nevidím změny v `multipath.conf` souboru s účinností.

A. Pokud jste v souboru `multipath.conf` provedli nějaké změny, bude nutné restartovat službu multipathing. Zadejte následující příkaz:

    service multipathd restart

Otázka: Povolil jsem dvě síťová rozhraní na zařízení StorSimple a dvě síťová rozhraní na hostiteli. Při zobrazení seznamu dostupných cest se zobrazí pouze dvě cesty. Čekal jsem, že uvidím čtyři dostupné cesty.

A. Ujistěte se, že dvě cesty jsou ve stejné podsíti a směrovatelné. Pokud jsou síťová rozhraní na různých sítích vLAN a nejsou směrovatelná, zobrazí se pouze dvě cesty. Jedním ze způsobů, jak to ověřit, je zajistit, že můžete dosáhnout obou hostitelských rozhraní ze síťového rozhraní na zařízení StorSimple. Budete muset [kontaktovat podporu společnosti Microsoft,](storsimple-8000-contact-microsoft-support.md) protože toto ověření lze provést pouze prostřednictvím relace podpory.

Otázka: Když jsem seznam dostupných cest, nevidím žádný výstup.

A. Obvykle není vidět žádné vícecestné cesty naznačuje problém s multipathing daemon, a to je s `multipath.conf` největší pravděpodobností, že jakýkoli problém zde leží v souboru.

Bylo by také vhodné zkontrolovat, že můžete skutečně vidět některé disky po připojení k cíli, protože žádná odpověď z multipath výpisy by také mohlo znamenat, že nemáte žádné disky.

* K opětovnému prohledání sběrnice SCSI použijte následující příkaz:
  
    `$ rescan-scsi-bus.sh`(součást sg3_utils balíčku)
* Zadejte následující příkazy:
  
    `$ dmesg | grep sd*`
     
     Nebo
  
    `$ fdisk -l`
  
    Ty vrátí podrobnosti o nedávno přidané disky.
* Chcete-li zjistit, zda se jedná o disk StorSimple, použijte následující příkazy:
  
    `cat /sys/block/<DISK>/device/model`
  
    Tím se vrátí řetězec, který určí, zda se jedná o disk StorSimple.

Méně pravděpodobnou, ale možnou příčinou může být také zatuchlý iscsid pid. K odhlášení z relací iSCSI použijte následující příkaz:

    iscsiadm -m node --logout -p <Target_IP>

Tento příkaz opakujte pro všechna připojená síťová rozhraní v cíli iSCSI, což je vaše zařízení StorSimple. Po odhlášení ze všech relací iSCSI použijte cílovou IQN iSCSI k obnovení relace iSCSI. Zadejte následující příkaz:

    iscsiadm -m node --login -T <TARGET_IQN>


Otázka: Nejsem si jistý, zda je moje zařízení na seznamu povolených.

A. Chcete-li ověřit, zda je vaše zařízení na seznamu povolených, použijte následující interaktivní příkaz pro řešení potíží:

    multipathd -k
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


Další informace naleznete v [řešení potíží s multipathingem](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot).

## <a name="list-of-useful-commands"></a>Seznam užitečných příkazů
| Typ | Příkaz | Popis |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Spuštění služby iSCSI |
| &nbsp; |`service iscsid stop` |Zastavit službu iSCSI |
| &nbsp; |`service iscsid restart` |Restartování služby iSCSI |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Zjistit dostupné cíle na zadané adrese |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Přihlášení k cíli iSCSI |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Odhlášení z cíle iSCSI |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Tisk názvu iniciátoru iSCSI |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |Kontrola stavu relace iSCSI a svazku zjištěného na hostiteli |
| &nbsp; |`iscsi -m session` |Zobrazuje všechny relace iSCSI vytvořené mezi hostitelem a zařízením StorSimple. |
|  | | |
| **Používání více cest** |`service multipathd start` |Spuštění vícecestného daemonu |
| &nbsp; |`service multipathd stop` |Zastavit vícecestný daemon |
| &nbsp; |`service multipathd restart` |Restartovat vícecestný daemon |
| &nbsp; |`chkconfig multipathd on` </br> NEBO </br> `mpathconf -with_chkconfig y` |Povolení spuštění vícecestné daemonu při spuštění |
| &nbsp; |`multipathd -k` |Spuštění interaktivní konzoly pro řešení potíží |
| &nbsp; |`multipath -l` |Seznam vícecestných připojení a zařízení |
| &nbsp; |`mpathconf --enable` |Vytvoření ukázkového souboru mulitpath.conf`/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Další kroky
Při konfiguraci mpio na linuxovém hostiteli může být také nutné odkazovat na následující dokumenty CentoS 6.6:

* [Nastavení MPIO na CentOS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Linux Training Guide](http://linux-training.be/linuxsys.pdf)

