---
title: Nastavte Oracle ASM na virtuálním počítači Azure s Linuxem | Dokumentace Microsoftu
description: Rychle zprovoznit Oracle ASM se v prostředí Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/19/2017
ms.author: rclaus
ms.openlocfilehash: cc75235680eeace5107ef6ac0380e8b7a42974fc
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38618433"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Nastavte Oracle ASM na linuxovém virtuálním počítači Azure  

Virtuální počítače Azure poskytují plně konfigurovatelné a flexibilní výpočetní prostředí. Tento kurz se zaměřuje na nasazení základního virtuálního počítače Azure v kombinaci s instalací a konfigurací z Oracle automatizované Storage Management (ASM).  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření a připojení k virtuálnímu počítači databáze Oracle
> * Instalace a konfigurace Oracle automatizované správy úložiště
> * Instalace a konfigurace infrastruktury Oracle mřížky
> * Inicializace instalace Oracle ASM
> * Vytvoření služby Oracle DB spravuje ASM


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Příprava prostředí

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupinu prostředků vytvoříte pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logický kontejner, ve které se nasazují a spravují prostředky. V tomto příkladu skupina prostředků s názvem *myResourceGroup* v *eastus* oblasti.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Chcete-li vytvořit virtuální počítač na základě image Oracle Database a nakonfigurujte ho na použití Oracle ASM, použijte [az vm vytvořit](/cli/azure/vm#az_vm_create) příkazu. 

Následující příklad vytvoří virtuální počítač s názvem můjvp přesměrovat, která má velikost Standard_DS2_v2 čtyři připojené datové disky o velikosti 50 GB. Pokud ještě neexistují ve výchozím umístění klíčů, také vytvoří klíče SSH.  Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Po vytvoření virtuálního počítače se zobrazí Azure CLI informace podobně jako v následujícím příkladu. Poznamenejte si hodnotu pro `publicIpAddress`. Tuto adresu použijete pro přístup k virtuálnímu počítači.

   ```azurecli
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

Vytvořte s virtuálním Počítačem relaci SSH a nakonfigurovat další nastavení, použijte následující příkaz. Nahraďte IP adresu s `publicIpAddress` hodnoty pro váš virtuální počítač.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Nainstalujte Oracle ASM

Pokud chcete nainstalovat Oracle ASM, proveďte následující kroky. 

Další informace o instalaci Oracle ASM, naleznete v tématu [Oracle ASMLib soubory ke stažení pro Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Budete muset přihlásit jako uživatel root, aby bylo možné pokračovat v instalaci ASM:

   ```bash
   sudo su -
   ```
   
2. Spusťte tyto další příkazy pro instalaci součástí Oracle ASM:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Ověřte, zda je nainstalován Oracle ASM:

   ```bash
   rpm -qa |grep oracleasm
   ```

    Výstup tohoto příkazu zveřejnit následující komponenty:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM vyžaduje konkrétní uživatelé a role mohl správně fungovat. Následující příkazy vytvoří požadovaného uživatelské účty a skupiny: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Ověření uživatele a skupiny byly vytvořeny správně:

   ```bash
   id grid
   ```

    Výstup tohoto příkazu zveřejnit následujícím uživatelům a skupinám:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Vytvořte složku pro uživatele *mřížky* a změnit vlastníka:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Nastavte Oracle ASM

Pro účely tohoto kurzu je výchozí uživatel *mřížky* a je výchozí skupina *asmadmin*. Ujistěte se, *oracle* uživatele je součástí skupiny asmadmin. Nastavení instalace Oracle ASM, proveďte následující kroky:

1. Nastavení knihovny ovladače Oracle ASM zahrnuje definování výchozí uživatel (grid) a výchozí skupiny (asmadmin), stejně jako konfiguraci disku spustit při spuštění (zvolte y) a vyhledávat disky při spuštění (zvolte y). Je třeba do zobrazených výzev z následujícího příkazu:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Výstup tohoto příkazu by měl vypadat podobně jako následujícím zastavuje se zobrazí výzvu k zodpovězení.

    ```bash
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

2. Zobrazte informace o konfiguraci disku:
   ```bash
   cat /proc/partitions
   ```

   Výstup tohoto příkazu by měl vypadat podobně jako následující seznam dostupných disků

   ```bash
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

3. Formát disku */dev/sdc* spuštěním následujícího příkazu a vyžádání zadejte:
   - *n* pro nový oddíl
   - *p* pro primární oddíl
   - *1* vyberte první oddíl
   - Stisknutím klávesy `enter` pro výchozí první válcový
   - Stisknutím klávesy `enter` pro výchozí poslední válcový
   - Stisknutím klávesy *w* zapsat změny do tabulky oddílu  

   ```bash
   fdisk /dev/sdc
   ```
   
   Pomocí odpovědi výše uvedené ve výstupu příkazu fdisk by měl vypadat nějak takto:

   ```bash
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

4. Opakujte předchozí příkaz fdisk pro `/dev/sdd`, `/dev/sde`, a `/dev/sdf`.

5. Kontrola konfigurace disku:

   ```bash
   cat /proc/partitions
   ```

   Výstup příkazu by měl vypadat nějak takto:

   ```bash
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

6. Zkontrolujte stav služby Oracle ASM a spustit službu Oracle ASM:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   Výstup příkazu by měl vypadat nějak takto:
   
   ```bash
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Vytvoření Oracle ASM disky:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```    

   Výstup příkazu by měl vypadat nějak takto:

   ```bash
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Výpis disků Oracle ASM:

   ```bash
   service oracleasm listdisks
   ```   

   Výstup příkazu by měl seznamu vypnout následující disky Oracle ASM:

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Změna hesla pro uživatele root, oracle a mřížky. **Poznamenejte si tyto nová hesla** je použijete později v průběhu instalace.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Změna oprávnění složky:

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

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Stáhněte si a příprava infrastruktury mřížky Oracle

Chcete-li stáhnout a příprava softwaru Oracle mřížky infrastruktury, proveďte následující kroky:

1. Stáhněte si Oracle mřížky infrastruktury z [stránku pro stažení Oracle ASM](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   V části Stažení s názvem **Oracle Database 12c verze 1 mřížky infrastruktury (12.1.0.2.0) pro Linux x86 – x 64**, stáhněte si příslušné dva soubory .zip.

2. Po stažení souborů ZIP na klientském počítači, můžete použít protokol Secure Copy (SCP) ke zkopírování souborů do virtuálního počítače:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH zpět do vašich virtuálních počítačů Oracle v Azure, aby bylo možné přesunout soubory .zip na / opt složky. Potom změníte vlastníka soubory:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Rozbalte soubory. (Instalace Linuxu nástroj rozbalte, pokud ještě není nainstalovaná.)
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Změna oprávnění:
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Aktualizace nakonfigurované velikosti odkládacího souboru. Oracle mřížky vyžadují aspoň 6.8 GB místa odkládacího souboru k instalaci mřížky. Výchozí velikost odkládacího souboru pro Image Oracle Linuxu v Azure je pouze 2 048 MB. Je potřeba zvýšit `ResourceDisk.SwapSizeMB` v `/etc/waagent.conf` souboru a restartujte službu WALinuxAgent aktualizované nastavení projevilo. Protože je jen pro čtení souboru, musíte změnit oprávnění souboru chcete povolit přístup pro zápis.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   Vyhledejte `ResourceDisk.SwapSizeMB` a změňte hodnotu na **8192**. Bude nutné stisknout klávesu `insert` režimu vkládání, zadat hodnotu **8192** a potom stiskněte klávesu `esc` se vraťte do příkazového řádku. Chcete-li zapsat změny a ukončit soubor, zadejte `:wq` a stiskněte klávesu `enter`.
   
   > [!NOTE]
   > Důrazně doporučujeme vždy používat `WALinuxAgent` konfigurace velikosti odkládacího souboru, tak, aby se vždy vytvoří na místní dočasný disk (dočasný disk) pro zajištění nejlepšího výkonu. Další informace o najdete v tématu [přidání odkládacího souboru v Azure pro Linux virtual machines](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Příprava místního klienta a virtuálních počítačů pro spuštění x11
Konfigurace Oracle ASM vyžaduje grafické rozhraní pro dokončení instalace a konfigurace. X11 používáme protokol k usnadnění této instalace. Pokud používáte systém klienta (Mac nebo Linux), který už má X11 možnosti povolené a nakonfigurované – můžete přeskočit této konfigurace a nastavení exkluzivní na počítače s Windows. 

1. [Stáhněte si PuTTY](http://www.putty.org/) a [stáhnout Xming](https://xming.en.softonic.com/) do počítače Windows. Budete potřebovat k dokončení instalace obou těchto aplikací s výchozími hodnotami, než budete pokračovat.

2. Po instalaci klienta PuTTY, otevřete příkazový řádek, přejděte do složky PuTTY (například C:\Program Files\PuTTY) a spusťte `puttygen.exe` aby bylo možné vygenerovat klíč.

3. V PuTTY Key Generator:
   
   1. Vygenerujte klíč tak, že vyberete `Generate` tlačítko.
   2. Zkopírujte obsah klávesu (kombinaci kláves Ctrl + C).
   3. Vyberte tlačítko `Save private key`.
   4. Ignorovat upozornění na zabezpečení klíče pomocí přístupového hesla a pak vyberte `OK`.

   ![Snímek obrazovky PuTTY Key Generator](./media/oracle-asm/puttykeygen.png)

4. Ve virtuálním počítači spusťte tyto příkazy:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Vytvořte soubor s názvem `authorized_keys`. Vložte obsah klíče v tomto souboru a pak soubor uložte.

   > [!NOTE]
   > Klíč musí obsahovat řetězce `ssh-rsa`. Obsah klíče musí být také jeden řádek textu.
   >  

6. V systému klienta spusťte PuTTY. V **kategorie** podokně přejděte na **připojení** > **SSH** > **Auth**. V **soubor privátního klíče pro ověřování** pole, přejděte na klíč, který jste vygenerovali dříve.

   ![Snímek obrazovky s možností ověřování SSH](./media/oracle-asm/setprivatekey.png)

7. V **kategorie** podokně přejděte na **připojení** > **SSH** > **X11**. Vyberte **povolit X11 předávání** zaškrtávací políčko.

   ![Snímek obrazovky s SSH X11 předávání možností](./media/oracle-asm/enablex11.png)

8. V **kategorie** podokně přejděte na **relace**. Zadejte virtuální počítač Oracle ASM `<publicIPaddress>` v dialogovém okně název hostitele, zadejte v novém `Saved Session` název a potom klikněte na `Save`.  Po uložení, klikněte na `open` pro připojení k virtuálnímu počítači Oracle ASM.  Při prvním připojení se zobrazí upozornění, že vzdálený systém neukládá do mezipaměti v registru. Klikněte na `yes` ho přidejte a pokračujte.

   ![Snímek obrazovky s možností relaci PuTTY](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalace infrastruktury mřížky Oracle

Instalace infrastruktury mřížky Oracle, proveďte následující kroky:

1. Přihlaste se jako **mřížky**. (Musí být schopni se přihlásit bez zobrazování výzev k zadání hesla.) 

   > [!NOTE]
   > Pokud používáte systém Windows, ujistěte se, že jste spustili Xming před zahájením instalace.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Otevře se Oracle mřížky infrastruktury 12c verze 1 Instalační služby. (Může trvat několik minut, než spustíte instalační program.)

2. Na **vyberte možnost instalace** stránce **nainstalovat a nakonfigurovat infrastrukturu mřížky Oracle pro samostatný Server**.

   ![Snímek obrazovky stránky instalačního programu vyberte možnost instalace](./media/oracle-asm/install01.png)

3. Na **vyberte jazyky produktu** stránce **Angličtina** nebo vybraný jazyk, ve kterém chcete.  Klikněte na `next` (Další).

4. Na **vytvořit skupinu disku ASM** stránky:
   - Zadejte název skupiny disků.
   - V části **redundance**vyberte **externí**.
   - V části **velikost alokační jednotky**vyberte **4**.
   - V části **přidat disky**vyberte **ORCLASMSP**.
   - Klikněte na `next` (Další).

5. Na **zadejte heslo ASM** stránky, vyberte **pomocí stejného hesla pro tyto účty** možnost a zadejte heslo.

   ![Snímek obrazovky stránky zadejte heslo ASM instalačního programu](./media/oracle-asm/install04.png)

6. Na **zadat možnosti správy** stránky, máte možnost konfigurovat ovládací prvek EM cloudu. Jsme přeskočili tuto možnost – klikněte na tlačítko `next` pokračujte. 

7. Na **privilegovaných skupin operačního systému** stránky, použijte výchozí nastavení. Klikněte na tlačítko `next` pokračujte.

8. Na **zadejte umístění instalace** stránky, použijte výchozí nastavení. Klikněte na tlačítko `next` pokračujte.

9. Na **vytvořit inventáře** stránce, změňte adresář inventáře na `/u01/app/grid/oraInventory`. Klikněte na tlačítko `next` pokračujte.

   ![Snímek obrazovky stránky inventáře vytvoření instalačního programu](./media/oracle-asm/install08.png)

10. Na **konfigurace spuštění skriptu kořenového adresáře** stránky, vyberte **automaticky spouštět skripty konfigurace** zaškrtávací políčko. Vyberte **použijte přihlašovací údaje uživatele "root"** možnost a zadejte heslo uživatele root.

    ![Snímek obrazovky instalačního programu kořenový skript provádění konfigurace stránky](./media/oracle-asm/install09.png)

11. Na **provádění kontrol požadovaných součástí** stránky, spusťte instalační program se nezdaří s chybami. Toto je očekávané chování. Vyberte `Fix & Check Again`.

12. V **opravy skript** dialogové okno, klikněte na tlačítko `OK`.

13. Na **Souhrn** stránce zkontrolujte vybrané nastavení a potom klikněte na tlačítko `Install`.

    ![Snímek obrazovky stránky Souhrn instalačního programu](./media/oracle-asm/install12.png)

14. Dialogové okno upozornění se zobrazí informující jste konfigurační skripty potřebné ke spuštění jako uživatel. Klikněte na tlačítko `Yes` pokračujte.

15. Na **Dokončit** klikněte na `Close` pro dokončení instalace.

## <a name="set-up-your-oracle-asm-installation"></a>Nastavení instalace Oracle ASM

Nastavení instalace Oracle ASM, proveďte následující kroky:

1. Ujistěte se ještě jste přihlášeni jako **mřížky**, z vaší X11 relace. Možná budete muset přístupů `enter` chcete obnovit terminálu. Potom spusťte Oracle automatizované úložiště správy konfigurace Pomocníka pro:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Oracle ASM konfigurace pomocníka otevře.

2. V **konfigurace ASM: skupiny disků** dialogové okno, klikněte na tlačítko `Create` tlačítko a pak klikněte na tlačítko `Show Advanced Options`.

3. V **vytvořit skupinu disku** dialogové okno:

   - Zadejte název skupiny disku **DATA**.
   - V části **vyberte discích**vyberte **ORCL_DATA** a **ORCL_DATA1**.
   - V části **velikost alokační jednotky**vyberte **4**.
   - Klikněte na tlačítko `ok` k vytvoření skupiny disků.
   - Klikněte na tlačítko `ok` potvrzovací okno se zavře.

   ![Snímek obrazovky dialogového okna vytvořit skupinu disku](./media/oracle-asm/asm02.png)

4. V **konfigurace ASM: skupiny disků** dialogové okno, klikněte na tlačítko `Create` tlačítko a pak klikněte na tlačítko `Show Advanced Options`.

5. V **vytvořit skupinu disku** dialogové okno:

   - Zadejte název skupiny disku **FRA**.
   - V části **redundance**vyberte **externí (žádný)**.
   - V části **vyberte discích**vyberte **ORCL_FRA**.
   - V části **velikost alokační jednotky**vyberte **4**.
   - Klikněte na tlačítko `ok` k vytvoření skupiny disků.
   - Klikněte na tlačítko `ok` potvrzovací okno se zavře.

   ![Snímek obrazovky dialogového okna vytvořit skupinu disku](./media/oracle-asm/asm04.png)

6. Vyberte **ukončovací** zavřete průvodce nastavením konfigurace ASM.

   ![Snímek obrazovky konfigurace ASM: dialogové okno skupiny disků s tlačítko Konec](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Vytvoření databáze

Software Oracle databáze je již nainstalována na image Azure Marketplace. K vytvoření databáze, proveďte následující kroky:

1. Přepnout uživatele superuživatele Oracle a potom inicializovat naslouchací proces pro protokolování:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   Otevře se Průvodce nastavením konfigurace databáze.

2. Na **operace databáze** klikněte na `Create Database`.

3. Na **režim vytváření** stránky:

   - Zadejte název databáze.
   - Pro **typ úložiště**, zkontrolujte **automatické Storage Management (ASM)** zaškrtnuto.
   - Pro **umístění souborů databáze**, použijte výchozí ASM navrhované umístění.
   - Pro **rychlé obnovení oblasti**, použijte výchozí ASM navrhované umístění.
   - Zadejte **hesla pro správu** a **potvrzení hesla**.
   - zajištění `create as container database` zaškrtnuto.
   - Zadejte `pluggable database name` hodnotu.

4. Na **Souhrn** stránce zkontrolujte vybrané nastavení a potom klikněte na tlačítko `Finish` k vytvoření databáze.

   ![Snímek obrazovky stránky Souhrn](./media/oracle-asm/createdb03.png)

5. Databázi se vytvořil. Na **Dokončit** stránky, máte možnost odemknout další účty služby pro tuto databázi použít a změnit hesla. Pokud si chcete udělat, vyberte **Správa hesel** – v opačném případě klikněte na `close`.

## <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Úspěšně jste nakonfigurovali Oracle automatizované správy úložiště na image Oracle DB z Azure Marketplace.  Když tento virtuální počítač už nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků příkaz:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

[Kurz: Konfigurace Oracle Dataguardu](configure-oracle-dataguard.md)

[Kurz: Konfigurace Oracle GoldenGate](Configure-oracle-golden-gate.md)

Kontrola [vytvoření Oracle DB](oracle-design.md)
