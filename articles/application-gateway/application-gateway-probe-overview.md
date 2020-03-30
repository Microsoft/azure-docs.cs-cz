---
title: Přehled monitorování stavu pro Azure Application Gateway
description: Azure Application Gateway monitoruje stav všech prostředků v jeho back-endfondu a automaticky odebere všechny prostředky, které jsou považovány za není v pořádku z fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2020
ms.author: victorh
ms.openlocfilehash: c5a53167c6a4ca6c886b858a1608eaa173185bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335850"
---
# <a name="application-gateway-health-monitoring-overview"></a>Přehled monitorování stavu brány aplikace

Azure Application Gateway ve výchozím nastavení monitoruje stav všech prostředků v jeho back-endfondu a automaticky odebere všechny prostředky, které jsou považovány za není v pořádku z fondu. Služba Application Gateway bude v monitorování instancí, které nejsou v pořádku, pokračovat, a jakmile budou opět k dispozici a začnou reagovat na sondy stavu, přidá je zpět do backendového fondu. Aplikační brána odešle sondy stavu se stejným portem, který je definován v nastavení http back-endu. Tato konfigurace zajišťuje, že sonda testuje stejný port, který by zákazníci používali pro připojení k back-endu. 

Zdrojová IP adresa, kterou aplikační brána používá pro sondy stavu, závisí na back-endovém fondu:
 
- Pokud back-endový fond je veřejný koncový bod, pak zdrojová adresa je front-endové veřejné IP adresy aplikační brány.
- Pokud je back-endový fond privátní koncový bod, zdrojová ADRESA IP pochází z privátního ip adresního prostoru podsítě aplikační brány.


![příklad sondy aplikační brány][1]

Kromě použití výchozího monitorování sondy stavu můžete také přizpůsobit sondu stavu tak, aby vyhovovala požadavkům vaší aplikace. V tomto článku jsou pokryty výchozí a vlastní sondy stavu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Výchozí sonda stavu

Aplikační brána automaticky nakonfiguruje výchozí sondu stavu, když nenastavíte žádnou vlastní konfiguraci sondy. Chování monitorování funguje tak, že požadavek HTTP na adresy IP nakonfigurované pro fond back-end. Pro výchozí sondy, pokud jsou nastavení http back-endu nakonfigurováno pro protokol HTTPS, sonda používá také protokol HTTPS k testování stavu back-endů.

Například: Nakonfigurujete aplikační bránu tak, aby používala servery a, B a C pro příjem síťového provozu HTTP na portu 80. Výchozí monitorování stavu testuje tři servery každých 30 sekund pro odpověď HTTP v pořádku. V pořádku http odpověď má [stavový kód](https://msdn.microsoft.com/library/aa287675.aspx) mezi 200 a 399.

Pokud se pro server A nezdaří výchozí kontrola sondy, odebere ji aplikační brána ze svého back-endového fondu a síťový provoz přestane proudit na tento server. Výchozí sonda stále pokračuje v kontrole serveru A každých 30 sekund. Když server A úspěšně reaguje na jeden požadavek z výchozí ho sondy stavu, přidá se zpět jako v pořádku do back-endového fondu a provoz začne znovu proudit na server.

### <a name="probe-matching"></a>Shoda sondy

Ve výchozím nastavení je odpověď HTTP(S) se stavovým kódem mezi 200 a 399 považována za v pořádku. Vlastní sondy stavu navíc podporují dvě kritéria párování. Odpovídající kritéria lze volitelně upravit výchozí interpretaci co dělá v pořádku odpověď.

Následující kritéria jsou odpovídající: 

- **Shoda kódu stavu odpovědi HTTP** – kritérium přiřazování sondy pro přijetí kódu http nebo rozsahů kódu odpovědi zadaného uživatelem. Jsou podporovány individuální kódy stavu odezvy oddělené čárkami nebo rozsah stavového kódu.
- **Shoda těla odpovědi HTTP** – kritérium přiřazování sondy, která se dívá na tělo odpovědi HTTP a shoduje se s uživatelem zadaným řetězcem. Shoda hledá pouze přítomnost uživatelem zadaného řetězce v těle odpovědi a není úplnou shodou regulárních výrazů.

Kritéria shody lze `New-AzApplicationGatewayProbeHealthResponseMatch` zadat pomocí rutiny.

Například:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Jakmile jsou zadána kritéria shody, lze je připojit `-Match` ke konfiguraci sondy pomocí parametru v prostředí PowerShell.

### <a name="default-health-probe-settings"></a>Výchozí nastavení sondy stavu

| Vlastnost sondy | Hodnota | Popis |
| --- | --- | --- |
| Adresa URL sondy |http://127.0.0.1:\<port\>/ |Cesta url |
| Interval |30 |Doba v sekundách čekání před odesláním další sondy stavu.|
| Časový limit |30 |Doba v sekundách, po kterou brána aplikace čeká na odpověď sondy, než označí sondu jako nefunkční. Pokud se sonda vrátí jako v pořádku, odpovídající back-end je okamžitě označen jako v pořádku.|
| Prahová hodnota pro poškozený stav |3 |Určuje, kolik sond má být odesláno pro případ selhání běžné zdravotní sondy. Tyto další sondy stavu jsou odesílány v rychlém sledu k určení stavu back-endu rychle a nečekejte na interval sondy. Tento behaivor je pouze v1 SKU. V případě v2 Skladové položky sondy stavu čekat interval. Server back-end je označen dolů po sobě jdoucích počet selhání sondy dosáhne prahovou hodnotu není v pořádku. |

> [!NOTE]
> Port je stejný port jako nastavení http back-endu.

Výchozí sonda hledá pouze na\/http: /127.0.0.1:\<port\> k určení stavu. Pokud potřebujete nakonfigurovat sondu stavu tak, aby přecvála na vlastní adresu URL nebo změnila jiná nastavení, je nutné použít vlastní sondy.

### <a name="probe-intervals"></a>Intervaly sondy

Všechny instance application gateway sonda back-end nezávisle na sobě. Stejná konfigurace sondy platí pro každou instanci aplikační brány. Například pokud konfigurace sondy je odeslat sondy stavu každých 30 sekund a aplikační brána má dvě instance, pak obě instance odeslat sondu stavu každých 30 sekund.

Také pokud existuje více naslouchacích procesy, pak každý naslouchací proces sondy back-endu nezávisle na sobě. Například pokud existují dva naslouchací procesy směřující do stejného back-endového fondu na dvou různých portech (nakonfigurované dvěma nastaveními http back-endu), pak každý naslouchací proces sondy stejný back-end nezávisle. V tomto případě existují dvě sondy z každé instance brány aplikace pro dva naslouchací procesy. Pokud existují dvě instance brány aplikace v tomto scénáři, back-endový virtuální počítač uvidí čtyři sondy na nakonfigurovaný interval sondy.

## <a name="custom-health-probe"></a>Vlastní sonda stavu

Vlastní sondy umožňují mít podrobnější kontrolu nad monitorování stavu. Při použití vlastních sond můžete nakonfigurovat interval sondy, adresu URL a cestu k testování a počet neúspěšných odpovědí, které chcete přijmout před označením instance fondu back-end jako není v pořádku.

### <a name="custom-health-probe-settings"></a>Vlastní nastavení sondy stavu

Následující tabulka obsahuje definice vlastností vlastní sondy stavu.

| Vlastnost sondy | Popis |
| --- | --- |
| Name (Název) |Název sondy. Tento název se používá k odkazování na sondu v nastavení http back-endu. |
| Protocol (Protokol) |Protokol použitý k odeslání sondy. Sonda používá protokol definovaný v nastavení http back-endu. |
| Hostitel |Název hostitele pro odeslání sondy. Platí pouze v případě, že je v application gateway nakonfigurováno více zařízení, jinak použijte "127.0.0.1". Tato hodnota se liší od názvu hostitele virtuálního montova. |
| Cesta |Relativní cesta sondy. Platná cesta začíná od '/'. |
| Interval |Interval sondy v sekundách. Tato hodnota je časový interval mezi dvěma po sobě jdoucími sondami. |
| Časový limit |Časový čas sondy v sekundách. Pokud není přijata platná odpověď v tomto časovém období, sonda je označena jako neúspěšná.  |
| Prahová hodnota pro poškozený stav |Počet opakování sondy. Server back-end je označen dolů po sobě jdoucích počet selhání sondy dosáhne prahovou hodnotu není v pořádku. |

> [!IMPORTANT]
> Pokud je aplikační brána nakonfigurována pro jednu lokalitu, měl by být ve výchozím nastavení název hostitele zadán jako "127.0.0.1", pokud není ve vlastní sondě nakonfigurováno jinak.
> Pro referenci je vlastní \<sonda\>\<odeslána\>\<do\>protokolu :// host\>:\<cesta k portu . Použitý port bude stejný port, jak je definovánv nastavení http back-endu.

## <a name="nsg-considerations"></a>Důležité informace o nevládních tahos

Příchozí internetový provoz na portech TCP 65503-65534 je nutné povolit pro skladovou položku SKU brány aplikace v1 a porty TCP 65200-65535 pro skladovou položku v2 s cílovou podsítí jako **libovolnou** a zdroj jako značku služby **GatewayManager.** Tento rozsah portů je vyžadován pro komunikaci infrastruktury Azure.

Navíc odchozí připojení k Internetu nelze blokovat a příchozí provoz přicházející ze značky **AzureLoadBalancer** musí být povolena.

Další informace naleznete v tématu [Přehled konfigurace aplikační brány](configuration-overview.md#network-security-groups-on-the-application-gateway-subnet).

## <a name="next-steps"></a>Další kroky
Po zjištění o monitorování stavu aplikační brány můžete nakonfigurovat [vlastní sondu stavu](application-gateway-create-probe-portal.md) na webu Azure portal nebo vlastní sondu [stavu](application-gateway-create-probe-ps.md) pomocí Prostředí PowerShell a modelu nasazení Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
