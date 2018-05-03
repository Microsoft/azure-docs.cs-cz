<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-add-a-storage-account-in-storsimple-8000-series-update-10"></a>Přidání účtu úložiště v zařízení StorSimple řady 8000 Update 1.0
1. Na úvodní stránce služby StorSimple Manager vyberte svoji službu a dvakrát na ni klikněte. Tím přejdete na stránku **Rychlý start**. Vyberte stránku **Konfigurace**.
2. Klikněte na **Přidat nebo upravit účet úložiště**.
3. V dialogovém okně **Přidat nebo upravit účet úložiště** klikněte na **Přidat nový**.
4. V poli **Poskytovatel** vyberte příslušného poskytovatele cloudové služby. Podporovaní poskytovatelé jsou Azure, Amazon S3, Amazon S3 s RRS, HP a OpenStack. Zadejte přihlašovací údaje a umístění přidružené k účtu úložiště vašich poskytovatelů cloudových služeb. Pole zobrazená pro přihlašovací údaje se budou lišit v závislosti na zadaném poskytovateli cloudových služeb. 
   
   * Pokud jste jako poskytovatele cloudových služeb vybrali Azure, zadejte **Název** a primární **Přístupový klíč** vašeho účtu úložiště Microsoft Azure. Umístění je v případě účtu Azure automaticky doplněno.
     
        ![Přidání účtu úložiště Azure](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)
   * Pokud jste jako poskytovatele cloudových služeb vybrali Amazon S3 nebo Amazon S3 s RRS, zadejte popisný **Název účtu úložiště**, **Přístupový klíč** a **Tajný klíč**. U poskytovatelů Amazon S3 a Amazon S3 s RRS jsou podporována následující umístění:
     
     * USA – standardní
     * USA – západ (Oregon)
     * USA – západ (severní Kalifornie)
     * EU (Irsko)
     * Asie a Tichomoří (Singapur)
     * Asie a Tichomoří (Sydney)
     * Asie a Tichomoří (Tokio)
     * Jižní Amerika (Sao Paulo)
       
       ![Přidání účtu úložiště Amazon](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
   * Pokud jste jako poskytovatele cloudových služeb vybrali HP, zadejte popisný **Název účtu úložiště**, **ID klienta**, **Uživatelské jméno** a **Heslo**. V případě poskytovatele HP jsou podporována následující umístění:
     
     * USA – východ
     * USA – západ
       
       ![Přidání účtu úložiště HP](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
   * Pokud jste jako poskytovatele cloudových služeb vybrali **Openstack**, zadejte **Název hostitele**, **Přístupový klíč** a **Tajný klíč**.
     
     > [!NOTE]
     > U všech poskytovatelů cloudových služeb kromě Azure je povoleno zadání popisného názvu. S jednou sadou přihlašovacích údajů můžete používat různé popisné názvy a vytvořit více než jeden účet úložiště.
     > 
     > 
     
        ![Přidání účtu úložiště Openstack](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)
5. Výběrem možnosti **Povolit režim SSL** vytvořte zabezpečený kanál pro síťovou komunikaci mezi vaším zařízením a cloudem. Zaškrtnutí políčka **Povolit režim SSL** zrušte jenom v případě, že pracujete v privátním cloudu.
   
   > [!NOTE]
   > Pokud jako poskytovatele cloudových služeb používáte HP, protokol SSL bude vždy povolen.
   > 
   > 
6. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png). Poté úspěšném vytvoření účtu úložiště se zobrazí oznámení.
7. Nově vytvořený účet úložiště se zobrazí na stránce **Konfigurace** v části **Účty úložiště**. Uložte nově vytvořený účet úložiště kliknutím na **Uložit**. Po zobrazení výzvy k potvrzení klikněte na **OK**.

