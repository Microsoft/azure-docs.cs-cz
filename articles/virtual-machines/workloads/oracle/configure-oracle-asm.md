---
title: Nastavení řešení Oracle ASM na virtuálním počítači Azure Linux | Dokumenty společnosti Microsoft
description: Rychle zprovozněte řešení Oracle ASM ve vašem prostředí Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: a27d4c1712e9d65afcfc8792eac88be468829f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536373"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Nastavení řešení Oracle ASM na virtuálním počítači Azure Linux  

Virtuální počítače Azure poskytují plně konfigurovatelné a flexibilní výpočetní prostředí. Tento kurz se zabývá základním nasazením virtuálních počítačů Azure v kombinaci s instalací a konfigurací oracle automated storage management (ASM).  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření virtuálního zařízení oracle database a připojení k němu
> * Instalace a konfigurace automatické správy úložišť Oracle
> * Instalace a konfigurace infrastruktury Oracle Grid
> * Inicializovat instalaci oracle ASM
> * Vytvoření databáze Oracle DB spravované společností ASM


Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Příprava prostředí

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupinu prostředků vytvoříte pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. V tomto příkladu skupina prostředků s názvem *myResourceGroup* v oblasti *eastus.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Chcete-li vytvořit virtuální počítač založený na bitové kopii databáze Oracle a nakonfigurovat jej tak, aby používal systém Oracle ASM, použijte příkaz [az vm create.](/cli/azure/vm) 

Následující příklad vytvoří virtuální hod s názvem myVM, který je Standard_DS2_v2 velikost se čtyřmi připojenými datovými disky o velikosti 50 GB. Pokud ještě neexistují ve výchozím umístění klíče, vytvoří také klíče SSH.  Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Po vytvoření virtuálního počítače azure CLI zobrazí informace podobné v následujícím příkladu. Poznamenejte `publicIpAddress`si hodnotu pro . Tuto adresu používáte pro přístup k virtuálnímu virtuálnímu serveru.

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

Chcete-li vytvořit relaci SSH s virtuálním počítačem a nakonfigurovat další nastavení, použijte následující příkaz. Nahraďte IP `publicIpAddress` adresu hodnotou pro váš virtuální počítač.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Instalace řešení Oracle ASM

Chcete-li nainstalovat systém Oracle ASM, proveďte následující kroky. 

Další informace o instalaci systému Oracle ASM naleznete v [tématu Oracle ASMLib Downloads for Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Chcete-li pokračovat v instalaci ASM, musíte se přihlásit jako root:

   ```bash
   sudo su -
   ```
   
2. Spusťte tyto další příkazy pro instalaci součástí Oracle ASM:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Ověřte, zda je nainstalován systém Oracle ASM:

   ```bash
   rpm -qa |grep oracleasm
   ```

    Výstup tohoto příkazu by měl uvádět následující součásti:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM vyžaduje konkrétní uživatele a role, aby správně fungovaly. Následující příkazy vytvářejí požadované uživatelské účty a skupiny: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Ověřte, zda byli uživatelé a skupiny vytvořeny správně:

   ```bash
   id grid
   ```

    Výstup tohoto příkazu by měl uvádět následující uživatele a skupiny:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Vytvořte složku pro *mřížku* uživatele a změňte vlastníka:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Nastavení řešení Oracle ASM

Pro účely tohoto kurzu je výchozím uživatelem *mřížka* a výchozí skupinou je *asmadmin*. Ujistěte se, že uživatel *oracle* je součástí skupiny asmadmin. Chcete-li nastavit instalaci řešení Oracle ASM, proveďte následující kroky:

1. Nastavení ovladače knihovny Oracle ASM zahrnuje definování výchozího uživatele (mřížky) a výchozí skupiny (asmadmin) a konfiguraci jednotky pro spuštění při startu (zvolte y) a pro hledání disků při startu (zvolte y). Na výzvy je třeba odpovědět z následujícího příkazu:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Výstup tohoto příkazu by měl vypadat podobně jako následující, zastavení s výzvami, které mají být zodpovězeny.

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

2. Zobrazit konfiguraci disku:

   ```bash
   cat /proc/partitions
   ```

   Výstup tohoto příkazu by měl vypadat podobně jako následující seznam dostupných disků

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

3. Naformátujte disk */dev/sdc* spuštěním následujícího příkazu a přijetím výzev pomocí:
   - *n* pro nový oddíl
   - *p* pro primární oddíl
   - *1* pro výběr prvního oddílu
   - stiskněte `enter` pro výchozí první válec
   - stiskněte `enter` pro výchozí poslední válec
   - Stisknutím *klávesy w* napíšete změny do tabulky oddílů  

   ```bash
   fdisk /dev/sdc
   ```
   
   Pomocí výše uvedených odpovědí by `fdisk` měl výstup příkazu vypadat takto:

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

4. Opakujte předchozí `fdisk` příkaz `/dev/sdd` `/dev/sde`pro `/dev/sdf`, , a .

5. Zkontrolujte konfiguraci disku:

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

6. Zkontrolujte stav služby Oracle ASM a spusťte službu Oracle ASM:

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

7. Vytvořte disky Oracle ASM:

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

8. Seznam disků Oracle ASM:

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

9. Změňte hesla pro uživatele kořenového adresáře, oracle a mřížky. **Poznamenejte si tato nová hesla** při jejich pozdějším použití během instalace.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Změna oprávnění ke složce:

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

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Stažení a příprava infrastruktury Oracle Grid

Chcete-li stáhnout a připravit software Oracle Grid Infrastructure, proveďte následující kroky:

1. Stáhněte si aplikaci Oracle Grid Infrastructure ze [stránky pro stažení služby Oracle ASM](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   V části ke stažení s názvem **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) pro Linux x86-64**stáhněte dva soubory ZIP.

2. Po stažení souborů ZIP do klientského počítače můžete pomocí protokolu SCP (Secure Copy Protocol) zkopírovat soubory do virtuálního počítače:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH zpět do vašeho virtuálního počítače Oracle v Azure za účelem přesunutí souborů ZIP do složky /opt. Potom změňte vlastníka souborů:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Rozbalte soubory. (Nainstalujte nástroj pro rozbalení Linuxu, pokud ještě není nainstalován.)

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Změnit oprávnění:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Aktualizace nakonfigurovaného odkládacího prostoru. Součásti Oracle Grid potřebují k instalaci gridu alespoň 6,8 GB odkládacího prostoru. Výchozí velikost odkládacího souboru pro ibi Oracle Linux v Azure je pouze 2048 MB. Je třeba `ResourceDisk.SwapSizeMB` zvýšit `/etc/waagent.conf` soubor a restartovat službu WALinuxAgent, aby se aktualizovaná nastavení projevila. Vzhledem k tomu, že se jedná o soubor jen pro čtení, je třeba změnit oprávnění k souborům, abyste povolili přístup pro zápis.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Vyhledejte `ResourceDisk.SwapSizeMB` a změňte hodnotu na **8192**. Budete muset stisknout `insert` pro vstup do režimu vložení, zadejte hodnotu **8192** a pak stiskněte `esc` pro návrat do příkazového režimu. Chcete-li změny zapsat a `:wq` soubor `enter`ukončit, zadejte a stiskněte klávesu .
   
   > [!NOTE]
   > Důrazně doporučujeme, abyste `WALinuxAgent` vždy nakonfigurovali odkládací prostor tak, aby byl vždy vytvořen na místním dočasném disku (dočasném disku) pro dosažení nejlepšího výkonu. Další informace najdete v [tématu Jak přidat odkládací soubor ve virtuálních počítačích Linux Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Příprava místního klienta a virtuálního počítače ke spuštění x11
Konfigurace nástroje Oracle ASM vyžaduje grafické rozhraní pro dokončení instalace a konfigurace. K usnadnění instalace používáme protokol x11. Pokud používáte klientský systém (Mac nebo Linux), který již má možnosti X11 povolené a nakonfigurované - můžete přeskočit tuto konfiguraci a nastavení výhradně pro počítače se systémem Windows. 

1. [Stáhněte si PuTTY](https://www.putty.org/) a [stáhněte si Xming](https://xming.en.softonic.com/) do počítače se systémem Windows. Před pokračováním budete muset dokončit instalaci obou těchto aplikací s výchozími hodnotami.

2. Po instalaci PuTTY otevřete příkazový řádek, změňte do složky PuTTY (například C:\Program Files\PuTTY) a spusťte `puttygen.exe` za účelem generování klíče.

3. V PutTY Key Generator:
   
   1. Vygenerujte klíč `Generate` výběrem tlačítka.
   2. Zkopírujte obsah klávesy (Ctrl+C).
   3. Vyberte tlačítko `Save private key`.
   4. Ignorujte upozornění na zabezpečení klíče přístupovým heslem a vyberte . `OK`

   ![Snímek obrazovky s generátorem klíčů PuTTY](./media/oracle-asm/puttykeygen.png)

4. Ve virtuálním počítači spusťte tyto příkazy:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Vytvořte soubor s názvem `authorized_keys`. Vložte obsah klíče do tohoto souboru a uložte soubor.

   > [!NOTE]
   > Klíč musí obsahovat `ssh-rsa`řetězec . Obsah klíče musí být také jeden řádek textu.
   >  

6. Ve vašem klientském systému spusťte PuTTY. V podokně **Kategorie** přejděte na **připojení** > **SSH** > **Auth**. V **souboru soukromého klíče pro ověřování** přejděte na klíč, který jste vygenerovali dříve.

   ![Snímek obrazovky s možnostmi ověřování SSH](./media/oracle-asm/setprivatekey.png)

7. V podokně **Kategorie** přejděte na **připojení** > **SSH** > **X11**. Zaškrtněte políčko **Povolit předávání X11.**

   ![Snímek obrazovky s možnostmi předávání SSH X11](./media/oracle-asm/enablex11.png)

8. V podokně **Kategorie** přejděte na **Session**. Zadejte virtuální počítač `<publicIPaddress>` Oracle ASM do dialogového okna `Saved Session` název hostitele, `Save`vyplňte nový název a klikněte na tlačítko .  Po uložení se `open` kliknutím na svůj virtuální počítač Oracle ASM připojte.  Při prvním připojení budete upozorněni, že vzdálený systém není v registru uložen do mezipaměti. Klikněte `yes` na přidat a pokračovat.

   ![Snímek obrazovky s možnostmi relace PuTTY](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalace infrastruktury Oracle Grid

Chcete-li nainstalovat infrastrukturu Oracle Grid Infrastructure, proveďte následující kroky:

1. Přihlaste se jako **mřížka**. (Měli byste být schopni se přihlásit, aniž byste byli vyzváni k zadání hesla.) 

   > [!NOTE]
   > Pokud používáte systém Windows, ujistěte se, že jste před zahájením instalace spustili službu Xming.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Otevře se instalační program Oracle Grid Infrastructure 12c Release 1. (Spuštění instalačního programu může trvat několik minut.)

2. Na stránce **Vybrat možnost instalace** vyberte možnost Instalace a konfigurace **infrastruktury oracle gridu pro samostatný server**.

   ![Snímek obrazovky se stránkou Možnost volby vybrat instalační program instalačního programu](./media/oracle-asm/install01.png)

3. Na stránce **Vybrat jazyky produktu** zkontrolujte, zda je **vybrána angličtina** nebo požadovaný jazyk.  Klikněte na `next` (Další).

4. Na stránce **Vytvořit skupinu disků ASM:**
   - Zadejte název skupiny disků.
   - V části **Redundancy**vyberte **Externí**.
   - V části **Velikost alokační jednotky**vyberte **4**.
   - V části **Přidat disky**vyberte **možnost ORCLASMSP**.
   - Klikněte na `next` (Další).

5. Na stránce **Zadat heslo ASM** vyberte možnost **Použít pro tyto účty stejná hesla** a zadejte heslo.

   ![Snímek obrazovky se stránkou Zadat heslo ASM instalačního programu](./media/oracle-asm/install04.png)

6. Na stránce **Určit možnosti správy** máte možnost konfigurovat řízení EM cloud. Tuto možnost přeskočíme `next` - klikněte pro pokračování. 

7. Na stránce **Skupiny privilegovaných operačních systémů** použijte výchozí nastavení. Chcete-li pokračovat, klepněte `next` na tlačítko .

8. Na stránce **Zadat umístění instalace** použijte výchozí nastavení. Chcete-li pokračovat, klepněte `next` na tlačítko .

9. Na stránce **Vytvořit inventář** změňte `/u01/app/grid/oraInventory`skladový adresář na . Chcete-li pokračovat, klepněte `next` na tlačítko .

   ![Snímek obrazovky se stránkou Vytvořit inventář instalačního programu](./media/oracle-asm/install08.png)

10. Na stránce **Konfigurace spuštění kořenového skriptu** zaškrtněte políčko **Automaticky spouštět konfigurační skripty.** Potom vyberte **možnost Použít "root" pověření uživatele** a zadejte heslo uživatele root.

    ![Snímek obrazovky s konfigurační stránkou konfigurace spuštění kořenového skriptu instalačního programu](./media/oracle-asm/install09.png)

11. Na stránce **Provést kontroly předpokladů** se aktuální nastavení nezdaří s chybami. Toto je očekávané chování. Vyberte `Fix & Check Again`.

12. V dialogovém okně **Skript** `OK`opravy klepněte na tlačítko .

13. Na stránce **Souhrn** zkontrolujte vybraná nastavení `Install`a klikněte na tlačítko .

    ![Snímek obrazovky se stránkou Souhrn instalačního programu](./media/oracle-asm/install12.png)

14. Zobrazí se dialogové okno s upozorněním informující, že konfigurační skripty je třeba spustit jako privilegovaný uživatel. Chcete-li pokračovat, klepněte `Yes` na tlačítko .

15. Na stránce **Dokončit** `Close` klepnutím dokončete instalaci.

## <a name="set-up-your-oracle-asm-installation"></a>Nastavení instalace řešení Oracle ASM

Chcete-li nastavit instalaci řešení Oracle ASM, proveďte následující kroky:

1. Ujistěte se, že jste stále přihlášeni jako **mřížka**, z relace X11. Možná budete muset `enter` zasáhnout, abyste oživili terminál. Poté spusťte Pomocníka pro automatickou správu úložišť Oracle:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Otevře se Pomocník pro konfiguraci oracle ASM.

2. V dialogovém **okně Konfigurovat modul ASM: Skupiny disků** klepněte `Create` na tlačítko a potom klepněte na tlačítko `Show Advanced Options`.

3. V dialogovém okně **Vytvořit skupinu disků:**

   - Zadejte název skupiny **disků DATA**.
   - V části **Vybrat členské disky**vyberte **ORCL_DATA** a **ORCL_DATA1**.
   - V části **Velikost alokační jednotky**vyberte **4**.
   - Klepnutím `ok` vytvoříte skupinu disků.
   - Klepnutím `ok` zavřete potvrzovací okno.

   ![Snímek obrazovky s dialogovém oknem Vytvořit skupinu disků](./media/oracle-asm/asm02.png)

4. V dialogovém **okně Konfigurovat modul ASM: Skupiny disků** klepněte `Create` na tlačítko a potom klepněte na tlačítko `Show Advanced Options`.

5. V dialogovém okně **Vytvořit skupinu disků:**

   - Zadejte název skupiny **disků FRA**.
   - V části **Redundancy**vyberte **Externí (žádný).**
   - V části **Vybrat členské disky**vyberte **ORCL_FRA**.
   - V části **Velikost alokační jednotky**vyberte **4**.
   - Klepnutím `ok` vytvoříte skupinu disků.
   - Klepnutím `ok` zavřete potvrzovací okno.

   ![Snímek obrazovky s dialogovém oknem Vytvořit skupinu disků](./media/oracle-asm/asm04.png)

6. Výběrem **možnosti Ukončit** zavřete Pomocníka pro konfiguraci ASM.

   ![Snímek obrazovky s dialogovým oknem Konfigurovat asm: Skupiny disků s tlačítkem Ukončit](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Vytvoření databáze

Databázový software Oracle je už nainstalovaný v bitové kopii Azure Marketplace. Chcete-li vytvořit databázi, proveďte následující kroky:

1. Přepněte uživatele na superuživatele Oracle a potom inicializovat naslouchací proces pro protokolování:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Otevře se Pomocník pro konfiguraci databáze.

2. Na stránce **Operace** databáze `Create Database`klepněte na tlačítko .

3. Na stránce **Režim vytváření:**

   - Zadejte název databáze.
   - U **typu úložiště**zkontrolujte, zda je vybraná volba Automatická správa úložiště **(ASM).**
   - Pro **umístění databázových souborů**použijte výchozí umístění doporučené pomocí asm.
   - Pro **oblast rychlého obnovení**použijte výchozí umístění doporučené asm.
   - zadejte **heslo pro správu** a **potvrďte heslo**.
   - ujistěte se, že `create as container database` je vybrána.
   - zadejte `pluggable database name` hodnotu.

4. Na stránce **Souhrn** zkontrolujte vybraná nastavení `Finish` a kliknutím vytvořte databázi.

   ![Snímek obrazovky se stránkou Shrnutí](./media/oracle-asm/createdb03.png)

5. Databáze byla vytvořena. Na stránce **Dokončení** máte možnost odemknout další účty pro použití této databáze a změnit hesla. Chcete-li tak učinit, vyberte možnost `close`Správa **hesel** - jinak klikněte na .

## <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Úspěšně jste nakonfigurovali oracle automated storage management na image Oracle DB z Azure Marketplace.  Když už tento virtuální hod nepotřebujete, můžete pomocí následujícího příkazu odebrat skupinu prostředků, virtuální hod a všechny související prostředky:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

[Kurz: Konfigurace služby Oracle DataGuard](configure-oracle-dataguard.md)

[Kurz: Konfigurace služby Oracle GoldenGate](Configure-oracle-golden-gate.md)

Recenze [Architekt Oracle DB](oracle-design.md)
