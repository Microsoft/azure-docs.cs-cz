---
title: Instalace hlavního cílového serveru s Linuxem pro navrácení služeb po obnovení do místní lokality | Dokumentace Microsoftu
description: Zjistěte, jak nastavit hlavní cílový server Linux pro navrácení služeb po obnovení do místní lokality během zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
author: nsoneji
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: nisoneji
ms.openlocfilehash: 415b50b94052e7d428ddfa55d5288c8954a3ff1a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212365"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Instalace hlavního cílového serveru s Linuxem pro navrácení služeb po obnovení
Po převzetí služeb při selhání virtuálních počítačů do Azure, které můžete navrácení služeb po obnovení virtuálních počítačů do místní lokality. K navrácení služeb po obnovení, budete muset znovu nastavit ochranu virtuálního počítače z Azure do místní lokality. Tento proces je nutné místní hlavní cílový server pro příjem provozu. 

Pokud se chráněný virtuální počítač je virtuální počítač s Windows, potřebujete hlavní cílový Windows. Pro virtuální počítač s Linuxem potřebujete hlavní cílový Linux. Přečtěte si následující kroky a zjistěte, jak vytvořit a nainstalovat Linux hlavní cíl.

> [!IMPORTANT]
> Od verze 9.10.0 hlavní cílový server na nejnovější hlavní cílový server můžete nainstalovat jenom na serveru se systémem Ubuntu 16.04. Nové instalace nejsou povoleny na serverech CentOS6.6. Ale můžete dál upgrade původní hlavní cílový server pomocí 9.10.0 verze.

## <a name="overview"></a>Přehled
Tento článek obsahuje pokyny pro instalaci hlavního cílového systému Linux.

Dotazy nebo připomínky můžete publikovat na konci tohoto článku nebo na [fóru Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Požadavky

* Zvolte hostitele, na které se má nasadit na hlavním cíli, zjistit, pokud navrácení služeb po obnovení bude do existujícího virtuálního počítače v místním nebo do nového virtuálního počítače. 
    * Pro existující virtuální počítač hostitele na hlavním cíli mají mít přístup k úložišti dat virtuálního počítače.
    * Pokud na místním virtuálním počítači (v případě obnovení alternativního umístění) neexistuje, vytvoří se navrácení služeb po obnovení virtuálního počítače na stejném hostiteli jako hlavní cíl. Můžete použít všechny hostitele ESXi za účelem instalace na hlavním cíli.
* Na hlavním cíli by měl být v síti, který může komunikovat s procesovým serverem a konfiguračním serverem.
* Verze hlavního cíle musí být větší nebo starší než verze procesového serveru a konfiguračního serveru. Například pokud konfiguračního serveru na verzi 9.4, verze hlavního cíle může být 9.4 nebo 9.3, ale ne 9.5.
* Na hlavním cíli lze pouze virtuální počítač VMware, nikoli na fyzický server.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Pokyny pro nastavení velikosti pro vytvoření hlavního cílového serveru

Vytvořte na hlavním cíli v souladu s velikosti podle následujících pokynů:
- **Paměť RAM**: 6 GB nebo více
- **Velikost disku OS**: 100 GB nebo více (pro instalaci operačního systému)
- **Další velikosti jednotky pro uchovávání dat**: 1 TB
- **Jádra procesoru**: 4 jádra nebo více

Jsou podporovány následující podporované jádrech Ubuntu.


|Řada jádra  |Až  |
|---------|---------|
|4.4      |4.4.0-81-Generic         |
|4.8      |4.8.0-56-Generic         |
|4.10     |4.10.0-24-Generic        |


## <a name="deploy-the-master-target-server"></a>Nasazení hlavního cílového serveru

### <a name="install-ubuntu-16042-minimal"></a>Nainstalujte Ubuntu 16.04.2 minimální

Následující kroky pro instalaci operačního systému Ubuntu 16.04.2 64-bit.

1.   Přejděte [odkaz ke stažení](https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64), zvolte nejbližší anddownload zrcadlení Image ISO se systémem Ubuntu 16.04.2 minimální 64-bit.
Ponechat soubor ISO se systémem Ubuntu 16.04.2 minimální 64-bit do jednotky DVD a spuštění systému.

1.  Vyberte **Angličtina** jako váš preferovaný jazyk a pak vyberte **Enter**.
    
    ![Výběr jazyka](./media/vmware-azure-install-linux-master-target/image1.png)
1. Vyberte **instalace serveru Ubuntu**a pak vyberte **Enter**.

    ![Vyberte možnost instalace serveru Ubuntu](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Vyberte **Angličtina** jako váš preferovaný jazyk a pak vyberte **Enter**.

    ![Vybrat jako upřednostňovaný jazyk angličtina](./media/vmware-azure-install-linux-master-target/image3.png)

1. Vyberte odpovídající možnost z **časové pásmo** seznam možností a pak vyberte **Enter**.

    ![Vyberte správné časové pásmo.](./media/vmware-azure-install-linux-master-target/image4.png)

1. Vyberte **ne** (výchozí možnost) a pak vyberte **Enter**.

     ![Nakonfigurovat klávesnici](./media/vmware-azure-install-linux-master-target/image5.png)
1. Vyberte **angličtinu (US)** jako země původu klávesnice a pak vyberte **Enter**.

1. Vyberte **angličtinu (US)** rozložení klávesnice, a pak vyberte **Enter**.

1. Zadejte název hostitele pro váš server v **Hostname** a potom vyberte **pokračovat**.

1. Chcete-li vytvořit uživatelský účet, zadejte uživatelské jméno a pak vyberte **pokračovat**.

      ![Vytvoření uživatelského účtu](./media/vmware-azure-install-linux-master-target/image9.png)

1. Zadejte heslo pro nový uživatelský účet a potom vyberte **pokračovat**.

1.  Potvrďte heslo pro nového uživatele a pak vyberte **pokračovat**.

    ![Potvrzení hesla](./media/vmware-azure-install-linux-master-target/image11.png)

1.  V dalším výběru pro šifrování domovský adresář, vyberte **ne** (výchozí možnost) a pak vyberte **Enter**.

1. Pokud je časové pásmo, který se zobrazí správný, vyberte **Ano** (výchozí možnost) a pak vyberte **Enter**. Chcete-li změnit konfiguraci časového pásma, vyberte **ne**.

1. Dělicí metoda možnosti, vyberte **s asistencí – použít celý disk**a pak vyberte **Enter**.

     ![Vyberte možnost dělicí metody](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Vyberte příslušný disk z **vyberte disk do oddílu** možnosti a pak vyberte **Enter**.

    ![Vyberte disk](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Vyberte **Ano** se zapsat změny na disk a potom vyberte **Enter**.

    ![Vyberte výchozí možnost](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Ve výběru konfigurace proxy serveru, vyberte možnost výchozí, vyberte **pokračovat**a pak vyberte **Enter**.
     
     ![Vyberte, jak spravovat upgrady](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Vyberte **žádné automatické aktualizace** možnost výběru pro správu inovace ve vašem systému a pak vyberte **Enter**.

     ![Vyberte, jak spravovat upgrady](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Vzhledem k tomu, že hlavní cílový server Azure Site Recovery vyžaduje specifickou verzi Ubuntu, budete muset zajistit, aby jádra upgrady jsou zakázané pro virtuální počítač. Pokud se povolí, všechny pravidelné aktualizace způsobit hlavní cílový server fungovat správně. Ujistěte se, že jste vybrali **žádné automatické aktualizace** možnost.

1.  Vyberte výchozí možnosti. Pokud chcete openSSH připojení přes SSH, vyberte **OpenSSH server** a potom vyberte možnost **pokračovat**.

    ![Vybraný software](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. V selction pro instalaci GRUB spouštěcí zavaděč, vyberte **Ano**a pak vyberte **Enter**.
     
    ![GRUB spuštění instalačního programu](./media/vmware-azure-install-linux-master-target/image20.png)


1. Vyberte příslušné zařízení pro instalaci spouštěcí zavaděč (pokud možno **/dev/sda**) a pak vyberte **Enter**.
     
    ![Vyberte odpovídající zařízení](./media/vmware-azure-install-linux-master-target/image21.png)

1. Vyberte **pokračovat**a pak vyberte **Enter** pro dokončení instalace.

    ![Dokončení instalace](./media/vmware-azure-install-linux-master-target/image22.png)

1. Po dokončení instalace se přihlaste k virtuálnímu počítači s novými pověřeními uživatele. (Odkazovat **krok 10** Další informace.)

1. Pomocí kroků, které jsou popsány v následující snímek obrazovky nastavení KOŘENOVÉ heslo uživatele. Pak se přihlaste jako uživatel ROOT.

    ![Nastavte KOŘENOVÉ heslo uživatele](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Nakonfigurujte počítač jako hlavní cílový server

Chcete-li získat ID pro každý SCSI pevný disk do virtuálního počítače s Linuxem **disku. EnableUUID = TRUE** parametr musí být povolená. Chcete-li tento parametr, proveďte následující kroky:

1. Vypnete virtuální počítač.

2. Klikněte pravým tlačítkem na položku pro virtuální počítač v levém podokně a pak vyberte **upravit nastavení**.

3. Vyberte **možnosti** kartu.

4. V levém podokně vyberte **Upřesnit** > **Obecné**a pak vyberte **parametry konfigurace** tlačítko v pravé dolní části obrazovky.

    ![Otevřete konfigurační parametr](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    **Parametry konfigurace** možnost není k dispozici, když na počítači běží. Chcete-li na této kartě aktivní, vypnete virtuální počítač.

5. Zda řádek s **disku. EnableUUID** již existuje.

    - Pokud hodnota existuje a je nastavená na **False**, změňte hodnotu na **True**. (Hodnoty nejsou malá a velká písmena.)

    - Pokud hodnota existuje a je nastavená na **True**vyberte **zrušit**.

    - Pokud hodnota neexistuje, vyberte **přidat řádek**.

    - Ve sloupci Název přidat **disku. EnableUUID**a pak nastavte hodnotu na **TRUE**.

    ![Kontroluje, jestli disk. EnableUUID již existuje.](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Zakázat upgrady jádra

Azure Site Recovery hlavní cílový server vyžaduje určitou verzi Ubuntu a ujistěte se, že je zakázáno upgrady jádra pro virtuální počítač. Pokud jsou povolené jádra upgrady, může to způsobit selhání hlavního cílového serveru.

#### <a name="download-and-install-additional-packages"></a>Stáhnout a nainstalovat další balíčky

> [!NOTE]
> Ujistěte se, že máte připojení k Internetu stáhnout a nainstalovat další balíčky. Pokud nemáte připojení k Internetu, musíte ručně vyhledat tyto balíčky ot. / min a nainstalujte je.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Získat instalační program pro instalaci

Pokud se hlavní cíl je připojený k Internetu, můžete použít následující kroky pro stažení instalačního programu. V opačném případě můžete zkopírovat instalační program z procesového serveru a nainstalujte ji.

#### <a name="download-the-master-target-installation-packages"></a>Stáhnout instalační balíčky hlavní cíl

[Stáhněte si nejnovější bity instalace hlavního cíle Linuxu](https://aka.ms/latestlinuxmobsvc).

Chcete-li stáhnout pomocí Linuxu, zadejte:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Ujistěte se, stáhněte a rozbalte instalační program ve svém domovském adresáři. Pokud rozbalte do **/usr/Local**, instalace selže.


#### <a name="access-the-installer-from-the-process-server"></a>Přístup k Instalační program z procesového serveru

1. Na procesovém serveru, přejděte na **C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Zkopírujte požadované instalační soubor z procesového serveru a uložte ho jako **latestlinuxmobsvc.tar.gz** ve svém domovském adresáři.


### <a name="apply-custom-configuration-changes"></a>Použít změny vlastní konfigurace

Chcete-li použít změny vlastní konfigurace, postupujte následovně:


1. Spusťte následující příkaz, který untar binárního souboru.

    `tar -zxvf latestlinuxmobsvc.tar.gz`

    ![Snímek obrazovky příkaz ke spuštění](./media/vmware-azure-install-linux-master-target/image16.png)

2. Spusťte následující příkaz k udělení oprávnění.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Spusťte následující příkaz pro spuštění skriptu.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Spusťte skript jenom jednou na serveru. Vypněte na serveru. Po přidání disku, jak je popsáno v další části, restartujte server.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Přidat disk pro uchování na hlavním cílovém virtuálním počítači s Linuxem

Chcete-li vytvořit disk pro uchování postupujte následovně:

1. Připojit nový disk 1 TB na hlavním cílovém virtuálním počítači s Linuxem a potom počítač spustit.

2. Použití **více cest – vše** příkazu se dozvíte více cest ID disku pro uchování: **více cest – vše**

    ![Funkce Multipath ID](./media/vmware-azure-install-linux-master-target/image27.png)

3. Formátování disku a poté vytvořit novou jednotku systému souborů: **mkfs.ext4 /dev/Mapovač/< více cest id disku uchování >**.
    
    ![Systém souborů](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Po vytvoření systému souborů, připojte disk pro uchování.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Vytvořte **fstab** položka připojit jednotka pro uchovávání dat pokaždé, když se spustí v systému.
    
    `vi /etc/fstab`
    
    Vyberte **vložit** zahájíte úpravou souboru. Vytvořit novou čáru a vložte následující text. Upravte více cest ID disku na základě Identifikátoru zvýrazněné více cest z předchozího příkazu.

    **/dev/Mapovač/ <Retention disks multipath id> /mnt/uchování ext4 rw 0 0**

    Vyberte **Esc**a pak zadejte **: QW** (zápisu a ukončete) zavřete okno editoru.

### <a name="install-the-master-target"></a>Instalace na hlavním cíli

> [!IMPORTANT]
> Verze hlavního cílového serveru musí být větší nebo starší než verze procesového serveru a konfiguračního serveru. Pokud tato podmínka není splněna, úspěšné operace opětovného zapnutí ochrany, ale replikace selže.


> [!NOTE]
> Před instalací hlavní cílový server zkontrolujte, že **/etc/hosts** soubor na virtuální počítač obsahuje položky, které mapují místní název hostitele na IP adresy, které jsou spojeny s všechny síťové adaptéry.

1. Zkopírujte heslo ze **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** na konfiguračním serveru. Uložte ho jako **passphrase.txt** ve stejném adresáři místního spuštěním následujícího příkazu:

    `echo <passphrase> >passphrase.txt`

    Příklad: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Poznamenejte si IP adresu konfiguračního serveru. Spusťte následující příkaz pro instalaci hlavního cílového serveru a registrace serveru u konfiguračního serveru.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Příklad: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Počkejte na dokončení skriptu. Pokud na hlavním cíli úspěšně zaregistruje, na hlavním cíli je uvedený na **infrastruktura Site Recovery** stránky portálu.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Instalace na hlavním cíli pomocí interaktivní instalace

1. Spusťte následující příkaz k instalaci na hlavním cíli. Pro roli agenta vyberte **hlavní cíl**.

    ```
    ./install
    ```

2. Zvolte výchozí umístění pro instalaci a pak vyberte **Enter** pokračujte.

    ![Výběrem výchozí umístění pro instalaci hlavního cíle](./media/vmware-azure-install-linux-master-target/image17.png)

Po dokončení instalace zaregistrujte konfigurační server pomocí příkazového řádku.

1. Poznamenejte si IP adresu konfiguračního serveru. Budete ho potřebovat v dalším kroku.

2. Spusťte následující příkaz pro instalaci hlavního cílového serveru a registrace serveru u konfiguračního serveru.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Příklad: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Počkejte na dokončení skriptu. Pokud na hlavním cíli je úspěšně zaregistrovaný, na hlavním cíli je uvedený na **infrastruktura Site Recovery** stránky portálu.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Nainstalujte nástroje VMware tools / otevřít nástroje virtuální počítače na hlavním cílovém serveru

Je potřeba nainstalovat nástroje VMware nebo nástroje open virtuální počítače na hlavním cílovém, aby ji mohla zjistit úložišť dat. Pokud nejsou nainstalovány nástroje, není v úložištích dat, uvedené obrazovce operace opětovného zapnutí ochrany. Po instalaci nástrojů VMware budete muset restartovat.

### <a name="upgrade-the-master-target-server"></a>Upgradujte hlavní cílový server

Spusťte instalační program. Automaticky zjišťuje, zda je agent nainstalovaný na hlavním cílovém. Chcete-li provést upgrade, vyberte **Y**.  Po dokončení instalace zkontrolujte verzi na hlavním cíli nainstalovali pomocí následujícího příkazu:

`cat /usr/local/.vx_version`


Uvidíte, že **verze** pole obsahuje číslo verze na hlavním cíli.

## <a name="common-issues"></a>Běžné problémy

* Zajistěte, aby že nezapínejte úložiště vMotion na jakékoli komponentami pro správu jako je hlavní cíl. Pokud se hlavní cíl přesune po úspěšné opětovné zapnutí ochrany, nelze odpojit disky virtuálního počítače (Vmdk). V takovém případě navrácení služeb po obnovení selže.

* Na hlavním cíli by neměl mít všechny snímky virtuálního počítače. Pokud existují snímky, navrácení služeb po obnovení se nezdaří.

* Kvůli některé vlastní konfigurace síťové karty síťového rozhraní je zakázán během spouštění a hlavního cílového agenta nelze inicializovat. Ujistěte se, že jsou správně nastaveny následující vlastnosti. Zkontrolujte tyto vlastnosti v Ethernet karty souboru /etc/sysconfig/network-scripts/ifcfg-eth *.
    * BOOTPROTO=dhcp
    * ONBOOT = Ano


## <a name="next-steps"></a>Další postup
Po dokončení instalace a registrace hlavního cíle, zobrazí se hlavní cíl se zobrazí na **hlavní cílový** tématu **infrastruktura Site Recovery**, v části Konfigurace Přehled serveru.

Teď můžete přejít pomocí [opětovného nastavování ochrany](vmware-azure-reprotect.md)následovaný navrácení služeb po obnovení.

