Podpora pro dvě funkce ladění je nyní k dispozici v Azure: výstup konzoly a snímek podporu pro virtuální počítače Azure modelu nasazení Resource Manager. 

Při zpětném přepnutí vlastní image Azure nebo ani jeden z Image platformy spouštění, může být mnoha důvodů, proč se virtuální počítač získá do jiných spouštěcího stavu. Tyto funkce umožňují snadno diagnostikovat a obnovení virtuálních počítačů v případě selhání spuštění.

Pro virtuální počítače s Linuxem budete moci snadno zobrazit výstup konzoly protokolu z portálu:

![Azure Portal](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
Ale pro virtuální počítače Windows a Linux, Azure také umožní vám prohlédnout snímek virtuálního počítače z hypervisor:

![Chyba](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

Obě tyto funkce jsou podporované pro virtuální počítače, které jsou ve všech oblastech Azure. Mějte na paměti, že může trvat až 10 minut, než se snímky obrazovky a výstup zobrazí v účtu úložiště.

## <a name="common-boot-errors"></a>Běžné chyby spuštění

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Operační systém nebyl nalezen](https://support.microsoft.com/help/4010142)
- [Chyba při spuštění nebo NEDOSTUPNÉ_SPOUŠTĚCÍ_ZAŘÍZENÍ](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Povolení diagnostiky na novém virtuálním počítači
1. Při vytváření nového virtuálního počítače z portálu Azure, vyberte **Azure Resource Manager** z rozevíracího seznamu model nasazení:
 
    ![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. V **nastavení**, povolte **spouštění diagnostiky**a potom vyberte účet úložiště, které chcete umístit tyto diagnostické soubory.
 
    ![Vytvoření virtuálního počítače](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > Spouštění diagnostiky funkce nepodporuje prémiový účet úložiště. Pokud používáte prémiový účet úložiště pro Diagnostika spouštění, může zobrazit chyba StorageAccountTypeNotSupported při spuštění virtuálního počítače.
    >
    > 

3. Pokud nasazujete z šablony Azure Resource Manager, přejděte do virtuálního počítače zdroje a připojte části Diagnostika profilu. Nezapomeňte použít hlavičku verze rozhraní API s hodnotou 2015-06-15.

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. Diagnostický profil umožňuje vybrat účet úložiště pro ukládání protokolů.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

Nasazení ukázkové virtuálního počítače s Diagnostika spouštění povolena, podívejte se na naše úložišti sem.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Povolit spuštění diagnostiky na existující virtuální počítač 

Pokud chcete povolit spouštění diagnostiky na existující virtuální počítač, postupujte takto:

1. Přihlaste se k [portál Azure](https://portal.azure.com)a potom vyberte virtuální počítač.
2. V **podpory a řešení potíží s**, vyberte **spouštění diagnostiky** > **nastavení**, změnit stav na **na**a potom Vyberte účet úložiště. 
4. Ujistěte se, že je vybrána možnost spouštění diagnostiky a potom uložte změny.

    ![Aktualizace stávajícího virtuálního počítače](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. Restartujte virtuální počítač, aby se projevily změny.


