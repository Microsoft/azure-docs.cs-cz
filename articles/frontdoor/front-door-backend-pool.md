---
title: Fondy back-end a back-EndY branou služby Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jaké fondy back-end a back-endů jsou v popředí dveře konfigurace.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 543e237a4a8390a8ebf74d0eb2a1f4be41dcd911
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193706"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Fondy back-end a back-EndY branou služby Azure
Tento článek popisuje koncepty o tom, jak mapovat nasazení aplikace s branou služby Azure. Také vysvětluje různé podmínky v konfiguraci branou kolem back-EndY aplikací.

## <a name="backends"></a>Back-endy
Back-end je rovna instance nasazení vaší aplikace v oblasti. Branou služba podporuje Azure i mimo Azure back-EndY, takže oblasti se uplatní jenom do oblastí Azure. Kromě toho může být místní datacentrum nebo app instance v jiném cloudu.

Back-EndY Service branou odkazovat na název hostitele nebo veřejnou IP adresu vaší aplikace, která může obsluhovat požadavky klientů. Back-EndY, neměly by být zaměňovány s databázová vrstva, vrstva úložiště a tak dále. Back-EndY by měla zobrazit jako veřejný koncový bod služby back-endu aplikace. Při přidání back-endu do back-endový fond branou, je nutné přidat také následující:

- **Typ hostitele back-endu**. Typ prostředku, který chcete přidat. Přední dveře služba podporuje automatické zjišťování z vašeho back-EndY aplikací ze služby app service, cloudovou službu nebo úložiště. Pokud chcete jinému prostředku v Azure nebo dokonce i mimo Azure back-endu, vyberte **vlastního hostitele**.

    >[!IMPORTANT]
    >Během konfigurace rozhraní API není ověřit, jestli je nepřístupný z branou prostředí back-endu. Ujistěte se, že branou dosáhnout back-endu.

- **Název předplatného a back-endu hostitele**. Pokud jste nevybrali **vlastního hostitele** pro typ hostitele back-endu, vyberte svůj back-end výběrem příslušné předplatné a odpovídající název hostitele back-end v uživatelském rozhraní.

- **Hlavička hostitele back-endu**. Hodnota hlavičky hostitele posílaná back-endu pro každý požadavek. Další informace najdete v tématu [hlavičku hostitele back-endu](#hostheader).

- **Priorita**. Pokud chcete použít primární služby back-endu pro veškerý provoz přiřadíte priority váš různé back-end. Pokud jsou k dispozici primární nebo zálohování back-EndY, zadejte také zálohy. Další informace najdete v tématu [Priority](front-door-routing-methods.md#priority).

- **Váha**. Přiřadíte váhu vaše různé back-EndY za účelem distribuce provozu mezi sadu back-EndY, rovnoměrně nebo podle váhy koeficienty. Další informace najdete v tématu [váhy](front-door-routing-methods.md#weighted).

### <a name = "hostheader"></a>Hlavička hostitele back-endu

Požadavky branou předány back-endu obsahovat pole hlavičky hostitele, který back-endu používá k načtení na cílový prostředek. Hodnota tohoto pole obvykle pochází z identifikátoru URI back-endu a má hostitele a port.

Například pro webovou žádost\.contoso.com bude mít hlavičky www hostitele\.contoso.com. Pokud pomocí webu Azure portal nakonfigurovat back-endu, výchozí hodnota pro toto pole je název hostitele back-endu. Pokud back-endu je contoso-westus.azurewebsites.net na webu Azure Portal, bude vyplní automaticky hodnota hlavičky hostitele back-endu contoso westus.azurewebsites.net. Ale pokud použijete šablony Azure Resource Manageru nebo jiným způsobem bez explicitní nastavení tohoto pole, branou služby odešle příchozí název hostitele jako hodnota pro hlavičku hostitele. Pokud byl požadavek pro www\.contoso.com a back-endu je contoso-westus.azurewebsites.net, který má prázdný záhlaví pole, nastaví hlavičku hostitele branou služba jako webové\.contoso.com.

Většina back-EndY aplikací (Azure Web Apps, Blob storage a Cloud Services) vyžadují Hlavička hostitele shodovala s doménou back-endu. Hostitele front-endu, který směruje do back-endu se však použít jiný název hostitele, jako je například www\.contoso.azurefd.net.

Pokud back-endu vyžaduje hlavičku hostitele tak, aby odpovídaly názvu hostitele back-endu, ujistěte se, že hlavička hostitele back-end obsahuje název hostitele, back-endu.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurace back-endu hlavičku hostitele pro back-endu

Ke konfiguraci **hlavičku hostitele back-endu** pole v části back-endový fond back-endu:

1. Otevřete prostředek branou a vyberte back-endový fond back-end ke konfiguraci.

2. Přidáte back-endu. Pokud takto neučinili, nebo upravte nějaký existující.

3. Nastavte pole hlavičky hostitele back-endu na vlastní hodnotu nebo pole ponechte prázdné. Název hostitele pro tento požadavek se použije jako hodnota hlavičky hostitele.

## <a name="backend-pools"></a>Back-endové fondy
Back-endového fondu v popředí dveře služba odkazuje na sadu back-EndY, která přijímají provoz podobně jako u svých aplikací. Jinými slovy je logické seskupení instancí aplikace po celém světě, které přijímají stejný provoz a reakce očekávané chování. Tyto back-endů jsou nasazené v různých oblastech nebo v rámci stejné oblasti. Všechny back-EndY může být v režimu aktivní/aktivní nasazení nebo definovaná jako konfigurace aktivní/pasivní vysokou dostupnost.

Back-endový fond definuje, jak by měl být vyhodnocen různé back-EndY via sondy stavu. Také definuje, jak dojde k vyrovnávání zatížení mezi nimi.

### <a name="health-probes"></a>Sondy stavu
Služba branou odešle pravidelné požadavky sondy HTTP/HTTPS pro všechny vaše nakonfigurované back-EndY. Požadavky testu určit blízkosti a stav každého back-endového načíst vyvážit požadavky vašich koncových uživatelů. Nastavení sondy stavu pro back-endový fond definovat, jak jsme dotazování stav back-EndY aplikací. Jsou k dispozici pro následující nastavení konfigurace služby Vyrovnávání zatížení:

- **Cesta**. Adresa URL pro požadavky testu pro všechny back-endů v back-endový fond. Například pokud některý váš back-end contoso westus.azurewebsites.net a nastavení cesty k /probe/test.aspx, pak prostředí branou služby, za předpokladu, že protokol je nastavena na HTTP, odešle požadavky sondy stavu protokolu http\:/ / contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protokol**. Definuje, zda chcete odesílat požadavky sondy stavu ze branou služby pro váš back-end pomocí protokolu HTTP nebo HTTPS.

- **Interval (sekundy)**. Definuje frekvence sondy stavu služby pro váš back-end nebo intervaly, ve kterých každé prostředí branou odešle testu.

    >[!NOTE]
    >Pro rychlejší převzetí služeb při selhání nastavte interval na nižší hodnotu. Čím nižší hodnota, tím větší objem test stavu zobrazí váš back-end. Například pokud interval globálně nastavená na 30 sekund 90 branou prostředí nebo bodů POP, každý back-end se zobrazí informace o 3 až 5 testu požadavků za sekundu.

Další informace najdete v tématu [sondy stavu](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Nastavení služby Vyrovnávání zatížení
Nastavení back-endový fond Vyrovnávání zatížení definují, jak vyhodnocujeme sondy stavu. Toto nastavení určuje, zda je back-endu v pořádku nebo není v pořádku. Jsou také zkontrolujte, jak vyrovnávat zatížení provozu mezi různé back-endů v back-endový fond. Jsou k dispozici pro následující nastavení konfigurace služby Vyrovnávání zatížení:

- **Ukázkové velikosti**. Určuje, kolik ukázky sond stavu potřebujeme má zohlednit při vyhodnocování stavu back-endu.

- **Velikost vzorku úspěšné**. Definuje velikost vzorku jak už jsme zmínili, počet úspěšných ukázky potřebné k volání back-endu v pořádku. Předpokládejme například, intervalem branou stavu testu je 30 sekund, velikost vzorku je 5 a velikost vzorku úspěšné je 3. Pokaždé, když vyhodnotíme stav sondy pro back-endu, podíváme na posledních pět ukázky víc než 150 sekund (5 × 30). Alespoň tři úspěšné testy, je potřeba deklarovat back-end jako v pořádku.

- **Latence citlivosti (Další latence)**. Definuje, zda chcete, aby vstupní brána k odesílání požadavku do back-endů do rozsahu citlivosti měření latence nebo předat požadavek na nejbližší back-endu.

Další informace najdete v tématu [směrování metodu na základě nejnižší latence](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Další postup

- [Vytvoření profilu branou](quickstart-create-front-door.md)
- [Jak funguje branou](front-door-routing-architecture.md)