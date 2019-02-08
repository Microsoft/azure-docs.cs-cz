---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 56514f5dcf4bfe205ef46ee64dcf4dcf638d4f62
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889192"
---
#### <a name="to-configure-and-register-the-device"></a>Konfigurace a registrace zařízení

1. V konzole sériového portu zařízení StorSimple spusťte rozhraní Windows PowerShell. Další informace najdete v článku [Použití klienta PuTTY k připojení ke konzole sériového portu zařízení](#use-putty-to-connect-to-the-device-serial-console). **Postup proveďte přesně, jinak ke konzole nezískáte přístup.**

2. Ve spuštěné relaci jedním stisknutím klávesy **Enter** zobrazte příkazový řádek.

3. Budete vyzváni k volbě jazyka, který chcete pro zařízení nastavit. Zadejte jazyk a stiskněte **Enter**.

4. V zobrazené nabídce sériové konzoly se výběrem možnosti 1 **přihlaste s oprávněním k úplnému přístupu**.
     Provedením kroků 5–12 konfigurujte minimální požadovaná síťová nastavení zařízení. **Tyto kroky konfigurace je nutné provést v aktivním řadiči zařízení.** Nabídka konzoly sériového portu zobrazuje stav řadiče ve zprávě v záhlaví. Pokud nejste připojeni k aktivnímu řadiči, odpojte se a potom se připojte k aktivnímu řadiči.

5. Na příkazovém řádku zadejte heslo. Výchozí heslo zařízení je **Password1**.

6. Zadejte následující příkaz: `Invoke-HcsSetupWizard`.

7. Zobrazí se průvodce instalací, který vám pomůže konfigurovat nastavení sítě pro zařízení. Zadejte následující informace:
   
   * IP adresa síťového rozhraní DATA 0
   * maska podsítě
   * brána
   * IP adresa primárního serveru DNS

   Ukázkový výstup je uveden níže.

    ```
        ---------------------------------------------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: 8100-SHX0991003G44MT
        Software Version: 6.3.9600.17759
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Active
        ---------------------------------------------------------------

        Your device needs to be registered with the Microsoft Azure StorSimple Manager service. Please run 'Invoke-HcsSetupWizard' to set up your device.

        Controller0>Invoke-HcsSetupWizard

        Which IP address family would you like to configure on interface Data0?
        [4] IPv4 [6] IPv6 [B] Both (Default is "4"): 4

        Data0 IPv4 address:10.111.111.00
        Data0 IPv4 subnet: 255.255.252.0
        Data0 IPv4 gateway: 10.111.111.11

        IPv4 primary DNS server [10.222.118.154]:10.222.222.111
    ```

    <br>
    V předchozím ukázkovém výstupu vidíte, že systém ověřuje nastavení sítě po provedení každého kroku postupu.

     > [!NOTE]
     > Na použití nastavení masky podsítě a služby DNS může být nutné několik minut počkat. Pokud se zobrazí chybová zpráva „Zkontrolujte síťové připojení rozhraní Data 0“, zkontrolujte fyzické připojení síťového rozhraní DATA 0 aktivního řadiče k síti.

8. Volitelně konfigurujte proxy server. Konfigurace proxy serveru je volitelná, ale **můžete ji provést jenom v tomto kroku**. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](../articles/storsimple/storsimple-8000-configure-web-proxy.md).
9. Konfigurujte primární server NTP pro zařízení. Servery NTP jsou požadovány, protože zařízení musí synchronizovat čas, aby ho mohli ověřit poskytovatelé cloudových služeb. Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet. Pokud to není možné, zadejte interní server NTP.

    Ukázkový výstup najdete níž.

    ```
        Would you like to configure a web proxy?
        [Y] Yes [N] No (Default is "N"):N

        Primary NTP server [time.windows.com]:time.windows.com

    ```

10. Platnost hesla správce zařízení z bezpečnostních důvodů vyprší po první relaci a je nutné heslo nyní změnit. Až k tomu budete vyzváni, zadejte heslo správce zařízení. Platné heslo správce zařízení musí být tvořeno 8 až 15 znaky. Heslo musí obsahovat kombinaci tří z následujících čtyř typů znaků: malá písmena, velká písmena, číslice a speciální znaky.

    ```
        The device administrator password must be between 8 and 15 characters. The password must contain a combination of uppercase letters, lowercase letters, numbers and special characters.
        Administrator Password:********
        Confirm Administrator Password:********
    ```
11. V posledním kroku průvodce instalací zařízení zaregistruje zařízení ve službě Správce zařízení StorSimple. K provedení tohoto kroku budete potřebovat registrační klíč služby, který jste získali v kroku 2. Po zadání registračního klíče může být nutné 2–3 minuty počkat, než se zařízení zaregistruje.
    
    > [!NOTE]
    > Průvodce lze kdykoliv ukončit stisknutím kombinace kláves Ctrl+C. Pokud jste zadali všechna nastavení sítě (IP adresu pro rozhraní DATA 0, masku podsítě a bránu), vaše záznamy zůstanou zachovány.
    
    Ukázkový výstup najdete níž.

    ```
        The service registration key is available in the StorSimple Manager service.
        Enter service registration key:**************************************
        Device registration is in progress. Please wait.

    ```

12. Po provedení registrace zařízení se zobrazí šifrovací klíč dat služby. Klíč zkopírujte a uložte na bezpečném místě. **Tento klíč bude požadován spolu s registračním klíčem služby k registraci dalších zařízení ve službě Správce zařízení StorSimple.** Další informace o tomto klíči najdete v článku [Zabezpečení zařízení StorSimple](../articles/storsimple/storsimple-security.md).
    
    ![Registrace zařízení StorSimple 7](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup1.png)
    
    > [!NOTE]
    > Pokud chcete zkopírovat text z okna konzoly sériového portu, jednoduše ho vyberte. Potom by mělo být možné text vložit do schránky nebo libovolného textového editoru. Ke zkopírování šifrovacího klíče dat služby NEPOUŽÍVEJTE kombinaci kláves Ctrl+C. Použití kombinace kláves Ctrl+C způsobí ukončení průvodce instalací. Heslo správce zařízení nebude změněno a zařízení bude používat výchozí heslo.
    
13. Ukončete konzolu sériového portu.
14. Přejděte zpět na web Azure Portal a proveďte následující kroky:
    
    1. Přejděte do služby Správce zařízení StorSimple.
    2. Klikněte na **Zařízení**.
    3. V tabulkovém výpisu zařízení vyhledejte stav zařízení, abyste ověřili, že se úspěšně připojilo ke službě. Stav zařízení musí být **Připraveno k nastavení**.
       
        ![Stránka zařízení StorSimple](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup2.png)
       
        Možná budete muset několik minut počkat, než se stav zařízení změní na **Připraveno k nastavení**.
       
        Pokud se zařízení v seznamu nezobrazuje, je nutné zkontrolovat, jestli je brána firewall nastavená způsobem popsaným v článku o [požadavcích zařízení StorSimple na sítě](../articles/storsimple/storsimple-8000-system-requirements.md). Zkontrolujte, jestli je port 9354 otevřený pro odchozí komunikaci, protože ho používá sběrnice služby pro komunikaci služby Správce zařízení StorSimple se zařízením.

