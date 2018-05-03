<!--author=SharS last changed: 9/17/15-->

#### <a name="to-create-a-volume-container"></a>Vytvoření kontejneru svazků
1. Na obrazovce **Rychlý start** zařízení klikněte na **Add a volume container** (Přidat kontejner svazků). Zobrazí se dialogové okno **Create Volume Container** (Vytvořit kontejner svazků).
   
    ![Vytvoření kontejneru svazků](./media/storsimple-create-volume-container/HCS_CreateVolumeContainerM-include.png)
2. V dialogovém okně **Create Volume Container** (Vytvořit kontejner svazků):
   
   1. Zadejte **Název** kontejneru svazků. Název musí mít délku 3 až 32 znaků.
   2. Vyberte **Účet úložiště**, který chcete k tomuto kontejneru svazků přidružit. Můžete vybrat výchozí účet, který se vygeneruje při vytváření služby. Můžete také pomocí možnosti **Přidat nový** zadat účet úložiště, který není spojený s předplatným této služby.
   3. Výběrem možnosti **Enable Cloud Storage Encryption** (Povolit šifrování cloudového úložiště) povolte šifrování dat odesílaných ze zařízení do cloudu.
   4. Zadejte a potvrďte **Cloud Storage Encryption Key** (Šifrovací klíč cloudového úložiště), který má délku 8 až 32 znaků. Tento klíč zařízení používá k přístupu k zašifrovaným datům.
   5. V rozevíracím seznamu **Specify bandwidth** (Zadejte šířku pásma) vyberte možnost **Unlimited** (Neomezená), pokud chcete spotřebovávat veškerou dostupnou šířku pásma. U této možnosti také můžete vybrat **Vlastní**, pokud chcete použít ovládací prvky šířky pásma a zadat hodnotu mezi 1 a 1000 Mb/s. 
      Pokud máte informace o využití šířky pásma, můžete vybrat možnost **Select a bandwidth template** (Vybrat šablonu šířky pásma), abyste mohli přidělovat šířku pásma na základě plánu. Podrobný postup najdete v článku [Přidání šablony šířky pásma](../articles/storsimple/storsimple-manage-bandwidth-templates.md#add-a-bandwidth-template).
   6. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-create-volume-container/HCS_CheckIcon-include.png) pro uložení tohoto kontejneru svazků a ukončení průvodce. 
   
   Nově vytvořený kontejner svazků bude uvedený na stránce **Volume containers** (Kontejnery svazků).

![Dostupné video](./media/storsimple-create-volume-container/Video_icon.png)**Dostupné video**

Pokud si chcete přehrát video, které ukazuje, jak v řešení StorSimple vytvořit kontejner svazků, klikněte [sem](https://azure.microsoft.com/documentation/videos/create-a-volume-container-in-your-storsimple-solution/).

