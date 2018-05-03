<!--author=SharS last changed: 02/04/2016-->

#### <a name="to-create-a-volume"></a>Vytvoření svazku
1. Na stránce **Rychlý start** zařízení klikněte na **Přidat svazek**. Spustí se průvodce přidáním svazku.
2. V průvodci přidáním svazku v části **Základní nastavení** proveďte tyto kroky:
   
   1. Zadejte **Název** svazku.
   2. Zadejte hodnotu **Provisioned capacity** (Zřízená kapacita) pro svazek (v GB nebo TB). Kapacita svazku musí být v případě fyzického zařízení mezi 1 GB a 64 TB.
   3. V rozevíracím seznamu vyberte **Usage Type** (Typ použití) svazku. 
   4. Pokud tento svazek používáte k archivaci dat, zaškrtněte políčko **Use this volume for less frequently accessed archival data** (Použít tento svazek pro archivní data s méně častým přístupem). Ve všech ostatních případech vyberte možnost **Tiered Volume** (Vrstvený svazek). (Vrstvené svazky se dřív označovaly jako primární svazky).
      
        ![Přidání svazku](./media/storsimple-create-volume/ScreenshotUpdate1VolumeFlow.png)
      
      1. Kliknutím na ikonu šipky ![ikona šipky](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) přejděte na další stránku.
3. V dialogovém okně **Další nastavení** přidejte nový záznam řízení přístupu (ACR):
   
   1. Zadejte **Název** záznamu ACR.
   2. V části **Název iniciátoru iSCSI** zadejte kvalifikovaný název iSCSI (IQN) hostitele s Windows. Pokud název IQN nemáte, přejděte do části [Získání názvu hostitele se systémem Windows Server](#get-the-iqn-of-a-windows-server-host).
   3. Doporučujeme zaškrtnutím políčka **Povolit pro tento svazek výchozí zálohování** povolit výchozí zálohování. Výchozí zálohování vytvoří zásadu, která se spustí každý den ve 22:30 (čas zařízení) a vytvoří cloudový snímek tohoto svazku.
      
      > [!NOTE]
      > Jakmile tady zásadu povolíte, nastavení už se nedá vrátit. Pokud budete chtít nastavení změnit, musíte upravit svazek.
      > 
      > 
      
        ![Přidání svazku](./media/storsimple-create-volume/AddVolume2-include.png)
4. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-create-volume/HCS_CheckIcon-include.png). Vytvoří se svazek se zadaným nastavením.

![Dostupné video](./media/storsimple-create-volume/Video_icon.png)**Dostupné video**

Pokud si chcete přehrát video, které ukazuje, jak vytvořit svazek StorSimple, klikněte [sem](https://azure.microsoft.com/documentation/videos/create-a-storsimple-volume/).

