---
title: Stav monitorování přehled Azure Application Gateway.
description: Další informace o možnosti monitorování v Azure Application Gateway
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/30/2018
ms.author: victorh
ms.openlocfilehash: 2f62f01c1178f9529eb46051f088affccc5279a7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="application-gateway-health-monitoring-overview"></a>Monitorování přehled stavu aplikace brány

Ve výchozím nastavení služba Azure Application Gateway monitoruje stav všechny prostředky v jeho fond back-end a automaticky odebere jakémukoli prostředku, považoval za poškozený z fondu. Aplikační brána pokračuje v monitorování není v pořádku instancí a přidá je zpět do fondu back-end v pořádku, jakmile jsou k dispozici a reagovat na sondy stavu. Aplikační brána odešle sondy stavu s stejný port, který je definován v nastavení HTTP back-end. Tato konfigurace zajistí, že tato kontrola je testování stejný port, který zákazníky se používají pro připojení k back-end.

![Příklad testu brány aplikace][1]

Kromě použití výchozího stavu testu monitorování, můžete také upravit test stavu podle potřeb vaší aplikace. V tomto článku jsou popsané výchozí i vlastní stavu sondy.

> [!NOTE]
> Pokud je skupina NSG na podsítě brány aplikace, musí být otevřen rozsahy portů 65503 65534 na podsíť aplikační brány pro příchozí provoz. Tyto porty jsou povinné pro back-end stav rozhraní API pro práci.

## <a name="default-health-probe"></a>Výchozí kontroly stavu

Služby application gateway automaticky nakonfiguruje výchozí kontrolu stavu, když nemáte nastavíte žádnou konfiguraci vlastní test paměti. Monitorování chování funguje tak, že požadavek HTTP na IP adresy nakonfigurované pro fond back-end. Pro výchozí sondy Pokud nastavení http back-end jsou nakonfigurované pro protokol HTTPS, že test využívá protokol HTTPS také k testování stavu back-EndY.

Příklad: Konfigurace brány aplikace používat back-end serverů A, B a C přijímat síťový provoz protokolu HTTP na portu 80. Sledování stavu výchozí testy tři servery každých 30 sekund pro pořádku odpověď HTTP. Je v pořádku odpovědi HTTP [stavový kód](https://msdn.microsoft.com/library/aa287675.aspx) 200 až 399.

Pokud kontrola výchozí test nezdaří serveru A, služby application gateway odstraní ji z jeho fond back-end a síťový provoz bude zastaven k tomuto serveru. Výchozí kontroly stále bude kontrolovat pro server každých 30 sekund. Pokud serveru A odpoví na žádost o jeden z výchozí stav kontroly úspěšně, ho je přidat do fondu back-end zpět jako v pořádku a provozu spustí předávaných na server znovu.

### <a name="probe-matching"></a>Sběru dat odpovídající

Ve výchozím nastavení je považován za pořádku odpověď HTTP (S) se stavovým kódem 200. Sondy vlastní stavu kromě podporují dva odpovídající kritériím. Vyhovující kritériím umožňuje Volitelně změňte výchozí interpretaci co consititutes pořádku odpovědi.

Toto jsou odpovídající kritériím: 

- **Shoda kódu stavu odpovědi HTTP** – test kritéria pro přijetí přiřazování http odpovědi kódu nebo odpovědi kód rozsahy zadaný uživatel. Jednotlivé oddělených kódy stavu odpovědi nebo rozsah stavový kód není podporováno.
- **Shoda textu odpovědi HTTP** – test, který vypadá v textu odpovědi HTTP a odpovídá s uživatelem zadaný řetězec kritéria přiřazování. Všimněte si, že na shodu pouze vyhledá přítomnosti uživatele zadaný řetězec v textu odpovědi a není úplné regulárního výrazu.

Kritéria shody je možné zadat pomocí `New-AzureRmApplicationGatewayProbeHealthResponseMatch` rutiny.

Příklad:

```
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Jakmile je zadaná kritéria shody, může být připojen testovat pomocí konfigurace `-Match` parametr v prostředí PowerShell.

### <a name="default-health-probe-settings"></a>Výchozí nastavení kontroly stavu

| Vlastnost testu | Hodnota | Popis |
| --- | --- | --- |
| Adresa URL testu |http://127.0.0.1:\<port\>/ |Cesta URL |
| Interval |30 |Interval testu paměti v sekundách |
| Časový limit |30 |Časový limit testu v sekundách |
| Prahová hodnota špatného stavu |3 |Počet opakování testu. Back-end serverů je označena po počet po sobě jdoucích test selhání dosáhne prahová hodnota špatného stavu. |

> [!NOTE]
> Port je stejný port jako nastavení HTTP back-end.

Výchozí kontroly zjistí pouze http://127.0.0.1: \<port\> k určení stavu. Pokud potřebujete nakonfigurovat test stavu na Přejít na vlastní adresu URL nebo změnit další nastavení, musíte použít vlastní testy paměti, jak je popsáno v následujících krocích:

## <a name="custom-health-probe"></a>Test vlastní stavu

Vlastní testy paměti umožňují podrobnější ovládat sledování stavu. Pokud používáte vlastní testy paměti, můžete nakonfigurovat interval testu, adresu URL a cesty k testování a kolik neúspěšných odpovědí tak, aby přijímal před označením fond back-end instance jako chybné.

### <a name="custom-health-probe-settings"></a>Nastavení testu vlastní stavu

Následující tabulka obsahuje definice pro vlastnosti test vlastní stavu.

| Vlastnost testu | Popis |
| --- | --- |
| Název |Název kontroly. Tento název se používá k odkazování na test v nastavení HTTP back-end. |
| Protocol (Protokol) |Protokol používaný k odesílání sonda. Tato kontrola používá protokol definované v nastavení HTTP back-end |
| Hostitel |Název hostitele k odeslání test. Platí jenom v případě více lokalit je nakonfigurovaná na aplikační bránu, v opačném případě použijte "127.0.0.1". Tato hodnota se liší od název hostitele virtuálního počítače. |
| Cesta |Relativní cesta kontroly. Platná cesta spustí z '/'. |
| Interval |Interval testu paměti v sekundách. Tato hodnota je časový interval mezi dvě po sobě jdoucích sondy. |
| Časový limit |Časový limit testu v sekundách. Pokud není platnou odpověď v rámci tento časový limit, tato kontrola je označeno jeho selhání.  |
| Prahová hodnota špatného stavu |Počet opakování testu. Back-end serverů je označena po počet po sobě jdoucích test selhání dosáhne prahová hodnota špatného stavu. |

> [!IMPORTANT]
> Pokud aplikace brána je nakonfigurovaná pro jednu lokalitu, ve výchozím nastavení hostitele název musí být zadán jako "127.0.0.1", pokud nebudou jinak nakonfigurovaná v vlastní test paměti.
> Pro referenci vlastní test paměti posílá \<protokol\>://\<hostitele\>:\<port\>\<cestu\>. Port používaný bude stejný port, jak jsou definovány v nastavení HTTP back-end.

## <a name="next-steps"></a>Další postup
Po informací o sledování stavu Application Gateway, můžete nakonfigurovat [test vlastní stavu](application-gateway-create-probe-portal.md) na portálu Azure nebo [test vlastní stavu](application-gateway-create-probe-ps.md) pomocí prostředí PowerShell a modelu nasazení Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
