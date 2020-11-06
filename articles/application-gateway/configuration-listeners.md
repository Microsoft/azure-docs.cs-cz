---
title: Konfigurace naslouchacího procesu Azure Application Gateway
description: Tento článek popisuje, jak nakonfigurovat naslouchací procesy Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 15f68e8cbca65e7b970944f7ca5ef1952140cc6b
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397634"
---
# <a name="application-gateway-listener-configuration"></a>Konfigurace naslouchacího procesu Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Naslouchací proces je logická entita, která kontroluje příchozí požadavky na připojení pomocí portu, protokolu, hostitele a IP adresy. Při konfiguraci naslouchacího procesu je nutné zadat hodnoty, které odpovídají odpovídajícím hodnotám v příchozím požadavku na bráně.

Když vytváříte Aplikační bránu pomocí Azure Portal, vytvoříte také výchozí naslouchací proces zvolením protokolu a portu pro naslouchací proces. Můžete zvolit, jestli se má povolit podpora HTTP2 pro naslouchací proces. Po vytvoření aplikační brány můžete upravit nastavení tohoto výchozího naslouchacího procesu ( *appGatewayHttpListener* ) nebo vytvořit nové naslouchací procesy.

## <a name="listener-type"></a>Typ naslouchacího procesu

Při vytváření nového naslouchacího procesu si zvolíte mezi [ *základními* a *více lokalitami*](./application-gateway-components.md#types-of-listeners).

- Pokud chcete, aby všechny vaše žádosti (pro libovolnou doménu) byly přijaty a předány do fondů back-endu, vyberte základní. Přečtěte si, [jak vytvořit Aplikační bránu s využitím základního naslouchacího procesu](./quick-create-portal.md).

- Pokud chcete překládat požadavky na různé back-endové fondy na základě hlavičky *hostitele* nebo názvů hostitelů, zvolte možnost naslouchací proces pro více webů, kde musíte zadat také název hostitele, který se shoduje s příchozím požadavkem. Důvodem je to, že Application Gateway spoléhá na hlavičky hostitele HTTP 1,1 k hostování více než jednoho webu na stejné veřejné IP adrese a portu. Další informace najdete v tématu [hostování více lokalit pomocí Application Gateway](multiple-site-overview.md).

### <a name="order-of-processing-listeners"></a>Pořadí naslouchacího procesu zpracování

V případě SKU v1 jsou požadavky porovnány podle pořadí pravidel a typu naslouchacího procesu. Pokud se pravidlo se základním naslouchacím rozhraním bude nacházet jako první v uvedeném pořadí, zpracuje se nejprve a přijme všechny žádosti o tuto kombinaci portů a IP adres. Aby k tomu nedocházelo, napřed nakonfigurujte pravidla s využitím naslouchacího procesu Multi-Site a nasaďte pravidlo pomocí základního naslouchacího procesu na poslední v seznamu.

V případě SKU verze v2 jsou procesy naslouchání na více lokalitách zpracovány před základními naslouchacími procesy.

## <a name="front-end-ip-address"></a>Front-endová IP adresa

Vyberte front-end IP adresu, kterou plánujete přidružit k tomuto naslouchacímu procesu. Naslouchací proces bude naslouchat příchozím žádostem v této IP adrese.

## <a name="front-end-port"></a>Front-end port

Vyberte front-end port. Vyberte existující port nebo vytvořte nový. Vyberte libovolnou hodnotu z [povoleného rozsahu portů](./application-gateway-components.md#ports). Můžete použít nejen známé porty, například 80 a 443, ale kterýkoli povolený vlastní port je vhodný. Port lze použít pro veřejné naslouchací procesy nebo privátní naslouchací procesy.

## <a name="protocol"></a>Protokol

Vyberte HTTP nebo HTTPS:

- Pokud zvolíte protokol HTTP, přenosy mezi klientem a aplikační bránou nejsou šifrované.

- Pokud chcete [ukončení TLS](features.md#secure-sockets-layer-ssltls-termination) nebo [komplexní šifrování TLS](./ssl-overview.md), vyberte https. Přenos dat mezi klientem a aplikační bránou je zašifrovaný. A připojení TLS končí na aplikační bráně. Pokud chcete šifrování TLS od začátku do konce, musíte zvolit HTTPS a nakonfigurovat nastavení **back-endu http** . Tím se zajistí, že se provoz po přenosu z aplikační brány do back-endu znovu zašifruje.


Chcete-li nakonfigurovat ukončení protokolu TLS a komplexní šifrování TLS, je nutné do naslouchacího procesu přidat certifikát, aby služba Application Gateway mohla odvodit symetrický klíč. To je určeno specifikací protokolu TLS. Symetrický klíč slouží k šifrování a dešifrování provozu, který je odeslán do brány. Certifikát brány musí být ve formátu PFX (Personal Information Exchange). Tento formát vám umožní exportovat soukromý klíč, který brána používá k šifrování a dešifrování provozu.

## <a name="supported-certificates"></a>Podporované certifikáty

Přečtěte si téma [Přehled ukončení protokolu TLS a koncového šifrování TLS s Application Gateway](ssl-overview.md#certificates-supported-for-tls-termination)

## <a name="additional-protocol-support"></a>Podpora dalších protokolů

### <a name="http2-support"></a>Podpora HTTP2

Podpora protokolu HTTP/2 je dostupná pro klienty, kteří se připojují pouze ke službě Application Gateway Listeners. Komunikace se fondy back-end serverů je přes protokol HTTP/1.1. Ve výchozím nastavení je podpora HTTP/2 zakázaná. Následující fragment kódu Azure PowerShell ukazuje, jak tuto možnost povolit:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>Podpora protokolu WebSocket

Podpora protokolu WebSocket je ve výchozím nastavení povolená. Neexistuje žádné uživatelsky konfigurovatelné nastavení, které by bylo možné povolit nebo zakázat. Můžete použít objekty WebSockets s naslouchacími procesy HTTP i HTTPS.

## <a name="custom-error-pages"></a>Vlastní chybové stránky

Vlastní chybu můžete definovat na globální úrovni nebo na úrovni naslouchacího procesu. Ale vytváření vlastních chybových stránek na globální úrovni z Azure Portal aktuálně není podporováno. Vlastní chybovou stránku můžete nakonfigurovat pro chybu brány firewall webové aplikace 403 nebo pro stránku údržby 502 na úrovni naslouchacího procesu. Pro daný stavový kód chyby je nutné zadat také veřejně dostupnou adresu URL objektu BLOB. Další informace najdete v tématu [Vytvoření vlastních chybových stránek služby Application Gateway](./custom-error.md).

![Kódy chyb Application Gateway](/azure/application-gateway/media/custom-error/ag-error-codes.png)

Pokud chcete nakonfigurovat globální vlastní chybovou stránku, přečtěte si téma [Azure PowerShell Configuration](./custom-error.md#azure-powershell-configuration).

## <a name="tls-policy"></a>Zásada TLS

Můžete centralizovat správu certifikátů TLS/SSL a snížit režijní náklady na dešifrování pro back-endové serverové farmy. Centralizované zpracování TLS také umožňuje určit centrální zásady TLS, které jsou vhodné pro vaše požadavky na zabezpečení. Můžete zvolit *výchozí* , *předdefinované* nebo *vlastní* zásady TLS.

Zásady TLS se konfigurují pro řízení verzí protokolu TLS. Aplikační bránu můžete nakonfigurovat tak, aby používala minimální verzi protokolu pro handshake TLS z TLS 1.0, TLS 1.1 a TLS 1.2. Ve výchozím nastavení jsou SSL 2,0 a 3,0 zakázané a nedají se konfigurovat. Další informace najdete v tématu [Přehled zásad Application Gateway TLS](./application-gateway-ssl-policy-overview.md).

Po vytvoření naslouchacího procesu ho přidružíte k pravidlu směrování požadavků. Toto pravidlo určuje, jak jsou požadavky přijaté na naslouchací službě směrovány do back-endu.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si informace o pravidlech směrování žádostí](configuration-request-routing-rules.md).