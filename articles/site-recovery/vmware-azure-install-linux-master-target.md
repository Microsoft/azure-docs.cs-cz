---
title: Instalace hlavního cílového serveru pro obnovení služeb virtuálního počítače s Linuxem pomocí azure site recovery
description: Zjistěte, jak nastavit hlavní cílový server Linuxu pro navrácení služeb po obnovení na místní web během zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: mayg
ms.openlocfilehash: 5b4d625d28584bb601905e9439c112c845219e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954383"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Instalace hlavního cílového serveru s Linuxem pro účely navrácení služeb po obnovení
Po převzetí služeb při selhání virtuálních počítačů do Azure, můžete navrácení služeb po selhání virtuálních počítačů do místního webu. Chcete-li navrácení služeb po obnovení, je třeba znovu chránit virtuální počítač z Azure do místního webu. Pro tento proces potřebujete místní hlavní cílový server pro příjem provozu. 

Pokud je váš chráněný virtuální počítač virtuální počítač s Windows, potřebujete hlavní cíl windows. Pro virtuální počítač Linux potřebujete hlavní cíl Linuxu. Přečtěte si následující kroky, jak vytvořit a nainstalovat hlavní cíl Linuxu.

> [!IMPORTANT]
> Počínaje vydáním hlavního cílového serveru 9.10.0 může být nejnovější hlavní cílový server nainstalován pouze na serveru Ubuntu 16.04. Nové instalace nejsou na serverech CentOS6.6 povoleny. Můžete však pokračovat v inovaci starých hlavních cílových serverů pomocí verze 9.10.0.
> Hlavní cílový server na LVM není podporován.

## <a name="overview"></a>Přehled
Tento článek obsahuje pokyny k instalaci hlavního cíle Linuxu.

Komentáře nebo dotazy zveřejňujte na konci tohoto článku nebo na [fóru služby Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Požadavky

* Chcete-li zvolit hostitele, na kterém chcete nasadit hlavní cíl, zjistěte, jestli navrácení služeb po obnovení bude na stávající mstovadov nebo do nového virtuálního počítače. 
    * Pro existující virtuální počítač by měl mít hostitel hlavního cíle přístup k úložištím dat virtuálního počítače.
    * Pokud místní virtuální počítač neexistuje (v případě alternativního obnovení umístění), virtuální počítač navrácení služeb po obnovení se vytvoří na stejném hostiteli jako hlavní cíl. Můžete si vybrat libovolného hostitele ESXi pro instalaci hlavního cíle.
* Hlavní cíl by měl být v síti, která může komunikovat s procesním serverem a konfiguračním serverem.
* Verze hlavního cíle musí být rovna nebo starší než verze procesního serveru a konfiguračního serveru. Pokud je například verze konfiguračního serveru 9.4, může být verze hlavního cíle 9.4 nebo 9.3, ale ne 9.5.
* Hlavním cílem může být pouze virtuální počítač VMware a nikoli fyzický server.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Pokyny pro vytváření hlavního cílového serveru

Vytvořte hlavní cíl v souladu s následujícími pokyny pro velikost:
- **RAM**: 6 GB nebo více
- **Velikost disku operačního systému:** 100 GB nebo více (pro instalaci operačního systému)
- **Další velikost disku pro retenční jednotku**: 1 TB
- **Cpu jádra:** 4 jádra nebo více

Následující jádra Ubuntu jsou podporovány.


|Řada jádra  |Podpora až  |
|---------|---------|
|4.4      |4.4.0-81-genový         |
|4.8      |4.8.0-56-generika         |
|4.10     |4.10.0-24-generika        |


## <a name="deploy-the-master-target-server"></a>Nasazení hlavního cílového serveru

### <a name="install-ubuntu-16042-minimal"></a>Instalace Ubuntu 16.04.2 Minimální

Postupujte podle následujících kroků k instalaci 64bitového operačního systému Ubuntu 16.04.2.

1.   Přejděte na [odkaz ke stažení](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), zvolte nejbližší zrcadlo a stáhněte si Ubuntu 16.04.2 minimální 64bitový ISO.
Udržujte Ubuntu 16.04.2 minimální 64-bit ISO v jednotce DVD a spusťte systém.

1.  Jako preferovaný jazyk vyberte **angličtinu** a pak vyberte **Enter**.
    
    ![Vyberte jazyk](./media/vmware-azure-install-linux-master-target/image1.png)
1. Vyberte **Nainstalovat Ubuntu Server**a pak vyberte **Enter**.

    ![Vyberte Nainstalovat Ubuntu Server](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Jako preferovaný jazyk vyberte **angličtinu** a pak vyberte **Enter**.

    ![Vyberte angličtinu jako preferovaný jazyk](./media/vmware-azure-install-linux-master-target/image3.png)

1. Vyberte příslušnou volbu ze seznamu možností **časového pásma** a pak vyberte **Enter**.

    ![Výběr správného časového pásma](./media/vmware-azure-install-linux-master-target/image4.png)

1. Vyberte **Ne** (výchozí možnost) a pak vyberte **Enter**.

     ![Konfigurace klávesnice](./media/vmware-azure-install-linux-master-target/image5.png)
1. Jako zemi nebo oblast původu klávesnice vyberte **Angličtina (USA)** a pak vyberte **Enter**.

1. Jako rozložení klávesnice vyberte **Angličtina (USA)** a pak vyberte **Enter**.

1. Do pole **Název** hostitele zadejte název hostitele serveru a vyberte **pokračovat**.

1. Chcete-li vytvořit uživatelský účet, zadejte uživatelské jméno a vyberte **pokračovat**.

      ![Vytvoření uživatelského účtu](./media/vmware-azure-install-linux-master-target/image9.png)

1. Zadejte heslo nového uživatelského účtu a vyberte **pokračovat**.

1.  Potvrďte heslo pro nového uživatele a pak vyberte **pokračovat**.

    ![Potvrzení hesel](./media/vmware-azure-install-linux-master-target/image11.png)

1.  V dalším výběru pro šifrování domovského adresáře vyberte **Ne** (výchozí možnost) a pak vyberte **Enter**.

1. Pokud je zobrazené časové pásmo správné, vyberte **Ano** (výchozí možnost) a pak vyberte **Enter**. Chcete-li změnit konfiguraci časového pásma, vyberte možnost **Ne**.

1. V možnostech metody dělení vyberte **Možnost ID ... použijte celý disk**a pak vyberte **Enter**.

     ![Výběr možnosti metody dělení](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Vyberte příslušný disk z možností **Vybrat disk do oddílu** a pak vyberte **Enter**.

    ![Vyberte disk](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Výběrem **možnosti Ano** zapíšete změny na disk a pak vyberte **Enter**.

    ![Výběr výchozí možnosti](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Ve výběru konfigurovat proxy server vyberte výchozí možnost, vyberte **Pokračovat**a pak vyberte **Enter**.
     
     ![Vyberte způsob správy upgradů](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Ve výběru **možnosti Žádné automatické aktualizace** pro správu upgradů v systému vyberte možnost Žádné automatické aktualizace a pak vyberte **Enter**.

     ![Vyberte způsob správy upgradů](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Vzhledem k tomu, že hlavní cílový server Azure Site Recovery vyžaduje velmi specifickou verzi Ubuntu, musíte zajistit, aby upgrady jádra byly pro virtuální počítač zakázány. Pokud jsou povoleny, pak všechny pravidelné upgrady způsobit selhání hlavnícílový server. Ujistěte se, že jste vybrali možnost **Bez automatických aktualizací.**

1.  Vyberte výchozí volby. Pokud chcete openSSH pro Připojení SSH, vyberte možnost **OpenSSH server** a pak vyberte **Pokračovat**.

    ![Vybrat software](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Ve výběru pro instalaci zavaděče GRUB vyberte **Ano**a pak vyberte **Enter**.
     
    ![GRUB boot installer](./media/vmware-azure-install-linux-master-target/image20.png)


1. Vyberte vhodné zařízení pro instalaci zavaděče (nejlépe **/dev/sda)** a pak vyberte **Enter**.
     
    ![Výběr vhodného zařízení](./media/vmware-azure-install-linux-master-target/image21.png)

1. Vyberte **Pokračovat**a pak dokončete instalaci vyberte **Enter.**

    ![Dokončení instalace](./media/vmware-azure-install-linux-master-target/image22.png)

1. Po dokončení instalace se přihlaste k virtuálnímu virtuálnímu virtuálnímu zařízení pomocí pověření nového uživatele. (Další informace naleznete v **kroku 10.)**

1. Pomocí kroků popsaných na následujícím snímku obrazovky nastavte uživatelské heslo ROOT. Pak se přihlaste jako uživatel ROOT.

    ![Nastavení uživatelského hesla ROOT](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Konfigurace počítače jako hlavního cílového serveru

Chcete-li získat ID pro každý pevný disk SCSI ve virtuálním počítači Linux, **disk. EnableUUID = PARAMETR TRUE** musí být povolen. Chcete-li tento parametr povolit, postupujte takto:

1. Vypněte svůj virtuální počítač.

2. Klikněte pravým tlačítkem myši na položku virtuálního počítače v levém podokně a potom vyberte **Upravit nastavení**.

3. Vyberte kartu **Možnosti.**

4. V levém podokně vyberte **Upřesnit** > **obecné**a pak vyberte tlačítko **Parametry konfigurace** v pravé dolní části obrazovky.

    ![Otevřít konfigurační parametr](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    Možnost **Parametry konfigurace** není k dispozici, pokud je počítač spuštěn. Chcete-li tuto kartu aktivní, vypněte virtuální počítač.

5. Zjistěte, zda řádek s **diskem. EnableUUID** již existuje.

   - Pokud hodnota existuje a je nastavena na **Hodnotu False**, změňte hodnotu na **Hodnotu True**. (Hodnoty nejsou rozlišována malá a velká písmena.)

   - Pokud hodnota existuje a je nastavena na **hodnotu True**, vyberte **zrušit**.

   - Pokud hodnota neexistuje, vyberte **přidat řádek**.

   - Ve sloupci název přidejte **disk. EnableUUID**a nastavte hodnotu na **hodnotu TRUE**.

     ![Kontrola, zda disk. EnableUUID již existuje.](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Zakázání upgradů jádra

Cílový server azure site recovery vyžaduje konkrétní verzi Ubuntu, ujistěte se, že upgrady jádra jsou pro virtuální počítač zakázány. Pokud jsou povoleny upgrady jádra, může dojít k selhání hlavního cílového serveru.

#### <a name="download-and-install-additional-packages"></a>Stažení a instalace dalších balíčků

> [!NOTE]
> Ujistěte se, že máte připojení k Internetu ke stažení a instalaci dalších balíčků. Pokud nemáte připojení k Internetu, musíte ručně najít tyto balíčky Deb a nainstalovat je.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Získání instalačního programu pro instalaci

Pokud má hlavní cíl připojení k Internetu, můžete instalační program stáhnout pomocí následujících kroků. V opačném případě můžete instalační program zkopírovat z procesního serveru a potom jej nainstalovat.

#### <a name="download-the-master-target-installation-packages"></a>Stažení hlavních cílových instalačních balíčků

[Stáhněte si nejnovější linuxové cílové instalační bity](https://aka.ms/latestlinuxmobsvc).

Chcete-li jej stáhnout pomocí Linuxu, zadejte:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Ujistěte se, že jste stáhli a rozbalili instalační program v domovském adresáři. Pokud rozbalíte **/usr/Local**, instalace se nezdaří.


#### <a name="access-the-installer-from-the-process-server"></a>Přístup k instalačnímu programu z procesního serveru

1. Na procesním serveru přejděte na **stránku C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Zkopírujte požadovaný instalační soubor z procesního serveru a uložte jej jako **nejnovější linuxmobsvc.tar.gz** do domovského adresáře.


### <a name="apply-custom-configuration-changes"></a>Použití vlastních změn konfigurace

Chcete-li použít vlastní změny konfigurace, použijte jako uživatel ROOT následující kroky:

1. Spusťte následující příkaz pro zrušení binárního souboru.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![Snímek obrazovky příkazu, který chcete spustit](./media/vmware-azure-install-linux-master-target/image16.png)

2. Spusťte následující příkaz a udělit oprávnění.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Spuštěním následujícího příkazu spusťte skript.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Spusťte skript pouze jednou na serveru. Pak vypněte server. Po přidání disku restartujte server, jak je popsáno v další části.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Přidání retenčního disku do hlavního cílového virtuálního počítače Linuxu

K vytvoření retenčního disku postupujte takto:

1. Připojte nový disk o velikosti 1 TB k hlavnímu cílovému virtuálnímu počítači Linuxu a spusťte počítač.

2. Pomocí příkazu **multipath -ll** se dozvíte id vícecestného disku: **multipath -ll**

    ![ID vícecestných cest](./media/vmware-azure-install-linux-master-target/image27.png)

3. Naformátujte jednotku a potom vytvořte systém souborů na nové jednotce: **mkfs.ext4 /dev/mapper/\<retention disk's multipath id>**.
    
    ![Systém souborů](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Po vytvoření systému souborů připojte retenční disk.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Vytvořte **položku fstab** pro připojení retenční jednotky při každém spuštění systému.
    
    `vi /etc/fstab`
    
    Vyberte **Vložit,** abyste začali soubor upravovat. Vytvořte nový řádek a vložte následující text. Upravte ID vícecestných cest disku na základě zvýrazněného ID více cest z předchozího příkazu.

    **/dev/mapper/\<Retenční disky multipath ID> /mnt/retention ext4 rw 0**

    Vyberte **Esc**a zadejte **:wq** (write and quit), chcete-li zavřít okno editoru.

### <a name="install-the-master-target"></a>Instalace hlavního cíle

> [!IMPORTANT]
> Verze hlavního cílového serveru musí být stejná nebo starší než verze procesního serveru a konfiguračního serveru. Pokud tato podmínka není splněna, znovu chránit úspěšné, ale replikace se nezdaří.


> [!NOTE]
> Před instalací hlavního cílového serveru zkontrolujte, zda soubor **/etc/hosts** ve virtuálním počítači obsahuje položky, které mapují místní název hostitele na adresy IP, které jsou přidruženy ke všem síťovým adaptérům.

1. Zkopírujte přístupové heslo z **c:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** na konfiguračním serveru. Potom jej uložte jako **soubor passphrase.txt** ve stejném místním adresáři spuštěním následujícího příkazu:

    `echo <passphrase> >passphrase.txt`

    Příklad: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Poznamenejte si adresu IP konfiguračního serveru. Spusťte následující příkaz k instalaci hlavního cílového serveru a zaregistrujte server u konfiguračního serveru.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Příklad: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Počkejte, až se skript dokončí. Pokud se hlavní cíl úspěšně zaregistruje, je hlavní cíl uveden na stránce **Infrastruktura obnovení webu** na portálu.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Instalace hlavního cíle pomocí interaktivní instalace

1. Chcete-li nainstalovat hlavní cíl, spusťte následující příkaz. Pro roli agenta zvolte **hlavní cíl**.

    ```
    ./install
    ```

2. Zvolte výchozí umístění pro instalaci a pak pokračujte vyberte **Enter.**

    ![Výběr výchozího umístění pro instalaci hlavního cíle](./media/vmware-azure-install-linux-master-target/image17.png)

Po dokončení instalace zaregistrujte konfigurační server pomocí příkazového řádku.

1. Poznamenejte si adresu IP konfiguračního serveru. Potřebujete ji v dalším kroku.

2. Spusťte následující příkaz k instalaci hlavního cílového serveru a zaregistrujte server u konfiguračního serveru.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Příklad: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Počkejte, až se skript dokončí. Pokud je hlavní cíl úspěšně zaregistrován, je hlavní cíl uveden na stránce **Infrastruktura obnovení webu** na portálu.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Instalace nástrojů VMware / open-vm-tools na hlavní cílový server

Je třeba nainstalovat nástroje VMware nebo nástroje pro otevření virtuálního počítače na hlavní cíl, aby mohl zjistit úložiště dat. Pokud nejsou nainstalovány nástroje, obrazovka reprotect není uvedena v úložištích dat. Po instalaci nástrojů VMware je třeba restartovat.

### <a name="upgrade-the-master-target-server"></a>Upgrade hlavního cílového serveru

Spusťte instalační program. Automaticky zjistí, že agent je nainstalován na hlavní cíl. Chcete-li provést upgrade, vyberte **položku Y**.  Po dokončení instalace zkontrolujte verzi hlavního cíle nainstalovanou pomocí následujícího příkazu:

`cat /usr/local/.vx_version`


Uvidíte, že pole **Verze** udává číslo verze hlavního cíle.

## <a name="common-issues"></a>Běžné problémy

* Ujistěte se, že nezapnete Storage vMotion na žádné součásti správy, jako je hlavní cíl. Pokud se hlavní cíl přesune po úspěšném opětovném nahrazení, disky virtuálního počítače (VMDK) nelze odpojit. V takovém případě se nezdaří navrácení služeb po selhání.

* Hlavní cíl by neměl mít žádné snímky ve virtuálním počítači. Pokud existují snímky, navrácení služeb po obnovení se nezdaří.

* Z důvodu některých vlastních konfigurací síťové sítě je síťové rozhraní při spuštění zakázáno a hlavní cílový agent nemůže inicializovat. Ujistěte se, že jsou správně nastaveny následující vlastnosti. Zkontrolujte tyto vlastnosti v souboru ethernetové karty /etc/sysconfig/network-scripts/ifcfg-eth*.
    * BOOTPROTO=dhcp
    * ONBOOT=ano


## <a name="next-steps"></a>Další kroky
Po dokončení instalace a registrace hlavního cíle se hlavní cíl zobrazí v **hlavní cílové** části v **části Infrastruktura obnovení lokality**v přehledu konfiguračního serveru.

Nyní můžete pokračovat s [opětovnou ochranou](vmware-azure-reprotect.md), následovanou navrácením služeb po obnovení.

