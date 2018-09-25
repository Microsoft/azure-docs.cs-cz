Další informace o discích najdete v tématu věnovaném [diskům a virtuálním pevným diskům pro virtuální počítače](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

<a id="attachempty"></a>

## <a name="attach-an-empty-disk"></a>Připojení prázdného disku
1. Otevření příkazového řádku Azure classic a [připojit ke svému předplatnému Azure](/cli/azure/authenticate-azure-cli). Zkontrolujte, že jste v režimu Azure Service Management (`azure config mode asm`).
2. Zadáním příkazu `azure vm disk attach-new` vytvořte a připojte nový disk, jak ukazuje následující příklad. Místo *myVM* použijte názve vašeho linuxového virtuálního počítače a zadejte velikost v GB (v tomto příkladu je to *100 GB*):

    ```azurecli
    azure vm disk attach-new myVM 100
    ```

3. Jakmile se datový disk vytvoří a připojí, zobrazí se ve výstupu příkazu `azure vm disk list <virtual-machine-name>`, jak ukazuje následující příklad:
   
    ```azurecli
    azure vm disk list TestVM
    ```

    Výstup se podobá následujícímu příkladu:

    ```bash
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        myVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

<a id="attachexisting"></a>

## <a name="attach-an-existing-disk"></a>Připojení stávajícího disku
Připojení stávajícího disku vyžaduje, aby v účtu úložiště byl dostupný soubor .vhd.

1. Otevření příkazového řádku Azure classic a [připojit ke svému předplatnému Azure](/cli/azure/authenticate-azure-cli). Zkontrolujte, že jste v režimu Azure Service Management (`azure config mode asm`).
2. Zkontrolujte, jestli virtuální pevný disk, který chcete připojit, už je nahraný v předplatném Azure:
   
    ```azurecli
    azure vm disk list
    ```

    Výstup se podobá následujícímu příkladu:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
    ```

3. Pokud se nepodařilo najít disk, který chcete použít, můžete k nahrání místního virtuálního pevného disku do předplatného použít `azure vm disk create` nebo `azure vm disk upload`. Použití příkazu `disk create` se bude podobat následujícímu příkladu:
   
    ```azurecli
    azure vm disk create myVhd .\TempDisk\test.VHD -l "East US" -o Linux
    ```

    Výstup se podobá následujícímu příkladu:

    ```azurecli
    info:    Executing command vm disk create
    + Retrieving storage accounts
    info:    VHD size : 10 GB
    info:    Uploading 10485760.5 KB
    Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
    info:    Finishing computing MD5 hash, 16% is complete.
    info:    https://mystorageaccount.blob.core.windows.net/disks/myVHD.vhd was
    uploaded successfully
    info:    vm disk create command OK
    ```
   
   Můžete také použít `azure vm disk upload` a virtuální pevný disk nahrát do konkrétního účtu úložiště. Víc si o příkazech pro správu datových disků virtuálních počítačů Azure můžete přečíst [tady](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

4. Teď připojíte požadovaný virtuální pevný disk k virtuálnímu počítači:
   
    ```azurecli
    azure vm disk attach myVM myVhd
    ```
   
   Nezapomeňte nahradit *myVM* názvem virtuálního počítače a *myVHD* požadovaným virtuálním pevným diskem.

5. K ověření toho, že disk je připojený k virtuálnímu počítači, můžete použít příkaz `azure vm disk list <virtual-machine-name>`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    Výstup se podobá následujícímu příkladu:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

> [!NOTE]
> Po přidání datového disku se musíte přihlásit k virtuálnímu počítači a disk inicializovat, aby ho virtuální počítač mohl využívat jako úložiště (další informace o postupu při inicializaci disku najdete v následujících krocích).
> 
> 

