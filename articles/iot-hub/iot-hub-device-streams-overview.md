---
title: Streamy zařízení Azure IoT Hub | Dokumenty společnosti Microsoft
description: Přehled datových proudů zařízení Azure IoT Hub, které usnadňují zabezpečené obousměrné tunelové propojení TCP pro různé scénáře komunikace mezi cloudy.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.openlocfilehash: ff738e56226f7cbb720a754573a9d8607e0e3247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890457"
---
# <a name="iot-hub-device-streams-preview"></a>Datové proudy zařízení centra IoT Hub (náhled)

*Datové proudy zařízení* Azure IoT Hub usnadňují vytváření zabezpečených obousměrných tunelových propojení TCP pro různé scénáře komunikace mezi cloudy. Datový proud zařízení je zprostředkovaný *koncovým bodem streamování* služby IoT Hub, který funguje jako proxy server mezi koncovými body zařízení a služby. Toto nastavení, znázorněné na obrázku níže, je užitečné zejména v případě, že jsou zařízení za síťovým firewallem nebo jsou umístěna uvnitř privátní sítě. Streamy zařízení služby IoT Hub proto pomáhají řešit potřeby zákazníků oslovit zařízení IoT způsobem, který je vhodný pro bránu firewall, a bez nutnosti široce otevírat příchozí nebo odchozí porty síťové brány firewall.

!["Přehled datových proudů zařízení ioT hubu"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Pomocí datových proudů zařízení služby IoT Hub zůstávají zařízení zabezpečená a budou potřebovat pouze otevřít odchozí připojení TCP ke koncovému bodu streamování služby IoT hub přes port 443. Po vytvoření datového proudu budou mít aplikace na straně služby a na straně zařízení programový přístup k objektu klienta WebSocket pro vzájemné odesílání a přijímání nezpracovaných bajtů. Záruky spolehlivosti a objednání poskytované tímto tunelem jsou na stejné úrovni jako TCP.

## <a name="benefits"></a>Výhody

Datové proudy zařízení ioT Hub poskytují následující výhody:

* **Zabezpečené připojení vhodné pro bránu firewall:** Zařízení IoT lze dosáhnout z koncových bodů služby bez otevření vstupního portu brány firewall na obvodu zařízení nebo sítě (pouze odchozí připojení k ioT hubu je potřeba přes port 443).

* **Ověřování:** Stránky zařízení i služby tunelového propojení je třeba ověřit pomocí služby IoT Hub pomocí jejich odpovídající pověření.

* **Šifrování:** Ve výchozím nastavení používají datové proudy zařízení služby IoT Hub připojení s podporou TLS. Tím je zajištěno, že provoz je vždy šifrována bez ohledu na to, zda aplikace používá šifrování nebo ne.

* **Jednoduchost konektivity:** V mnoha případech použití datových proudů zařízení eliminuje potřebu složitého nastavení virtuálních privátních sítí, které umožní připojení k zařízením IoT.

* **Kompatibilita se zásobníkem protokolu TCP/IP:** Datové proudy zařízení služby IoT Hub mohou přenášet přenosy aplikací TCP/IP. To znamená, že tuto funkci může využít široká škála proprietárních i standardů založených protokolů.

* **Snadné použití v nastavení privátních sítí:** Služba může komunikovat se zařízením odkazem na jeho ID zařízení, nikoli ip adresu zařízení. To je užitečné v situacích, kdy je zařízení umístěno uvnitř privátní sítě a má privátní IP adresu nebo jeho IP adresa je přiřazena dynamicky a není známa straně služby.

## <a name="device-stream-workflows"></a>Pracovní postupy datového proudu zařízení

Datový proud zařízení se inicializuje, když služba požádá o připojení k zařízení poskytnutím ID zařízení. Tento pracovní postup se vejde zejména do komunikačního modelu klient/server, včetně SSH a RDP, kde se uživatel zamýšlí vzdáleně připojit k serveru SSH nebo RDP spuštěnému na zařízení pomocí klientského programu SSH nebo RDP.

Proces vytváření datového proudu zařízení zahrnuje vyjednávání mezi zařízením, službou, hlavními a streamovacími koncovými body služby IoT hub. Zatímco hlavní koncový bod služby IoT hub orchestruje vytváření datového proudu zařízení, koncový bod streamování zpracovává přenosy, které se točtou mezi službou a zařízením.

### <a name="device-stream-creation-flow"></a>Tok vytváření datového proudu zařízení

Programové vytvoření datového proudu zařízení pomocí sady SDK zahrnuje následující kroky, které jsou také znázorněny na následujícím obrázku:

!["Proces handshake datového proudu zařízení"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. Aplikace zařízení zaregistruje zpětné volání předem, aby byla upozorněna na spuštění nového datového proudu zařízení do zařízení. Tento krok obvykle probíhá, když se zařízení spustí a připojí se k centru IoT Hub.

2. Program na straně služby iniciuje datový proud zařízení v případě potřeby poskytnutím ID zařízení _(nikoli_ IP adresy).

3. Centrum IoT upozorní program na straně zařízení vyvoláním zpětného volání registrovaného v kroku 1. Zařízení může přijmout nebo odmítnout požadavek na zahájení datového proudu. Tato logika může být specifická pro váš scénář aplikace. Pokud je požadavek na datový proud odmítnut zařízením, služba IoT Hub o tom informuje službu; v opačném případě postupujte podle následujících kroků.

4. Zařízení vytvoří zabezpečené odchozí připojení TCP ke koncovému bodu datového proudu přes port 443 a inovuje připojení na websocket. Adresa URL koncového bodu streamování, stejně jako pověření pro ověření jsou k dispozici zařízení službou IoT Hub jako součást požadavku odeslaného v kroku 3.

5. Služba je upozorněna na výsledek zařízení přijetí datového proudu a pokračuje k vytvoření vlastního klienta WebSocket do koncového bodu streamování. Podobně obdrží adresu URL koncového bodu streamování a ověřovací informace z ioT hubu.

V procesu handshake výše:

* Proces handshake musí být dokončen do 60 sekund (krok 2 až 5), jinak handshake selže s časový limit a služba bude odpovídajícím způsobem upozorněna.

* Po dokončení toku vytváření datového proudu nad koncový bod streamování bude fungovat jako proxy server a bude přenášet provoz mezi službou a zařízením přes jejich příslušné WebSockets.

* Zařízení i služba vyžadují odchozí připojení k hlavnímu koncovému bodu služby IoT Hub a koncový bod streamování přes port 443. Adresa URL těchto koncových bodů je k dispozici na kartě *Přehled* na portálu služby IoT Hub.

* Záruky spolehlivosti a objednání zavedeného datového proudu jsou na stejné úrovni jako tcp.

* Všechna připojení k IoT Hub a koncového bodu streamování používají TLS a jsou šifrována.

### <a name="termination-flow"></a>Ukončovací tok

Vytvořený datový proud se ukončí, když je odpojeno jedno z připojení TCP k bráně (službou nebo zařízením). To může probíhat dobrovolně zavřením WebSocket na zařízení nebo servisní programy nebo nedobrovolně v případě časového omezení připojení k síti nebo selhání procesu. Po ukončení připojení zařízení nebo služby ke koncovému bodu streamování bude druhé připojení TCP také (vynuceně) ukončeno a služba a zařízení jsou v případě potřeby odpovědné za opětovné vytvoření datového proudu.

## <a name="connectivity-requirements"></a>Požadavky na připojení

Stránky zařízení i služby datového proudu zařízení musí být schopny navázat připojení s podporou TLS k ioT hubu a jeho koncovému bodu streamování. To vyžaduje odchozí připojení přes port 443 k těmto koncovým bodům. Název hostitele přidružený k těmto koncovým bodům najdete na kartě *Přehled* služby IoT Hub, jak je znázorněno na obrázku níže:

!["Koncové body datového proudu zařízení"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Případně informace o koncových bodech lze načíst pomocí rozhraní příkazového příkazu Azure v části vlastností centra, konkrétně `property.hostname` a `property.deviceStreams` klíče.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

Výstup je json objekt všech koncových bodů, které zařízení a služby rozbočovače může potřebovat k připojení k vytvoření datového proudu zařízení.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Ujistěte se, že jste nainstalovali Azure CLI verze 2.0.57 nebo novější. Nejnovější verzi si můžete stáhnout na stránce [Instalace rozhraní příkazového příkazu k webu Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Povolit odchozí připojení ke koncovým bodům streamování zařízení

Jak je uvedeno na začátku tohoto článku, vaše zařízení vytvoří odchozí připojení ke koncovému bodu streamování služby IoT Hub během procesu zahájení datových proudů zařízení. Brány firewall v zařízení nebo v jeho síti musí umožňovat odchozí připojení k bráně streamování přes port 443 (všimněte si, že komunikace probíhá prostřednictvím připojení WebSocket, které je šifrováno pomocí TLS).

Název hostitele koncového bodu streamování zařízení najdete na portálu Azure IoT Hub na kartě Přehled. ![](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Tyto informace můžete také najít pomocí příkazového příkazového příkazu Azure:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Ujistěte se, že jste nainstalovali Azure CLI verze 2.0.57 nebo novější. Nejnovější verzi si můžete stáhnout na stránce [Instalace rozhraní příkazového příkazu k webu Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
>

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Poradce při potížích prostřednictvím protokolů aktivit datových proudů zařízení

Protokoly Azure Monitoru můžete nastavit tak, aby shromažďovaly protokol y aktivit datových proudů zařízení ve vašem centru IoT Hub. To může být velmi užitečné při řešení potíží scénáře.

Podle následujících kroků nakonfigurujte protokoly Azure Monitoru pro aktivity streamování zařízení služby IoT Hub:

1. Přejděte na kartu *Nastavení diagnostiky* ve službě IoT Hub a klikněte na *Odkaz Zapnout diagnostiku.*

   !["Povolení protokolů diagnostiky"](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings-pane.png)

2. Zadejte název nastavení diagnostiky a zvolte *Možnost Odeslat do analýzy protokolů.* Budete vedeni k výběru existujícího prostředku pracovního prostoru Log Analytics nebo k vytvoření nového. Navíc zkontrolujte *DeviceStreams* ze seznamu.

    !["Povolit protokoly datových proudů zařízení"](./media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png)

3. Teď máte přístup k protokolům datových proudů zařízení na kartě *Protokoly* na portálu ioT Hubu. Protokoly aktivit datového proudu `AzureDiagnostics` zařízení `Category=DeviceStreams`se zobrazí v tabulce a mají .

   Jak je znázorněno níže, identita cílového zařízení a výsledek operace je také k dispozici v protokolech.

   !["Přístup k protokolům datových proudů zařízení"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

## <a name="regional-availability"></a>Dostupnost podle oblastí

Během veřejné verze Preview jsou streamy zařízení služby IoT Hub k dispozici v oblastech Střední USA, Střední USA EUAP, Severní Evropa a Jihovýchodní Asie. Prosím, ujistěte se, že jste vytvořili rozbočovač v jedné z těchto oblastí.

## <a name="sdk-availability"></a>Dostupnost sady SDK

Dvě strany každého datového proudu (na straně zařízení a služby) používají k vytvoření tunelového propojení sazbě služby IoT Hub SDK. Během veřejné verze Preview si zákazníci mohou vybrat z následujících jazyků sady SDK:

* C a C# SDK zařízení podpory proudy na straně zařízení.

* NodeJS a C# SDK podporují datové proudy zařízení na straně služby.

## <a name="iot-hub-device-stream-samples"></a>Ukázky datového proudu zařízení centra IoT Hub

Na stránce služby IoT Hub jsou k dispozici dvě [ukázky rychlého startu.](/azure/iot-hub) Ty ukazují použití datových proudů zařízení aplikacemi.

* Ukázka *ozvěny* demonstruje programové použití datových proudů zařízení (voláním přímo rozhraní API sady SDK).

* Ukázka *místního proxy serveru* demonstruje tunelové propojení přenosů aplikací klient/server (například SSH, RDP nebo web) prostřednictvím datových proudů zařízení.

Tyto vzorky jsou podrobněji popsány níže.

### <a name="echo-sample"></a>Ukázka ozvěny

Ukázka ozvěny demonstruje programové použití datových proudů zařízení k odesílání a přijímání bajtů mezi aplikacemi služby a zařízení. Všimněte si, že můžete používat programy služeb a zařízení v různých jazycích. Můžete například použít program zařízení C se servisním programem Jazyka C#.

Zde jsou ukázky ozvěny:

* [Servisní a servisní program jazyka C#](quickstart-device-streams-echo-csharp.md)

* [Servisní program Node.js](quickstart-device-streams-echo-nodejs.md)

* [Program zařízení C](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Ukázka místního proxy serveru (pro SSH nebo RDP)

Ukázka místního proxy serveru ukazuje způsob, jak povolit tunelové propojení přenosů existující aplikace, který zahrnuje komunikaci mezi klientem a serverovým programem. Toto nastavení funguje pro protokoly klient/server, jako jsou SSH a RDP, kde na straně služby funguje jako klient (spuštěné klientské programy SSH nebo RDP) a na straně zařízení funguje jako server (spouštění programů daemonu SSH nebo rdp serveru).

Tato část popisuje použití datových proudů zařízení k povolení uživatele s ssh na zařízení přes datové proudy zařízení (případ pro RDP nebo jiné aplikace klient/server jsou podobné pomocí odpovídající port protokolu).

Instalační program využívá dva *místní proxy* programy uvedené na obrázku níže, a to *zařízení-místní proxy* a *service-local proxy*. Místní proxy programy jsou zodpovědné za provádění [handshake zahájení datového proudu zařízení](#device-stream-creation-flow) s IoT Hub a interakci s klientem SSH a SSH daemon pomocí pravidelných klient/server soketů.

!["Nastavení proxy datového proudu zařízení pro SSH/RDP"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. Uživatel spustí proxy server místní služby, aby zahájil datový proud zařízení do zařízení.

2. Místní proxy zařízení přijímá požadavek na zahájení datového proudu a tunelové propojení je vytvořeno pro koncový bod streamování služby IoT Hub (jak je popsáno výše).

3. Místní proxy zařízení se připojí ke koncovému bodu daemonu SSH na slouícím se na portu 22 v zařízení.

4. Server proxy místní služby naslouchá na určeném portu a čeká na nová připojení SSH od uživatele (port 2222 použitý v ukázce, ale to lze nakonfigurovat na jakýkoli jiný dostupný port). Uživatel odkazuje klienta SSH na port proxy místní služby na localhost.

### <a name="notes"></a>Poznámky

* Výše uvedené kroky dokončit end-to-end tunel mezi klientem SSH (vpravo) na Daemon SSH (vlevo). Součástí tohoto připojení od konce do konce zahrnuje odesílání přenosů přes datový proud zařízení do služby IoT Hub.

* Šipky na obrázku označují směr, ve kterém jsou navázána spojení mezi koncovými body. Konkrétně všimněte si, že neexistuje žádné příchozí připojení jít do zařízení (to je často blokován bránou firewall).

* Volba použití portu 2222 na serveru proxy místní služby je libovolná volba. Proxy server lze nakonfigurovat tak, aby používal jakýkoli jiný dostupný port.

* Volba portu 22 je závislá na protokolu a specifické pro SSH v tomto případě. Pro případ RDP musí být použit port 3389. To lze nakonfigurovat v zadaných ukázkových programech.

Pomocí níže uvedených odkazů naleznete pokyny, jak spustit místní proxy programy ve zvoleném jazyce. Podobně jako [ukázka ozvěny](#echo-sample)můžete spouštět proxy programy místní zařízení a služby v různých jazycích, protože jsou plně interoperabilní.

* [Servisní a servisní program jazyka C#](quickstart-device-streams-proxy-csharp.md)

* [Servisní program Node.js](quickstart-device-streams-proxy-nodejs.md)

* [Program zařízení C](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Další kroky

Další informace o streamech zařízení najdete v následujících odkazech.

> [!div class="nextstepaction"]
> [Datové proudy zařízení na ioT show (Kanál 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
