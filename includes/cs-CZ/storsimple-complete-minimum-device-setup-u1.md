<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>Dokončení minimální instalace zařízení StorSimple
1. Vyberte zařízení a klikněte na **Rychlý start**. Kliknutím na **Dokončit nastavení zařízení** spusťte průvodce Konfigurace zařízení.
2. V průvodci Konfigurace zařízení v dialogu **Základní nastavení** proveďte následující akce:
   
   1. Zadejte **popisný název** zařízení. Výchozí název zařízení odráží informace, jako je model zařízení a sériové číslo. Můžete přiřadit popisný název tvořený až 64 znaky, který budete používat při správě zařízení.
   2. Nastavte **časové pásmo** podle zeměpisného umístění, ve kterém se zařízení nasazuje. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.
   3. V části **Nastavení DNS** zadejte adresu pro **Sekundární server DNS**. Pokud používáte protokol IPv6, zadá se do pole údaj na základě předpony protokolu IPv6 zadané v rozhraní Windows PowerShellu. 
      Pokud není sekundární server DNS nakonfigurovaný, nebudete moct konfiguraci zařízení uložit.
   4. V seznamu rozhraní s podporou iSCSI povolte aspoň jednu síť pro iSCSI. Aspoň jedno síťové rozhraní musí mít povolenou podporu cloudu a jedno rozhraní musí mít podporu iSCSI. Rozhraní DATA 0 má automaticky povolenou podporu cloudu.
      
      ![Základní nastavení minimální instalace zařízení StorSimple](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupBasicSettings1-include.png)
3. Klikněte na ikonu šipky. ![Ikona šipky StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)
4. Na stránce **Síťová rozhraní** zadejte pevné IP adresy pro řadič 0 a řadič 1. **Pevné IP adresy řadiče musí být volné IP adresy s podsítí přístupnou pro IP adresu zařízení.** Pokud jste u rozhraní DATA 0 nakonfigurovali protokol IPv4, pevné IP adresy musí být zadané ve formátu IPv4. Pokud jste v konfiguraci protokolu IPv6 uvedli předponu, pevné IP adresy se do těchto polí zadají automaticky.

    ![Síťová rozhraní pro minimální instalaci zařízení StorSimple](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

    Pevné IP adresy řadiče se používají k doručování aktualizací do zařízení, takže musí být směrovatelné a musí umožňovat připojení k internetu. Směrovatelnost pevných IP adres řadiče můžete zkontrolovat pomocí rutiny [Test-HcsmConnection][Test]. Následující příklad ukazuje, že pevné IP adresy řadiče jsou směrovány na internet a mohou přistupovat k serverům služby Microsoft Update. 

     ![Rutina Test-HcsmConnection zobrazující směrovatelné IP adresy](./media/storsimple-complete-minimum-device-setup-u1/Test-HcsmConnectionOutputRegisteredDevice.png)

1. Klikněte na ikonu zaškrtnutí ![Ikona zaškrtnutí StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png).
   Vrátíte se na obrazovku **Rychlý start**.
   
   > [!NOTE]
   > Všechna ostatní nastavení zařízení můžete kdykoli upravit na stránce **Konfigurace**.
   > 
   > 

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx