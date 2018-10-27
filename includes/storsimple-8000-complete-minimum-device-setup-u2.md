---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 879241b56ddb3be76b1b54d7a3bb5429facf148a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166384"
---
<!--author=alkohli last changed: 09/28/17-->

#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>Dokončení minimální instalace zařízení StorSimple

   > [!NOTE]
   > Po dokončení minimální instalace zařízení už nebude možné změnit název zařízení.
   
1. V tabulkovém výpisu zařízení v okně **Zařízení** vyberte a klikněte na své zařízení. Zařízení je ve stavu **Připraveno k nastavení**. Otevře se okno **Konfigurace zařízení**.

     ![Síťová rozhraní pro minimální instalaci zařízení StorSimple](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. V okně **Konfigurace zařízení**:
   
   1. Zadejte **Přátelské oslovení** svého zařízení. Výchozí název zařízení odráží informace, jako je model zařízení a sériové číslo. Můžete přiřadit popisný název tvořený až 64 znaky, který budete používat při správě zařízení.
   2. Nastavte **časové pásmo** podle zeměpisného umístění, ve kterém se zařízení nasazuje. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.
   3. V části **Nastavení DATA 0**:

       1. Vaše síťové rozhraní DATA 0 se zobrazí jako povolené s nastavením sítě (IP adresa, podsíť, brána) nakonfigurovaným prostřednictvím průvodce instalací. Síťové rozhraní DATA 0 je zároveň automaticky povolené pro cloud i standard iSCSI.

       2. Zadejte pevné IP adresy pro Řadič 0 a Řadič 1. **Pevné IP adresy řadiče musí být volné IP adresy s podsítí přístupnou pro IP adresu zařízení.** Pokud jste u rozhraní DATA 0 nakonfigurovali protokol IPv4, pevné IP adresy musí být zadané ve formátu IPv4. Pokud jste v konfiguraci protokolu IPv6 zadali předponu, pevné IP adresy se do těchto polí vyplní automaticky.

            ![Síťová rozhraní pro minimální instalaci zařízení StorSimple](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            Pevné IP adresy pro řadič slouží k doručování aktualizací do zařízení a uvolňování paměti. Pevné IP adresy proto musí být směrovatelné a schopné připojení k internetu. Směrovatelnost pevných IP adres řadiče můžete zkontrolovat pomocí rutiny [Test-HcsmConnection][Test]. Následující příklad ukazuje, že pevné IP adresy řadiče jsou směrovány na internet a mohou přistupovat k serverům služby Microsoft Update.

            ![Rutina Test-HcsmConnection zobrazující směrovatelné IP adresy](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. Klikněte na **OK**. Spustí se konfigurace zařízení. Po dokončení konfigurace zařízení se zobrazí oznámení. Stav zařízení v okně **Zařízení** se změní na **Online**.

    ![Síťová rozhraní pro minimální instalaci zařízení StorSimple](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx
