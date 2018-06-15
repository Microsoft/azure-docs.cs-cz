---
title: Konfigurace funkce MPIO na hostiteli systému StorSimple Linux | Microsoft Docs
description: Konfigurace funkce MPIO na StorSimple připojené k Linux hostitele se systémem CentOS 6.6
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
ms.openlocfilehash: 2fbae15c1c6a9ec886f57f9df903612ae10d8e12
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2018
ms.locfileid: "27779087"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Konfigurace funkce MPIO na StorSimple hostitele se systémem CentOS
Tento článek vysvětluje kroky nutné ke konfiguraci více cest vstupně-výstupní operace (MPIO) na serveru hostitele Centos 6.6. Hostitelský server je připojená k zařízení s Microsoft Azure StorSimple pro vysokou dostupnost prostřednictvím iniciátory iSCSI. Je podrobně popisuje automatické zjišťování vícenásobný zařízení a konkrétní nastavení pouze pro svazky zařízení StorSimple.

Tento postup se vztahuje na všechny modely řadu zařízení StorSimple 8000.

> [!NOTE]
> Tento postup nelze použít pro zařízení StorSimple cloudu. Další informace naleznete v části Konfigurace hostitele serverů pro vaše zařízení cloudu.


## <a name="about-multipathing"></a>O vytváření více cest
Tato funkce více cest můžete konfigurovat více vstupně-výstupních cest mezi hostitelským serverem a zařízením úložiště. Tyto cesty vstupně-výstupní operace jsou fyzické připojení SAN, která může zahrnovat samostatné kabely, přepínače, síťových rozhraní a řadiče. Více cest agreguje vstupně-výstupních cest ke konfiguraci nové zařízení, která souvisí s všechny agregované cesty.

Účelem více cest je dvojí:

* **Vysoká dostupnost**: poskytuje alternativní cestu, pokud se nezdaří libovolný element vstupně-výstupní cestu (například kabel, přepínače, síťové nebo řadič).
* **Vyrovnávání zatížení**: v závislosti na konfiguraci zařízení úložiště, můžete zvýšit výkon zjišťování zatížením na vstupně-výstupních cest a dynamicky vyrovnává těchto zatížení.

### <a name="about-multipathing-components"></a>O komponentách více cest
Více cest v systému Linux se skládá z jádra a komponenty uživatelského prostoru jako v následující tabulce.

* **Jádra**: hlavní součást je *zařízení mapper* , bude přesměrována vstupně-výstupních operací a podporuje převzetí služeb při selhání pro cesty a cesty skupiny.

* **Uživatel místo**: Jedná se o *multipath nástroje* , správě zařízení multipathed instruující vícenásobný modul Mapovač zařízení, co dělat. Nástroje obsahovat:
   
   * **Funkce Multipath**: uvádí a konfiguruje multipathed zařízení.
   * **Multipathd**: démon, která se spustí multipath a monitoruje cesty.
   * **Název Devmap**: poskytuje smysluplný název zařízení pro proces udev pro devmaps.
   * **Kpartx**: mapuje lineární devmaps zařízení oddíly, aby rozdělený vícenásobný mapy.
   * **Multipath.conf**: konfigurační soubor pro více cest démon procesu, který se používá k přepsání předdefinovaných konfigurace tabulky.

### <a name="about-the-multipathconf-configuration-file"></a>O konfiguračního souboru multipath.conf
Konfigurační soubor `/etc/multipath.conf` umožňuje mnoho funkcí více cest uživatelsky konfigurovatelného. `multipath` Příkaz a démon jádra `multipathd` použijte informace v tomto souboru. Soubor je konzultaci pouze při konfiguraci funkce multipath zařízení. Ujistěte se, že jsou všechny změny provedené před spuštěním `multipath` příkaz. Pokud upravíte soubor později, musíte zastavit a spustit multipathd znovu pro změny se projeví.

Multipath.conf má pět částí:

- **Výchozí nastavení na úrovni systému** *(výchozí)*: můžete přepsat výchozí nastavení na úrovni systému.
- **Zakázáno zařízení** *(černý list)*: můžete zadat seznam zařízení, které by neměly být řízené zařízení mapper.
- **Blokovaných výjimky** *(blacklist_exceptions)*: můžete identifikovat konkrétní zařízení jsou považovány za vícenásobný zařízení i v případě, že uvedené v blacklist.
- **Konkrétní nastavení řadiče úložiště** *(zařízení)*: můžete zadat nastavení konfigurace, které budou použity na zařízení, které mají dodavatele a informace o produktu.
- **Nastavení pro konkrétní zařízení** *(multipaths)*: v této části můžete doladit nastavení konfigurace pro jednotlivé logické jednotky.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Konfigurace více cest na StorSimple připojený k hostiteli systému Linux
Zařízení StorSimple připojený k hostiteli systému Linux lze nakonfigurovat pro vysokou dostupnost a vyrovnávání zatížení. Například pokud má hostitel Linux dvě rozhraní, které jsou připojené k síti SAN a zařízení má dvě rozhraní, které jsou připojené k síti SAN, tak, aby tato rozhraní jsou ve stejné podsíti, pak bude 4 cesty k dispozici. Ale pokud každé rozhraní DATA na zařízení a hostitele rozhraní jsou v jiné podsíti protokolu IP (a ne směrovatelné), pak pouze 2 cesty nebudou k dispozici. Můžete nakonfigurovat více cest automaticky zjistit všechny cesty k dispozici, zvolte algoritmu Vyrovnávání zatížení u těchto cest, použít specifické nastavení pro svazky jen StorSimple a potom povolit a ověřit více cest.

Následující postup popisuje, jak nakonfigurovat více cest při připojení zařízení StorSimple se dvěma síťovými rozhraními na hostitele se dvěma síťovými rozhraními.

## <a name="prerequisites"></a>Požadavky
Tato část podrobně popisuje požadavky konfigurace pro CentOS server a zařízení StorSimple.

### <a name="on-centos-host"></a>Na hostiteli CentOS
1. Ujistěte se, že má váš hostitel CentOS 2 síťových rozhraní, které jsou povolené. Zadejte:
   
    `ifconfig`
   
    Následující příklad ukazuje výstup, pokud dva síťové rozhraní (`eth0` a `eth1`) jsou k dispozici na hostiteli.
   
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
   
   1. Přihlaste se jako `root` do svého CentOS hostitele.
   2. Nainstalujte *iSCSI. iniciátoru utils*. Zadejte:
      
       `yum install iscsi-initiator-utils`
   3. Po *iSCSI. iniciátoru utils* je úspěšně nainstalována, spusťte službu iSCSI. Zadejte:
      
       `service iscsid start`
      
       V případech `iscsid` ve skutečnosti se nemusí spustit a `--force` možnost může být potřeba.
   4. K zajištění, že vaše iniciátor iSCSI je povolena při spuštění, použijte `chkconfig` příkaz, který povolí službu.
      
       `chkconfig iscsi on`
   5. Pokud chcete ověřit, aby byla správně nastavit, spusťte příkaz:
      
       `chkconfig --list | grep iscsi`
      
       Ukázkový výstup najdete níž.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       Z výše uvedeném příkladu vidíte, že prostředí iSCSI bude spouštět na spuštění spuštění úrovně 2, 3, 4 a 5.
3. Nainstalujte *zařízení. mapper multipath*. Zadejte:
   
    `yum install device-mapper-multipath`
   
    Spustí se instalace. Typ **Y** pokračovat po zobrazení výzvy k potvrzení.

### <a name="on-storsimple-device"></a>Na zařízení StorSimple
Zařízení StorSimple musí mít:

* Minimálně dvě rozhraní povolená pro iSCSI. Pokud chcete ověřit, zda jsou dvě rozhraní iSCSI povolený v zařízení StorSimple, proveďte následující kroky na portálu Azure classic pro zařízení StorSimple:
  
  1. Přihlaste se ke klasickému portálu pro zařízení StorSimple.
  2. Vybrat služby StorSimple Manager, klikněte na tlačítko **zařízení** a zvolte konkrétní zařízení StorSimple. Klikněte na tlačítko **konfigurace** a ověřte nastavení síťového rozhraní. Snímek obrazovky s dvě rozhraní iSCSI povolený sítě jsou uvedeny níže. Sem DATA 2 a DATA 3, oba 10 GbE je povoleno rozhraní iSCSI.
     
      ![Konfigurace funkce MPIO StorsSimple DATA 2](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![Funkce MPIO StorSimple dat 3 konfigurace](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      V **konfigurace** stránky
     
     1. Zajistěte, aby obě síťová rozhraní iSCSI povolený. **ISCSI povoleno** pole musí být nastavena na **Ano**.
     2. Zkontrolujte, zda síťových rozhraní má stejnou rychlostí, jak by měla být 1 GbE nebo 10 GbE.
     3. Poznámka: adresy IPv4 rozhraní iSCSI povolený a uložit pro pozdější použití na hostiteli.
* Na rozhraní iSCSI v zařízení StorSimple by měl být dostupný ze serveru, CentOS.
      Chcete-li to ověřit, zadejte IP adresy vašich rozhraní iSCSI povolený sítě StorSimple na hostitelském serveru. Příkazy používají a odpovídající výstup s DATA2 (10.126.162.25) a DATA3 (10.126.162.26) jsou uvedeny níže:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Konfigurace hardwaru
Doporučujeme, abyste připojení dvě síťová rozhraní iSCSI, na samostatné cesty pro redundanci. Následující obrázek znázorňuje doporučené hardwarové konfigurace pro vysokou dostupnost a vyrovnávaní zatížení více cest pro váš CentOS server a zařízení StorSimple.

![Hardwarová konfigurace funkce MPIO pro zařízení StorSimple na hostitele platformy Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Jak je vidět na předchozím obrázku:

* Zařízení StorSimple je v konfiguraci aktivní pasivní s dva řadiče.
* Dva přepínače sítě SAN jsou připojené k vašim řadičům zařízení.
* Dva iniciátory iSCSI jsou povolené v zařízení StorSimple.
* Na hostiteli CentOS jsou povolené dvě síťová rozhraní.

Výše uvedené konfigurace předá 4 samostatné cesty mezi vaším zařízením a hostitele, pokud jsou data hostitelů a rozhraní směrovatelné.

> [!IMPORTANT]
> * Doporučujeme vám, že nemíchat 1 GbE a 10 GbE síťová rozhraní pro vytváření více cest. Při použití dvou síťových rozhraní, jak rozhraní musí být identické typu.
> * V zařízení StorSimple, DATA0, DATA1, DATA4 a DATA5 jsou 1 GbE rozhraní zatímco DATA2 a DATA3 10 GbE síťových rozhraní. |
> 
> 

## <a name="configuration-steps"></a>Kroky konfigurace
Postup konfigurace pro více cest zahrnuje konfigurace k dispozici cesty pro automatické zjišťování, zadání algoritmus Vyrovnávání zatížení, pokud chcete použít, povolení více cest a nakonec ověření konfigurace. Každý z těchto kroků je podrobněji v následujících částech.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Krok 1: Konfigurace používání více cest pro automatické zjišťování
Zařízení podporující funkci multipath může automaticky zjistit a nakonfigurovat.

1. Inicializace `/etc/multipath.conf` souboru. Zadejte:
   
     `mpathconf --enable`
   
    Výše uvedený příkaz vytvoří `sample/etc/multipath.conf` souboru.
2. Vícenásobný službu spusťte. Zadejte:
   
    `service multipathd start`
   
    Zobrazí se následující výstup:
   
    `Starting multipathd daemon:`
3. Povolte automatické zjišťování multipaths. Zadejte:
   
    `mpathconf --find_multipaths y`
   
    To slouží k úpravě části výchozí nastavení vaší `multipath.conf` jak je uvedeno níže:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Krok 2: Konfigurace používání více cest pro svazky zařízení StorSimple
Ve výchozím nastavení všechna zařízení jsou černé uvedené v souboru multipath.conf a bude možné obejít. Musíte vytvořit zakázaných výjimky, které umožňují více cest pro svazky zařízení StorSimple.

1. Upravit `/etc/mulitpath.conf` souboru. Zadejte:
   
    `vi /etc/multipath.conf`
2. Vyhledejte v souboru multipath.conf blacklist_exceptions oddíl. Zařízení StorSimple musí být uveden jako zakázaných výjimka v této části. Zrušením komentáře u příslušné řádky v tomto souboru upravit podle následujícího obrázku (použijte pouze konkrétní model zařízení, které používáte):
   
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

### <a name="step-3-configure-round-robin-multipathing"></a>Krok 3: Konfigurace pomocí kruhového dotazování více cest
Tento algoritmus Vyrovnávání zatížení používá všechny dostupné multipaths k aktivnímu řadiči vyrovnáváním, kruhové dotazování způsobem.

1. Upravit `/etc/multipath.conf` souboru. Zadejte:
   
    `vi /etc/multipath.conf`
2. V části `defaults` nastavte `path_grouping_policy` k `multibus`. `path_grouping_policy` Určuje výchozí cestu seskupování zásadu použít neurčené multipaths. V části výchozí hodnoty bude vypadat, jak je uvedeno níže.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> Nejběžnější hodnoty `path_grouping_policy` zahrnují:
> 
> * převzetí služeb při selhání = 1 cesty na skupinu s prioritou
> * multibus = všechny platné cesty ve skupině s prioritou 1
> 
> 

### <a name="step-4-enable-multipathing"></a>Krok 4: Povolení používání více cest
1. Restartujte `multipathd` démona. Zadejte:
   
    `service multipathd restart`
2. Výstup bude, jak je uvedeno níže:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>Krok 5: Ověření více cest
1. Nejdříve se ujistěte, že iSCSI se naváže připojení zařízení StorSimple následujícím způsobem:
   
   a. Zjistit zařízení StorSimple. Zadejte:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    Výstup, pokud je IP adresa pro DATA0 10.126.162.25 a otevřít port 3260 v zařízení StorSimple pro přenosy iSCSI odchozí je, jak je uvedeno níže:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Zkopírujte IQN zařízení StorSimple `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, z předchozí výstupu.

   b. Připojte k zařízení pomocí cíl IQN. Zařízení StorSimple je zde cíle iSCSI. Zadejte:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    Následující příklad ukazuje výstup s cílem IQN systému `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. Výstup označuje, že jste úspěšně připojení dvě iSCSI povolený síťovým rozhraním na vašem zařízení.

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

    Pokud se zobrazí pouze jednoho hostitele rozhraní a dvě cesty sem, budete muset povolit rozhraní na hostiteli pro iSCSI. Můžete provést [podrobné pokyny v dokumentaci k systému Linux](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

2. Svazek má přístup k serveru CentOS ze zařízení StorSimple. Další informace najdete v tématu [krok 6: vytvoření svazku](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) prostřednictvím portálu Azure v zařízení StorSimple.

3. Ověření cesty k dispozici. Zadejte:

      ```
      multipath –l
      ```

      Následující příklad ukazuje výstup pro dvě síťová rozhraní na zařízení StorSimple připojené k jeden hostitel síťové rozhraní se dvě cesty k dispozici.

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
Tato část obsahuje některé užitečné tipy, pokud narazíte na potíže během konfigurace více cest.

OTÁZKY. Nevidím změny v `multipath.conf` souboru neprojeví.

A. Pokud jste provedli všechny změny `multipath.conf` soubor, budete muset restartovat službu více cest. Zadejte následující příkaz:

    service multipathd restart

OTÁZKY. Je povoleno dvě síťová rozhraní v zařízení StorSimple a dvě síťová rozhraní na hostiteli. Při zobrazení seznamu cest k dispozici, vidím pouze dvě cesty. Očekávání zobrazíte čtyři dostupné cesty.

A. Ujistěte se, že tyto dvě cesty jsou ve stejné podsíti a směrovat. Pokud rozhraní sítě jsou v jiné sítě VLAN a není směrovatelné, zobrazí se pouze dvě cesty. Chcete-li to ověřit je a ujistěte se, že může kontaktovat rozhraní hostitele ze síťového rozhraní v zařízení StorSimple. Budete muset [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md) jako toto ověření provést pouze prostřednictvím podpory relace.

OTÁZKY. Při zobrazení seznamu cest k dispozici, nezobrazí žádný výstup.

A. Obvykle není zobrazuje všechny cesty multipathed naznačuje problém s démon více cest a bude s největší pravděpodobností, jakýkoli problém s zde spočívá v `multipath.conf` souboru.

Toto nastavení by také být vhodné kontrola, zda se ve skutečnosti zobrazí některé disky po připojení k cíli, neboť žádná odpověď z vícecestného výpisů může taky znamenat, že nemáte žádné disky.

* Jestliže chcete prohledat sběrnice SCSI, použijte následující příkaz:
  
    `$ rescan-scsi-bus.sh `(součást balíčku sg3_utils)
* Zadejte následující příkazy:
  
    `$ dmesg | grep sd*`
     
     Nebo
  
    `$ fdisk –l`
  
    Tyto vrátí podrobnosti o nedávno přidané disky.
* K určení, zda se jedná o disk StorSimple, použijte následující příkazy:
  
    `cat /sys/block/<DISK>/device/model`
  
    Tato možnost vrátí řetězec, který bude zjistit, jestli je StorSimple disk.

Méně pravděpodobné, ale možná příčina A mohou být také zastaralé iscsid pid. Použijte následující příkaz k odhlášení z relace iSCSI:

    iscsiadm -m node --logout -p <Target_IP>

Opakujte tento příkaz pro všechna rozhraní propojená síť na cíli iSCSI, která je zařízení StorSimple. Jakmile jste se odhlásili z všechny relace iSCSI, použijte pokud chcete znovu vytvořit relace iSCSI cíle iSCSI IQN. Zadejte následující příkaz:

    iscsiadm -m node --login -T <TARGET_IQN>


OTÁZKY. Nejste si jisti, pokud zařízení je seznam povolených adres.

A. Pokud chcete ověřit, jestli vaše zařízení je seznam povolených adres, použijte následující řešení potíží interaktivní příkaz:

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


Další informace, přejděte na [použít řešení potíží s interaktivního příkazu pro více cest](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## <a name="list-of-useful-commands"></a>Seznam užitečné příkazy
| Typ | Příkaz | Popis |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Spuštění služby iSCSI |
| &nbsp; |`service iscsid stop` |Zastavit službu iSCSI |
| &nbsp; |`service iscsid restart` |Restartujte službu iSCSI |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Zjišťování dostupných cílů na zadanou adresu |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Přihlaste se k cíli iSCSI |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Odhlaste se z cíle iSCSI |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Tisk – název iniciátoru iSCSI |
| &nbsp; |`iscsiadm –m session –s <sessionid> -P 3` |Zkontrolujte stav relace iSCSI a svazek zjištěných na hostiteli |
| &nbsp; |`iscsi –m session` |Zobrazí všechny relace iSCSI navázat mezi hostitelem a zařízení StorSimple |
|  | | |
| **Více cest** |`service multipathd start` |Démon spuštění více cest |
| &nbsp; |`service multipathd stop` |Zastavit vícenásobný démon |
| &nbsp; |`service multipathd restart` |Restartujte vícenásobný démon |
| &nbsp; |`chkconfig multipathd on` </br> NEBO </br> `mpathconf –with_chkconfig y` |Povolení funkce multipath démon spustit při spuštění |
| &nbsp; |`multipathd –k` |Spusťte interaktivní konzolu pro řešení potíží |
| &nbsp; |`multipath –l` |Připojení více cest seznamu a zařízení |
| &nbsp; |`mpathconf --enable` |Vytvořte soubor ukázka mulitpath.conf v`/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Další postup
Jak na hostiteli systému Linux jsou konfigurace funkce MPIO, může také muset naleznete v následujících dokumentech CentoS 6.6:

* [Nastavení funkce MPIO na CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
* [Průvodce školení Linux](http://linux-training.be/files/books/LinuxAdm.pdf)

