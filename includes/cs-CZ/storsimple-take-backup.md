<!--author=alkohli last changed: 9/17/15-->

### <a name="to-take-a-backup"></a>Provedení zálohy
1. Na obrazovce **Rychlý start** zařízení klikněte na **Add a backup policy** (Přidat zásadu zálohování). Tím spustíte průvodce přidáním zásady zálohování. 
2. Na stránce **Define your backup policy** (Definujte zásadu zálohování):
   
   1. Zadejte název zásady zálohování, který obsahuje 3 až 150 znaků.
   2. Vyberte svazky, které chcete zálohovat. Pokud vyberete víc než jeden svazek, tyto svazky se seskupí, aby byla vytvořená záloha konzistentní v případě selhání.
   3. Kliknutím na ikonu šipky ![ikona šipky](./media/storsimple-take-backup/HCS_ArrowIcon-include.png). 
      
      ![Přidání zásady zálohování](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard1M-include.png)
3. Na stránce **Define a schedule** (Definujte plán):
   
   1. V rozevíracím seznamu vyberte typ zálohy. Pokud chcete rychlejší zálohování, vyberte **Local Snapshot** (Místní snímek). Pokud vám záleží na odolnosti dat, vyberte **Cloud Snapshot** (Cloudový snímek).
   2. Zadejte četnost zálohování v minutách, hodinách, dnech nebo týdnech.
   3. Vyberte dobu uchování. Volby uchování závisí na požadované četnosti zálohování. Třeba v případě denní zásady se dá doba uchování zadat v týdnech, kdežto v případě měsíční zásady v měsících.
   4. Vyberte počáteční čas a datum zásady zálohování.
   5. Povolte zásadu zálohování zaškrtnutím políčka **Povolit**. 
   6. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-take-backup/HCS_CheckIcon-include.png) a zásadu uložte.
      
      ![Přidání zásady zálohování](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard2M-include.png)
      
      Teď máte zásadu zálohování, která bude podle plánu vytvářet zálohy dat svazku.

Dokončili jste konfiguraci zařízení. 

![Dostupné video](./media/storsimple-take-backup/Video_icon.png)**Dostupné video**

Pokud si chcete přehrát video, které ukazuje, jak vytvořit zálohu StorSimple, klikněte [sem](https://azure.microsoft.com/documentation/videos/take-a-storsimple-backup/).

