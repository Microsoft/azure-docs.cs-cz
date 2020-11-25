---
title: Nastavení Oracle ASM na virtuálním počítači Azure Linux | Microsoft Docs
description: Rychlé zprovoznění Oracle ASM v prostředí Azure.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: e5593d530891e39404e0b9760861f2f22ae333d3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999990"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Nastavení Oracle ASM na virtuálním počítači Azure Linux  

Virtuální počítače Azure poskytují plně konfigurovatelné a flexibilní výpočetní prostředí. Tento kurz se věnuje základnímu nasazení virtuálních počítačů Azure v kombinaci s instalací a konfigurací Oracle Automated Storage Management (ASM).  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Oracle Database a připojení k němu
> * Instalace a konfigurace automatizované správy úložiště Oracle
> * Instalace a konfigurace infrastruktury pro mřížku Oracle
> * Inicializace instalace Oracle ASM
> * Vytvoření Oracle DB spravovaného ASM


Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Příprava prostředí

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupinu prostředků vytvoříte pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. V tomto příkladu se jedná o skupinu prostředků s názvem *myResourceGroup* v oblasti *eastus* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Pokud chcete vytvořit virtuální počítač na základě bitové kopie Oracle Database a nakonfigurovat ho tak, aby používal Oracle ASM, použijte příkaz [AZ VM Create](/cli/azure/vm) . 

Následující příklad vytvoří virtuální počítač s názvem myVM, který má velikost Standard_DS2_v2 se čtyřmi připojenými datovými disky o velikosti 50 GB. Pokud ve výchozím umístění klíče ještě neexistují, vytvoří se také klíče SSH.  Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Po vytvoření virtuálního počítače se v Azure CLI zobrazí podobné informace jako v následujícím příkladu. Všimněte si hodnoty pro `publicIpAddress` . Tuto adresu použijete pro přístup k virtuálnímu počítači.

   ```output
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Pokud chcete vytvořit relaci SSH s virtuálním počítačem a nakonfigurovat další nastavení, použijte následující příkaz. Nahraďte IP adresu `publicIpAddress` hodnotou svého virtuálního počítače.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Nainstalovat Oracle ASM

Pro instalaci Oracle ASM proveďte následující kroky. 

Další informace o instalaci Oracle ASM najdete v tématu [Oracle ASMLib Ke_stažení pro Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Aby bylo možné pokračovat v instalaci ASM, musíte se přihlásit jako kořenový adresář:

   ```bash
   sudo su -
   ```
   
2. Spuštěním těchto dalších příkazů nainstalujete součásti Oracle ASM:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Ověřte, zda je nainstalováno Oracle ASM:

   ```bash
   rpm -qa |grep oracleasm
   ```

    Výstup tohoto příkazu by měl zobrazovat následující součásti:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. Aby bylo možné správně fungovat, ASM vyžaduje konkrétní uživatele a role. Následující příkazy vytvoří požadované uživatelské účty a skupiny: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Ověřte, jestli se uživatelé a skupiny správně vytvořily:

   ```bash
   id grid
   ```

    Výstup tohoto příkazu by měl zobrazit následující uživatele a skupiny:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Vytvořte složku pro uživatelskou *mřížku* a změňte její vlastníka:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Nastavení Oracle ASM

Pro tento kurz je výchozím uživatelem *Mřížka* a výchozí skupina je *asmadmin*. Zajistěte, aby byl uživatel *Oracle* součástí skupiny asmadmin. K nastavení instalace Oracle ASM proveďte následující kroky:

1. Nastavení ovladače knihovny Oracle ASM zahrnuje definování výchozího uživatele (mřížky) a výchozí skupiny (asmadmin) a také konfiguraci jednotky, která se má spustit při spuštění (zvolit y) a vyhledat disky při spuštění (zvolit y). Musíte zodpovědět výzvy z následujícího příkazu:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Výstup tohoto příkazu by měl vypadat podobně jako v následujícím příkladu, zastavení s výzvami k zodpovězení.

    ```output
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Zobrazení konfigurace disku:

   ```bash
   cat /proc/partitions
   ```

   Výstup tohoto příkazu by měl vypadat podobně jako následující výpis dostupných disků.

   ```output
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Naformátujte disk */dev/sdc* spuštěním následujícího příkazu a odpovědí na tyto výzvy:
   - *n* pro nový oddíl
   - *p* pro primární oddíl
   - *1* pro výběr prvního oddílu
   - stisknout `enter` výchozí první válec
   - stisknout `enter` pro výchozí poslední válec
   - Stisknutím klávesy *w* napíšete změny v tabulce oddílů.  

   ```bash
   fdisk /dev/sdc
   ```
   
   Pomocí odpovědí uvedených výše `fdisk` by měl výstup příkazu vypadat takto:

   ```output
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Opakujte předchozí `fdisk` příkaz pro `/dev/sdd` , `/dev/sde` a `/dev/sdf` .

5. Ověřte konfiguraci disku:

   ```bash
   cat /proc/partitions
   ```

   Výstup příkazu by měl vypadat takto:

   ```output
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Ověřte stav služby Oracle ASM a spusťte službu Oracle ASM:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   Výstup příkazu by měl vypadat takto:

   ```output
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Vytvořit disky Oracle ASM:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```

   Výstup příkazu by měl vypadat takto:

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Vypsat disky Oracle ASM:

   ```bash
   service oracleasm listdisks
   ```

   Výstup příkazu by měl vypsat následující disky Oracle ASM:

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Změňte hesla pro uživatele root, Oracle a Grid. **Tato nová hesla si poznamenejte** při jejich používání později během instalace.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Změňte oprávnění složky:

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Stažení a Příprava infrastruktury pro mřížku Oracle

Pokud chcete stáhnout a připravit software infrastruktury pro mřížku Oracle, proveďte následující kroky:

1. Stáhněte si infrastrukturu pro mřížku Oracle ze [stránky pro stažení pro Oracle ASM](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   Pod položkou stáhnout s názvem **Oracle Database 12c (12.1.0.2.0) pro Linux X86-64** Stáhněte dva soubory. zip.

2. Po stažení souborů zip do klientského počítače můžete k kopírování souborů na virtuální počítač použít protokol SCP (Secure Copy Protocol):

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. Přes SSH zpátky do virtuálního počítače Oracle v Azure, aby se soubory. zip přesunuly do složky/opt Pak změňte vlastníka souborů:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Soubory rozbalte. (Nainstalujte nástroj pro rozbalení pro Linux, pokud ještě není nainstalovaný.)

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Změnit oprávnění:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Aktualizuje nakonfigurované místo odkládacího souboru. Komponenty mřížky Oracle potřebují pro instalaci mřížky aspoň 6,8 GB prostoru pro odkládací umístění. Výchozí velikost stránkovacího souboru pro Image Oracle Linux v Azure je jenom 2 048 MB. Aby se aktualizované nastavení projevilo, musíte `ResourceDisk.SwapSizeMB` `/etc/waagent.conf` soubor zvětšit a restartovat službu WALinuxAgent. Vzhledem k tomu, že se jedná o soubor jen pro čtení, je nutné změnit oprávnění k souboru, aby bylo možné povolit přístup pro zápis.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Vyhledejte `ResourceDisk.SwapSizeMB` a změňte hodnotu na **8192**. Budete muset stisknout, pokud `insert` chcete zadat režim vložení, zadat hodnotu **8192** a potom se stisknutím klávesy `esc` vraťte do režimu příkazu. Chcete-li zapsat změny a soubor ukončit, zadejte `:wq` a stiskněte klávesu `enter` .
   
   > [!NOTE]
   > Důrazně doporučujeme, abyste vždy použili `WALinuxAgent` ke konfiguraci odkládacího prostoru tak, aby se vždy vytvořil na místním dočasném disku (dočasný disk), aby se co nejlépe využíval. Další informace o najdete v tématu [Postup přidání odkládacího souboru ve virtuálních počítačích se systémem Linux Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Příprava místního klienta a virtuálního počítače na spuštění X11
Konfigurace Oracle ASM vyžaduje grafické rozhraní k dokončení instalace a konfigurace. K usnadnění této instalace používáme protokol X11. Pokud používáte klientský systém (Mac nebo Linux), který už má povolené a nakonfigurované možnosti X11, můžete tuto konfiguraci a nastavení přeskočit výhradně pro počítače se systémem Windows. 

1. [Stáhněte](https://www.putty.org/) si a [Stáhněte si Xming](https://xming.en.softonic.com/) do počítače s Windows. Než budete pokračovat, musíte dokončit instalaci obou těchto aplikací s výchozími hodnotami.

2. Po instalaci výstupu otevřete příkazový řádek, přejděte do složky pro výstupy (například C:\Program Files\PuTTY) a spusťte příkaz, aby `puttygen.exe` se vygeneroval klíč.

3. Generátor klíčů ve výstupu:
   
   1. Vygenerujte klíč výběrem `Generate` tlačítka.
   2. Zkopírujte obsah klíče (CTRL + C).
   3. Vyberte tlačítko `Save private key`.
   4. Ignorujte upozornění týkající se zabezpečení klíče pomocí přístupového hesla a pak vyberte `OK` .

   ![Snímek generátoru klíčů pro výstupu](./media/oracle-asm/puttykeygen.png)

4. Na svém VIRTUÁLNÍm počítači spusťte tyto příkazy:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Vytvořte soubor s názvem `authorized_keys`. Vložte obsah klíče do tohoto souboru a pak soubor uložte.

   > [!NOTE]
   > Klíč musí obsahovat řetězec `ssh-rsa` . Obsah klíče musí být také jeden řádek textu.
   >  

6. V klientském systému zahajte výstup do výstupu. V podokně **kategorie** přejít na **připojení**  >  **SSH**  >  **ověřování**. V poli **soubor privátního klíče pro ověřování** přejděte na klíč, který jste dříve vygenerovali.

   ![Snímek obrazovky s možnostmi ověřování SSH](./media/oracle-asm/setprivatekey.png)

7. V podokně **kategorie** přejít na **připojení**  >  **SSH**  >  **X11**. Zaškrtněte políčko **Povolit předávání X11** .

   ![Snímek obrazovky s možnostmi předávání X11 SSH](./media/oracle-asm/enablex11.png)

8. V podokně **kategorie** přejít na **relace**. Do dialogového okna název hostitele zadejte svůj virtuální počítač Oracle ASM `<publicIPaddress>` , zadejte nový `Saved Session` název a klikněte na zapnout `Save` .  Po uložení klikněte na zapnout `open` a připojte se k virtuálnímu počítači Oracle ASM.  Při prvním připojení budete upozorněni, že vzdálený systém není uložen do mezipaměti v registru. Klikněte na tlačítko `yes` a přidejte ho a pokračujte.

   ![Snímek možností pro relaci výstupu](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalace infrastruktury pro mřížku Oracle

Pro instalaci infrastruktury pro mřížku Oracle proveďte následující kroky:

1. Přihlaste se jako **Mřížka**. (Měli byste být schopni se přihlásit, aniž by se vám zobrazila výzva k zadání hesla.) 

   > [!NOTE]
   > Pokud používáte systém Windows, před zahájením instalace se ujistěte, že jste spustili Xming.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Spustí se instalační program pro infrastrukturu Oracle Grid 12c verze 1. (Spuštění instalačního programu může trvat několik minut.)

2. Na stránce **vybrat možnost instalace vyberte možnost** **instalovat a konfigurovat infrastrukturu mřížky Oracle pro samostatný server**.

   ![Snímek obrazovky se stránkou možností výběru instalace instalačního programu](./media/oracle-asm/install01.png)

3. Na stránce **Vybrat jazyky produktu** ověřte, zda je zvolena **Angličtina** nebo jazyk, který chcete vybrat.  Klikněte na `next` (Další).

4. Na stránce **vytvořit skupinu disků ASM** :
   - Zadejte název skupiny disků.
   - V části **redundance** vyberte **externí**.
   - V části **velikost alokační jednotky** vyberte **4**.
   - V části **Přidat disky** vyberte **ORCLASMSP**.
   - Klikněte na `next` (Další).

5. Na stránce **zadat heslo ASM** zaškrtněte možnost **použít stejná hesla pro tyto účty** a zadejte heslo.

   ![Snímek obrazovky se stránkou pro zadání hesla ASM instalační služby](./media/oracle-asm/install04.png)

6. Na stránce **zadat možnosti správy** máte možnost konfigurovat em Cloud Control. Tuto možnost přeskočíme – kliknutím budete `next` pokračovat. 

7. Na stránce **privilegované skupiny operačních systémů** použijte výchozí nastavení. Kliknutím `next` pokračujte.

8. Na stránce **zadat umístění instalace** použijte výchozí nastavení. Kliknutím `next` pokračujte.

9. Na stránce **vytvořit inventář** změňte adresář inventáře na `/u01/app/grid/oraInventory` . Kliknutím `next` pokračujte.

   ![Snímek obrazovky se stránkou pro vytvoření inventáře instalační služby](./media/oracle-asm/install08.png)

10. Na stránce **Konfigurace spuštění kořenového skriptu** zaškrtněte políčko **automaticky spouštět konfigurační skripty** . Pak vyberte možnost **použít "kořen" přihlašovací údaje uživatele** a zadejte heslo uživatele root.

    ![Snímek obrazovky se stránkou konfigurace spuštění kořenového skriptu instalačního programu](./media/oracle-asm/install09.png)

11. Na stránce **provést kontroly požadovaných součástí** selže aktuální nastavení s chybami. Toto chování je očekávané. Vyberte `Fix & Check Again`.

12. V dialogovém okně **opravit skript** klikněte na `OK` .

13. Na stránce **Souhrn** zkontrolujte zvolená nastavení a potom klikněte na tlačítko `Install` .

    ![Snímek obrazovky se souhrnem stránky instalačního programu](./media/oracle-asm/install12.png)

14. Zobrazí se dialogové okno s upozorněním, ve kterém je třeba spustit konfigurační skripty jako privilegovaný uživatel. Kliknutím `Yes` pokračujte.

15. Na stránce **Dokončit** klikněte na `Close` Dokončit instalaci.

## <a name="set-up-your-oracle-asm-installation"></a>Nastavení instalace Oracle ASM

K nastavení instalace Oracle ASM proveďte následující kroky:

1. Ujistěte se, že jste stále přihlášeni jako **Mřížka** z relace X11. Možná budete muset být `enter` připotřebuji obnoviti k terminálu. Pak spusťte pomocníka s konfigurací Správce automatizovaného úložiště Oracle:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Otevře se Pomocník s konfigurací Oracle ASM.

2. V dialogovém okně **Konfigurovat ASM: skupiny disků** klikněte na `Create` tlačítko a pak klikněte na `Show Advanced Options` .

3. V dialogovém okně **vytvořit skupinu disků** :

   - Zadejte název skupiny disků **data**.
   - V části **Vybrat členské disky** vyberte **ORCL_DATA** a **ORCL_DATA1**.
   - V části **velikost alokační jednotky** vyberte **4**.
   - Kliknutím `ok` vytvoříte skupinu disků.
   - Kliknutím `ok` zavřete okno potvrzení.

   ![Snímek obrazovky dialogového okna vytvořit skupinu disků](./media/oracle-asm/asm02.png)

4. V dialogovém okně **Konfigurovat ASM: skupiny disků** klikněte na `Create` tlačítko a pak klikněte na `Show Advanced Options` .

5. V dialogovém okně **vytvořit skupinu disků** :

   - Zadejte **název skupiny** disků.
   - V části **redundance** vyberte **externí (žádné)**.
   - V části **Vybrat členské disky** vyberte **ORCL_FRA**.
   - V části **velikost alokační jednotky** vyberte **4**.
   - Kliknutím `ok` vytvoříte skupinu disků.
   - Kliknutím `ok` zavřete okno potvrzení.

   ![Snímek obrazovky dialogového okna vytvořit skupinu disků a zvýrazní možnost externí (žádné).](./media/oracle-asm/asm04.png)

6. Vyberte možnost **ukončit** a ukončete pomocníka s konfigurací ASM.

   ![Snímek obrazovky dialogového okna Konfigurovat ASM: skupiny disků s tlačítkem ukončit](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Vytvoření databáze

Software Oracle Database je již nainstalován na Azure Marketplace imagi. Chcete-li vytvořit databázi, proveďte následující kroky:

1. Přepněte uživatele na uživatele Oracle a pak inicializujte naslouchací proces pro protokolování:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Otevře se Pomocník s konfigurací databáze.

2. Na stránce **databázová operace** klikněte na `Create Database` .

3. Na stránce **režim vytváření** :

   - Zadejte název databáze.
   - V případě **typu úložiště** zkontrolujte, zda je vybrána možnost **Automatická správa úložiště (ASM)** .
   - Pro **umístění souborů databáze** použijte výchozí navrhované umístění ASM.
   - Pro **oblast rychlé obnovení** použijte výchozí navrhované umístění ASM.
   - Zadejte **heslo pro správu** a **potvrďte heslo**.
   - Ujistěte `create as container database` se, že je vybraná možnost.
   - Zadejte `pluggable database name` hodnotu.

4. Na stránce **Souhrn** zkontrolujte zvolená nastavení a potom kliknutím na `Finish` vytvořit databázi.

   ![Snímek obrazovky se stránkou Shrnutí](./media/oracle-asm/createdb03.png)

5. Databáze byla vytvořena. Na stránce **Dokončit** máte možnost odemknout další účty pro použití této databáze a měnit hesla. Pokud to chcete udělat, vyberte **Správa hesel** – jinak klikněte na `close` .

## <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Úspěšně jste nakonfigurovali správu automatizovaného úložiště Oracle na Oracle DB imagi z Azure Marketplace.  Pokud už tento virtuální počítač nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít následující příkaz:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

[Kurz: Konfigurace Oracle dataguardu](configure-oracle-dataguard.md)

[Kurz: Konfigurace Oracle GoldenGate](Configure-oracle-golden-gate.md)

Kontrola [Oracle DB v architektu](oracle-design.md)
