---
title: Datové proudy zařízení v Azure IoT Hub | Microsoft Docs
description: Přehled streamů zařízení IoT Hub Azure, které usnadňují zabezpečená obousměrná tunelová propojení TCP pro nejrůznější scénáře komunikace z cloudu na zařízení.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: 9487fc562fa099d2650aabc8d15fc1449c7fcb5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97825181"
---
# <a name="iot-hub-device-streams-preview"></a>Datové proudy zařízení IoT Hub (Preview)

*Datové proudy zařízení* Azure IoT Hub usnadňují vytváření zabezpečených obousměrných tunelů TCP pro nejrůznější scénáře komunikace z cloudu na zařízení. Datový proud zařízení je vyzpůsobený *koncovým bodem streamování* IoT Hub, který funguje jako proxy mezi koncovými body zařízení a služeb. Toto nastavení zobrazené v následujícím diagramu je zvlášť užitečné v případě, že zařízení jsou za síťovou bránou firewall nebo se nacházejí v privátní síti. V takovém případě IoT Hub datové proudy zařízení usnadňují adresování zákazníků, aby měli přístup k zařízením IoT v souladu s bránou firewall a bez nutnosti široce otevírat příchozí nebo odchozí porty brány firewall sítě.

![Přehled "IoT Hub datových proudů zařízení"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Pomocí IoT Hub datových proudů zařízení zůstanou zařízení zabezpečená a bude potřeba otevřít jenom odchozí připojení TCP na koncový bod streamování služby IoT Hub přes port 443. Po navázání datového proudu budou mít aplikace na straně služby a na straně zařízení k dispozici programový přístup k objektu klienta WebSocket pro odesílání a příjem nezpracovaných bajtů do sebe. Záruky spolehlivosti a řazení poskytované tímto tunelovým propojením jsou v nominální hodnotě TCP.

## <a name="benefits"></a>Výhody

Datové proudy zařízení IoT Hub poskytují následující výhody:

* **Zabezpečené připojení s bránou firewall:** K zařízením IoT se dá získat přístup z koncových bodů služby bez otevření portu příchozí brány firewall na hraničních zařízeních zařízení nebo sítě (pro port 443 se vyžaduje jenom odchozí připojení k IoT Hub).

* **Ověřování:** Strany zařízení i služby tunelového propojení potřebují ověřit pomocí IoT Hub pomocí příslušných přihlašovacích údajů.

* **Šifrování:** IoT Hub datové proudy zařízení ve výchozím nastavení používají připojení s povoleným protokolem TLS. Tím se zajistí, že se přenosy vždycky šifrují bez ohledu na to, jestli aplikace používá šifrování nebo ne.

* **Jednoduchost připojení:** V mnoha případech použití datových proudů zařízení eliminuje nutnost složitosti nastavení virtuálních privátních sítí, aby bylo možné povolit připojení k zařízením IoT.

* **Kompatibilita se zásobníkem protokolu TCP/IP:** Datové proudy zařízení IoT Hub můžou vyhovovat provozu aplikace TCP/IP. To znamená, že tato funkce může využívat rozsáhlou škálu vlastních a také protokolů založených na standardech.

* **Snadné použití v nastavení privátní sítě:** Služba může komunikovat se zařízením, a to tak, že odkazuje na ID zařízení, nikoli na IP adresu zařízení. To je užitečné v situacích, kdy se zařízení nachází uvnitř privátní sítě a má privátní IP adresu, nebo je adresa IP přiřazena dynamicky a je na straně služby neznámá.

## <a name="device-stream-workflows"></a>Pracovní postupy streamování zařízení

Datový proud zařízení se iniciuje, když se služba pokusí o připojení k zařízení tím, že poskytuje ID zařízení. Tento pracovní postup zejména zapadá do komunikačního modelu typu klient/server, včetně SSH a RDP, kde se uživatel chce vzdáleně připojit k serveru SSH nebo RDP běžícímu na zařízení pomocí klientského programu SSH nebo RDP.

Proces vytváření datových proudů zařízení zahrnuje vyjednávání mezi koncovými body pro zařízení, službu, hlavní a streamování služby IoT Hub. Zatímco hlavní koncový bod služby IoT Hub orchestruje vytváření streamu zařízení, koncový bod streamování zpracovává přenos dat, která se mezi službou a zařízením pracuje.

### <a name="device-stream-creation-flow"></a>Tok vytváření datových proudů zařízení

Programové vytvoření datového proudu zařízení pomocí sady SDK zahrnuje následující kroky, které jsou také znázorněny na následujícím obrázku:

!["Proces handshake vysílání datového proudu zařízení"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. Aplikace zařízení zaregistruje zpětné volání předem, aby bylo upozorněno na to, kdy je do zařízení inicializován nový datový proud zařízení. Tento krok se obvykle provádí při spuštění zařízení a připojení k IoT Hub.

2. Program na straně služby inicializuje datový proud zařízení v případě potřeby tím, že poskytuje ID zařízení (_nikoli_ IP adresu).

3. Služba IoT Hub upozorní program na straně zařízení vyvoláním zpětného volání registrovaného v kroku 1. Zařízení může přijmout nebo odmítnout požadavek na spuštění streamu. Tato logika může být specifická pro váš scénář aplikace. Pokud zařízení požadavek datového proudu odmítne, služba IoT Hub odpovídajícím způsobem informují. v opačném případě následují následující kroky.

4. Zařízení vytvoří zabezpečené odchozí připojení TCP ke koncovému bodu streamování přes port 443 a upgraduje připojení k objektu WebSocket. Adresa URL koncového bodu streamování a přihlašovací údaje, které se mají použít k ověřování, se do zařízení dodávají IoT Hub jako součást žádosti odeslané v kroku 3.

5. Služba je informována o výsledku zařízení, které přijímá datový proud, a pokračuje v vytváření vlastního klienta protokolu WebSocket pro koncový bod streamování. Podobně přijímá adresu URL koncového bodu streamování a ověřovací informace z IoT Hub.

V procesu handshake výše postupujte takto:

* Proces handshake musí být dokončen do 60 sekund (krok 2 až 5), jinak se handshake nezdaří s časovým limitem a služba bude odpovídajícím způsobem oznámena.

* Po dokončení toku vytváření datových proudů bude koncový bod streamování fungovat jako proxy server a bude přenášet provoz mezi službou a zařízením přes příslušné objekty WebSocket.

* Zařízení a služba potřebují odchozí připojení k hlavnímu koncovému bodu IoT Hub a také koncový bod streamování přes port 443. Adresa URL těchto koncových bodů je k dispozici na kartě *Přehled* na portálu IoT Hub.

* Záruky spolehlivosti a řazení zavedeného datového proudu jsou v nominální hodnotě TCP.

* Všechna připojení k IoT Hub a datovým koncovým bodem streamování používají TLS a jsou zašifrované.

### <a name="termination-flow"></a>Tok ukončení

Zavedený Stream se ukončí, když se některá z připojení TCP k bráně odpojí (pomocí služby nebo zařízení). To může probíhat dobrovolně tím, že se WebSocket uzavírá na zařízení nebo v programu služby nebo pokud nedošlo k vypršení časového limitu připojení k síti nebo selhání procesu. Po ukončení připojení zařízení nebo služby ke koncovému bodu streamování bude i další připojení TCP (vynuceně) ukončeno a služba a zařízení budou v případě potřeby v případě potřeby datového proudu znovu vytvářet.

## <a name="connectivity-requirements"></a>Požadavky na připojení

Zařízení i strany datového proudu zařízení musí být schopné vytvořit připojení s povoleným protokolem TLS pro IoT Hub a jeho koncový bod streamování. K těmto koncovým bodům vyžaduje odchozí připojení přes port 443. Název hostitele přidružený k těmto koncovým bodům lze najít na kartě *přehled* IoT Hub, jak je znázorněno na následujícím obrázku:

!["Koncové body streamu zařízení"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Alternativně lze informace o koncových bodech načíst pomocí rozhraní příkazového řádku Azure CLI v části vlastnosti rozbočovače, konkrétně `property.hostname` i v `property.deviceStreams` klíčích.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

Výstupem je objekt JSON všech koncových bodů, ke kterým se musí zařízení a služba rozbočovače připojit, aby bylo možné vytvořit datový proud zařízení.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Ujistěte se, že máte nainstalovanou verzi rozhraní příkazového řádku Azure CLI 2.0.57 nebo novější. Nejnovější verzi si můžete stáhnout ze stránky [instalace Azure CLI](/cli/azure/install-azure-cli) .
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Povolení odchozího připojení k koncovým bodům streamování zařízení

Jak je uvedeno na začátku tohoto článku, vaše zařízení vytvoří odchozí připojení k IoT Hub koncový bod streamování během procesu inicializace datových proudů zařízení. Brány firewall na zařízení nebo v jeho síti musí umožňovat odchozí připojení k bráně streamování přes port 443 (Všimněte si, že komunikace probíhá přes připojení protokolu WebSocket šifrované pomocí protokolu TLS).

Název hostitele koncového bodu streamování zařízení najdete na portálu Azure IoT Hub na kartě ![ Přehled. Koncové body streamu zařízení](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Případně můžete tyto informace najít pomocí Azure CLI:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Ujistěte se, že máte nainstalovanou verzi rozhraní příkazového řádku Azure CLI 2.0.57 nebo novější. Nejnovější verzi si můžete stáhnout ze stránky [instalace Azure CLI](/cli/azure/install-azure-cli) .
>

## <a name="troubleshoot-via-device-streams-resource-logs"></a>Řešení potíží prostřednictvím protokolů zdrojů datových proudů zařízení

Můžete nastavit Azure Monitor pro shromažďování [protokolů prostředků pro datové proudy zařízení](monitor-iot-hub-reference.md#device-streams-preview) vysílané vaším IoT Hub. To může být velmi užitečné při řešení potíží.

Pomocí následujících kroků vytvořte nastavení diagnostiky pro odesílání protokolů datových proudů zařízení pro vaši IoT Hub do protokolů Azure Monitor:

1. V Azure Portal přejděte do služby IoT Hub. V levém podokně v části **monitorování** vyberte **nastavení diagnostiky**. Pak vyberte **Přidat nastavení diagnostiky**.

2. Zadejte název pro nastavení diagnostiky a v seznamu protokolů vyberte **DeviceStreams** . Pak vyberte **Odeslat do Log Analytics**. Průvodce vám umožní zvolit existující Log Analytics pracovní prostor nebo vytvořit nový.

    :::image type="content" source="media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png" alt-text="Povolit protokoly datových proudů zařízení":::

3. Po vytvoření nastavení diagnostiky pro odeslání protokolů datových proudů zařízení do Log Analyticsho pracovního prostoru můžete získat přístup k protokolům tak, že **v levém** podokně služby IoT hub v Azure Portal vyberete **protokoly** . V tabulce se zobrazí protokoly datových proudů zařízení `AzureDiagnostics` `Category=DeviceStreams` . Mějte na paměti, že může trvat několik minut, než se v tabulce objeví protokoly.

   Jak vidíte níže, identita cílového zařízení a výsledek operace je také k dispozici v protokolech.

   !["Přístup k protokolům datových proudů zařízení"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

Další informace o použití Azure Monitor s IoT Hub najdete v tématu [monitorování IoT Hub](monitor-iot-hub.md). Informace o všech protokolech prostředků, metrikách a tabulkách dostupných pro IoT Hub najdete v tématu [monitorování Azure IoT Hub data reference](monitor-iot-hub-reference.md).

## <a name="regional-availability"></a>Regionální dostupnost

Během veřejné verze Preview jsou IoT Hub datové proudy zařízení dostupné v oblastech Střed USA, Střed USA EUAP, Severní Evropa a jihovýchodní Asie. Ujistěte se prosím, že jste vytvořili centrum v jedné z těchto oblastí.

## <a name="sdk-availability"></a>Dostupnost sady SDK

Dvě strany každého streamu (na straně zařízení a služeb) používají k vytvoření tunelu sadu IoT Hub SDK. Během veřejné verze Preview si zákazníci můžou vybrat z těchto jazyků SDK:

* Datové proudy zařízení podpory sady C a C# SDK na straně zařízení.

* Sada NodeJS and C# SDK podporuje streamy zařízení na straně služby.

## <a name="iot-hub-device-stream-samples"></a>Ukázka streamu zařízení IoT Hub

Na stránce IoT Hub jsou k dispozici dva [ukázky rychlý Start](./index.yml) . Ty ukazují použití datových proudů zařízení podle aplikací.

* Ukázka *ozvěny* znázorňuje programové použití datových proudů zařízení (přímým VOLÁNÍM rozhraní API SDK).

* Ukázka *místního proxy serveru* demonstruje tunelování provozu klientských a serverových aplikací (například SSH, RDP nebo Web) prostřednictvím datových proudů zařízení.

Tyto ukázky jsou podrobněji popsány níže.

### <a name="echo-sample"></a>Ukázka ozvěny

Ukázka ozvěny znázorňuje programové použití datových proudů zařízení k posílání a přijímání bajtů mezi aplikacemi a aplikacemi pro zařízení. Všimněte si, že v různých jazycích můžete používat služby a programy zařízení. Například můžete použít program zařízení jazyka C s programem služby C#.

Tady jsou ukázky ozvěny:

* [Služba a program služby C#](quickstart-device-streams-echo-csharp.md)

* [ Program službyNode.js](quickstart-device-streams-echo-nodejs.md)

* [Program zařízení v jazyce C](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Ukázka místního proxy serveru (pro SSH nebo RDP)

Ukázka místního proxy serveru demonstruje způsob, jak povolit tunelování provozu existující aplikace, který zahrnuje komunikaci mezi klientem a serverovým programem. Toto nastavení funguje u protokolů klienta a serveru, jako jsou SSH a RDP, kde se na straně služby chová jako klient (spouštějí se klientské programy SSH nebo RDP) a na straně zařízení funguje jako server (spouští se programy démona SSH nebo server RDP).

Tato část popisuje použití datových proudů zařízení k tomu, aby uživatel mohl na zařízení přes datové proudy zařízení používat protokol RDP (případ protokolu RDP nebo jiná aplikace klienta/serveru je podobný pomocí odpovídajícího portu protokolu).

Instalační program využívá dva *místní proxy* programy uvedené na následujícím obrázku, konkrétně *proxy servery* a *místní proxy služby*. Místní proxy programy zodpovídají za provádění [metody handshake iniciace datového proudu zařízení](#device-stream-creation-flow) pomocí IoT Hub a interakce s klientem SSH a démonem SSH pomocí běžných soketů klientů a serverů.

!["Nastavení proxy streamu zařízení pro SSH/RDP"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. Uživatel spustí místní proxy server služby, aby inicializoval datový proud zařízení do zařízení.

2. Místní proxy zařízení přijme požadavek na spuštění datového proudu a tunel se naváže na IoT Hub koncový bod streamování (jak je popsáno výše).

3. Místní proxy zařízení se připojí ke koncovému bodu démona SSH naslouchat na portu 22 na zařízení.

4. Místní proxy server služby naslouchá na určeném portu, který čeká na nová připojení SSH od uživatele (ve vzorku se používá port 2222, ale dá se nakonfigurovat na jiný dostupný port). Uživatel odkazuje na port proxy serveru místní služby na místním hostiteli.

### <a name="notes"></a>Poznámky

* Výše uvedené kroky dokončí ucelené tunelové propojení mezi klientem SSH (na pravé straně) do démona SSH (na levé straně). Součástí tohoto kompletního připojení je odeslání provozu přes datový proud zařízení do IoT Hub.

* Šipky na obrázku označují směr, ve kterém jsou propojení mezi koncovými body vytvořena. Konkrétně si všimněte, že neexistují žádná příchozí připojení, která na zařízení přestanou (to je často blokováno bránou firewall).

* Volba použití portu 2222 pro místní proxy služby je libovolná volba. Proxy server je možné nakonfigurovat tak, aby používal jakýkoli jiný dostupný port.

* Volba portu 22 je závislá na protokolu a specifická pro SSH v tomto případě. V případě protokolu RDP je nutné použít port 3389. Tato možnost se dá nakonfigurovat v uvedených ukázkových programech.

Použijte odkazy níže, kde najdete pokyny ke spuštění místních proxy programů v jazyce, který si vyberete. Podobně jako u [ukázky ozvěny](#echo-sample)můžete spouštět programy proxy v místním zařízení a službách v různých jazycích, protože jsou plně interoperabilní.

* [Služba a program služby C#](quickstart-device-streams-proxy-csharp.md)

* [ Program službyNode.js](quickstart-device-streams-proxy-nodejs.md)

* [Program zařízení v jazyce C](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Další kroky

Pomocí odkazů níže můžete získat další informace o datových proudech zařízení.

> [!div class="nextstepaction"]
> [Datové proudy zařízení v IoT show (Channel 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
