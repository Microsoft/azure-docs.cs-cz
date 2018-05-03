<!--author=alkohli last changed: 08/16/2016-->

#### <a name="to-create-a-volume"></a>Vytvoření svazku
1. Na stránce zařízení **Rychlý start** kliknutím na **Přidat svazek** spustíte průvodce přidáním svazku.
2. V průvodci přidáním svazku v části **Základní nastavení** proveďte tyto kroky:
   
   1. Zadejte **Název** svazku.
   2. V rozevíracím seznamu **Typ použití** vyberte typ použití svazku. Pro úlohy, které vyžadují místní záruky, nízkou latenci a vyšší výkon, vyberte typ svazku **Místně vázaný**. Pro ostatní typy dat vyberte typ svazku **Vrstvený**. Pokud tento svazek používáte k archivaci dat, zaškrtněte políčko **Použít tento svazek pro archivní data s méně častým přístupem**. 
      
       Místně vázaný svazek je tlustě zřízený a zajišťuje, že primární data uložená ve svazku zůstanou uložená v místním zařízení a nebudou distribuována do cloudu.  Pokud vytvoříte místně vázaný svazek, zařízení zkontroluje dostupné místo v místních vrstvách a zřídí svazek požadované velikosti. Při operaci vytváření místně vázaného svazku mohou být existující data ze zařízení distribuována do cloudu a vytvoření svazku může trvat dlouhou dobu. Celková doba závisí na velikosti zřizovaného svazku, dostupné šířce pásma sítě a datech uložených v zařízení. 
      
       Vrstvený svazek je zřizovaný dynamicky a lze jej rychle vytvořit. Výběrem možnosti **Použít tento svazek pro archivní data s méně častým přístupem** u vrstveného svazku určeného k uchovávání archivních dat změníte velikost bloku odstranění duplicit vašeho svazku na 512 kB. Pokud políčko není zaškrtnuté, příslušný vrstvený svazek bude používat velikost bloku 64 kB. Větší velikost deduplikačního bloku dat umožňuje zařízení urychlit přenos velkých objemů archivních dat do cloudu.
   3. Zadejte hodnotu **Zřízená kapacita** svazku. Poznamenejte si kapacitu dostupnou na základě vybraného typu svazku. Zadaná velikost svazku nesmí překročit velikost dostupného místa.
      
       Zařízení 8100 umožňuje zřizovat místně vázané svazky o velikosti až 8.5 TB a vrstvené svazky o velikosti až 200 TB. Větší zařízení 8600 umožňuje zřizovat místně vázané svazky o velikosti až 22.5 TB a vrstvené svazky o velikosti až 500 TB. Protože k hostování fungující sady vrstvených svazků je vyžadováno volné místo v zařízení, vytváření místně vázaných svazků ovlivňuje volné místo dostupné ke zřizování vrstvených svazků. Pokud vytvoříte místně vázaný svazek, zmenší se dostupné volné místo potřebné k vytváření vrstvených svazků. Podobně pokud vytvoříte vrstvený svazek, zmenší se dostupné volné místo potřebné k vytváření místně vázaných svazků.
      
       Pokud v zařízení 8100 zřídíte místně vázaný svazek o velikosti 8.5 TB (maximální možná velikost), vyčerpáte tím veškeré volné místo dostupné v zařízení. Nebudete už moct vytvořit žádné vrstvené svazky a v zařízení už nezbude žádné volné místo k hostování pracovní sady vrstveného svazku. Objem dostupného volného místa ovlivňují také vrstvené svazky. Pokud například používáte zařízení 8100, ve kterém jsou už zřízeny vrstvené svazky o velikosti zhruba 106 TB, k vytváření místně vázaných svazků zbude už jenom 4 TB dostupného volného místa.
      
       Na následujícím obrázku je dialogové okno **Základní nastavení** pro místně vázaný svazek.
      
        ![Přidání místního svazku](./media/storsimple-create-volume-u2/add-local-volume-include.png)
      
       Na následujícím obrázku je dialogové okno **Základní nastavení** pro vrstvený svazek.
      
        ![Přidání místního svazku](./media/storsimple-create-volume-u2/add-tiered-volume-include.png)
   
   1. Kliknutím na ikonu šipky ![ikona šipky](./media/storsimple-create-volume-u2/HCS_ArrowIcon-include.png) přejděte na další stránku.
3. V dialogovém okně **Další nastavení** přidejte nový záznam řízení přístupu (ACR):
   
   1. Zadejte **Název** záznamu ACR.
   2. V části **Název iniciátoru iSCSI** zadejte kvalifikovaný název iSCSI (IQN) hostitele s Windows. Pokud název IQN nemáte, přejděte do části [Získání názvu hostitele se systémem Windows Server](#get-the-iqn-of-a-windows-server-host).
   3. V části **Výchozí zálohování tohoto svazku?** zaškrtněte políčko **Povolit**. Výchozí zálohování vytvoří zásadu, která se spustí každý den ve 22:30 (čas zařízení) a vytvoří snímek tohoto svazku v cloudu.
      
      > [!NOTE]
      > Jakmile tady zásadu povolíte, nastavení už se nedá vrátit. Chcete-li změnit toto nastavení, musíte upravit svazek.
      > 
      > 
      
      ![Přidání svazku](./media/storsimple-create-volume-u2/AddVolumeAdditionalSettings1.png)
4. Klikněte na ikonu zaškrtnutí ![ikona zaškrtnutí](./media/storsimple-create-volume-u2/HCS_CheckIcon-include.png). Vytvoří se svazek se zadaným nastavením.

