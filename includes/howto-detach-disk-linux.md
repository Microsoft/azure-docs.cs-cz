Když už nepotřebujete datový disk připojený k virtuálnímu počítači, můžete jej jednoduše odpojit. Při odpojení disku od virtuálního počítače nedojde k odebrání disku z úložiště. Pokud znovu chcete použít existující data na disku, můžete jej znovu připojit ke stejnému nebo jinému virtuálnímu počítači.  

> [!NOTE]
> Virtuální počítač v Azure používá různé typy disků – disk operačního systému, místní dočasný disk a volitelné datové disky. Podrobnosti najdete v tématu [Disky a virtuální pevné disky (VHD) pro virtuální počítače](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Disk operačního systému nelze odpojit bez odstranění virtuálního počítače.

## <a name="find-the-disk"></a>Vyhledání disku
Než budete moci odpojit disk od virtuálního počítače, musíte zjistit číslo logické jednotky (LUN), což je identifikátor disku, který se má odpojit. Provedete to podle těchto kroků:

1. Otevřete rozhraní příkazového řádku Azure a [připojte se k předplatnému Azure](/cli/azure/authenticate-azure-cli). Zkontrolujte, že jste v režimu Azure Service Management (`azure config mode asm`).
2. Zjistěte, které disky jsou připojené k virtuálnímu počítači. Následující příklad zobrazí seznam disků pro virtuální počítač `myVM`:

    ```azurecli
    azure vm disk list myVM
    ```

    Výstup se podobá následujícímu příkladu:

    ```azurecli
    * Fetching disk images
    * Getting virtual machines
    * Getting VM disks
      data:    Lun  Size(GB)  Blob-Name                         OS
      data:    ---  --------  --------------------------------  -----
      data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
      data:    0    30        myDataDisk.vhd
      info:    vm disk list command OK
    ```

3. Poznamenejte si **logickou jednotku** (LUN) disku, který chcete odpojit.

## <a name="remove-operating-system-references-to-the-disk"></a>Odebrání odkazů operačního systému na disk
Před odpojením disku od hostitele s Linuxem se ujistěte, že se nepoužívají žádné oddíly disku. Zajistěte, aby se je operační systém po restartu nepokusil znovu připojit. Tyto kroky vrátí zpět konfiguraci, kterou jste pravděpodobně vytvořili při [připojení](../articles/virtual-machines/linux/classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) disku.

1. Pomocí příkazu `lsscsi` zjistěte identifikátor disku. `lsscsi` můžete nainstalovat pomocí příkazu `yum install lsscsi` (v distribucích založených na Red Hat) nebo `apt-get install lsscsi` (v distribucích založených na Debian). Hledaný identifikátor disku najdete pomocí čísla logické jednotky (LUN). Poslední číslo v řazené kolekci členů na každém řádku je logická jednotka (LUN). V následujícím příkladu výstupu z příkazu `lsscsi` se logická jednotka LUN 0 mapuje na */dev/sdc*.

    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```

2. Pomocí příkazu `fdisk -l <disk>` najděte oddíly přidružené k disku, který se má odpojit. Následující příklad zobrazí výstup pro `/dev/sdc`:

    ```bash
    Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x5a1d2a1a
    
        Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1            2048  2145386495  1072692224   83  Linux
    ```

3. Odpojte všechny oddíly uvedené u disku. Následující příklad odpojí `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

4. Pomocí příkazu `blkid` zjistěte identifikátory UUID pro všechny oddíly. Výstup se podobá následujícímu příkladu:

    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

5. V souboru **/etc/fstab** odeberte záznamy související s cestami zařízení nebo s identifikátory UUID pro všechny oddíly disku, který se má odpojit.  Záznamy pro tento příklad můžou být:

    ```sh  
   UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   ```

    nebo
   
   ```sh   
   /dev/sdc1   /datadrive   ext4   defaults   1   2
   ```

## <a name="detach-the-disk"></a>Odpojení disku
Po zjištění čísla logické jednotky (LUN) disku a odebrání odkazů operačního systému jste připraveni disk odpojit:

1. Odpojte vybraný disk od virtuálního počítače spuštěním příkazu `azure vm disk detach
   <virtual-machine-name> <LUN>`. Následující příklad odpojí logickou jednotku (LUN) `0` od virtuálního počítače `myVM`:
   
    ```azurecli
    azure vm disk detach myVM 0
    ```

2. Odpojení disku můžete ověřit opětovným spuštěním příkazu `azure vm disk list`. Následující příklad zkontroluje virtuální počítač `myVM`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    Výstup bude vypadat podobně jako v následujícím příkladu, který ukazuje, že datový disk už není připojen:

    ```azurecli
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     info:    vm disk list command OK
    ```

Odpojený disk zůstává v úložišti, ale už není připojen k virtuálnímu počítači.

