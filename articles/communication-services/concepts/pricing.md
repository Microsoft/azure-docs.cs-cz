---
title: Cenové scénáře pro volání (hlasové a video) a chat
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si o cenovém modelu komunikačních služeb.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5f7b1e6d600f5d3652ce6a66a72cbfbf33b336c4
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091867"
---
# <a name="pricing-scenarios"></a>Cenové scénáře

Ceny za komunikační služby Azure jsou obecně založené na modelu průběžných plateb. Ceny v následujících příkladech jsou pro ilustrativní účely a nemusí odrážet nejnovější ceny Azure.

## <a name="voicevideo-calling-and-screen-sharing"></a>Volání hlasu a videa a sdílení obrazovky

Komunikační služby Azure umožňují přidat do vašich aplikací volání hlasu a videa a sdílení obrazovky. Do svých aplikací můžete vkládat prostředí pomocí JavaScriptu, objektivu – C (Apple), Java (Android) nebo sady .NET SDK. Podívejte se na náš [úplný seznam dostupných sad SDK](./sdk-options.md).

### <a name="pricing"></a>Ceny

Služby pro volání a sdílení obrazovky se účtují za minutu za účastníka na úrovni $0,004 za účastníka za minutu pro volání skupin. Chcete-li pochopit různé toky volání, které jsou možné, přečtěte si [tuto stránku](./call-flows.md).

Každý účastník volání se bude počítat za každou minutu, ke které jsou připojeny ke volání. To má hodnotu true bez ohledu na to, zda uživatel používá audiovizuální volání, volání hlasu nebo sdílení obrazovky.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-sdks"></a>Příklad ceny: rozdělit zvuk/video volání pomocí sad JS a iOS

Alice provedla volání skupiny s kolegy, Bobem a Charlie. Alice a Bob používaly sady SDK JS, Charlie sady iOS.

- Volání trvá celkem 60 minut.
- Alice a Bob se účastnily celého volání. Alice zapnula své video po dobu pěti minut a Nasdílela obrazovku po dobu 23 minut. U Boba bylo video zapnuté pro celé volání (60 minut) a sdílená obrazovka po dobu 12 minut.
- Charlie opustí volání po 43 minutách. Charlie využití zvuku a videa po dobu, po kterou se účastnila (43 minut).

**Výpočty nákladů**

- 2 účastníci × 60 minut × $0,004 za účastníka za minutu = $0,48 [video i zvuk se účtují stejnou sazbou.]
- 1 účastník x 43 minut × $0,004 za účastníka za minutu = $0,172 [video i zvuk se účtují stejnou sazbou.]

**Celkové náklady na volání skupiny**: $0,48 + $0,172 = $0,652


### <a name="pricing-example-outbound-call-from-app-using-js-sdk-to-a-pstn-number"></a>Příklad ceny: odchozí volání z aplikace pomocí SDK JS na číslo veřejné telefonní číslo

Alice vytvoří z aplikace hovor ze sítě PSTN Bobovi na svém telefonním číslu od společnosti `+1-425` .

- Alice použila k sestavení aplikace sadu JS SDK.
- Volání trvá celkem 5 minut.

**Výpočty nákladů**

- 1 účastník na nožkě VoIP (Alice) z aplikace na servery služby Communications x 10 minut × $0,004 na jednu stranu účastníka za minutu = $0,04
- 1 účastník na odchozí nožkě PSTN (Charlie) ze serverů komunikačních služeb na telefonní číslo USA × 10 minut × $0,013 na jednu stranu účastníka za minutu = $0,13.

Poznámka: smíšená sazba USA na `+1-425` hodnotu je $0,013. Podrobnosti najdete na následujícím odkazu: https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)

**Celkové náklady na volání skupiny**: $0,04 + $0,13 = $0,17


### <a name="pricing-example-group-audio-call-using-js-sdk-and-1-pstn-leg"></a>Příklad ceny: seskupení zvukového hovoru pomocí sady JS SDK a 1 nožky PSTN

Alice a Bob jsou ve volání VOIP. Bob převolalo volání Charlie na číslo PSTN Charlie, telefonní číslo USA, které začíná na `+1-425` .

- Alice použila k sestavení aplikace sadu JS SDK. Vychází po dobu 10 minut před voláním Charlie na čísle veřejné telefonní číslo.
- Jakmile Bob přeložilo volání Charlie na svém čísle veřejné telefonní číslo, tři z nich prochází po dobu dalších 10 minut.

**Výpočty nákladů**

- 2 účastníci na nožkě VoIP (Alice a Bob) z aplikace na servery komunikačních služeb x 20 minut × $0,004 na jednu stranu účastníka za minutu = $0,16
- 1 účastník na odchozích nožkách na veřejné síti (Charlie) ze serverů komunikačních služeb na telefonní číslo v USA × 10 minut × $0,013 na nohy účastníka za minutu = $0,13

Poznámka: smíšená sazba USA na `+1-425` hodnotu je $0,013. Podrobnosti najdete na následujícím odkazu: https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)

**Celkové náklady na hovor VoIP + eskalace**: $0,16 + $0,13 = $. 29


### <a name="pricing-example-a-user-of-the-communication-services-javascript-sdk-joins-a-scheduled-microsoft-teams-meeting"></a>Příklad ceny: uživatel služby Communications JavaScript SDK se připojí k plánované schůzce Microsoft Teams.

Alice je lékařem, který se účastní svého pacienta, Bob. Alice bude připojovat se k návštěvě z desktopové aplikace Teams. Bob obdrží odkaz pro připojení pomocí webu poskytovatele zdravotní péče, který se připojí ke schůzce pomocí sady SDK služby Communications Services JavaScript. Bob použije svůj mobilní telefon k zadání schůzky pomocí webového prohlížeče (iPhone s Safari). Chat bude k dispozici během virtuální návštěvy.

- Volání trvá celkem 30 minut.
- Alice a Bob se účastní celého volání. Alice zapíná své video pět minut po zahájení hovoru a sdílí obrazovku po dobu 13 minut. Bob má své video pro celé volání.
- Alice posílá pět zpráv a Bob odpoví třemi zprávami.


**Výpočty nákladů**

- 1 účastník (Bob) × 30 minut × $0,004 za účastníka za minutu = $0,12 [pro video a zvuk se účtuje stejná sazba]
- 1 účastník (Alice) × 30 minut × $0,000 na účastníka za minutu = $0,0 *.
- 1 účastník (Bob) × 3 zprávy chatu × $0,0008 = $0,0024.
- 1 účastník (Alice) × 5 zpráv chatu × $0,000 = $0,0 *.

* Účast Alice se zabývá licencí týmu. Vaše faktura Azure bude zobrazovat zprávy o minutách a chatech, které týmům mají uživatelé služby Communication Services pro vaše pohodlí, ale tyto minuty a zprávy pocházející z klienta týmů nebudou mít náklady.

**Celkové náklady na návštěvu**:
- Spojení s uživatelem pomocí komunikačních služeb JavaScript SDK: $0,12 + $0,0024 = $0,1224
- Připojování uživatelů na desktopové aplikaci Teams: $0 (zahrnuté v licencích týmu)


## <a name="chat"></a>Chat

Pomocí komunikačních služeb můžete rozšířit aplikaci o možnost odesílat a přijímat zprávy chatu mezi dvěma nebo více uživateli. Sady SDK pro chat jsou k dispozici pro jazyky JavaScript, .NET, Python a Java. [Další informace o sadách SDK najdete na této stránce](./sdk-options.md) .

### <a name="price"></a>Cena

Za každou odeslanou zprávu chatu se vám bude účtovat $0,0008.

### <a name="pricing-example-chat-between-two-users"></a>Příklad ceny: chat mezi dvěma uživateli

Geeta spustí chatovací vlákno s Emily ke sdílení aktualizace a pošle 5 zpráv. Chat bude trvat 10 minut. Geeta a Emily posílají další 15 zpráv.

**Výpočty nákladů**
- Počet odeslaných zpráv (5 + 15 + 15) × $0,0008 = $0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Příklad ceny: seskupení chatu s více uživateli

Charlie spustí chatovací vlákno se svými přáteli Casey & jednotek Jasmine k naplánování dovolené. Při současném chatování Charlie, Casey & jednotek Jasmine odesílají 20, 30 a 18 zpráv. Uvědomují si, že jejich přítel růže může být zajímat se i na cestách, aby se připojili ke konverzačnímu vláknu a aby s ní sdílel veškerou historii zpráv.

Tato zpráva se zobrazí a spustí se konverzace. V mezitím Casey získá hovor a rozhodne o tom, aby se v konverzaci později mohl zachytit. Charlie, jednotek Jasmine & růže na data o cestování a pošle další 30, 25 a 35 zpráv.

**Výpočty nákladů**

- Počet odeslaných zpráv (20 + 30 + 18 + 30 + 25 + 35) x $0,0008 = $0,1264


## <a name="telephony-and-sms"></a>Telefonie a SMS

## <a name="price"></a>Cena

Poplatky za telefonní služby se účtují po minutách, zatímco SMS se účtuje na základě jednotlivých zpráv. Ceny se určují podle typu a umístění čísla, které používáte, i cíle volání a SMS zpráv.

### <a name="telephone-number-leasing"></a>Leasing telefonního čísla

Poplatky za telefonní číslo se účtují předem a pak se opakují na základě měsíce na měsíc:

|Typ čísla   |Měsíční poplatek   |
|--------------|-----------|
|Místní (USA)     |$1/měsíc        |
|Bezplatná linka (USA) |$2/mo |


### <a name="telephone-calling"></a>Telefonní volání

Tradiční telefonní volání (volání, ke kterému dochází přes telefonickou síť v rámci veřejné sítě) je dostupné s cenami průběžných plateb pro telefonní čísla založená na USA. Cena je sazba za minutu na základě typu použitého čísla a cíle volání. V následující tabulce jsou uvedeny podrobnosti o cenách nejoblíbenějších volajících cílů. Úplný seznam cílů najdete v [seznamu s podrobnými cenami](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv) .


#### <a name="united-states-calling-prices"></a>USA ceny za volání

Následující ceny zahrnují požadavky na komunikaci a poplatky až do 30. června 2021:

|Typ čísla   |Chcete-li provést volání   |Pro příjem volání|
|--------------|-----------|------------|
|Místní     |Začíná na $0.013/min.       |$0.0085/min        |
|Bezplatná linka |$0.013/min   |$0.0220/min |

#### <a name="other-calling-destinations"></a>Jiné cíle volání

Následující ceny zahrnují požadavky na komunikaci a poplatky až do 30. června 2021:

|Volat na   |Cena za minutu|
|-----------|------------|
|Kanada     |Začíná na $0.013/min.   |
|Spojené království     |Začíná na $0,015/min.   |
|Německo     |Začíná na $0,015/min.   |
|Francie     |Začíná na $0,016/min.   |


### <a name="sms"></a>SMS

SMS nabízí ceny průběžných plateb. Cena je poplatek za zprávu na základě cíle zprávy. Zprávy mohou být zasílány pomocí telefonních čísel bez poplatků na telefonní čísla umístěná v rámci USA. Všimněte si, že místní (geografické) telefonní čísla nelze použít k zasílání zpráv SMS.

Následující ceny zahrnují požadavky na komunikaci a poplatky až do 30. června 2021:

|Země   |Odesílání zpráv|Příjem zpráv|
|-----------|------------|------------|
|USA (bez poplatků)    |$0.0075/MSG   | $0.0075/MSG |
