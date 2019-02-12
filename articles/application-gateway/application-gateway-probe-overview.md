---
title: Přehled služby Azure Application Gateway monitorování stavu
description: Další informace o možnostech monitorování ve službě Azure Application Gateway
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 8/6/2018
ms.author: victorh
ms.openlocfilehash: 884775fc2783256d9fff43e8bc6b26cc4f638648
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998616"
---
# <a name="application-gateway-health-monitoring-overview"></a>Přehled monitorování stavu Application Gateway

Ve výchozím nastavení služba Azure Application Gateway monitoruje stav všech prostředků v jeho back endovém fondu a automaticky odstraní všechny prostředky z fondu považoval za poškozený. Služba Application Gateway pokračuje v monitorování instance v nedobrém stavu a přidá je zpět do fondu back-end v pořádku, jakmile budou k dispozici a reagují na sondy stavu. Služba Application gateway odesílá že sondy stavu se stejný port, který je definován v nastavení HTTP back-end. Tato konfigurace zajistí, že test je testování stejný port, který by se zákazníky, kteří využívají pro připojení k back-endu.

![Příkladem testu Application gateway][1]

Kromě používání výchozího stavu testu monitorování, můžete také přizpůsobit sondu stavu, aby odpovídala požadavkům vaší aplikace. V tomto článku se vztahují výchozích a vlastních testů stavu paměti.

## <a name="default-health-probe"></a>Výchozí kontroly stavu

Službu application gateway automaticky nakonfiguruje výchozí kontroly stavu, pokud nenastavíte žádnou konfiguraci vlastní test paměti. Monitorování chování funguje tak, že požadavek HTTP na IP adresy nakonfigurované pro back endový fond. Pro výchozí sondy Pokud nastavení http back-end jsou nakonfigurovány pro protokol HTTPS, sondy používá protokol HTTPS a testování stavu back-EndY.

Příklad: Nakonfigurujete vaše brána application gateway pomocí back-end serverů A, B a C přijímat síťový provoz protokolu HTTP na portu 80. Monitorování stavu výchozí testuje tři servery každých 30 sekund pro v dobrém stavu odpovědi HTTP. Je v dobrém stavu odpovědi HTTP [stavový kód](https://msdn.microsoft.com/library/aa287675.aspx) mezi 200 a 399.

Pokud selže server A kontrola testu výchozí, application gateway, odebere ho z jeho back endového fondu a síťový provoz přestanou přicházet do tohoto serveru. Výchozí kontroly i nadále pokračuje v kontrole pro server každých 30 sekund. Když serveru A odpoví na žádost o jeden z výchozí kontroly stavu úspěšně, je přidána zpět jako v pořádku do back endového fondu a začne provoz směřující do serveru znovu.

### <a name="probe-matching"></a>Porovnávání pro zjišťování

Ve výchozím nastavení je považován za v pořádku odpověď HTTP (S) se stavovým kódem 200. Vlastních testů stavu také podporují dvě kritéria přiřazování. Volitelně můžete změnit výchozí výklad o tom, co v dobrém stavu odpovědi lze použít odpovídající kritériím.

Následující jsou odpovídající kritériím: 

- **Shoda kód stavu odpovědi HTTP** - test kritéria pro příjem přiřazování zadané uživatelem, které http odpovědi kódu nebo odpovědi kódu rozsahů. Stavové kódy odezvy jednotlivých čárkami nebo rozsah stavový kód je podporován.
- **Porovnání textu odpovědi HTTP** – test paměti, že vyhledá v textu odpovědi HTTP a shody s uživatelem zadaný řetězec kritéria přiřazování. Pouze vyhledá shodu přítomnost uživatele zadaného řetězce v textu odpovědi a není úplné regulárního výrazu.

Kritéria shody se dá nastavit pomocí `New-AzureRmApplicationGatewayProbeHealthResponseMatch` rutiny.

Příklad:

```powershell
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Jakmile je zadaná kritéria shody, může být připojen testovat pomocí konfigurace `-Match` parametr v prostředí PowerShell.

### <a name="default-health-probe-settings"></a>Výchozí nastavení sondy stavu

| Vlastnosti testu | Hodnota | Popis |
| --- | --- | --- |
| Adresa URL testu |http://127.0.0.1:\<port\>/ |Cesta URL |
| Interval |30 |Množství času během několika sekund se má čekat před dalším sondu stavu se odesílají.|
| Časový limit |30 |Množství času v sekundách application gateway čeká na odpověď testu před označením testu jako není v pořádku. Pokud test vrátí jako v pořádku, odpovídající back-endu okamžitě označen jako v pořádku.|
| Prahová hodnota špatného stavu |3 |Určuje, kolik testy k odeslání v případě, že dojde k selhání sondy stavu regulárních. Tyto testy další stavu se odesílají rychle po sobě do rychle určit stav back-endu a nečekat na interval testu. Back endového serveru je označena po počet selhání testu po sobě jdoucích dosáhne prahová hodnota špatného stavu. |

> [!NOTE]
> Port, který je stejný port jako nastavení HTTP back-end.

Výchozí kontroly zabývá pouze http://127.0.0.1:\<port\> k určení stavu. Pokud je potřeba nakonfigurovat sondu stavu přejděte na vlastní adresu URL nebo upravovat ostatní nastavení, musíte použít vlastní sondy.

### <a name="probe-intervals"></a>Intervaly testu

Všechny instance služby Application Gateway probe back-endu nezávisle na sobě navzájem. Ke každé instanci Application Gateway se vztahuje stejnou konfiguraci sondy. Například pokud je konfigurace testu k odeslání sond stavu každých 30 sekund a application gateway má dvě instance, pak obě instance odeslat sondu stavu každých 30 sekund.

Také pokud existuje víc naslouchacích procesů, pak každý naslouchací proces sondy back-endu nezávisle na sobě navzájem. Například pokud existují dva naslouchací procesy odkazující na stejném back-endový fond na dva různé porty (nakonfiguroval dvě nastavení http back-end) pak každý naslouchací proces sondy stejný back-end nezávisle na sobě. V tomto případě existují dva testy z každé instance služby application gateway pro dva naslouchací procesy. Pokud existují dvě instance služby application gateway v tomto scénáři, back-endový virtuální počítač zobrazí čtyři sond za interval nakonfigurované testu.

## <a name="custom-health-probe"></a>Sonda stavu vlastní

Vlastní sondy vám umožní mít podrobnější kontrolu nad monitorování stavu. Pokud používáte vlastní sondy, můžete nakonfigurovat interval testu, adresu URL a cesty k testování a počet neúspěšných odpovědí tak, aby přijímal před označením instance back endovém fondu jako není v pořádku.

### <a name="custom-health-probe-settings"></a>Nastavení testu pro vlastní stavu

Následující tabulka obsahuje definice pro vlastnosti sondu stavu vlastní.

| Vlastnosti testu | Popis |
| --- | --- |
| Název |Název testu. Tento název se používá k odkazování na test v nastavení HTTP back-end. |
| Protocol (Protokol) |Protokol používaný k posílání sondy. Test paměti používá protokol definované v nastavení HTTP back-end |
| Hostitel |Název hostitele k odeslání testu. Použít pouze v případě více webů je nakonfigurovaná ve službě Application Gateway, v opačném případě použijte "127.0.0.1". Tato hodnota se liší od názvu hostitele virtuálního počítače. |
| Cesta |Relativní cesta testu. Platná cesta začíná od "/". |
| Interval |Interval testu paměti v sekundách. Tato hodnota je časový interval mezi dvěma po sobě jdoucích sondy. |
| Časový limit |Časový limit testu v sekundách. Pokud není přijetí platné odpovědi během tohoto období časového limitu testu označen jako neúspěšný.  |
| Prahová hodnota špatného stavu |Počet opakování testu. Back endového serveru je označena po počet selhání testu po sobě jdoucích dosáhne prahová hodnota špatného stavu. |

> [!IMPORTANT]
> Pokud služba Application Gateway je nakonfigurována pro jednu lokalitu, ve výchozím nastavení hostitele název musí být zadán jako "127.0.0.1", pokud nebudou jinak nakonfigurovaná v vlastní test paměti.
> Pro srovnání je odesílat vlastní test paměti \<protokol\>://\<hostitele\>:\<port\>\<cesta\>. Port používaný bude stejný port, jak jsou definovány v nastavení HTTP back-end.

## <a name="nsg-considerations"></a>Důležité informace o NSG

Pokud se podsítě služby application gateway je skupina zabezpečení sítě (NSG), rozsahy portů 65503 65534 musí být otevřen na podsítě služby application gateway pro příchozí provoz. Tyto porty jsou povinné pro stav back-endu rozhraní API pro práci.

Kromě toho nelze blokovat odchozí připojení k Internetu a musí být povolené příchozí provoz přicházející z značka AzureLoadBalancer.

## <a name="next-steps"></a>Další postup
Po získání informací o monitorování stavu Application Gateway, můžete nakonfigurovat [sondu stavu vlastní](application-gateway-create-probe-portal.md) na webu Azure Portal nebo [sondu stavu vlastní](application-gateway-create-probe-ps.md) pomocí Powershellu a Azure Resource Manageru model nasazení.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
