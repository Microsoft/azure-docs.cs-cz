---
title: Koncepce hlasu a videa v komunikačních službách Azure
titleSuffix: An Azure Communication Services concept document
description: Seznamte se s typy volání komunikačních služeb.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 6ec616047a99465bace0b401f659765e4399634b
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604562"
---
# <a name="voice-and-video-concepts"></a>Koncepce hlasových hovorů a videohovorů

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Pomocí komunikačních služeb Azure můžete vytvořit a přijmout jedno pro jedno nebo více skupin volání hlasu a videa. Vaše volání se dají provádět na jiných zařízeních připojených k Internetu a na obyčejných telefonních hovorech. Sady SDK komunikačních služeb JavaScript, Android nebo iOS můžete použít k sestavování aplikací, které umožňují uživatelům vzájemně mluvit v privátních konverzacích nebo v diskusích skupin. Komunikační služby Azure podporují volání a ze služeb nebo roboty.

## <a name="call-types-in-azure-communication-services"></a>Typy volání ve komunikačních službách Azure

V komunikačních službách Azure můžete provádět různé typy volání. Typ volání zjistíte podle schématu signalizace, toků přenosů multimédií a cenového modelu.

### <a name="voice-over-ip-voip"></a>VoIP (Voice over IP)

Když uživatel vaší aplikace volá jiného uživatele vaší aplikace přes Internet nebo datové připojení, volání se provede přes VoIP (Voice over IP). V takovém případě je přes Internet signalizace i tok médií.

### <a name="public-switched-telephone-network-pstn"></a>Síť PSTN (Public Switched Telephone Network)

Pokaždé, když vaši uživatelé komunikují s tradičním telefonním číslem, se volání usnadňují hlasovým voláním v síti PSTN (s použitím veřejné komutované telefonní sítě). Pokud chcete uskutečnit a přijímat volání do veřejné telefonní sítě, musíte do svého prostředku Azure Communications Services přidat možnosti telefonního subsystému. V takovém případě signalizace a média používají pro připojení uživatelů kombinaci technologií založených na protokolu IP a PSTN.

### <a name="one-to-one-call"></a>Volání 1:1

Pokud se jeden z vašich uživatelů připojí k jinému uživateli pomocí jedné z našich sad SDK, stane se volání služby Azure Communication Services jedním z nich. Volání může být VoIP nebo PSTN.

### <a name="group-call"></a>Seskupit volání

Volání skupiny ve službě Azure Communication Services se stane, když se na sebe připojí tři nebo více účastníků. Jakákoli kombinace uživatelů VoIP a PSTN propojených přes síť PSTN může být k dispozici ve skupině volání. Volání typu 1:1 lze převést na volání skupiny přidáním dalších účastníků do volání. Jedním z těchto účastníků může být robot.

### <a name="supported-video-standards"></a>Podporované standardy videí
Podporujeme H. 264 (MPEG-4).

### <a name="video-quality"></a>Kvalita videa
V nativních sadách (iOS, Android) SDK podporujeme až v plném rozsahu HD 1080p. Pro web (JS) SDK podporujeme Standard HD 720p. Kvalita závisí na dostupné šířce pásma.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s voláním](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Další informace najdete v následujících článcích:
- Seznamte se s obecnými [toky volání](../call-flows.md)
- [Typy telefonních čísel](../telephony-sms/plan-solution.md)
- Další informace o [možnostech volání sady SDK](../voice-video-calling/calling-sdk-features.md)
