---
title: NEJČASTĚJŠÍ DOTAZY K SMS
titleSuffix: An Azure Communication Services concept document
description: NEJČASTĚJŠÍ DOTAZY K SMS
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: reference
ms.service: azure-communication-services
ms.openlocfilehash: 1ba7c730542adb74356d71f2482cce57e633cb65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646032"
---
# <a name="sms-faq"></a>NEJČASTĚJŠÍ DOTAZY K SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>Může zákazník používat komunikační služby Azure pro nouzové účely?

Komunikační služby Azure v USA nepodporují funkce typu text-911, ale je možné, že v souladu s pravidly federálních komunikačních Komise (FCC) se může stát, že k tomu budete mít povinnost.  Měli byste vyhodnotit, jestli se pravidla SMS až 911 vztahují na vaši službu nebo aplikaci. V rozsahu, na který tato pravidla pokrýváte, zodpovídáte za směrování 911 textových zpráv do nouzových Center volání, která je požadují. Můžete určit svůj vlastní model doručování textu na 911, ale jeden přístup přijatý FCC vyžaduje automatické spuštění nativního vytáčení na mobilním zařízení uživatele a doručí 911 texty prostřednictvím příslušného mobilního dopravce.

## <a name="are-there-any-limits-on-sending-messages"></a>Existují nějaké limity pro posílání zpráv?

Aby bylo zajištěno, že budeme dál nabízet vysokou kvalitu služby v souladu s našimi SLA, uplatní komunikační služby Azure omezení četnosti (pro jednotlivé primitivní prvky). Vývojáři, kteří volají naše rozhraní API nad rámec tohoto limitu, obdrží odpověď stavového kódu HTTP 429. Pokud má vaše společnost požadavky, které překračují omezení četnosti, pošlete nám prosím e-mail na adresu phone@microsoft.com .

Omezení přenosové rychlosti pro SMS:

|Operace|Obor|Časové rámce| Limit (Request #) | Jednotky zpráv za minutu|
|---------|-----|-------------|-------------------|-------------------------|
|Odeslat zprávu|Za číslo|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Jak služby Azure Communication Services zpracovávají výslovný souhlas s čísly bez poplatků?

Výslovný souhlas s telefonními čísly pro USA – bezplatné počty jsou pověřené a vyžádané provozovateli USA.
- ZASTAVIT – Pokud se příjemce textové zprávy chce odhlásit, může poslat "STOP" na bezplatné telefonní číslo. Přepravce pošle následující výchozí odpověď pro STOP: "Síťová zpráva: odpověděli jste se slovem stop, který blokuje všechny texty odeslané z tohoto čísla. Vrátí text zpět, aby se zprávy znovu přijímaly.
- SPUSTIT/zrušit – pokud si chce příjemce znovu přihlásit k odběru textových zpráv z bezplatného čísla, může poslat "START" nebo "zrušit zastavení na bezplatné číslo. Přepravce pošle následující výchozí odpověď pro spuštění/zrušení zastavení: "Síťová zpráva: odpověděli jste na zastavit a začne znovu přijímat zprávy od tohoto čísla."
- Služba Azure Communication Services zjistí zprávu o zastavení a zablokuje všem dalším zprávám příjemce. Zpráva o doručení indikuje neúspěšné doručení se stavovou zprávou "odesílatel zablokován pro daného příjemce".
- Zprávy o zastavení, zastavení a spuštění budou předávány zpět na vás. Komunikační služby Azure vám pomohou monitorovat a implementovat tyto odhlašování, aby se zajistilo, že se žádné další pokusy o odeslání zprávy neodešlou na příjemce, kteří si nevyjádřili oznámení.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Jak mohu přijímat zprávy pomocí komunikačních služeb Azure?

Zákazníci se službou Azure Communication Services můžou přijímat příchozí zprávy pomocí Azure Event Grid. Podle tohoto [rychlého](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/handle-sms-events) startu nastavte svou událost-Grid pro příjem zpráv.

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>Můžu odesílat nebo přijímat dlouhé zprávy (>2048 znaků)?

Komunikační služby Azure podporují odesílání a příjem dlouhých zpráv přes SMS. Některé provozovatelé nebo zařízení s bezdrátovým připojením ale můžou při přijímání dlouhých zpráv fungovat odlišně.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>Jak se zpracují zprávy do pevnéých čísel?

V USA komunikační služby Azure nekontrolují čísla pevné a pokusí se je odeslat dopravcům pro doručení. Zákazníkům se budou účtovat zprávy odeslané na pevné čísla. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>Můžu odesílat zprávy více příjemcům?


Ano, můžete vytvořit jednu žádost s více příjemců. Pomocí tohoto [rychlého](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/send?pivots=programming-language-csharp) startu můžete posílat zprávy více příjemcům.
