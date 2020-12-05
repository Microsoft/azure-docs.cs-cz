---
title: Konfigurace funkce MPIO na hostiteli StorSimple Linux
description: Seznamte se s kroky potřebnými ke konfiguraci funkce MPIO (Path) na hostitelském serveru s StorSimple Linux (CentOS 6,6).
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: how-to
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 6584b2ecc54efd257bb30c479fd0f22150e8d9e1
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608584"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Konfigurace funkce MPIO na hostiteli StorSimple se systémem CentOS
Tento článek popisuje kroky potřebné ke konfiguraci funkce MPIO (CentOS) na hostitelském serveru s 6,6 v/v. Hostitelský server je připojený k vašemu zařízení Microsoft Azure StorSimple pro zajištění vysoké dostupnosti prostřednictvím iniciátorů iSCSI. Podrobně popisuje automatické zjišťování zařízení s více cestami a konkrétní nastavení jenom pro StorSimple svazky.

Tento postup platí pro všechny modely zařízení řady StorSimple 8000.

> [!NOTE]
> Tuto proceduru nelze použít pro StorSimple Cloud Appliance. Další informace najdete v tématu Postup konfigurace hostitelských serverů pro vaše cloudové zařízení.

> [!NOTE]
> Tento článek obsahuje odkazy na termín nesouhlasný – *termín, který* společnost Microsoft už nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.


## <a name="about-multipathing"></a>O více cestách
Funkce více cest umožňuje konfigurovat více cest I/O mezi hostitelským serverem a úložným zařízením. Tyto vstupně-výstupní cesty jsou fyzická připojení SAN, která můžou zahrnovat samostatné kabely, přepínače, síťová rozhraní a řadiče. Více cest agreguje cesty I/O pro konfiguraci nového zařízení, které je přidruženo ke všem agregovaným cestám.

Účelem s více cestami je dvě přeložení:

* **Vysoká dostupnost**: poskytuje alternativní cestu, pokud některý z prvků vstupně-výstupních cest (například kabel, přepínač, síťové rozhraní nebo kontroler) selhává.
* **Vyrovnávání zatížení**: v závislosti na konfiguraci úložného zařízení může zvýšit výkon tím, že detekuje zatížení v cestách I/O a dynamicky vyrovnává tyto zátěže.

### <a name="about-multipathing-components"></a>O komponentách s více cestami
S více cestami v systému Linux se skládají součásti jádra a součásti uživatelského prostoru, jak je uvedeno níže.

* **Jádro**: hlavní součást je *Mapovač zařízení* , který přesměruje vstupně-výstupní operace a podporuje převzetí služeb při selhání pro cesty a skupiny cest.

* **Uživatelské místo**: Jedná se o více *nástrojů* , které spravují zařízení s více cestami, a to pomocí pokynů k tomu, co dělat. Nástroje se skládají z těchto nástrojů:
   
   * **Multipath**: Vypíše a nakonfiguruje zařízení s více cestami.
   * S více **cestami**: démon, který spouští funkci Multipath a monitoruje cesty.
   * **Devmap-Name**: poskytuje smysluplné jméno zařízení – udev pro devmaps.
   * **Kpartx**: mapuje lineární devmaps na oddíly zařízení, aby bylo možné vytvářet oddíly s více mapami.
   * **Multipath. conf**: konfigurační soubor pro funkci Multipath démona, která se používá k přepsání předdefinované konfigurační tabulky.

### <a name="about-the-multipathconf-configuration-file"></a>Konfigurační soubor Multipath. conf
Konfigurační soubor `/etc/multipath.conf` usnadňuje mnoho funkcí s více cestami, které lze konfigurovat uživatelem. `multipath`Příkaz a démon jádra `multipathd` využívají informace, které se nacházejí v tomto souboru. Soubor se prochází jenom při konfiguraci zařízení s více cestami. Před spuštěním příkazu se ujistěte, že jsou všechny změny provedené `multipath` . Pokud soubor upravíte později, budete muset zastavit a znovu spustit více cest, aby se změny projevily.

Multipath. conf má pět částí:

- **Výchozí hodnoty na úrovni systému** *(výchozí nastavení)*: můžete přepsat výchozí hodnoty na úrovni systému.
- **Zakázaná zařízení** *(zakázaná)*: můžete určit seznam zařízení, která by se neměla kontrolovat pomocí mapovače zařízení.
- **Výjimky zakázané** *(blacklist_exceptions)*: můžete identifikovat konkrétní zařízení, která se budou považovat za zařízení s více zařízeními, i když jsou uvedená v seznamu zakázané.
- **Konkrétní nastavení řadiče úložiště** *(zařízení)*: můžete zadat nastavení konfigurace, která se použijí na zařízeních, která mají informace o dodavatelích a produktech.
- **Nastavení specifická pro zařízení** *(více cest)*: v této části můžete vyladit nastavení konfigurace pro jednotlivé logické jednotky (LUN).

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Konfigurace více cest v StorSimple připojených k hostiteli systému Linux
Zařízení StorSimple připojené k hostiteli se systémem Linux je možné nakonfigurovat pro vysokou dostupnost a vyrovnávání zatížení. Pokud má například hostitel Linux dvě rozhraní připojená k síti SAN a zařízení má dvě rozhraní připojená k síti SAN tak, že jsou tato rozhraní ve stejné podsíti, budou k dispozici 4 cesty. Pokud je však každé datové rozhraní v rozhraní zařízení a hostitele v jiné podsíti protokolu IP (a nikoli směrovatelný), budou k dispozici pouze 2 cesty. Můžete nakonfigurovat více cest pro automatické zjišťování všech dostupných cest, zvolit pro tyto cesty algoritmus vyrovnávání zatížení, použít specifická nastavení konfigurace pro StorSimple svazky a pak povolit a ověřit více cest.

Následující postup popisuje, jak nakonfigurovat více cest, pokud je zařízení StorSimple se dvěma síťovými rozhraními připojeno k hostiteli se dvěma síťovými rozhraními.

## <a name="prerequisites"></a>Předpoklady
Tato část podrobně popisuje požadavky na konfiguraci pro server CentOS a zařízení StorSimple.

### <a name="on-centos-host"></a>Na hostiteli CentOS
1. Ujistěte se, že má hostitel CentOS 2 síťová rozhraní povolena. Zadejte:
   
    `ifconfig`
   
    Následující příklad ukazuje výstup, pokud `eth0` jsou na hostiteli k dispozici dvě síťová rozhraní (a `eth1` ).
   
    ```output
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
    ```
1. Nainstalujte do svého serveru CentOS nástroje pro *iniciátory iSCSI* . Provedením následujících kroků nainstalujete nástroje *iniciátoru iSCSI*.
   
   1. Přihlaste se jako `root` do hostitele CentOS.
   1. Nainstalujte sady pro *iniciátory iSCSI*. Zadejte:
      
       `yum install iscsi-initiator-utils`
   1. Po úspěšné instalaci sady *iniciátoru iSCSI* (iSCSI) spusťte službu iSCSI. Zadejte:
      
       `service iscsid start`
      
       V některých případech se nemusí `iscsid` ve skutečnosti spouštět a `--force` může být potřeba mít možnost.
   1. Chcete-li zajistit, aby byl iniciátor iSCSI povolen během spouštění, použijte `chkconfig` příkaz pro povolení služby.
      
       `chkconfig iscsi on`
   1. Chcete-li ověřit, zda bylo správně nastaveno, spusťte příkaz:
      
       `chkconfig --list | grep iscsi`
      
       Ukázkový výstup najdete níž.
      
        ```output
        iscsi   0:off   1:off   2:on3:on4:on5:on6:off
        iscsid  0:off   1:off   2:on3:on4:on5:on6:off
        ```
      
       Z výše uvedeného příkladu vidíte, že se vaše prostředí iSCSI spustí při spuštění na úrovních spuštění 2, 3, 4 a 5.
1. Nainstalujte *Device-mapper-Multipath*. Zadejte:
   
    `yum install device-mapper-multipath`
   
    Spustí se instalace. Až budete vyzváni k potvrzení, zadejte **Y** .

### <a name="on-storsimple-device"></a>Na zařízení StorSimple
Vaše zařízení StorSimple by mělo mít:

* Minimálně dvě rozhraní povolená pro iSCSI. Pokud chcete ověřit, jestli jsou na zařízení StorSimple povolená dvě rozhraní, na portálu Azure Classic pro vaše zařízení StorSimple proveďte následující kroky:
  
  1. Přihlaste se na klasický portál pro zařízení StorSimple.
  1. Vyberte službu StorSimple Manager, klikněte na **zařízení** a zvolte konkrétní zařízení StorSimple. Klikněte na **Konfigurovat** a ověřte nastavení síťového rozhraní. Snímek obrazovky se dvěma síťovými rozhraními s podporou iSCSI je uveden níže. Tady jsou DATA 2 a DATA 3, což je povolené rozhraní iSCSI pro rozhraní standardu 10 GbE.
     
      ![Konfigurace StorsSimple dat 2 pro funkci MPIO](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![StorSimple DATA 3 – konfigurace pro MPIO](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      Na stránce **Konfigurace**
     
     1. Zajistěte, aby obě síťová rozhraní měla povolený iSCSI. Pole s **povoleným iSCSI** by mělo být nastavené na **Ano**.
     1. Ujistěte se, že síťová rozhraní mají stejnou rychlost, obě by měly být 1 GbE nebo 10 GbE.
     1. Poznamenejte si adresy IPv4 rozhraní s podporou iSCSI a uložte je pro pozdější použití na hostiteli.
* Rozhraní iSCSI v zařízení StorSimple by měla být dosažitelná ze serveru CentOS.
      Pokud to chcete ověřit, musíte na hostitelském serveru zadat IP adresy vašich StorSimple síťových rozhraní s podporou iSCSI. Jsou uvedené příkazy a odpovídající výstup s použitím příkazu DATA2 (10.126.162.25) a DATA3 (10.126.162.26):
  
    ```console
    [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    ```

### <a name="hardware-configuration"></a>Hardwarová konfigurace
Pro zajištění redundance doporučujeme propojit dvě síťová rozhraní iSCSI v samostatných cestách. Následující obrázek ukazuje doporučenou hardwarovou konfiguraci pro vysokou dostupnost a více cest vyrovnávání zatížení pro server CentOS a zařízení StorSimple.

![Konfigurace hardwaru funkce MPIO pro StorSimple na hostitele platformy Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Jak je znázorněno na předchozím obrázku:

* Vaše zařízení StorSimple je v konfiguraci aktivní-pasivní se dvěma řadiči.
* K řadičům zařízení jsou připojené dva přepínače sítě SAN.
* Na zařízení StorSimple jsou povolené dva iniciátory iSCSI.
* Na hostiteli CentOS jsou povolena dvě síťová rozhraní.

Výše uvedená konfigurace bude při směrování hostitelských a datových rozhraní vracet 4 samostatné cesty mezi zařízením a hostitelem.

> [!IMPORTANT]
> * Pro více cest doporučujeme nekombinovat 1 GbE a síťová rozhraní s 10 GbE. Při použití dvou síťových rozhraní by obě rozhraní měly být stejného typu.
> * Ve vašem zařízení StorSimple jsou DATA0, DATA1, DATA4 a DATA5 rozhraní 1 GbE, zatímco DATA2 a DATA3 jsou síťová rozhraní 10 GbE. |
> 
> 

## <a name="configuration-steps"></a>Postup konfigurace
Postup konfigurace pro více cest zahrnuje konfiguraci dostupných cest pro automatické zjišťování, určení používaného algoritmu vyrovnávání zatížení a povolení více cest a nakonec ověřování konfigurace. Každý z těchto kroků je podrobně popsán v následujících částech.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Krok 1: Konfigurace více cest pro automatické zjišťování
Zařízení s podporou více funkcí se dají automaticky zjistit a nakonfigurovat.

1. Inicializovat `/etc/multipath.conf` soubor. Zadejte:
   
     `mpathconf --enable`
   
    Pomocí výše uvedeného příkazu se vytvoří `sample/etc/multipath.conf` soubor.
1. Spusťte službu Multipath. Zadejte:
   
    `service multipathd start`
   
    Zobrazí se následující výstup:
   
    `Starting multipathd daemon:`
1. Povolí automatické zjišťování více cest. Zadejte:
   
    `mpathconf --find_multipaths y`
   
    Tím se upraví sekce výchozí hodnoty, `multipath.conf` jak je znázorněno níže:
   
    ```config
    defaults {
    find_multipaths yes
    user_friendly_names yes
    path_grouping_policy multibus
    }
    ```

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Krok 2: Konfigurace více cest pro StorSimple svazky
Ve výchozím nastavení jsou všechna zařízení černá uvedená v souboru Multipath. conf a budou se obejít. Budete muset vytvořit výjimky zakázané pro povolení více cest pro svazky ze zařízení StorSimple.

1. Úprava souboru `/etc/mulitpath.conf`. Zadejte:
   
    `vi /etc/multipath.conf`
1. V souboru Multipath. conf vyhledejte část blacklist_exceptions. Vaše zařízení StorSimple musí být v této části uvedené jako výjimka zakázané. V tomto souboru můžete odkomentovat relevantní řádky a upravit je tak, jak vidíte níže (použijte jenom konkrétní model zařízení, které používáte):
   
    ```config
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
    ```

### <a name="step-3-configure-round-robin-multipathing"></a>Krok 3: Konfigurace více cest pro kruhové dotazování
Tento algoritmus vyrovnávání zatížení používá všechny dostupné cesty k aktivnímu řadiči v vyváženém kruhovém dotazování.

1. Úprava souboru `/etc/multipath.conf`. Zadejte:
   
    `vi /etc/multipath.conf`
1. V `defaults` části nastavte na `path_grouping_policy` `multibus` . `path_grouping_policy`Určuje výchozí zásadu seskupování cest, která se má použít u nespecifikovaných cest. Oddíl Defaults (výchozí) bude vypadat jako v následujícím příkladu.
   
    ```config
    defaults {
            user_friendly_names yes
            path_grouping_policy multibus
    }
    ```

> [!NOTE]
> Mezi nejběžnější hodnoty `path_grouping_policy` patří:
> 
> * převzetí služeb při selhání = 1 cesta na skupinu priorit
> * multibus = všechny platné cesty ve skupině priorit 1
> 
> 

### <a name="step-4-enable-multipathing"></a>Krok 4: povolení více cest
1. Restartujte `multipathd` démona. Zadejte:
   
    `service multipathd restart`
1. Výstup bude, jak je znázorněno níže:
   
    ```output
    [root@centosSS ~]# service multipathd start
    Starting multipathd daemon:  [OK]
    ```

### <a name="step-5-verify-multipathing"></a>Krok 5: ověření více cest
1. Nejdřív se ujistěte, že se na zařízení StorSimple naváže připojení iSCSI, a to takto:
   
   a. Objevte zařízení StorSimple. Zadejte:
      
    `iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>`
    
    Výstup, pokud je IP adresa pro DATA0, 10.126.162.25 a na zařízení StorSimple je otevřený port 3260 pro odchozí přenosy iSCSI, jak je znázorněno níže:
    
    ```output
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Zkopírujte identifikátor IQN zařízení StorSimple, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` z předchozího výstupu.

   b. Připojte se k zařízení pomocí cíle IQN. Zařízení StorSimple je tady cíl iSCSI. Zadejte:

      `iscsiadm -m node --login -T <IQN of iSCSI target>`

    Následující příklad ukazuje výstup s cílovým identifikátorem IQN `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` . Výstup označuje, že jste úspěšně připojeni ke dvěma síťovým rozhraním podporujícím iSCSI v zařízení.

    ```output
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Pokud vidíte pouze jedno hostitelské rozhraní a dvě cesty, je nutné povolit obě rozhraní na hostiteli pro iSCSI. Můžete postupovat podle [podrobných pokynů v dokumentaci k platformě Linux](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

1. Svazek je vystavený serveru CentOS ze zařízení StorSimple. Další informace najdete v části [Krok 6: vytvoření svazku](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) prostřednictvím Azure Portal na zařízení StorSimple.

1. Ověřte dostupné cesty. Zadejte:

    `multipath -l`

      Následující příklad ukazuje výstup pro dvě síťová rozhraní na zařízení StorSimple připojené k síťovému rozhraní s jedním hostitelem a dvěma dostupnými cestami.

    ```output
    mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 7:0:0:1 sdc 8:32 active undef running
    `- 6:0:0:1 sdd 8:48 active undef running
    ```

    Následující příklad ukazuje výstup pro dvě síťová rozhraní na zařízení StorSimple, která se připojují ke dvěma síťovým rozhraním hostitele se čtyřmi dostupnými cestami.

    ```output
    mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 17:0:0:0 sdb 8:16 active undef running
    |- 15:0:0:0 sdd 8:48 active undef running
    |- 14:0:0:0 sdc 8:32 active undef running
    `- 16:0:0:0 sde 8:64 active undef running
    ```

    Po nakonfigurování cest si Projděte konkrétní pokyny v hostitelském operačním systému (CentOS 6,6), abyste mohli připojit a naformátovat tento svazek.

## <a name="troubleshoot-multipathing"></a>Řešení potíží s více cestami
V této části najdete několik užitečných tipů, pokud narazíte na problémy při konfiguraci s více cestami.

Otázka: Nezobrazují se změny v `multipath.conf` souboru.

A. Pokud jste v souboru provedli nějaké změny `multipath.conf` , budete muset službu s více cestami restartovat. Zadejte následující příkaz:

`service multipathd restart`

Otázka: Na zařízení StorSimple jsem povolil dvě síťová rozhraní a na hostiteli jsou dvě síťová rozhraní. Po vypsání dostupných cest se zobrazí pouze dvě cesty. Očekávalo se, že se zobrazily čtyři dostupné cesty.

A. Ujistěte se, že tyto dvě cesty jsou ve stejné podsíti a směrovatelný. Pokud jsou síťová rozhraní v různých sítích vLAN a ne směrovatelné, zobrazí se pouze dvě cesty. Jedním ze způsobů, jak to ověřit, je ujistit se, že máte přístup k rozhraním hostitele ze síťového rozhraní na zařízení StorSimple. Budete muset [kontaktovat podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) , protože toto ověření se může provádět jenom prostřednictvím relace podpory.

Otázka: Když mám seznam dostupných cest, nevidím žádný výstup.

A. Nezobrazení jakýchkoli cest s více cestami má obvykle problém s démonem s více cestami a je nejpravděpodobnější, že se v souboru nachází nějaký problém `multipath.conf` .

Mělo by to taky znamenat kontrolu nad tím, že se po připojení k cíli zobrazí některé disky, protože žádná odpověď ze seznamu Multipath by také nepředstavovala žádné disky.

* Pomocí následujícího příkazu znovu zkontrolujte sběrnici SCSI:
  
    `$ rescan-scsi-bus.sh` (součást sg3_utils balíčku)
* Zadejte následující příkazy:
  
    `$ dmesg | grep sd*`
     
     Nebo
  
    `$ fdisk -l`
  
    Tato akce vrátí podrobné informace o nedávno přidaných discích.
* Chcete-li zjistit, zda se jedná o StorSimple disk, použijte následující příkazy:
  
    `cat /sys/block/<DISK>/device/model`
  
    Tím se vrátí řetězec, který určí, jestli se jedná o StorSimple disk.

Nepravděpodobná, ale možná příčina může být zastaralý identifikátor PID v rámci iSCSI. K odhlášení z relací iSCSI použijte následující příkaz:

`iscsiadm -m node --logout -p <Target_IP>`

Tento příkaz opakujte pro všechna připojená síťová rozhraní v cíli iSCSI, což je vaše zařízení StorSimple. Po odhlášení ze všech relací iSCSI použijte k opětovnému vytvoření relace iSCSI cílový identifikátor IQN iSCSI. Zadejte následující příkaz:

`iscsiadm -m node --login -T <TARGET_IQN>`


Otázka: Nejste si jistí, jestli je moje zařízení povolené.

A. Pokud chcete ověřit, jestli je zařízení povolené, použijte následující řešení potíží s interaktivním příkazem:

```console
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
```


Další informace najdete v postupu [při odstraňování více cest](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot)na webu.

## <a name="list-of-useful-commands"></a>Seznam užitečných příkazů
| Typ | Příkaz | Popis |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Spustit službu iSCSI |
| &nbsp; |`service iscsid stop` |Zastavení služby iSCSI |
| &nbsp; |`service iscsid restart` |Restartovat službu iSCSI |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Zjistit dostupné cíle na zadané adrese |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Přihlášení k cíli iSCSI |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Odhlášení od cíle iSCSI |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Tisk názvu iniciátoru iSCSI |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |Zkontroluje stav relace iSCSI a zjištěného svazku na hostiteli. |
| &nbsp; |`iscsi -m session` |Zobrazuje všechny relace iSCSI navázané mezi hostitelem a zařízením StorSimple. |
|  | | |
| **Používání více cest** |`service multipathd start` |Spustit proces Multipath |
| &nbsp; |`service multipathd stop` |Zastavení procesu Multipath |
| &nbsp; |`service multipathd restart` |Opětovné spuštění procesu Multipath |
| &nbsp; |`chkconfig multipathd on` </br> NEBO </br> `mpathconf -with_chkconfig y` |Povolit spuštění procesu Multipath v době spuštění |
| &nbsp; |`multipathd -k` |Spustit interaktivní konzolu pro řešení potíží |
| &nbsp; |`multipath -l` |Seznam připojení a zařízení se seznamem funkcí Multipath |
| &nbsp; |`mpathconf --enable` |Vytvoření ukázkového souboru mulitpath. conf v `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Další kroky
Když konfigurujete funkci MPIO pro hostitele se systémem Linux, může se také vyžadovat, abyste odkazovali na následující dokumenty CentoS 6,6:

* [Nastavení funkce MPIO na CentOS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Průvodce školením pro Linux](http://linux-training.be/linuxsys.pdf)
