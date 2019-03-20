---
title: Služba Azure branou – back-end a back-endové fondy | Dokumentace Microsoftu
description: Tento článek pomůže pochopit, jaké jsou back-endu a back-endové fondy pro v konfiguraci branou.
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
ms.openlocfilehash: 228ed5c54a382db7b47d19adacf9e5db398c53ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123687"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Fondy back-end a back-EndY branou služby Azure
Tento článek vysvětluje různé pojmy týkající se mapování nasazení vaší aplikace s branou. Vysvětlíme taky zásadní rozdíly v popředí dveře konfigurace kolem back-endu aplikace význam.

## <a name="backend-pool"></a>Back-endový fond
Back-endové fondy ve službě Front Door označují sadu ekvivalentních back-endů, které můžou pro aplikaci přijímat stejný typ provozu. Jinými slovy jde o logické seskupení instancí aplikace po celém světě, které můžou přijímat stejný provoz a dokáží reagovat očekávaným způsobem. Tyto back-endů se obvykle nasazují v různých oblastech nebo v rámci stejné oblasti. Kromě toho tyto back-EndY můžou být v režimu aktivní-aktivní nasazení nebo v opačném případě může být definován jako konfiguraci aktivní/pasivní.

Back-Endového fondu také definuje, jak různé back-EndY všechny vyhodnocení pro jejich stav via sondy stavu služby a odpovídajícím způsobem jak by měla probíhat mezi back-end pro vyrovnávání zatížení.

### <a name="health-probes"></a>Sondy stavu
Přední dveře odešle pravidelné požadavky sondy HTTP/HTTPS pro všechny vaše nakonfigurované back-EndY k určení, že blízkosti a stav každého back-endového načíst vyvážit požadavky vašich koncových uživatelů. Nastavení sondy stavu pro back-endový fond definovat, jak jsme dotazování na stav back-EndY aplikací. Pro konfiguraci služby Vyrovnávání zatížení k dispozici jsou následující nastavení:

1. **Cesta**: Adresa URL cesta kde žádosti testu budou odeslány pro všechny back-endů v back-endový fond. Například pokud jedna z vašeho back-EndY `contoso-westus.azurewebsites.net` a nastavení cesty `/probe/test.aspx`, pak branou prostředí, za předpokladu, že je nastaven protokol HTTP, bude odesílat požadavky sondy stavu na http://contoso-westus.azurewebsites.net/probe/test.aspx. 
2. **Protokol**: Definuje, zda se pošlou požadavky sondy stavu z přední dveře k vaší back-endů přes protokol HTTP nebo HTTPS.
3. **Interval (sekundy)**: Toto pole definuje frekvence sondy stavu služby pro váš back-end, to znamená, intervaly, ve kterých každé prostředí branou pošle testu. Poznámka: Pokud chcete pro rychlejší převzetí služeb při selhání, nastavte toto pole na nižší hodnotu. Nicméně Čím nižší hodnota více sondu stavu svazku, který se zobrazí váš back-end. Kde získáte představu o objem dat testu bude generovat branou na váš back-end, Pojďme se na příklad. Můžeme říct, interval je nastaven na 30 sekund a jsou přibližně 90 branou prostředí nebo POP globálně. Takže každý z vašeho back-EndY dostanou přibližně o 3 až 5 testu požadavků za sekundu.

Čtení [sond stavu](front-door-health-probes.md) podrobnosti.

### <a name="load-balancing-settings"></a>Nastavení služby Vyrovnávání zatížení
Nastavení Vyrovnávání zatížení pro back-endový fond definovat jak vyhodnocujeme sondy stavu pro rozhodování o tom back-end, jako v pořádku nebo není v pořádku, a také jak musíme přenos dat mezi různé back-endů v back-endový fond Vyrovnávání zatížení. Pro konfiguraci služby Vyrovnávání zatížení k dispozici jsou následující nastavení:

1. **Ukázkové velikosti**: Tato vlastnost určuje, kolik ukázky sond stavu potřebujeme má zohlednit při vyhodnocování stavu back-endu.
2. **Velikost vzorku úspěšné**: Tato vlastnost určuje, že z velikost vzorku jak jsme vysvětlili výše, kolik ukázky potřebujeme ke kontrole úspěch volat back-end jako v pořádku. 
</br>Například Řekněme, že pro vaše branou nastavíte sondu stavu *interval* 30 sekund, *ukázkový velikost* je nastavena na "5" a *velikost vzorku úspěšné* je nastavena na "3". Poté, co tato konfigurace znamená, že pokaždé, když vyhodnocujeme sondy stavu pro back-endu, se podíváme na posledních pět ukázky, které by pokrývání uzlů poslední 150 sekund (5 * 30 s) a pouze v případě 3 nebo více těchto sond úspěšné jsme se deklarovat pozadí ukončení není v pořádku. Řekněme, že existují jenom dva testy úspěšné, a proto jsme označí back-end jako není v pořádku. Při příštím spuštění hodnocení Pokud se nám najít 3 v posledních pět testy úspěšné, pak jsme označit back-end jako v pořádku znovu.
3. **Latence citlivosti (Další latence)**: Pole citlivosti latence definuje, zda chcete, aby vstupní brána k odesílání požadavku do back-endů, které spadají do rozsahu citlivosti z hlediska měření latence nebo předání požadavku na nejbližší back-endu. Čtení [směrování metodu na základě nejnižší latence](front-door-routing-methods.md#latency) pro branou Další informace.

## <a name="backend"></a>Back-end
Back-end je ekvivalentní k instanci nasazení aplikace v oblasti. Přední dveře podporuje Azure i mimo Azure back-EndY a tak oblasti tady není omezena pouze na oblasti Azure, které ale také mohou být místní datacentrum nebo instance aplikace v některých cloudu.

Back-EndY, v rámci předních dveří, odkazuje na název hostitele nebo veřejnou IP adresu vaší aplikace, která může obsluhovat požadavky klientů. Back-EndY tak, neměly by být zaměňovány s databázovou vrstvu nebo svou úroveň úložiště atd., ale spíše zobrazovat jako veřejný koncový bod služby back-endu aplikace.

Při přidání back-endu do back-endový fond vašeho branou, je potřeba, vyplňte následující podrobnosti:

1. **Typ hostitele back-endu**: Typ prostředku, který chcete přidat. Přední dveře podporuje automatické zjišťování back-EndY aplikací z app service, cloudovou službu nebo úložiště. Pokud chcete jinému prostředku v Azure nebo dokonce i mimo Azure back-endu, vyberte možnost "Vlastního hostitele". Poznámka: během konfigurace že rozhraní API nelze ověřit, zda back-end je přístupný z prostředí branou, místo toho, že je potřeba zajistit, že back-endu může být dosažitelný podle branou. 
2. **Název předplatného a back-endu hostitele**: Pokud jste nevybrali typ vlastního hostitele pro hostitele back-end, je nutné k určení oboru dolů a vyberte svůj back-end výběrem příslušné předplatné a odpovídající název hostitele back-end z uživatelského rozhraní.
3. **Hlavička hostitele back-endu**: Hodnota hlavičky hostitele posílaná back-endu pro každý požadavek. Čtení [hlavičku hostitele back-endu](#hostheader) podrobnosti.
4. **Priorita**: Váš různé back-end můžete přiřadit priority, pokud chcete použít primární služby back-endu pro veškerý provoz a poskytují zálohování v případě, že nejsou k dispozici primární nebo zálohování back-EndY. Další informace o [priority](front-door-routing-methods.md#priority).
5. **Váha**: Váhu můžete přiřadit váš různé back-end, pokud chcete za účelem distribuce provozu mezi sadu back-EndY, rovnoměrně nebo podle váhy koeficienty. Další informace o [váhy](front-door-routing-methods.md#weighted).


### <a name = "hostheader"></a>Hlavička hostitele back-endu

Požadavků branou předány back-end obsahuje pole hlavičky hostitele, který back-endu používá k načtení na cílový prostředek. Hodnota tohoto pole obvykle pochází z identifikátoru URI back-endu a má hostitele a port. Například požadavek stanovit `www.contoso.com` bude mít Hlavička hostitele `www.contoso.com`. Pokud konfigurujete back-endu pomocí webu Azure portal, výchozí hodnotu, která naplní pro toto pole je název hostitele back-endu. Například, pokud je back-endu `contoso-westus.azurewebsites.net`, na webu Azure Portal bude automaticky vyplní hodnota hlavičky hostitele back-endu `contoso-westus.azurewebsites.net`. 
</br>Ale pokud se pomocí šablon Resource Manageru nebo jiný mechanismus a nejsou nastavení tohoto pole explicitně branou pošle příchozí název hostitele jako hodnota hlavičky hostitele. Například, pokud byl požadavek `www.contoso.com`, a back-endu je `contoso-westus.azurewebsites.net` s back-endu pole hlavičky hostitele jako prázdný, pak branou nastaví hlavičku hostitele jako `www.contoso.com`.

Většina aplikací back-EndY (jako jsou Web Apps, Blob Storage a Cloud Services) vyžadují Hlavička hostitele shodovala s doménou back-endu. Ale hostitele front-endu, který směruje do back-endu, bude mít jiný název hostitele jako www\.contoso.azurefd.net. Pokud back-end, který nastavujete vyžaduje hlavičku hostitele tak, aby odpovídaly názvu hostitele back-endu, měli byste zajistit také, že back-endu Hlavička hostitele je název hostitele back-endu.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurace back-endu hlavičku hostitele pro back-endu
Pole titele back-endu lze nakonfigurovat pro back-end v části back-endový fond.

1. Otevřete prostředek branou a klikněte na back-endový fond, který má být nakonfigurované back-endu.

2. Přidáte back-endu. Pokud jste nepřidali žádné nebo upravit existující. Pole titele back-endu můžete nastavit vlastní hodnoty nebo prázdné, což znamená, že název hostitele pro tento požadavek se použije jako hodnota hlavičky hostitele.



## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).