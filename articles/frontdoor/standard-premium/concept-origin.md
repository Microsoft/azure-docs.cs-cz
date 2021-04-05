---
title: Skupina původ a původ v Azure front-Premium Standard/Premium
description: Tento článek popisuje, co je původní a původní skupina v konfiguraci front-dveří Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 42a9a0ea23faa481140a46ec52b2214431dd723e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099215"
---
# <a name="origin-and-origin-group-in-azure-front-door-standardpremium-preview"></a>Původní a původní skupina v Azure front-in úrovně Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Tento článek se věnuje konceptům, jak vaše nasazení webové aplikace funguje s Azure front-Premium Standard/Premium. Dozvíte se také, co je *původní* a *původní skupina* v konfiguraci služby Azure front-Premium Standard/Premium.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="origin"></a>Zdroj

Přední dveře Azure (Standard/Premium Origin) odkazuje na název hostitele nebo veřejnou IP adresu vaší aplikace, která obsluhuje požadavky klientů. Přední dveře Azure (Standard/Premium) podporují prostředky Azure i mimo Azure ve skupině původu. Aplikace může být také hostována v místním datovém centru nebo s jiným poskytovatelem cloudu. Původ by neměl být zaměnitelný s vaší databázovou vrstvou nebo úložnou vrstvou. Počátek by měl být zobrazen jako veřejný koncový bod pro back-end aplikace. Když přidáte původ do skupiny zdrojů služby Azure front-end Standard/Premium, je nutné přidat také následující informace:

* **Typ počátku:** Typ prostředku, který chcete přidat. Přední dvířka podporují automatické zjišťování back-endu vaší aplikace z App Service, cloudové služby nebo úložiště. Pokud chcete jiný prostředek v Azure nebo dokonce back-end, vyberte **vlastní hostitel**.

    >[!IMPORTANT]
    >Při konfiguraci rozhraní API neověřuje, jestli zdroj není přístupný z prostředí front-dveří. Ujistěte se, že přední dveře můžou dosáhnout svého původu.

* **Název hostitelského a zdrojového hostitele:** Pokud jste nevybrali možnost **vlastní hostitel** pro typ hostitele back-end, vyberte back-end tak, že vyberete příslušné předplatné a odpovídající název hostitele back-endu.

* **Hlavička počátečního hostitele:** Hodnota hlavičky hostitele, která se pošle back-endu pro každý požadavek. Další informace najdete v části [záhlaví hostitele původu](#hostheader).

* **Priorita:** Pokud chcete použít primární původ pro veškerý provoz, přiřaďte priority k vašemu jinému zdroji. Dodejte také zálohy, pokud nejsou k dispozici primární nebo záložní původ. Další informace najdete v tématu [Priorita](#priority).

* **Váha:** Přiřaďte váhy k vašemu jinému zdroji pro distribuci provozu napříč sadou původních míst, a to buď rovnoměrně, nebo podle váhy. Další informace najdete v tématu [váhy](#weighted).

### <a name="origin-host-header"></a><a name = "hostheader"></a>Hlavička počátečního hostitele

Požadavky, které jsou předávány službou Azure front-Premium Standard/Premium na počátek, budou zahrnovat pole hlavičky hostitele, které zdroj používá k načtení cílového prostředku. Hodnota tohoto pole obvykle pochází z identifikátoru URI zdroje, který má hlavičku a port hostitele.

Například žádost, která byla vytvořena pro, `www.contoso.com` bude mít hlavičku hostitele `www.contoso.com` . Pokud ke konfiguraci původu použijete Azure Portal, výchozí hodnota tohoto pole je název hostitele back-endu. Pokud je váš původ `contoso-westus.azurewebsites.net` v Azure Portal, automaticky vyplněná hodnota pro hlavičku počátečního hostitele bude `contoso-westus.azurewebsites.net` . Pokud však použijete šablony Azure Resource Manager nebo jinou metodu bez explicitního nastavení tohoto pole, odešle fronta název příchozího hostitele jako hodnotu pro hlavičku hostitele. Pokud se žádost nastavila `www.contoso.com` a váš původ `contoso-westus.azurewebsites.net` má prázdné pole hlavičky, nastaví se na začátku v hlavičce hostitele `www.contoso.com` .

Většina back-endy aplikace (Azure Web Apps, BLOB Storage a Cloud Services) vyžaduje, aby Hlavička hostitele odpovídala doméně back-endu. Nicméně hostitel s front-end, který směruje na váš back-end, bude používat jiný název hostitele, jako je například `www.contoso.net` .

Pokud váš původ vyžaduje, aby Hlavička hostitele odpovídala názvu hostitele back-endu, ujistěte se, že Hlavička hostitele back-endu obsahuje název hostitele back-endu.

#### <a name="configuring-the-origin-host-header-for-the-origin"></a>Konfigurace původní hlavičky hostitele pro původní

Chcete-li konfigurovat pole **hlavičky zdrojového hostitele** pro počátek v části Skupina původních:

1. Otevřete prostředek front-dveří a vyberte skupinu původu s počátkem, který chcete konfigurovat.

2. Přidejte počátek, pokud jste to neudělali, nebo upravte existující.

3. Nastavte pole hlavičky zdrojového hostitele na vlastní hodnotu nebo nechte prázdné. Název hostitele příchozího požadavku bude použit jako hodnota hlavičky hostitele.

## <a name="origin-group"></a>Skupina původu

Skupina původu v Azure front-end Standard/Premium odkazuje na sadu zdrojů, které pro svoji aplikaci přijímají podobný provoz. Jinými slovy je logické seskupení instancí vaší aplikace po celém světě, které obdrží stejný provoz a reagovat s očekávaným chováním. Tyto počáteky je možné nasadit v různých oblastech nebo ve stejné oblasti. Všechny zdroje můžou být v režimu aktivního/aktivního nasazení nebo jaké jsou definované jako aktivní nebo pasivní konfigurace.

Skupina původu definuje, jak se mají vyhodnocovat zdroje prostřednictvím sond stavu. Definuje také, jak mezi nimi dochází k vyrovnávání zatížení.

### <a name="health-probes"></a>Sondy stavu

Přední dveře Azure (Standard/Premium) odesílají pravidelné zkušební požadavky HTTP/HTTPS každému nakonfigurovanému zdroji. Požadavky na testování určují vzdálenost a stav jednotlivých zdrojů k vyrovnávání zatížení vašich požadavků koncových uživatelů. Nastavení sond stavu pro skupinu původu definují, jak se dotazuje na stav back-endu aplikace. Pro konfiguraci vyrovnávání zatížení jsou k dispozici následující nastavení:

* **Cesta**: adresa URL, která se používá k testování požadavků pro všechny zdroje ve skupině původu. Například pokud je jeden z vašich zdrojů `contoso-westus.azurewebsites.net` a cesta je nastavená na/PROBE/test.aspx, pak na front-dvířková prostředí za předpokladu, že protokol je http, pošle požadavky sondy stavu na `http://contoso-westus.azurewebsites.net/probe/test.aspx` .

* **Protokol**: Určuje, jestli se mají odesílat požadavky sondy stavu z předních dveří do vašich původních dat pomocí protokolu HTTP nebo HTTPS.

* **Metoda**: metoda HTTP, která se má použít pro odesílání sond stavu. Mezi možnosti patří GET nebo HEAD (výchozí).
    > [!NOTE]
    > V případě nižšího zatížení a nákladů na back-endy doporučuje přední dveře použití požadavků hlav pro sondy stavu.

* **Interval (sekundy)**: definuje četnost sond stavu pro vaše zdroje, nebo intervaly, ve kterých každé z prostředí front-dveří odesílá test.

    >[!NOTE]
    >V případě rychlejšího převzetí služeb při selhání nastavte interval na nižší hodnotu. Čím nižší hodnota, tím vyšší je hlasitost sondy stavu, kterou vaše back-endy obdrží. Pokud je například interval nastaven na 30 sekund s vyslovením, 100 přední dvířka (pop) se zobrazí globálně, každý back-end bude dostávat asi 200 zkušebních požadavků za minutu.

Další informace najdete v tématu [sondy stavu](concept-health-probes.md).

### <a name="load-balancing-settings"></a>Nastavení vyrovnávání zatížení

Nastavení vyrovnávání zatížení pro skupinu původu definuje, jak vyhodnocujeme sondy stavu. Tato nastavení určují, jestli je původ v pořádku nebo není v pořádku. Také kontrolují, jak vyrovnávat zatížení provozu mezi různými zdroji v původní skupině. Pro konfiguraci vyrovnávání zatížení jsou k dispozici následující nastavení:

* **Velikost vzorku:** Určuje, kolik ukázek sond stavu musíme zvážit pro vyhodnocování stavu původu.

* **Úspěšná velikost vzorku:** Definuje velikost vzorku, jak je uvedeno výše, počet úspěšných vzorků potřebných k volání zdroje v pořádku. Předpokládejme například, že interval sondy stavů front je 30 sekund, velikost vzorku je 5 a úspěšná velikost vzorku je 3. Pokaždé, když vyhodnocujeme sondy stavu pro váš původ, podíváme se na posledních pět vzorků za více než 150 sekund (5 × 30). K deklaraci původu jako v pořádku se vyžadují aspoň tři úspěšné sondy.

* **Citlivost latence (mimořádná latence):** Definuje, jestli chcete, aby přední dveře Azure (Standard/Premium) odesílala požadavek na počátek v rozsahu citlivosti měření latence, nebo požadavek předá do nejbližšího back-endu.

Další informace najdete v tématu [minimální Metoda směrování na základě latence](#latency).

## <a name="routing-methods"></a>Metody směrování

Přední dveře Azure (Standard/Premium) podporují různé druhy metod směrování provozu, které určují, jak směrovat provoz HTTP/HTTPS do různých koncových bodů služby. Když si klient vyžádá připojení k frontě, použije se nakonfigurovaná Metoda směrování, aby se zajistilo, že se požadavky předají do nejlepší instance back-endu. 

Ve frontě Azure na úrovni Standard/Premium jsou dostupné čtyři metody směrování provozu:

* **[Latence](#latency):** Směrování na základě latence zajišťuje, aby se požadavky odesílaly do nejnižší latence back-endu přijatelné v rozsahu citlivosti. V podstatě se požadavky uživatelů odesílají do nejbližší sady back-endu v souvislosti s latencí sítě.
* **[Priorita](#priority):** Pokud chcete nakonfigurovat primární back-end pro provoz všech přenosů, můžete přiřadit priority k back-endu. Sekundární back-end může být záloha pro případ, že primární back-end nebude k dispozici.
* **[Vážená](#weighted):** Váhy můžete přiřadit k back-endu, pokud chcete distribuovat provoz napříč sadou back-endu. Bez ohledu na to, zda chcete rovnoměrně rozmístit nebo podle váhy.

Všechny konfigurace front-endu Azure Standard a Premium zahrnují monitorování stavu back-endu a automatizovaného okamžitého automatického převzetí služeb při selhání. Další informace najdete v tématu [monitorování back-endu](concept-health-probes.md). Vaše přední dveře můžou pracovat na základě jediné metody směrování. V závislosti na potřebách vaší aplikace můžete také kombinovat více metod směrování a vytvořit tak optimální topologii směrování.

### <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Nejnižší provoz na základě latencí – směrování

Nasazení back-endu ve dvou nebo více umístěních po celém světě může zlepšit rychlost odezvy vašich aplikací směrováním provozu do cílového umístění, které je nejblíže koncovým uživatelům. Výchozí metoda směrování provozu pro konfiguraci front-dveří přesměruje požadavky od koncových uživatelů do nejbližšího back-endu prostředí front-endu, které obdrží požadavek. Tento přístup se v kombinaci s architekturou libovolného vysílání pro přední vrátka Azure zajišťuje tím, že každý z vašich koncových uživatelů získá maximální individuální přizpůsobení výkonu na základě jejich umístění.

Back-end není nutně nejbližší k měření geografickou vzdáleností. Místo toho určuje nejbližší back-endy na základě měření latence sítě.

Níže je uveden celkový rozhodovací tok:

| Dostupná back-endy | Priorita | Signál latence (na základě sondy stavu) | Váhu |
|-------------| ----------- | ----------- | ----------- |
| Nejdřív vyberte všechny back-endy, které jsou povolené a vracené v pořádku (200 OK) pro sondu stavu. Pokud existuje šest back-endy a, B, C, D, E a F a mezi nimi není v pořádku a E je zakázaná. Seznam dostupných back-endu je A, B, D a F.  | V dalším kroku jsou vybrána nejvyšší priorita back-endy mezi dostupnými. Pokud má back-end A, B a D prioritu 1 a back-end F má prioritu 2. Vybrané back-endy pak budou a, B a D.| Vyberte back-endy s rozsahem latence (nejnižší citlivost & latence v MS). Pokud je back-end A 15 MS, B je 30 MS a 60 D v prostředí front-endu, kde se vyložila žádost, a citlivost latence je 30 ms, pak nejnižší fond latencí se skládá z back-endu a a B, protože D je více než 30 ms od nejbližšího back-endu, který je. | A konečně, přední dvířka dokončí dotazování provozu mezi konečným vybraným fondem back-endu v poměru určených vah. Řekněme, že pokud back-end A má váhu 5 a back-endu B s váhou 8, provoz se rozdělí v poměru 5:8 mezi back-endy a a B. |

>[!NOTE]
> Ve výchozím nastavení je vlastnost citlivosti na latenci nastavená na 0 MS, tj. vždycky předejte požadavek na nejrychlejší dostupný back-end.

### <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Provoz založený na prioritách – směrování

Organizace často chce poskytovat vysokou dostupnost pro své služby nasazením více než jedné zálohovací služby pro případ, že primární verze bude ukončena. V celém odvětví se tato topologie označuje také jako aktivní/pohotovostní nebo aktivní/pasivní topologie nasazení. Metoda "Priorita" přenosu dat umožňuje zákazníkům Azure snadno implementovat tento vzor převzetí služeb při selhání.

Výchozí přední dveře obsahují seznam back-endu s stejnou prioritou. Ve výchozím nastavení odesílají přední dveře provoz pouze do back-endu nejvyšší priority (nejnižší hodnota priority), která je primární sadou back-endu. Pokud primární back-endy není k dispozici, přední dveře směrují provoz do sekundární sady back-endu (druhá nejnižší hodnota priority). Pokud primární i sekundární back-endy nejsou k dispozici, provoz směřuje na třetí atd. Dostupnost back-endu vychází z nakonfigurovaného stavu (povoleného nebo zakázaného) a stavu probíhajícího back-endu, který je stanovený sondou stavu.

#### <a name="configuring-priority-for-backends"></a>Konfigurace priority pro back-endy

Každý back-end ve fondu back-end v konfiguraci frontových dveří má vlastnost s názvem priority, která může být číslo mezi 1 a 5. S předními dvířky Azure nakonfigurujete prioritu back-endu explicitně pomocí této vlastnosti pro každý back-end. Tato vlastnost je hodnota v rozmezí od 1 do 5. Nižší hodnoty reprezentují vyšší prioritu. Back-endy můžou sdílet prioritní hodnoty.

### <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Vážená Metoda směrování provozu
Metoda "váženého" přenosu dat umožňuje distribuci provozu rovnoměrně nebo pro použití předem definovaného vážení.

V rámci vážené metody směrování provozu přiřadíte každému back-endu váhu v konfiguraci front back-endu. Váha je celé číslo od 1 do 1000. Tento parametr používá výchozí váhu "50".

Díky seznamu dostupných back-endy, které mají přijatelnou citlivost na latenci, se provoz distribuuje pomocí mechanizmu kruhového dotazování s využitím určeného poměru vah. Pokud se citlivost latence nastaví na 0 milisekund, tato vlastnost se neprojeví, pokud neexistují dvě back-endy se stejnou latencí sítě. 

Vážená metoda umožňuje několik užitečných scénářů:

* **Postupný upgrade aplikace**: poskytuje procentuální podíl provozu na nový back-end a postupně zvyšují zatížení v čase, aby se vynesla hodnota s jinými back-endy.
* **Migrace aplikace do Azure**: Vytvořte back-end fond s Azure i s externími back-endy. Upravte váhu back-endy tak, aby dávala nové back-endy. Tuto možnost můžete kdykoli nastavit počínaje tím, že zaškrtnete nové back-endy a pak jim přiřadíte nejnižší váhu, pomalu ji zvýšíte na úrovně, kde přebírají většinu provozu. Nakonec můžete zakázat méně upřednostňovaná back-endy a odebrat je z fondu.  
* Rozšiřování **cloudů pro další kapacitu**: rychlé rozšíření místního nasazení do cloudu tak, že ho umístíte za přední dveře. Pokud potřebujete dodatečnou kapacitu v cloudu, můžete přidat nebo povolit další back-endy a určit, jakou část provozu přechází na jednotlivé back-end.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [vytvořit frontu Standard/Premium](create-front-door-portal.md) .
