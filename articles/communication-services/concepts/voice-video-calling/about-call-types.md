---
title: Koncepce hlasu a videa v komunikačních službách Azure
titleSuffix: An Azure Communication Services concept document
description: Seznamte se s typy volání komunikačních služeb.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 98c1ce9367e9bf2aa1a381a772b3cbc73f96b519
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650942"
---
# <a name="voice-and-video-concepts"></a>Koncepce hlasových hovorů a videohovorů

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Pomocí komunikačních služeb Azure můžete vytvořit a přijmout jedno pro jedno nebo více skupin volání hlasu a videa. Vaše volání se dají provádět na jiných zařízeních připojených k Internetu a na obyčejných telefonních hovorech. Klientské knihovny pro komunikační služby JavaScript, Android nebo iOS můžete použít k sestavování aplikací, které umožňují uživatelům vzájemně mluvit v privátních konverzacích nebo v diskusích skupin. Komunikační služby Azure podporují volání a ze služeb nebo roboty.

## <a name="call-types-in-azure-communication-services"></a>Typy volání ve komunikačních službách Azure

V komunikačních službách Azure můžete provádět různé typy volání. Typ volání zjistíte podle schématu signalizace, toků přenosů multimédií a cenového modelu.

### <a name="voice-over-ip-voip"></a>VoIP (Voice over IP) 

Když uživatel vaší aplikace volá jiného uživatele vaší aplikace přes Internet nebo datové připojení, volání se provede přes VoIP (Voice over IP). V takovém případě je přes Internet signalizace i tok médií.

### <a name="public-switched-telephone-network-pstn"></a>Síť PSTN (Public Switched Telephone Network)

Pokaždé, když vaši uživatelé komunikují s tradičním telefonním číslem, se volání usnadňují hlasovým voláním v síti PSTN (s použitím veřejné komutované telefonní sítě). Pokud chcete uskutečnit a přijímat volání do veřejné telefonní sítě, musíte do svého prostředku Azure Communications Services přidat možnosti telefonního subsystému. V takovém případě signalizace a média používají pro připojení uživatelů kombinaci technologií založených na protokolu IP a PSTN.

### <a name="one-to-one-call"></a>Volání 1:1

Pokud se jeden z vašich uživatelů připojuje k jinému uživateli pomocí jedné z našich klientských knihoven, volá se volání služby Azure Communication Services jedním z nich. Volání může být VoIP nebo PSTN.

### <a name="group-call"></a>Seskupit volání

Volání skupiny ve službě Azure Communication Services se stane, když se na sebe připojí tři nebo více účastníků. Jakákoli kombinace uživatelů VoIP a PSTN propojených přes síť PSTN může být k dispozici ve skupině volání. Volání typu 1:1 lze převést na volání skupiny přidáním dalších účastníků do volání. Jedním z těchto účastníků může být robot.

### <a name="supported-video-standards"></a>Podporované standardy videí
Podporujeme H. 264 (MPEG-4). 

### <a name="video-quality"></a>Kvalita videa
V nativních sadách (iOS, Android) SDK podporujeme až v plném rozsahu HD 1080p. Pro web (JS) SDK podporujeme Standard HD 720p. Kvalita závisí na dostupné šířce pásma.  

### <a name="rooms-concept"></a>Koncept místností
Místnosti jsou sadou rozhraní API a sad SDK, které vám umožňují snadno přidat zvuk, video, sdílení obrazovky, veřejné servery a interakce služby SMS na váš web nebo nativní aplikaci.
V rámci verze Preview můžete ke stejné konverzaci použít ID skupiny. Můžete vytvořit tolik ID skupin, kolik potřebujete, a oddělit uživatele podle "místností". Přesunutím do popředí zavedete více ovládacích prvků kolem "místností". 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s voláním](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Další informace najdete v následujících článcích:
- Seznamte se s obecnými [toky volání](../call-flows.md)
- [Typy telefonních čísel](../telephony-sms/plan-solution.md)
- Informace o [možnostech volání klientské knihovny](../voice-video-calling/calling-sdk-features.md)
