


## <a name="attach-an-empty-disk"></a>Připojení prázdného disku
Prázdný disk se připojuje je jednoduchý způsob, jak přidat datový disk, protože Azure pro vás vytvoří soubor VHD a ukládá je v účtu úložiště.

1. Klikněte na tlačítko **virtuálních počítačů (klasické)**a pak vyberte příslušný virtuální počítač.

2. V nabídce nastavení, klikněte na tlačítko **disky**.

   ![Připojit nový prázdný disk](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. Na panelu příkazů klikněte na tlačítko **připojit nový**.  
    **Připojit nový disk** zobrazí se dialogové okno.

    ![Připojit nový disk](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    Zadejte následující informace:
    - V **název souboru**, přijměte výchozí název nebo zadejte jinou pro soubor VHD. Datový disk používá automaticky vygenerovaným názvem, pokud zadáte jiný název souboru VHD.
    - Vyberte **typ** disku data. Všechny virtuální počítače podporují standardní disky. Mnoho virtuálních počítačů také nepodporují prémiové disky.
    - Vyberte **velikost (GB)** disku data.
    - Pro **použití mezipaměti u hostitele**, zvolte Žádný, nebo jen pro čtení.
    - Kliknutím na tlačítko OK dokončit.

4. Jakmile datový disk se vytvoří a připojené, je uvedena v části disky virtuálního počítače.

   ![Nové a prázdný datový disk se úspěšně připojil](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> Po přidání datový disk, musíte se přihlásit k virtuálnímu počítači a inicializujte disk, aby ho bylo možné použít.

## <a name="how-to-attach-an-existing-disk"></a>Postupy: připojit stávající disk
Připojení stávajícího disku vyžaduje, aby v účtu úložiště byl dostupný soubor .vhd. Použití [přidat AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) rutiny k nahrání souboru VHD do účtu úložiště. Po vytvoření a nahrát soubor VHD, můžete se připojit k virtuálnímu počítači.

1. Klikněte na tlačítko **virtuálních počítačů (klasické)**a pak vyberte příslušný virtuální počítač.

2. V nabídce nastavení, klikněte na tlačítko **disky**.

3. Na panelu příkazů klikněte na tlačítko **připojit existující**.

    ![Připojení datového disku](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. Klikněte na tlačítko **umístění**. Zobrazí se účty úložiště k dispozici. Potom vyberte účet odpovídající úložiště ze seznamu.

    ![Zadejte účet úložiště disku](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. A **účet úložiště** obsahuje jeden nebo více kontejnerů, které obsahují disky (VHD). Vyberte odpovídajícího kontejneru ze seznamu.

    ![Zadejte kontejner virtuálního počítače windows](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. **Virtuální pevné disky** panel uvádí diskové jednotky uchovávat v kontejneru. Klikněte na jeden z disků a pak klikněte na vybrat.

    ![Zadejte bitovou kopii disku pro virtuální počítače windows](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. **Připojit stávající disk** panel znovu, zobrazí se umístění obsahující účet úložiště, kontejneru a vybrané pevný disk (vhd) přidáte do virtuálního počítače.

  Nastavit **použití mezipaměti u hostitele** na hodnotu none nebo pro čtení, klikněte na tlačítko OK.

    ![Datový disk se úspěšně připojil](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
