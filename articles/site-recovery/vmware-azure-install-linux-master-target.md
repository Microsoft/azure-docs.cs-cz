---
title: Instalace hlavního cílového serveru pro navrácení služeb po obnovení virtuálního počítače se systémem Linux pomocí Azure Site Recovery
description: Přečtěte si, jak nastavit hlavní cílový server Linux pro navrácení služeb po obnovení do místní lokality během zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: mayg
ms.openlocfilehash: 9e1008f7acbfe0685b7a171176c7dc54592d1491
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019238"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Instalace hlavního cílového serveru s Linuxem pro účely navrácení služeb po obnovení
Po převzetí služeb při selhání virtuálních počítačů do Azure můžete navrátit služby virtuálních počítačů na místní lokalitu. Pro navrácení služeb po obnovení musíte virtuální počítač znovu ochránit z Azure do místní lokality. Pro tento proces budete potřebovat místní hlavní cílový server pro příjem provozu. 

Pokud je váš chráněný virtuální počítač virtuálním počítačem s Windows, budete potřebovat hlavní cíl Windows. Pro virtuální počítač se systémem Linux budete potřebovat hlavní cíl pro Linux. Přečtěte si následující postup, kde se dozvíte, jak vytvořit a nainstalovat hlavní cíl pro Linux.

> [!IMPORTANT]
> Od verze hlavního cílového serveru 9.10.0 se dá nejnovější hlavní cílový server nainstalovat jenom na server Ubuntu 16,04. Na serverech CentOS 6.6 nejsou povoleny nové instalace. Můžete ale dál upgradovat staré hlavní cílové servery pomocí verze 9.10.0.
> Hlavní cílový server na LVM se nepodporuje.

## <a name="overview"></a>Přehled
Tento článek poskytuje pokyny k instalaci hlavního cíle systému Linux.

Komentáře nebo dotazy můžete vystavit na konci tohoto článku nebo na [stránce s dotazy k Microsoft Q&pro Azure Recovery Services](/answers/topics/azure-site-recovery.html).

## <a name="prerequisites"></a>Požadavky

* Chcete-li vybrat hostitele, do kterého chcete nasadit hlavní cíl, určete, zda bude navrácení služeb po obnovení na existující místní virtuální počítač nebo na nový virtuální počítač. 
    * Pro existující virtuální počítač by měl mít hostitel hlavního cíle přístup k úložištím dat virtuálního počítače.
    * Pokud místní virtuální počítač neexistuje (v případě obnovení do alternativního umístění), vytvoří se virtuální počítač navrácení služeb po obnovení na stejném hostiteli jako hlavní cíl. Pro instalaci hlavního cíle můžete zvolit libovolného hostitele ESXi.
* Hlavní cíl by měl být v síti, která může komunikovat s procesovým serverem a konfiguračním serverem.
* Verze hlavního cíle musí být stejná nebo nižší než verze procesového serveru a konfiguračního serveru. Pokud je například verze konfiguračního serveru 9,4, verze hlavního cíle může být 9,4 nebo 9,3, ale ne 9,5.
* Hlavním cílem může být pouze virtuální počítač VMware, nikoli fyzický server.

> [!NOTE]
> Ujistěte se, že jste vMotion úložiště nepnuli na žádné součásti pro správu, jako je například hlavní cíl. Pokud se hlavní cíl přesune po úspěšném opětovném zapnutí ochrany, disky virtuálních počítačů (VMDK) se nedají odpojit. V tomto případě se navrácení služeb po obnovení nezdařilo.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Pokyny pro změnu velikosti pro vytvoření hlavního cílového serveru

Vytvořte hlavní cíl podle následujících pokynů pro změnu velikosti:
- **RAM**: 6 GB nebo více
- **Velikost disku operačního systému**: 100 GB nebo více (pro instalaci operačního systému)
- **Další velikost disku pro jednotku pro uchovávání dat**: 1 TB
- **Jádra procesoru**: 4 jádra nebo víc
- **Jádro**: 4,16. *

## <a name="deploy-the-master-target-server"></a>Nasazení hlavního cílového serveru

### <a name="install-ubuntu-16042-minimal"></a>Minimální instalace Ubuntu 16.04.2

Proveďte následující kroky pro instalaci operačního systému Ubuntu 16.04.2 64.

1.   Přejít na [odkaz ke stažení](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), zvolit nejbližší zrcadlo a stáhnout Ubuntu 16.04.2 s minimální 64 bitovou kopií ISO.
V jednotce DVD ponechte Ubuntu 16.04.2 s minimální 64 bitovou kopií ISO a spusťte systém.

1.  Jako preferovaný jazyk vyberte **angličtinu** a pak vyberte **ENTER**.
    
    ![Vyberte jazyk](./media/vmware-azure-install-linux-master-target/image1.png)
1. Vyberte **instalovat server Ubuntu** a pak vyberte **ENTER**.

    ![Výběr instalace serveru Ubuntu](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Jako preferovaný jazyk vyberte **angličtinu** a pak vyberte **ENTER**.

    ![Vyberte angličtinu jako preferovaný jazyk.](./media/vmware-azure-install-linux-master-target/image3.png)

1. V seznamu možnosti **časového pásma** vyberte vhodnou možnost a pak vyberte **zadat**.

    ![Vyberte správné časové pásmo.](./media/vmware-azure-install-linux-master-target/image4.png)

1. Vyberte **ne** (výchozí možnost) a pak vyberte **ENTER**.

     ![Konfigurace klávesnice](./media/vmware-azure-install-linux-master-target/image5.png)
1. Vyberte **angličtinu (USA)** jako zemi nebo oblast původu pro klávesnici a pak vyberte **ENTER**.

1. Jako rozložení klávesnice vyberte **angličtinu (US)** a pak vyberte **ENTER**(spustit).

1. Do pole **název hostitele** zadejte název hostitele serveru a pak vyberte **pokračovat**.

1. Chcete-li vytvořit uživatelský účet, zadejte uživatelské jméno a pak vyberte **pokračovat**.

      ![Vytvoření uživatelského účtu](./media/vmware-azure-install-linux-master-target/image9.png)

1. Zadejte heslo pro nový uživatelský účet a pak vyberte **pokračovat**.

1.  Potvrďte heslo pro nového uživatele a pak vyberte **pokračovat**.

    ![Potvrďte hesla](./media/vmware-azure-install-linux-master-target/image11.png)

1.  V dalším výběru pro šifrování domovského adresáře vyberte možnost **ne** (výchozí možnost) a pak vyberte **ENTER**.

1. Pokud je zobrazené časové pásmo správné, vyberte **Ano** (výchozí možnost) a pak vyberte **ENTER**. Pokud chcete změnit konfiguraci časového pásma, vyberte **ne**.

1. Z možností použití oddílů vyberte **Průvodce – použít celý disk** a pak vyberte **ENTER**.

     ![Vyberte možnost metody dělení.](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Vyberte příslušný disk z možností **vybrat disk a rozdělit na oddíly** a pak vyberte **ENTER**.

    ![Vybrat disk](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Vyberte **Ano** , pokud chcete zapsat změny na disk a pak vyberte **ENTER**.

    ![Vyberte výchozí možnost.](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  V okně Konfigurovat výběr proxy serveru vyberte výchozí možnost, vyberte **pokračovat** a potom vyberte **ENTER**.
     
     ![Snímek obrazovky, který ukazuje, kde vybrat pokračovat a pak vyberte Enter.](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Vyberte možnost **bez možnosti automatických aktualizací** ve výběru pro správu upgradů v systému a pak vyberte **ENTER**.

     ![Vyberte, jak se mají spravovat upgrady.](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Vzhledem k tomu, že Azure Site Recovery hlavní cílový server vyžaduje velmi specifickou verzi Ubuntu, musíte zajistit, aby byly upgrady jádra pro virtuální počítač zakázané. Pokud jsou povolené, pak všechny běžné upgrady způsobí nefunkčnost hlavního cílového serveru. Ujistěte se, že jste vybrali možnost **bez automatických aktualizací** .

1.  Vyberte výchozí možnosti. Pokud chcete openSSH pro SSH Connect, vyberte možnost **serveru openSSH** a pak vyberte **pokračovat**.

    ![Vybrat software](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Ve výběru pro instalaci zaváděcího nástroje GRUB spusťte výběr možnosti **Ano** a pak vyberte **ENTER**.
     
    ![Instalační program GRUB Boot](./media/vmware-azure-install-linux-master-target/image20.png)


1. Vyberte odpovídající zařízení pro instalaci spouštěcího zavaděče (nejlépe **/dev/sda**) a pak vyberte **ENTER**.
     
    ![Vyberte vhodné zařízení.](./media/vmware-azure-install-linux-master-target/image21.png)

1. Vyberte **pokračovat** a potom kliknutím na tlačítko **ENTER** dokončete instalaci.

    ![Dokončete instalaci.](./media/vmware-azure-install-linux-master-target/image22.png)

1. Po dokončení instalace se přihlaste k virtuálnímu počítači pomocí nových přihlašovacích údajů uživatele. (Další informace najdete v **kroku 10** .)

1. K nastavení hesla uživatele ROOT použijte postup, který je popsaný na následujícím snímku obrazovky. Pak se přihlaste jako uživatel ROOT.

    ![Nastavení hesla uživatele ROOT](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Konfigurace počítače jako hlavního cílového serveru

Chcete-li získat ID pro každý pevný disk SCSI ve virtuálním počítači se systémem Linux, **disk.** Je nutné povolit parametr EnableUUID = true. Chcete-li povolit tento parametr, proveďte následující kroky:

1. Vypněte virtuální počítač.

2. V levém podokně klikněte pravým tlačítkem myši na položku pro virtuální počítač a pak vyberte **Upravit nastavení**.

3. Vyberte kartu **Možnosti** .

4. V levém podokně vyberte možnost **Upřesnit**  >  **Obecné** a potom v pravé dolní části obrazovky vyberte tlačítko **parametry konfigurace** .

    ![Otevřít konfigurační parametr](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    Možnost **parametry konfigurace** není k dispozici, když je počítač spuštěný. Chcete-li nastavit tuto kartu jako aktivní, vypněte virtuální počítač.

5. Podívejte se, jestli řádek s **diskem není. EnableUUID** už existuje.

   - Pokud hodnota existuje a je nastavená na **false**, změňte hodnotu na **true**. (Hodnoty nerozlišují velká a malá písmena.)

   - Pokud hodnota existuje a je nastavená na **true**, vyberte **Zrušit**.

   - Pokud hodnota neexistuje, vyberte **Přidat řádek**.

   - Do sloupce název přidejte **disk. EnableUUID** a nastavte hodnotu na **true**.

     ![Probíhá kontrola, zda disk. EnableUUID už existuje.](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Zakázat upgrady jádra

Azure Site Recovery hlavní cílový server vyžaduje specifickou verzi Ubuntu, ujistěte se, že jsou pro virtuální počítač zakázané upgrady jádra. Pokud jsou upgrady jádra povoleny, může to způsobit nefunkčnost hlavního cílového serveru.

#### <a name="download-and-install-additional-packages"></a>Stažení a instalace dalších balíčků

> [!NOTE]
> Ujistěte se, že máte připojení k Internetu ke stažení a instalaci dalších balíčků. Pokud nemáte připojení k Internetu, musíte tyto balíčky deb vyhledat ručně a nainstalovat je.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Získat instalační program pro instalaci

Pokud má váš hlavní cíl připojení k Internetu, můžete instalační program stáhnout pomocí následujících kroků. V opačném případě můžete instalační program zkopírovat z procesového serveru a pak ho nainstalovat.

#### <a name="download-the-master-target-installation-packages"></a>Stažení instalačních balíčků hlavního cíle

[Stáhněte si nejnovější hlavní cílové instalační služby systému Linux](https://aka.ms/latestlinuxmobsvc).

Pokud ho chcete stáhnout pomocí systému Linux, zadejte:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Nezapomeňte stáhnout a rozbalit instalační program v domovském adresáři. Pokud jste **/usr/local**, instalace se nezdařila.


#### <a name="access-the-installer-from-the-process-server"></a>Přístup k instalačnímu programu z procesového serveru

1. Na procesovém serveru přejdete do **složky C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Z procesového serveru Zkopírujte požadovaný instalační soubor a uložte ho jako **latestlinuxmobsvc. tar. gz** do domovského adresáře.


### <a name="apply-custom-configuration-changes"></a>Použít vlastní změny konfigurace

Chcete-li použít vlastní změny konfigurace, použijte následující postup jako uživatel ROOT:

1. Spusťte následující příkaz, který untar binární soubor.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![Snímek obrazovky příkazu, který se má spustit](./media/vmware-azure-install-linux-master-target/image16.png)

2. K udělení oprávnění spusťte následující příkaz.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Spuštěním následujícího příkazu spusťte skript.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Spusťte skript jenom jednou na serveru. Pak server vypněte. Po přidání disku restartujte server, jak je popsáno v následující části.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Přidat disk pro uchovávání informací do cílového virtuálního počítače se systémem Linux

K vytvoření disku pro uchovávání informací použijte následující postup:

1. Připojte k virtuálnímu počítači s hlavním serverem Linux nový disk o 1 TB a spusťte počítač.

2. Pomocí příkazu **s** více cestami se dozvíte víc s ID disku pro uchovávání **informací:** více cest

    ![ID více cest](./media/vmware-azure-install-linux-master-target/image27.png)

3. Naformátujte jednotku a pak na nové jednotce vytvořte systém souborů: **mkfs. ext4/dev/Mapper/ \<Retention disk's multipath id>**.
    
    ![Systém souborů](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Po vytvoření systému souborů připojte disk pro uchovávání.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Vytvořte položku **fstab** pro připojení jednotky pro uchovávání při každém spuštění systému.
    
    `vi /etc/fstab`
    
    Vyberte **Vložit** a začněte upravovat soubor. Vytvořte nový řádek a vložte následující text. Upravte na základě zvýrazněného IDENTIFIKÁTORu Multipath z předchozího příkazu více než na disku.

    **/dev/Mapper/ \<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Vyberte **ESC** a potom zadejte **: WQ** (Write and quit), čímž zavřete okno editoru.

### <a name="install-the-master-target"></a>Instalace hlavního cíle

> [!IMPORTANT]
> Verze hlavního cílového serveru musí být stejná nebo nižší než verze procesového serveru a konfiguračního serveru. Pokud tato podmínka není splněná, bude znovu chráněna, ale replikace selže.


> [!NOTE]
> Než nainstalujete hlavní cílový server, ověřte, že soubor **/etc/hosts** ve virtuálním počítači obsahuje položky, které mapují místní název hostitele na IP adresy, které jsou přidružené ke všem síťovým adaptérům.

1. Spusťte následující příkaz, který nainstaluje hlavní cíl.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    ```

2. Zkopírujte heslo z **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** na konfiguračním serveru. Pak ho uložte jako **passphrase.txt** do stejného místního adresáře spuštěním následujícího příkazu:

    `echo <passphrase> >passphrase.txt`

    Příklad: 

    `echo itUx70I47uxDuUVY >passphrase.txt`
    

3. Poznamenejte si IP adresu konfiguračního serveru. Spuštěním následujícího příkazu zaregistrujte Server s konfiguračním serverem.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Příklad: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Počkejte, než se skript dokončí. Pokud se hlavní cíl úspěšně registruje, hlavní cíl se zobrazí na stránce **infrastruktura Site Recovery** na portálu.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Instalace hlavního cíle pomocí interaktivní instalace

1. Spusťte následující příkaz, který nainstaluje hlavní cíl. U možnosti role agenta vyberte **hlavní cíl**.

    ```
    ./install
    ```

2. Zvolte výchozí umístění pro instalaci a pak pokračujte výběrem **ENTER** .

    ![Výběr výchozího umístění pro instalaci hlavního cíle](./media/vmware-azure-install-linux-master-target/image17.png)

Po dokončení instalace Zaregistrujte konfigurační server pomocí příkazového řádku.

1. Poznamenejte si IP adresu konfiguračního serveru. Budete ho potřebovat v dalším kroku.

2. Spuštěním následujícího příkazu zaregistrujte Server s konfiguračním serverem.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh
    ```

     Počkejte, než se skript dokončí. Pokud je hlavní cíl úspěšně zaregistrován, je hlavní cíl uveden na stránce **infrastruktura Site Recovery** na portálu.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Instalace nástrojů VMware/otevřít-VM-Tools na hlavním cílovém serveru

Je potřeba nainstalovat nástroje VMware nebo otevřít-VM-Tools na hlavním cíli, aby mohl vyhledat úložiště dat. Pokud nástroje nejsou nainstalovány, není obrazovka pro opětovné zapnutí ochrany uvedena v úložištích dat. Po instalaci nástrojů VMware je nutné restartovat počítač.

### <a name="upgrade-the-master-target-server"></a>Upgrade hlavního cílového serveru

Spusťte instalační program. Automaticky zjistí, že je agent nainstalovaný na hlavním cíli. Pokud chcete upgradovat, vyberte **Y**.  Po dokončení instalace ověřte, že je nainstalovaná verze hlavního cíle, a to pomocí následujícího příkazu:

`cat /usr/local/.vx_version`


Uvidíte, že pole **verze** obsahuje číslo verze hlavního cíle.

## <a name="common-issues"></a>Běžné problémy

* Ujistěte se, že jste vMotion úložiště nepnuli na žádné součásti pro správu, jako je například hlavní cíl. Pokud se hlavní cíl přesune po úspěšném opětovném zapnutí ochrany, disky virtuálních počítačů (VMDK) se nedají odpojit. V tomto případě se navrácení služeb po obnovení nezdařilo.

* Hlavní cíl by neměl obsahovat žádné snímky na virtuálním počítači. Pokud existují snímky, navrácení služeb po obnovení selhalo.

* Vzhledem k některým vlastním konfiguracím síťových adaptérů je síťové rozhraní při spuštění zakázané a hlavní cílový Agent se nedá inicializovat. Ujistěte se, že jsou správně nastavené následující vlastnosti. Tyto vlastnosti ověřte v/etc/Network/Interfaces. souboru karty Ethernet.
    * Automatické eth0
    * iface eth0 inet DHCP <br>

    Restartujte síťovou službu pomocí následujícího příkazu: <br>

`sudo systemctl restart networking`


## <a name="next-steps"></a>Další kroky
Po dokončení instalace a registrace hlavního cíle se zobrazí hlavní cíl v sekci **hlavní cíl** v **Site Recovery infrastruktury** v části Přehled konfiguračního serveru.

Nyní můžete pokračovat v [reprotection](vmware-azure-reprotect.md)a potom navrácení služeb po obnovení.

