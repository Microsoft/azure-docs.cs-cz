---
title: Proudy zařízení Azure IoT Hub (preview) | Dokumentace Microsoftu
description: Přehled datových proudů zařízení služby IoT Hub.
author: rezasherafat
manager: briz
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 7ffe4a087ae94d6c96019cc045d3d7ff071780d4
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830351"
---
# <a name="iot-hub-device-streams-preview"></a>IoT Hub zařízení datové proudy (preview)

## <a name="overview"></a>Přehled
Azure IoT Hub *datové proudy zařízení* usnadnění vytváření zabezpečenými tunely obousměrné TCP pro celou řadu scénářů komunikace typu cloud zařízení. Datový proud zařízení je zprostředkována služby IoT Hub *koncový bod streamování* který funguje jako proxy mezi zařízením a službou koncové body. Toto nastavení je znázorněný na obrázku níže, což je espcially užitečné, pokud se zařízení nachází za bránou firewall sítě nebo se nacházejí v privátní síti. V důsledku toho datové proudy zařízení služby IoT Hub pomáhají adresy zákazníků potřebuje k dosažení zařízení IoT, firewallem procházející způsobem a bez nutnosti široce vám otevírají příchozí nebo odchozí síťové porty brány firewall.

![Alternativní text](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png "zařízení služby IoT Hub datové proudy – přehled")


Použití streamů zařízení služby IoT Hub, zařízení zabezpečení a bude muset otevřít odchozí připojení TCP ke službě IoT hub koncový bod streamování přes port 443. Po vytvoření datového proudu aplikace straně služby a zařízení se mají programový přístup k objektu objektu websocket na straně klienta pro odesílání a přijímání základním bajtovém mezi sebou. Spolehlivost a poskytuje toto tunelové propojení zaručuje řazení je stejné jako TCP.

## <a name="benefits"></a>Výhody
Datové proudy zařízení služby IoT Hub poskytuje následující výhody:
- **Firewallem procházející zabezpečené připojení:** Zařízení IoT můžete přejít z koncových bodů služby bez otevření brány firewall pro příchozí port na zařízení nebo sítě perimetry (jenom odchozí připojení ke službě IoT Hub je potřeba přes port 443).

- **Ověřování:** Zařízení a služby konce tunelu muset provádět ověřování pomocí služby IoT Hub pomocí svých přihlašovacích údajů odpovídající.

- **Šifrování:** Ve výchozím nastavení používají datové proudy zařízení služby IoT Hub povolený protokol TLS připojení. Tím se zajistí, že provoz se šifruje vždy bez ohledu na to, zda aplikace používá šifrování, nebo ne.

- **Jednoduchost připojení:** Použití datových proudů zařízení eliminuje potřebu složité nastavení virtuální privátní sítě chcete umožnit připojení k zařízením IoT.

- **Kompatibilita s zásobník protokolu TCP/IP:** Datové proudy zařízení služby IoT Hub zvládne provozu aplikace protokolu TCP/IP. To znamená, že tuto funkci využít širokou škálu protokolů proprietární i standardizované.

- **Snadné použití v privátní síti nastavení:** Služby mohou kontaktovat zařízení odkazováním na ID zařízení, místo IP adresy. To je užitečné v situacích, kdy zařízení se nachází v privátní síti a má privátní IP adresy nebo jeho IP adresa se dynamicky přiřadí a není známý na straně služby.

## <a name="device-stream-workflows"></a>Pracovní postupy Stream zařízení
Datový proud zařízení platit, když služba žádostí o připojení k zařízení tím, že poskytuje jeho ID zařízení. Tento pracovní postup se obzvlášť zapadá do vzor komunikace klient/server, včetně SSH a RDP, kde uživatel si klade za cíl pro vzdálené připojení k serveru SSH nebo RDP spuštěného v příslušném zařízení pomocí protokolu SSH nebo RDP klientský program.

Proces vytvoření datového proudu zařízení zahrnuje vyjednávání mezi zařízení, služby, hlavní služby IoT hub a koncové body streamování. Při vytváření datového proudu zařízení orchestruje hlavní koncový bod služby IoT hub zpracovává koncový bod streamování provozu, která probíhá mezi zařízení a služeb.

### <a name="device-stream-creation-flow"></a>Zařízení stream vytvoření toku
Programové vytváření streamu zařízení pomocí sady SDK zahrnuje následující kroky, které jsou také znázorněné na následujícím obrázku:

![Alternativní text](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png "zařízení Streamovat metodou Handshake")


1. Aplikace zařízení zaregistruje zpětné volání předem chcete být upozorněni při zahájení nového datového proudu zařízení na zařízení. Tento krok obvykle probíhá při zařízení se spustí a připojí ke službě IoT Hub.

2. Straně služby program zahájí datovém proudu zařízení v případě potřeby zadáním ID zařízení (_není_ IP adresu).

3. Služby IoT hub upozorní straně zařízení programu vyvoláním zpětného volání zaregistrovaný v kroku 1. Zařízení může přijmout nebo odmítnout žádosti o zahájení datového proudu. Tuto logiku mohou být specifické pro váš scénář aplikace. Pokud se požadavek streamu. zařízení, služby IoT Hub informuje službu. jinak postupujte podle následujících kroků.

4. Zařízení vytvoří zabezpečené odchozí připojení TCP pro koncový bod streamování přes port 443 a upgraduje připojení na protokol WebSocket. Adresa URL koncového bodu streamování, stejně jako přihlašovací údaje používají k ověření jsou i k dispozici na zařízení ve službě IoT Hub jako součást požadavku odeslaného v kroku 3.

5. Služba je informované o výsledku zařízení přijímá datový proud a pokračuje k vytvoření vlastního protokolu WebSocket pro koncový bod streamování. Podobně obdrží streamování koncový bod adresy URL a ověřovací informace ze služby IoT Hub.

V procesu metody handshake výše:
- Procesu musí dokončit během 60 sekund (krok 2 až 5), jinak signalizace selže s časovým limitem a služba bude odpovídajícím způsobem uvědomen.

- Po dokončení vytváření toku datového proudu výše, koncový bod streamování bude fungovat jako proxy a přenášet provoz mezi službou a zařízení prostřednictvím jejich odpovídajících objekty Websocket.

- Zařízení a služby potřebují odchozí připojení k hlavní koncový bod služby IoT Hub, stejně jako koncový bod streamování přes port 443. Adresa URL z těchto koncových bodů je k dispozici na kartě Přehled na portálu služby IoT Hub.

- Spolehlivost a zaručuje zavedené datového proudu řazení je stejné jako TCP.

- Všechna připojení k službě IoT Hub a koncový bod streamování pomocí protokolu TLS a zašifrují.

### <a name="termination-flow"></a>Ukončení toku
Zavedené datový proud je ukončeno buď TCP připojení k bráně jsou odpojené (pomocí služby nebo zařízení). To může proběhnout odpojit ukončením objektu WebSocket na zařízení nebo služba programy nebo nedobrovolně v případě selhání síťové připojení k vypršení časového limitu nebo proces. Po skončení zařízení nebo služby připojení na koncový bod streamování jiné připojení TCP také (nuceně) ukončí a zařízení a služeb odpovídají znovu vytvořit datový proud, v případě potřeby.


## <a name="connectivity-requirements"></a>Požadavky na připojení

Zařízení i ze strany služby stream zařízení musí být schopen vytvořit povolený protokol TLS připojení ke službě IoT Hub a její koncový bod streamování. To vyžaduje odchozí připojení přes port 443 s těmito koncovými body. Název hostitele spojené s těmito koncovými body najdete v *přehled* kartu služby IoT Hub, jak je znázorněno na následujícím obrázku: ![Alternativní text](./media/iot-hub-device-streams-overview/device-stream-portal.png "koncové body služby stream zařízení")

Můžete také informace koncové body pomocí se dají načíst pomocí rozhraní příkazového řádku Azure v části Vlastnosti centra konkrétně `property.hostname` a `property.deviceStreams` klíče.

```azurecli-interactive
az iot hub show --name <YourIoTHubName>
```

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Řešení potíží pomocí datové proudy zařízení protokolů aktivit

Můžete si nastavit Azure Log Analytics ke shromažďování streamů zařízení ve službě IoT Hub protokolu aktivit. To může být velmi užitečné při řešení potíží se scénáři.

Postupujte podle pokynů níže nakonfigurujte Azure Log Analytics pro aktivity stream zařízení služby IoT Hub:

1. Přejděte *nastavení diagnostiky* kartě ve službě IoT Hub a klikněte na *zapnout diagnostiku* odkaz.

  ![Alternativní text](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings.PNG "povolení disgnostics protokoly")


2. Zadejte název pro nastavení diagnostiky a zvolte *odesílat do Log Analytics* možnost. Průvodce zvolte existující prostředek Log Analytics nebo vytvořte novou. Dále zkontrolujte, *DeviceStreams* ze seznamu.

    ![Alternativní text](./media/iot-hub-device-streams-overview/device-streams-diagnostics.PNG "zařízení povolení streamování protokolů")

3. Teď umožňuje přistupovat k protokolům datové proudy zařízení v části *protokoly* karta na portálu služby IoT Hub. Zobrazí se v zařízení streamování protokolů aktivit `AzureDiagnostics` tabulky a mít `Category=DeviceStreams`.

    <p>
Jak je znázorněno níže identita cílového zařízení a výsledek operace je také k dispozici v protokolech.
    ![Alternativní text](./media/iot-hub-device-streams-overview/device-streams-log-analytics.PNG "přístup k zařízení streamování protokolů")
    

## <a name="whitelist-device-streaming-endpoints"></a>Koncové body streamování povolených zařízení

Jak už bylo zmíněno [starší](#Overview), vaše zařízení vytvoří odchozí připojení ke službě IoT Hub koncový bod streamování během datové proudy zařízení zahájení procesu. Brány firewall na zařízení nebo v jeho síti musíte povolit odchozí připojení k bráně streamování přes port 443 (Toto je připojení pomocí protokolu WebSocket, který je šifrovaný použití protokolu TLS).

Název hostitele koncového bodu streamování zařízení můžete najít na portálu Azure IoT Hub na kartě Přehled. ![Alternativní text](./media/iot-hub-device-streams-overview/device-stream-portal.PNG "koncové body služby stream zařízení")

Alternativně můžete najít tyto informace pomocí Azure CLI:
```cmd/sh
az iot hub show --name tcpstreaming-preview
```

## <a name="sdk-availability"></a>Dostupnost sady SDK
Obou stran jednotlivých datových proudech (na straně zařízení a služby) pomocí sady SDK IoT Hub pro vytvoření tunelu. Ve verzi public preview Zákazníci si mohou vybrat z následujících jazyků sady SDK:
- C a C# SDK podporují datové proudy zařízení na straně zařízení.

- Prostředí NodeJS a C# SDK podporují datové proudy zařízení na straně služby.

## <a name="iot-hub-device-stream-samples"></a>Ukázky Stream zařízení centra IoT
Jsme přidali dvě ukázky pro demonstraci použití datových proudů zařízení aplikací. *Echo* ukázce programový použití datových proudů zařízení (voláním rozhraní API sady SDK). *Místní proxy* ukázkový, ukazují použití funkce sady SDK pro přenos předem připravená aplikace tunelového propojení (jako jsou SSH, RDP i na webu) prostřednictvím datových proudů zařízení.

### <a name="echo-sample"></a>Ukázka programu echo
Echo ukázce programové využívání datových proudů zařízení k odesílání a příjem bajtů mezi zařízení a služeb aplikací. Pomocí odkazů níže pro přístup k příručky rychlý úvod (programy pro zařízení a služeb můžete použít v různých jazycích, například program pro zařízení jazyka C může spolupracovat s C# programu služby):

| Sada SDK    | Program služeb                                          | Program zařízení                                           |
|--------|----------------------------------------------------------|----------------------------------------------------------|
| C#     | [Odkaz](quickstart-device-streams-echo-csharp.md) | [Odkaz](quickstart-device-streams-echo-csharp.md) |
| NodeJS | [Odkaz](quickstart-device-streams-echo-nodejs.md) | -                                                        |
| C      | -                                                        | [Odkaz](quickstart-device-streams-echo-c.md)      |

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Ukázka místní Proxy (pro protokol RDP nebo SSH)
Místní proxy příklad znázorňuje způsob, jak povolit tunelování přenosů existující aplikaci, která zahrnuje komunikace mezi klientem a serverem programu. Toto nastavení platí pro protokoly klient/server, jako jsou SSH a protokolu RDP, kde straně služby slouží jako klient (spuštěné programy klienta SSH nebo RDP), a na straně zařízení jako serveru (běžícího proces démon programu SSH nebo RDP serverových programů). 

Tato část popisuje použití datových proudů zařízení, aby se povolily scénáře SSH do zařízení přes zařízení datové proudy (v případě protokolu RDP nebo jiné protokoly klient/server jsou podobné s použitím protokolu odpovídající port).

Nastavení využívá dva *místní proxy* programy je vidět na obrázku níže, a to *zařízení místní proxy* a *místní službu proxy*. Místní proxy jsou odpovědný za provedení [zařízení stream zahájení handshake](#Device-stream-creation-flow) pomocí služby IoT Hub a interakci s klientem SSH a proces démon programu SSH pomocí regulárních klient/server soketu programování.

![Alternativní text](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png "nastavení proxy serveru zařízení datový proud pro protokol RDP/SSH")

1. Uživatel spustí služba místní proxy server k zahájení datovém proudu zařízení na zařízení.

2. Zařízení přijme zahájení datového proudu a tunelu pro koncový bod streamování služby IoT Hub (jak je popsáno výše).

3. Proxy pro místní zařízení se připojí ke koncovému bodu démon procesu SSH naslouchá na portu 22 na zařízení.

4. Proxy místní služba naslouchá na určeném portu čekání na nová připojení SSH od uživatele (port 2222 použili v ukázce je libovolného portu). Uživatel odkazuje klienta SSH na portu proxy serveru místní služby na místním hostiteli.

### <a name="notes"></a>Poznámky
- Výše uvedené kroky provést začátku do konce tunelového propojení mezi klienta SSH (napravo) na proces démon programu SSH (na levé straně). 

- Šipky na obrázku označují směr, ve kterém se vytvoří připojení mezi koncovými body. Konkrétně Všimněte si, že neexistuje žádná příchozí připojení na zařízení (to je často blokovat brána firewall).

- Na výběr mezi používáním port `2222` na místní službu proxy serveru je na libovolnou volbu. Proxy server se dá používat další dostupný port.

- Volba portu `22` v tomto případě je závislé na procotocol a specifické pro SSH. Pro případ RDP, port `3389` musí být použita. To je možné nakonfigurovat v zadané ukázkové programy.

Pomocí odkazů níže pokyny o tom, jak spustit místní proxy programy v jazyce podle výběru. Podobně jako v příkladu programu echo, můžete spustit proxy pro místní zařízení a služby v různých jazycích, jako jsou plně interoperabilní.

| Sada SDK    | Místní služba Proxy                                       | Místní zařízení proxy serveru                                |
|--------|-----------------------------------------------------------|---------------------------------------------------|
| C#     | [Odkaz](quickstart-device-streams-proxy-csharp.md)  | [Odkaz](quickstart-device-streams-proxy-csharp.md) |
| NodeJS | [Odkaz](quickstart-device-streams-proxy-nodejs.md)         | -                                                 |
| C      | -                                                         | [Odkaz](quickstart-device-streams-proxy-c.md)      |

## <a name="next-steps"></a>Další postup

Další informace o datových proudů zařízení pomocí odkazů níže:

> [!div class="nextstepaction"]
> [Zobrazit datové proudy zařízení s využitím IoT (kanál 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
> [zkuste zařízení úvodní příručka, pomocí datového proudu](/azure/iot-hub)
