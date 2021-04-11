---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 03/22/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 388798b9f1ada6921fb79363678ecd17a3041227
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801455"
---
V této kategorii je **12** doporučení.

|Doporučení |Description |Závažnost |
|---|---|---|
|Výchozí zásady filtru IP adres by se měly odepřít. |Konfigurace filtru IP adres musí mít pravidla definovaná pro povolený provoz a měla by ve výchozím nastavení Odepřít všechny ostatní přenosy.<br />(Žádné související zásady) |Střední |
|Měly by být povolené diagnostické protokoly v IoT Hub. |Povolte protokoly a zachovejte je po celý rok. To umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení zabezpečení vaší sítě.<br />(Související zásady: [protokoly diagnostiky v IoT Hub by měly být povolené](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f383856f8-de7f-44a2-81fc-e5135b5c2aa4)) |Nízká |
|Totožné přihlašovací údaje pro ověřování |Stejné přihlašovací údaje pro ověřování pro IoT Hub používané více zařízeními To může znamenat, že zařízení illegitimate zosobňuje legitimní zařízení. Také zveřejňuje riziko zosobnění zařízení útočníkem.<br />(Žádné související zásady) |Vysoká |
|Zařízení IoT – agent odesílající nevyužité zprávy |Kapacita velikosti zprávy agenta IoT je aktuálně nevyužitá a způsobuje zvýšený počet odeslaných zpráv. Upravit intervaly zpráv pro lepší využití<br />(Žádné související zásady) |Nízká |
|Zařízení IoT – auditované procesy zastavily odesílání událostí |Události zabezpečení pocházející z auditovaného procesu už z tohoto zařízení neobdrží.<br />(Žádné související zásady) |Vysoká |
|Zařízení IoT – otevření portů na zařízení |Na zařízení se našel koncový bod naslouchací služby.<br />(Žádné související zásady) |Střední |
|Zařízení IoT – chyba ověřování standardních hodnot operačního systému |Zjištěny problémy s konfigurací systému související se zabezpečením<br />(Žádné související zásady) |Střední |
|Zařízení IoT – v jednom z řetězů se nacházely opravňující zásady brány firewall. |V hlavních řetězcích brány firewall se našla povolená zásada brány firewall (vstup/výstup). Zásada by měla odepřít veškerý provoz ve výchozím nastavení, aby bylo možné v zařízení povolit potřebnou komunikaci.<br />(Žádné související zásady) |Střední |
|Zařízení IoT – bylo nalezeno opravňující pravidlo brány firewall ve vstupním řetězci. |Bylo zjištěno pravidlo brány firewall, které obsahuje povolující vzor pro široké spektrum IP adres nebo portů.<br />(Žádné související zásady) |Střední |
|Zařízení IoT – bylo nalezeno opravňující pravidlo brány firewall ve výstupním řetězci. |Bylo zjištěno pravidlo brány firewall, které obsahuje povolující vzor pro široké spektrum IP adres nebo portů.<br />(Žádné související zásady) |Střední |
|Zařízení IoT – vyžaduje se upgrade TLS cipher suite |Byly zjištěny nezabezpečené konfigurace TLS. Doporučuje se upgrade přímo TLS šifr Suite<br />(Žádné související zásady) |Střední |
|Velký rozsah IP adres pravidla filtru IP |Zdrojový rozsah IP adres pravidla povoleného filtru IP adres je moc velký. Přesná povolující pravidla můžou vaše centrum IoT vystavit škodlivým nabídkám.<br />(Žádné související zásady) |Střední |
|||
