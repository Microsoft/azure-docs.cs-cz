---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ebe112103bc3eb30239e80095db9bb91a33bebf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "67175202"
---
## <a name="troubleshooting-update-failures"></a>Řešení potíží se selháním aktualizací
**Co dělat, pokud se zobrazí oznámení o selhání kontrol před upgradem?**

Pokud selže předběžná kontrola, nezapomeňte se podívat na podrobný oznamovací pruh v dolní části stránky. Najdete tam pokyny k předběžným kontrolám, které selhaly. Například se zobrazí oznámení, že došlo k chybě kontroly stavu kontroleru a kontroly stavu hardwarových součástí. Přejít na **Monitor > stav hardwaru**. Musíte zajistit, aby oba řadiče byly v pořádku a online. Také je potřeba zajistit, aby všechny hardwarové komponenty v zařízení StorSimple byly v tomto okně v pořádku. Potom se můžete pokusit nainstalovat aktualizace. Pokud se vám nedaří vyřešit problémy s hardwarovými komponentami, budete muset kontaktovat podporu Microsoftu, která vám pomůže s dalšími kroky.

**Co dělat, pokud se zobrazí chybová zpráva „Nepovedlo se nainstalovat aktualizace“ s doporučením použít průvodce odstraňováním potíží s aktualizacemi k určení příčiny selhání?**

Pravděpodobnou příčinou je, že nemáte připojení k serverům Microsoft Update. Jedná se o ruční kontrolu, kterou je potřeba provést. Pokud ztratíte připojení k serveru aktualizace, vaše úloha aktualizace selže. Připojení můžete zkontrolovat spuštěním následující rutiny z rozhraní Windows PowerShell vašeho zařízení StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Spusťte rutinu na obou řadičích.

Pokud jste ověřili, že připojení existuje, a problém přetrvává, kontaktujte podporu Microsoftu, která vám pomůže s dalšími kroky.

**Co dělat, pokud dojde k selhání při aktualizaci zařízení na verzi Update 4 a na obou řadičích je spuštěna verze Update 4?**

Počínaje verzí Update 4, pokud je na obou řadičích spuštěna stejná verze softwaru a dojde k selhání aktualizace, řadiče nepřejdou do režimu obnovení. Tato situace může nastat, pokud se na oba řadiče úspěšně nainstaluje oprava hotfix softwaru zařízení (aktualizace prvního řádu) ale ostatní opravy hotfix (aktualizace druhého a třetího řádu) ještě ne. Počínaje verzí Update 4 přejdou řadiče do režimu obnovení, pouze pokud jsou na nich spuštěné různé verze softwaru. 

Pokud dojde k chybě aktualizace, i když je na obou řadičích spuštěna verze Update 4, doporučujeme několik minut počkat a potom aktualizaci zkusit provést znovu. Pokud bude opakovaný pokus neúspěšný, měli byste kontaktovat podporu Microsoftu.
