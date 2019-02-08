---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 74a9764187b15bddf1dc48fa2b7988217d31abce
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55888724"
---
## <a name="troubleshooting-update-failures"></a>Řešení potíží se selháním aktualizací
**Co dělat, pokud se zobrazí oznámení o selhání kontrol před upgradem?**

Pokud selže předběžná kontrola, nezapomeňte se podívat na podrobný oznamovací pruh v dolní části stránky. Najdete tam pokyny k předběžným kontrolám, které selhaly. Následující obrázek ukazuje instanci, ve které se takové oznámení zobrazilo. V tomto případě selhala kontrola stavu řadičů a kontrola stavu komponent. V části **Stav hardwaru** vidíte, že **Řadič 0** a **Řadič 1** vyžadují pozornost.

  ![Selhání předběžné kontroly](./media/storsimple-install-troubleshooting/HCS_PreUpdateCheckFailed-include.png)

Budete se muset ujistit, že jsou oba řadiče v pořádku a online. Také budete muset ověřit, že se všechny hardwarové komponenty v zařízení StorSimple zobrazují na stránce Údržba se stavem V pořádku. Potom se můžete pokusit nainstalovat aktualizace. Pokud se vám nedaří vyřešit problémy s hardwarovými komponentami, budete muset kontaktovat podporu Microsoftu, která vám pomůže s dalšími kroky.

**Co dělat, pokud se zobrazí chybová zpráva „Nepovedlo se nainstalovat aktualizace“ s doporučením použít průvodce odstraňováním potíží s aktualizacemi k určení příčiny selhání?**

Pravděpodobnou příčinou je, že nemáte připojení k serverům Microsoft Update. Jedná se o ruční kontrolu, kterou je potřeba provést. Pokud ztratíte připojení k serveru aktualizace, vaše úloha aktualizace selže. Připojení můžete zkontrolovat spuštěním následující rutiny z rozhraní Windows PowerShell vašeho zařízení StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Spusťte rutinu na obou řadičích.

Pokud jste ověřili, že připojení existuje, a problém přetrvává, kontaktujte podporu Microsoftu, která vám pomůže s dalšími kroky.

**Co dělat, pokud dojde k selhání při aktualizaci zařízení na verzi Update 4 a na obou řadičích je spuštěna verze Update 4?**

Počínaje verzí Update 4, pokud je na obou řadičích spuštěna stejná verze softwaru a dojde k selhání aktualizace, řadiče nepřejdou do režimu obnovení. Tato situace může nastat, pokud se na oba řadiče úspěšně nainstaluje oprava hotfix softwaru zařízení (aktualizace prvního řádu) ale ostatní opravy hotfix (aktualizace druhého a třetího řádu) ještě ne. Počínaje verzí Update 4 přejdou řadiče do režimu obnovení, pouze pokud jsou na nich spuštěné různé verze softwaru. 

Pokud dojde k chybě aktualizace, i když je na obou řadičích spuštěna verze Update 4, doporučujeme několik minut počkat a potom aktualizaci zkusit provést znovu. Pokud bude opakovaný pokus neúspěšný, měli byste kontaktovat podporu Microsoftu.
