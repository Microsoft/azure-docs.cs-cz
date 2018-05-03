<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>Dokončení minimální instalace zařízení StorSimple
1. Na stránce **Zařízení** vyberte zařízení a kliknutím na šipku u názvu zařízení přejděte na stránku konkrétního zařízení. 
   
    ![Stránka Zařízení s online zařízeními](./media/storsimple-complete-minimum-device-setup/HCS_DevicesPageM-include.png) 
2. Kliknutím na ikonu rychlého startu ![Ikona Rychlý start](./media/storsimple-complete-minimum-device-setup/HCS_QuickStartIcon-include.png) otevřete stránku rychlého startu zařízení. Kliknutím na **Complete device setup** (Provést nastavení zařízení) spusťte průvodce **Configure device** (Konfigurace zařízení).
   
    ![Stránka rychlého startu zařízení](./media/storsimple-complete-minimum-device-setup/Device_Quick_Start_page_1M.png)
3. Na stránce **Základní nastavení** proveďte toto:
   
   1. Zadejte **Přátelské oslovení** svého zařízení. Výchozí název zařízení odráží informace, jako je model zařízení a sériové číslo. Můžete přiřadit popisný název tvořený až 64 znaky, který budete používat při správě zařízení.
   2. Nastavte **časové pásmo** podle zeměpisného umístění, ve kterém se zařízení nasazuje. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.
   3. V části **Nastavení DNS** zadejte adresu pro **Sekundární server DNS**. Pokud používáte protokol IPv6, zadá se do pole údaj na základě předpony protokolu IPv6 zadané v rozhraní Windows PowerShellu. 
      Pokud není sekundární server DNS nakonfigurovaný, nebudete moct konfiguraci zařízení uložit.
   4. V seznamu rozhraní s podporou iSCSI povolte aspoň jednu síť pro iSCSI. Aspoň jedno síťové rozhraní musí mít povolenou podporu cloudu a jedno rozhraní musí mít podporu iSCSI. Rozhraní DATA 0 má automaticky povolenou podporu cloudu.
      
      ![Základní nastavení minimální instalace zařízení StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupBasicSettings1-include.png)
4. Klikněte na ikonu šipky. ![Ikona šipky StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)
5. Na stránce **Síťová rozhraní** zadejte pevné IP adresy pro řadič 0 a řadič 1. Pokud jste u rozhraní DATA 0 nakonfigurovali protokol IPv4, pevné IP adresy musí být zadané ve formátu IPv4. Pokud jste v konfiguraci protokolu IPv6 uvedli předponu, pevné IP adresy se do těchto polí zadají automaticky.

    > [!NOTE] 
    > - Pevné IP adresy řadiče musí být volné IP adresy s podsítí přístupnou pro IP adresu zařízení.
    > - Pevné IP adresy řadiče se používají k doručování aktualizací do zařízení, takže musí být směrovatelné a musí umožňovat připojení k internetu.

    ![Síťová rozhraní pro minimální instalaci zařízení StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

1. Klikněte na ikonu zaškrtnutí ![Ikona zaškrtnutí StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png).
   Vrátíte se na obrazovku **Rychlý start**.
   
   > [!NOTE]
   > Všechna ostatní nastavení zařízení můžete kdykoli upravit na stránce **Konfigurace**.
   > 
   > 

![Dostupné video](./media/storsimple-complete-minimum-device-setup/Video_icon.png)**Dostupné video**

Pokud si chcete přehrát video, které ukazuje, jak provést minimální nastavení zařízení, klikněte [sem](https://azure.microsoft.com/documentation/videos/minimum-storsimple-device-setup/).

