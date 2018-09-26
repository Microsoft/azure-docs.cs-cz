


## <a name="attach-an-empty-disk"></a>Připojení prázdného disku
Připojení prázdného disku je jednoduchý způsob, jak přidat datový disk, protože Azure pro vás vytvoří soubor .vhd a ukládá ho do účtu úložiště.

1. Klikněte na tlačítko **virtuální počítače (classic)** a pak vyberte příslušný virtuální počítač.

2. V nabídce nastavení, klikněte na tlačítko **disky**.

   ![Připojit nový prázdný disk](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. Na panelu příkazů klikněte na tlačítko **připojit nový**.  
    **Připojit nový disk** zobrazí se dialogové okno.

    ![Připojit nový disk](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    Zadejte následující informace:
    - V **název_souboru**, přijměte výchozí název nebo zadejte jiný pro soubor .vhd. Datový disk používá automaticky vygenerovaný název, i když můžete zadat jiný název pro soubor .vhd.
    - Vyberte **typ** z datového disku. Všechny virtuální počítače podporují standardní disky. Velký počet virtuálních počítačů také nepodporují prémiové disky.
    - Vyberte **Size (GB)** z datového disku.
    - Pro **hostovat ukládání do mezipaměti**vyberte žádný nebo jen pro čtení.
    - Kliknutím na tlačítko OK dokončit.

4. Po datový disk vytvoří a připojí, je uvedena v části disky virtuálního počítače.

   ![Nové a prázdný datový disk se úspěšně připojil](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> Poté, co přidáte datový disk, budete muset přihlásit k virtuálnímu počítači a disk inicializovat, takže je možné.

## <a name="how-to-attach-an-existing-disk"></a>Postupy: připojení stávajícího disku
Připojení stávajícího disku vyžaduje, aby v účtu úložiště byl dostupný soubor .vhd. Použití [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) rutiny pro nahrání souboru VHD do účtu úložiště. Po vytvoření a nahrání souboru VHD, můžete ji připojit k virtuálnímu počítači.

1. Klikněte na tlačítko **virtuální počítače (classic)** a pak vyberte příslušný virtuální počítač.

2. V nabídce nastavení, klikněte na tlačítko **disky**.

3. Na panelu příkazů klikněte na tlačítko **připojit existující**.

    ![Připojení datového disku](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. Klikněte na tlačítko **umístění**. Zobrazení dostupných účtů úložiště. Potom vyberte účet úložiště odpovídající od těch uvedené.

    ![Zadejte disk účtu úložiště](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. A **účtu úložiště** obsahuje jeden nebo více kontejnerů, které obsahují disky (VHD). Vyberte příslušný kontejner od těch uvedené.

    ![Zadejte kontejner virtuálního počítače windows](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. **Virtuální pevné disky** panel uvádí na pevných discích uložených v kontejneru. Klikněte na jeden z disků a pak klikněte na vybrat.

    ![Zadejte bitové kopie disku pro virtuální počítače windows](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. **Připojit stávající disk** panelu se zobrazí znovu, s umístěním obsahující účet úložiště, kontejner a vybrané pevného disku (vhd) pro přidání do virtuálního počítače.

  Nastavte **hostovat ukládání do mezipaměti** na hodnotu none nebo pro čtení, klikněte na tlačítko OK.

    ![Datový disk se úspěšně připojil](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
