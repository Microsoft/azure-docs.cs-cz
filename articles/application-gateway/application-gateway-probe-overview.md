---
title: Přehled monitorování stavu pro Azure Application Gateway
description: Azure Application Gateway monitoruje stav všech prostředků v rámci fondu back-end a automaticky z fondu považuje prostředek, který je v pořádku.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2020
ms.author: victorh
ms.openlocfilehash: c5a53167c6a4ca6c886b858a1608eaa173185bd8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335850"
---
# <a name="application-gateway-health-monitoring-overview"></a>Přehled monitorování stavu Application Gateway

Služba Azure Application Gateway ve výchozím nastavení monitoruje stav všech prostředků v rámci fondu back-endu a automaticky odebere z fondu prostředky považované za chybné. Služba Application Gateway bude v monitorování instancí, které nejsou v pořádku, pokračovat, a jakmile budou opět k dispozici a začnou reagovat na sondy stavu, přidá je zpět do backendového fondu. Application Gateway odesílá sondy stavu se stejným portem, který je definovaný v nastavení back-endu HTTP. Tato konfigurace zajišťuje, že sonda testuje stejný port, který zákazníci používají pro připojení k back-endu. 

Zdrojová IP adresa Application Gateway použití pro sondy stavu závisí na back-end fondu:
 
- Pokud je back-end fondem veřejným koncovým bodem, pak je zdrojovou adresou veřejná IP adresa front-endu služby Application Gateway.
- Pokud je back-end fondem soukromým koncovým bodem, pak zdrojová IP adresa pochází z privátního adresního prostoru IP adres podsítě aplikační brány.


![Příklad testu služby Application Gateway][1]

Kromě používání výchozího monitorování sondy stavu můžete také přizpůsobit sondu stavu tak, aby vyhovovala požadavkům vaší aplikace. V tomto článku jsou pokryté výchozí i vlastní sondy stavu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Výchozí sonda stavu

Application Gateway automaticky nakonfiguruje výchozí sondu stavu, když nenastavíte žádnou vlastní konfiguraci sondy. Chování monitorování funguje tak, že provedete požadavek HTTP na IP adresy nakonfigurované pro fond back-end. Pokud jsou pro protokol HTTPS nakonfigurovaná nastavení http back-endu, sonda používá protokol HTTPS a také stav testu back-endy.

Například: nakonfigurujete Aplikační bránu tak, aby používala back-end servery A, B a C pro příjem síťového provozu HTTP na portu 80. Výchozí monitorování stavu testuje tři servery každých 30 sekund pro fungující odpověď HTTP. V pořádku HTTP odpověď má [stavový kód](https://msdn.microsoft.com/library/aa287675.aspx) mezi 200 a 399.

Pokud se u serveru A nezdařila výchozí kontrola sondy, brána Application Gateway ji odebere z fondu back-end a síťový provoz přestane přecházet do tohoto serveru. Výchozí sonda stále nadále kontroluje server A každých 30 sekund. Když server A odpoví úspěšně na jednu žádost z výchozího testu stavu, přidá se zpátky jako v pořádku do fondu back-end a provoz se znovu začne přesměrovat na server.

### <a name="probe-matching"></a>Porovnání sondy

Ve výchozím nastavení je odpověď HTTP (S) se stavovým kódem mezi 200 a 399 považována za v pořádku. Vlastní sondy stavu navíc podporují dvě kritéria pro porovnání. Kritéria porovnání lze použít k volitelné změně výchozího vyhodnocení toho, co způsobuje reakci v dobrém stavu.

Následující kritéria odpovídají kritériím: 

- **Shoda se stavovým kódem odpovědi HTTP** – kritérium porovnávání sondy pro přijetí kódu odpovědi HTTP nebo rozsahů kódu odpovědi na uživatele. Jsou podporovány jednotlivé stavové kódy odpovědi oddělené čárkami nebo rozsah stavového kódu.
- **Shoda těla zprávy odpovědi HTTP** – kritérium porovnávání sondy, které hledá text odpovědi HTTP a odpovídá řetězci zadaného uživatelem. Shoda hledá pouze přítomnost zadaného řetězce uživatelem v těle odpovědi a není úplný regulární výraz shodný.

Kritéria shody lze zadat pomocí `New-AzApplicationGatewayProbeHealthResponseMatch` rutiny.

Příklad:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Po zadání kritérií shody je lze připojit ke konfiguraci testu pomocí `-Match` parametru v prostředí PowerShell.

### <a name="default-health-probe-settings"></a>Výchozí nastavení sondy stavu

| Vlastnost sondy | Hodnota | Popis |
| --- | --- | --- |
| Adresa URL testu paměti |http://127.0.0.1:\<port\>/ |Cesta URL |
| Interval |30 |Doba v sekundách, po kterou se má čekat, než se pošle další sonda stavu.|
| Časový limit |30 |Doba v sekundách, po kterou Aplikační brána čeká na odezvu testu před označením sondy jako není v pořádku. Pokud se sonda vrátí jako v pořádku, odpovídající back-end se hned označí jako v pořádku.|
| Prahová hodnota pro poškozený stav |3 |Určuje, kolik sond se má odeslat v případě, že dojde k selhání normální sondy stavu. Tyto dodatečné sondy stavu se odesílají v rychlém úspěchu, aby bylo možné rychle zjistit stav back-endu a nečekat na interval sondy. Tato behaivor je pouze SKU v1. V případě SKU verze 2 se sondy stavu vyčká na interval. Back-end Server je označený po po sobě jdoucí počet po sobě jdoucích selhání testu dosáhne prahové hodnoty, která není v pořádku. |

> [!NOTE]
> Port je stejný port jako nastavení back-endu protokolu HTTP.

Výchozí sonda se vyhledá jenom na http\/:/127.0.0.1\<:\> port pro určení stavu. Pokud potřebujete nakonfigurovat sondu stavu tak, aby přešel na vlastní adresu URL nebo upravili jakékoli jiné nastavení, musíte použít vlastní testy.

### <a name="probe-intervals"></a>Intervaly sondy

Všechny instance Application Gateway sondují back-end nezávisle na sobě. Stejná konfigurace sondy se vztahuje na každou instanci Application Gateway. Pokud například konfigurace sondy odesílá sondy stavu každých 30 sekund a Aplikační brána má dvě instance, pak obě instance odesílají sondu stavu každých 30 sekund.

Pokud existuje více posluchačů, pak každý naslouchací proces prohledá back-end nezávisle na sobě. Například pokud existují dva naslouchací procesy ukazující na stejný back-end fond na dvou různých portech (nakonfigurovaných pomocí dvou nastavení http back-endu), pak každý naslouchací proces prohledá stejný back-end. V tomto případě existují dvě sondy z každé instance aplikační brány pro dva naslouchací procesy. V případě, že v tomto scénáři existují dvě instance aplikační brány, back-end virtuální počítač by se mohl podívat na čtyři sondy podle nakonfigurovaného intervalu sondy.

## <a name="custom-health-probe"></a>Vlastní sonda stavu

Vlastní testy vám umožní podrobnější kontrolu nad monitorováním stavu. Pokud používáte vlastní testy, můžete nakonfigurovat interval sondy, adresu URL a cestu k otestování a počet neúspěšných odpovědí, které se mají přijmout před označením instance back-end fondu jako chybné.

### <a name="custom-health-probe-settings"></a>Vlastní nastavení sondy stavu

Následující tabulka poskytuje definice vlastností pro vlastní sondu stavu.

| Vlastnost sondy | Popis |
| --- | --- |
| Název |Název sondy. Tento název se používá k odkazování na test v nastavení back-endu protokolu HTTP. |
| Protocol (Protokol) |Protokol použitý k odeslání testu. Sonda používá protokol definovaný v nastavení back-endu HTTP. |
| Hostitel |Název hostitele, který má odeslat test. Dá se použít jenom v případě, že je na Application Gateway nakonfigurovaný vícenásobný web, jinak použijte 127.0.0.1. Tato hodnota se liší od názvu hostitele virtuálního počítače. |
| Cesta |Relativní cesta sondy. Platná cesta začíná znakem/. |
| Interval |Interval testu paměti v sekundách. Tato hodnota představuje časový interval mezi dvěma po sobě jdoucími sondami. |
| Časový limit |Časový limit testu v sekundách. Pokud v tomto časovém limitu neobdrží platná odpověď, sonda je označena jako neúspěšná.  |
| Prahová hodnota pro poškozený stav |Počet opakování testu Back-end Server je označený po po sobě jdoucí počet po sobě jdoucích selhání testu dosáhne prahové hodnoty, která není v pořádku. |

> [!IMPORTANT]
> Pokud je pro jednu lokalitu nakonfigurované Application Gateway, ve výchozím nastavení by měl být název hostitele zadaný jako 127.0.0.1, pokud není v vlastní sondě nakonfigurovaný.
> Pro odkaz na vlastní test paměti se pošle \<protokol\>://\<hostitel\>:\<cesta\>\<\>k portu. Použitý port bude stejný jako port definovaný v nastavení back-endu protokolu HTTP.

## <a name="nsg-considerations"></a>NSG požadavky

Je nutné, aby příchozí internetový provoz na portech TCP 65503-65534 pro SKU Application Gateway V1 a porty TCP 65200-65535 pro SKU verze 2 v2 s cílovou podsítí jako **s označením služby a jako** zdroj jako služba **GatewayManager** . Tento rozsah portů je nutný pro komunikaci s infrastrukturou Azure.

Kromě toho se nedají zablokovat odchozí připojení k Internetu a příchozí provoz, který přichází ze značky **AzureLoadBalancer** , musí být povolený.

Další informace najdete v tématu [Přehled konfigurace Application Gateway](configuration-overview.md#network-security-groups-on-the-application-gateway-subnet).

## <a name="next-steps"></a>Další kroky
Po získání informací o Application Gateway sledování stavu můžete nakonfigurovat [vlastní sondu stavu](application-gateway-create-probe-portal.md) v Azure Portal nebo ve [vlastním testu stavu](application-gateway-create-probe-ps.md) pomocí PowerShellu a modelu nasazení Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
