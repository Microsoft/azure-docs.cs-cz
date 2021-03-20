---
title: Přehled monitorování stavu pro Azure Application Gateway
description: Azure Application Gateway monitoruje stav všech prostředků v rámci fondu back-end a automaticky z fondu považuje prostředek, který je v pořádku.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: f0e5a153efe26640e54f386600f07c7b3d4711d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89649062"
---
# <a name="application-gateway-health-monitoring-overview"></a>Přehled monitorování stavu Application Gateway

Služba Azure Application Gateway ve výchozím nastavení monitoruje stav všech prostředků v rámci fondu back-endu a automaticky odebere z fondu prostředky považované za chybné. Služba Application Gateway bude v monitorování instancí, které nejsou v pořádku, pokračovat, a jakmile budou opět k dispozici a začnou reagovat na sondy stavu, přidá je zpět do backendového fondu. Ve výchozím nastavení služba Application Gateway odesílá sondy stavu se stejným portem, který je definován v nastavení back-endu protokolu HTTP. Vlastní port testu se dá nakonfigurovat pomocí vlastního testu stavu.

Zdrojová IP adresa Application Gateway použití pro sondy stavu závisí na back-end fondu:
 
- Pokud je adresa serveru ve fondu back-endu veřejným koncovým bodem, pak je zdrojovou adresou veřejná IP adresa front-endu služby Application Gateway.
- Pokud je adresa serveru v back-end fondu soukromým koncovým bodem, pak je zdrojová IP adresa z privátního adresního prostoru IP adres podsítě služby Application Gateway.

![Příklad testu služby Application Gateway][1]

Kromě používání výchozího monitorování sondy stavu můžete také přizpůsobit sondu stavu tak, aby vyhovovala požadavkům vaší aplikace. V tomto článku jsou pokryté výchozí i vlastní sondy stavu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Výchozí sonda stavu

Application Gateway automaticky nakonfiguruje výchozí sondu stavu, když nenastavíte žádnou vlastní konfiguraci sondy. Chování monitorování funguje tak, že vytvoříte požadavek HTTP GET na IP adresy nebo plně kvalifikovaný název domény nakonfigurovaný ve fondu back-end. Pokud jsou pro protokol HTTPS nakonfigurovaná nastavení http back-endu, sonda pro testování stavu back-end serverů používá protokol HTTPS.

Například: nakonfigurujete Aplikační bránu tak, aby používala back-end servery A, B a C pro příjem síťového provozu HTTP na portu 80. Výchozí monitorování stavu testuje tři servery každých 30 sekund pro fungující odpověď HTTP s časovým limitem 30 sekund pro každý požadavek. V pořádku HTTP odpověď má [stavový kód](https://msdn.microsoft.com/library/aa287675.aspx) mezi 200 a 399. V takovém případě bude požadavek HTTP GET pro sondu stavu vypadat jako http://127.0.0.1/ .

Pokud se u serveru A nezdařila výchozí kontrola sondy, aplikace služby Application Gateway přestane předávat požadavky na tento server. Výchozí sonda stále nadále kontroluje server A každých 30 sekund. Když server A odpoví úspěšně na jednu žádost z výchozího testu stavu, začne aplikace Application Gateway znovu předávat požadavky na server.

### <a name="default-health-probe-settings"></a>Výchozí nastavení sondy stavu

| Vlastnost sondy | Hodnota | Popis |
| --- | --- | --- |
| Adresa URL testu paměti |\<protocol\>://127.0.0.1:\<port\>/ |Protokol a port se dědí z nastavení HTTP back-endu, ke kterým je sonda přidružená. |
| Interval |30 |Doba v sekundách, po kterou se má čekat, než se pošle další sonda stavu.|
| Časový limit |30 |Doba v sekundách, po kterou Aplikační brána čeká na odezvu testu před označením sondy jako není v pořádku. Pokud se sonda vrátí jako v pořádku, odpovídající back-end se hned označí jako v pořádku.|
| Prahová hodnota pro poškozený stav |3 |Určuje, kolik sond se má odeslat v případě, že dojde k selhání normální sondy stavu. V SKU V1 se tyto dodatečné sondy stavu odesílají v rychlém úspěchu, aby bylo možné rychle zjistit stav back-endu a nečekat na interval sondy. V případě SKU verze 2 se sondy stavu vyčká na interval. Back-end Server je označený po po sobě jdoucí počet po sobě jdoucích selhání testu dosáhne prahové hodnoty, která není v pořádku. |

Výchozí sonda se vyhledává pouze v těchto umístěních \<protocol\> : \/ /127.0.0.1: \<port\> pro zjištění stavu. Pokud potřebujete nakonfigurovat sondu stavu tak, aby přešel na vlastní adresu URL nebo upravili jakékoli jiné nastavení, musíte použít vlastní testy. Další informace o testech HTTPS najdete v tématu [Přehled ukončení protokolu TLS a koncového šifrování TLS s Application Gateway](ssl-overview.md#for-probe-traffic).

### <a name="probe-intervals"></a>Intervaly sondy

Všechny instance Application Gateway sondují back-end nezávisle na sobě. Stejná konfigurace sondy se vztahuje na každou instanci Application Gateway. Pokud například konfigurace sondy odesílá sondy stavu každých 30 sekund a Aplikační brána má dvě instance, pak obě instance odesílají sondu stavu každých 30 sekund.

Pokud existuje více posluchačů, pak každý naslouchací proces prohledá back-end nezávisle na sobě. Například pokud existují dva naslouchací procesy ukazující na stejný back-end fond na dvou různých portech (nakonfigurovaných pomocí dvou nastavení http back-endu), pak každý naslouchací proces prohledá stejný back-end. V tomto případě existují dvě sondy z každé instance aplikační brány pro dva naslouchací procesy. V případě, že v tomto scénáři existují dvě instance aplikační brány, back-end virtuální počítač by se mohl podívat na čtyři sondy podle nakonfigurovaného intervalu sondy.

## <a name="custom-health-probe"></a>Vlastní sonda stavu

Vlastní sondy umožňují podrobnější kontrolu nad monitorováním stavu. Pokud používáte vlastní testy, můžete nakonfigurovat vlastní název hostitele, cestu k adrese URL, interval testu a počet neúspěšných odpovědí, které se mají přijmout před označením instance back-end fondu jako není v pořádku atd.

### <a name="custom-health-probe-settings"></a>Vlastní nastavení sondy stavu

Následující tabulka poskytuje definice vlastností pro vlastní sondu stavu.

| Vlastnost sondy | Popis |
| --- | --- |
| Název |Název sondy. Tento název se používá k identifikaci a odkazování testu v nastavení protokolu HTTP back-endu. |
| Protokol |Protokol použitý k odeslání testu. To se musí shodovat s protokolem definovaným v nastaveních back-endu protokolu HTTP, ke kterému je přidružená.|
| Hostitel |Název hostitele, pomocí kterého se má test odeslat. V SKU V1 se tato hodnota použije jenom pro hlavičku hostitele žádosti sondy. V SKU verze V2 se bude používat jak jako Hlavička hostitele, tak i jako SNI |
| Cesta |Relativní cesta sondy. Platná cesta začíná znakem/. |
| Port |Pokud je tato definice definovaná, použije se jako cílový port. V opačném případě používá stejný port jako nastavení protokolu HTTP, ke kterému je přidruženo. Tato vlastnost je k dispozici pouze v SKU v2.
| Interval |Interval testu paměti v sekundách. Tato hodnota představuje časový interval mezi dvěma po sobě jdoucími testy. |
| Časový limit |Časový limit testu v sekundách. Pokud v tomto časovém limitu neobdrží platná odpověď, sonda je označena jako neúspěšná.  |
| Prahová hodnota pro poškozený stav |Počet opakování testu Back-end Server je označený po po sobě jdoucí počet po sobě jdoucích selhání testu dosáhne prahové hodnoty, která není v pořádku. |

### <a name="probe-matching"></a>Porovnání sondy

Ve výchozím nastavení je odpověď HTTP (S) se stavovým kódem mezi 200 a 399 považována za v pořádku. Vlastní sondy stavu navíc podporují dvě kritéria pro porovnání. Kritéria porovnání lze použít k volitelné změně výchozího vyhodnocení toho, co způsobuje reakci v dobrém stavu.

Následující kritéria odpovídají kritériím: 

- **Shoda se stavovým kódem odpovědi HTTP** – kritérium porovnávání sondy pro přijetí kódu odpovědi HTTP nebo rozsahů kódu odpovědi na uživatele. Jsou podporovány jednotlivé stavové kódy odpovědi oddělené čárkami nebo rozsah stavového kódu.
- **Shoda těla zprávy odpovědi HTTP** – kritérium porovnávání sondy, které hledá text odpovědi HTTP a odpovídá řetězci zadaného uživatelem. Shoda hledá pouze přítomnost zadaného řetězce uživatelem v těle odpovědi a není úplný regulární výraz shodný.

Kritéria shody lze zadat pomocí `New-AzApplicationGatewayProbeHealthResponseMatch` rutiny.

Například:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Po zadání kritérií shody je lze připojit ke konfiguraci testu pomocí `-Match` parametru v prostředí PowerShell.

## <a name="nsg-considerations"></a>NSG požadavky

Je nutné, aby příchozí internetový provoz na portech TCP 65503-65534 pro SKU Application Gateway V1 a porty TCP 65200-65535 pro SKU verze 2 v2 s cílovou podsítí jako **s označením služby a jako** zdroj jako služba **GatewayManager** . Tento rozsah portů je nutný pro komunikaci s infrastrukturou Azure.

Kromě toho se nedají zablokovat odchozí připojení k Internetu a příchozí provoz, který přichází ze značky **AzureLoadBalancer** , musí být povolený.

Další informace najdete v tématu [Přehled konfigurace Application Gateway](configuration-infrastructure.md#network-security-groups).

## <a name="next-steps"></a>Další kroky
Po získání informací o Application Gateway sledování stavu můžete nakonfigurovat [vlastní sondu stavu](application-gateway-create-probe-portal.md) v Azure Portal nebo ve [vlastním testu stavu](application-gateway-create-probe-ps.md) pomocí PowerShellu a modelu nasazení Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
