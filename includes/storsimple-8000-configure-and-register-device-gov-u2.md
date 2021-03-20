---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 338332eb90fda9afe7655f9f2ad19ed726419b6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95554828"
---
### <a name="to-configure-and-register-the-device"></a>Konfigurace a registrace zařízení
1. V konzole sériového portu zařízení StorSimple spusťte rozhraní Windows PowerShell. Další informace najdete v článku [Použití klienta PuTTY k připojení ke konzole sériového portu zařízení](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console). **Postup proveďte přesně, jinak ke konzole nezískáte přístup.**
2. Ve spuštěné relaci jedním stisknutím klávesy **Enter** zobrazte příkazový řádek.
3. Budete vyzváni k volbě jazyka, který chcete pro zařízení nastavit. Zadejte jazyk a stiskněte **Enter**.
   
    ![Konfigurace a registrace zařízení StorSimple 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. V zobrazené nabídce sériové konzoly se výběrem možnosti 1 **přihlaste s oprávněním k úplnému přístupu**.
   
    ![Registrace zařízení StorSimple 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Provedením následujících kroků nakonfigurujte minimální požadovaná nastavení sítě pro vaše zařízení.
   
   > [!IMPORTANT]
   > Tyto kroky konfigurace je nutné provést v aktivním řadiči zařízení. Nabídka konzoly sériového portu zobrazuje stav řadiče ve zprávě v záhlaví. Pokud se nepřipojujete k aktivnímu řadiči, odpojte se a připojte se k aktivnímu řadiči.
   
   1. Na příkazovém řádku zadejte heslo. Výchozí heslo zařízení je **Password1**.
   2. Zadejte následující příkaz:
      
        `Invoke-HcsSetupWizard`
   3. Zobrazí se průvodce instalací, který vám pomůže konfigurovat nastavení sítě pro zařízení. Zadejte následující informace:
      
      * IP adresa pro síťové rozhraní DATA 0
      * Maska podsítě
      * brána
      * IP adresa primárního serveru DNS
      * IP adresa primárního serveru NTP
      
      > [!NOTE]
      > Může se stát, že budete muset několik minut počkat, než se použije maska podsítě a nastavení DNS.
    
   4. Volitelně můžete nakonfigurovat web proxy server.
      
      > [!IMPORTANT]
      > Konfigurace proxy serveru je volitelná, ale můžete ji provést jenom v tomto kroku. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](../articles/storsimple/storsimple-8000-configure-web-proxy.md).
     
6. Stisknutím kombinace kláves CTRL + C Průvodce instalací ukončete.
8. Spusťte následující rutinu, která zařízení nasměruje na portál Microsoft Azure Government (protože ve výchozím nastavení odkazuje na veřejný portál Azure Classic). Tato akce restartuje oba řadiče. Doporučujeme, abyste pro souběžné připojení k oběma řadičům používali dvě relace na výstupu, abyste viděli, kdy se každý kontroler restartuje.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Zobrazí se potvrzovací zpráva. Přijměte výchozí (**Y**).
9. Spusťte následující rutinu a pokračujte v instalaci:
   
    `Invoke-HcsSetupWizard`
   
    ![Průvodce pokračováním instalačního programu](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. Přijměte nastavení sítě. Po přijetí každého nastavení se zobrazí ověřovací zpráva.
11. Platnost hesla správce zařízení z bezpečnostních důvodů vyprší po první relaci a je nutné heslo nyní změnit. Až k tomu budete vyzváni, zadejte heslo správce zařízení. Platné heslo správce zařízení musí být tvořeno 8 až 15 znaky. Heslo musí obsahovat kombinaci tří z následujících čtyř typů znaků: malá písmena, velká písmena, číslice a speciální znaky.
    
    <br/>![Registrace zařízení StorSimple 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. V posledním kroku průvodce instalací zařízení zaregistruje zařízení ve službě Správce zařízení StorSimple. K tomu budete potřebovat registrační klíč služby, který jste získali v [kroku 2: získání registračního klíče služby](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key). Po zadání registračního klíče může být nutné 2–3 minuty počkat, než se zařízení zaregistruje.
    
    > [!NOTE]
    > Průvodce lze kdykoliv ukončit stisknutím kombinace kláves Ctrl+C. Pokud jste zadali všechna nastavení sítě (IP adresu pro rozhraní DATA 0, masku podsítě a bránu), vaše záznamy zůstanou zachovány.
    
    ![Průběh registrace StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. Po provedení registrace zařízení se zobrazí šifrovací klíč dat služby. Klíč zkopírujte a uložte na bezpečném místě. **Tento klíč je vyžadován s registračním klíčem služby k registraci dalších zařízení ve službě StorSimple Správce zařízení.** Další informace o tomto klíči najdete v článku [Zabezpečení zařízení StorSimple](../articles/storsimple/storsimple-8000-security.md).
    
    ![Registrace zařízení StorSimple 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > Pokud chcete zkopírovat text z okna konzoly sériového portu, jednoduše ho vyberte. Potom by mělo být možné text vložit do schránky nebo libovolného textového editoru.
    > 
    > Nepoužívejte **klávesovou zkratku CTRL + C** ke zkopírování šifrovacího klíče dat služby. Pomocí **kombinace kláves CTRL + C** dojde k ukončení Průvodce instalací nástroje. Heslo správce zařízení nebude změněno a zařízení bude používat výchozí heslo.
    
14. Ukončete konzolu sériového portu.
15. Vraťte se na portál Azure Government a proveďte následující kroky:
    
    1. Přejděte do služby Správce zařízení StorSimple.
    2. Klikněte na **Zařízení**. V seznamu zařízení Identifikujte zařízení, které nasazujete. Vyhledáním stavu ověřte, že se zařízení úspěšně připojilo ke službě. Zařízení musí být ve stavu **Online**.
            
        Pokud je zařízení ve stavu **Offline**, počkejte několik minut, než zařízení přejde do stavu Online.
       
        Pokud je zařízení i po uplynutí několika minut stále v režimu Offline, je nutné zkontrolovat, jestli je brána firewall nastavená způsobem popsaným v článku o [požadavcích zařízení StorSimple na síťe](../articles/storsimple/storsimple-8000-system-requirements.md).
       
        Zkontrolujte, jestli je port 9354 otevřený pro odchozí komunikaci, protože ho používá sběrnice služby pro komunikaci služby Správce zařízení StorSimple se zařízením.