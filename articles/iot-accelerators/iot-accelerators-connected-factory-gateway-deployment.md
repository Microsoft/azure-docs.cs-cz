---
title: Nasazení brány připojené Factory - Azure | Microsoft Docs
description: Postup nasazení brány na pro umožnění připojení k akcelerátoru řešení připojen objekt pro vytváření systému Windows nebo Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: c2805ddf7627ad520f6cc6585baedc7f5194aad6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626900"
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-solution-accelerator-on-windows-or-linux"></a>Nasazení vstupní brána pro připojení objektu pro vytváření řešení akcelerátor v systému Windows nebo Linux

Budete potřebovat dvě součásti softwaru k nasazení hraniční brány pro *připojen objekt pro vytváření* akcelerátoru řešení:

- *OPC Proxy* naváže připojení k připojené pro vytváření. Proxy server OPC potom počká, než pro příkazy a ovládání zprávy z integrované OPC prohlížeče, který běží na portálu řešení připojen objekt pro vytváření.

- *OPC vydavatele* připojí k existující místní OPC UA servery a předává telemetrické zprávy z nich připojený objekt pro vytváření. Můžete připojit k OPC classic zařízení pomocí [OPC classic adaptér OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Obě součásti jsou open source a jsou k dispozici jako zdroj na Githubu a jako kontejnery Docker na DockerHub:

| GitHubu | DockerHub |
| ------ | --------- |
| [Vydavatel OPC](https://github.com/Azure/iot-edge-opc-publisher) | [Vydavatel OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC Proxy](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Není nutné veřejnou IP adresu nebo otevřít příchozí porty v bráně firewall pro žádné komponenty. Komponenty OPC Proxy a OPC vydavatele používat jenom odchozí port 443.

Kroky v tomto článku ukazují, jak nasadit vstupní brána pomocí Docker v systému Windows nebo Linux. Brána umožňuje připojení k akcelerátoru řešení připojen objekt pro vytváření. Můžete taky komponenty bez připojen objekt pro vytváření.

> [!NOTE]
> Obě součásti lze použít jako moduly v [Azure IoT Edge](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Vyberte zařízení brány

Pokud ještě nemáte zařízení brány, Microsoft doporučuje že zakoupit komerční brány jednoho z jeho partnerů. Seznam zařízení brány, které jsou kompatibilní s připojen objekt pro vytváření řešení, najdete v článku [katalogu zařízení Azure IoT](https://catalog.azureiotsuite.com/?q=opc). Postupujte podle pokynů, které jsou součástí zařízení nastavit bránu.

Alternativně použijte následující pokyny k ruční konfiguraci ze stávajících zařízení brány.

## <a name="install-and-configure-docker"></a>Instalace a konfigurace Docker

Nainstalujte [Docker pro systém Windows](https://www.docker.com/docker-windows) vaše zařízení brány se systémem Windows nebo použijete Správce balíčků pro instalaci docker na zařízení brány se systémem Linux.

Během instalace Docker pro systém Windows vyberte jednotku na počítači hostitele sdílet s Docker. Následující snímek obrazovky ukazuje sdílení **D** jednotky v systému Windows pro povolení přístupu k hostitelskou jednotku z uvnitř kontejner docker:

![Nainstalujte Docker pro Windows](./media/iot-accelerators-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> Můžete také provést tento krok po instalaci dockerem **nastavení** dialogové okno. Klikněte pravým tlačítkem na **Docker** ikonu na hlavním panelu systému Windows a vyberte **nastavení**. Pokud hlavní aktualizace systému Windows se nasadilo do systému, například aktualizaci Creators patří Windows, zrušení sdílení jednotky a sdílet je znovu a aktualizujte přístupová práva.

Pokud používáte Linux, není nutná žádná další konfigurace pro povolení přístupu k systému souborů.

V systému Windows vytvořte složku na jednotku, kterou jste sdíleli s Docker, v systému Linux vytvořte složku kořenové systému souborů. Tento názorný postup odkazuje na složku jako `<SharedFolder>`.

Když odkazujete `<SharedFolder>` v příkazu Docker, je nutné používat správnou syntaxi pro operační systém. Zde jsou dva příklady, jednu pro systém Windows a druhou pro Linux:

- Pokud vaše jsou pomocí složce `D:\shared` v systému Windows jako vaše `<SharedFolder>`, tady je syntax příkazu Docker `d:/shared`.

- Pokud vaše jsou pomocí složce `/shared` v systému Linux jako vaše `<SharedFolder>`, tady je syntax příkazu Docker `/shared`.

Další informace najdete v článku [použít svazky](https://docs.docker.com/engine/admin/volumes/volumes/) docker odkaz na modul.

## <a name="configure-the-opc-components"></a>Konfigurovat součásti OPC

Před instalací komponenty OPC, proveďte následující kroky při přípravě svého prostředí:

1. K dokončení nasazení brány, potřebujete **iothubowner** připojovací řetězec služby IoT Hub ve vašem nasazení připojen objekt pro vytváření. V [portál Azure](http://portal.azure.com/), přejděte do služby IoT Hub ve skupině prostředků vytvořili při nasazení řešení připojen objekt pro vytváření. Klikněte na tlačítko **zásady sdíleného přístupu** k přístupu **iothubowner** připojovací řetězec:

    ![Najděte připojovací řetězec služby IoT Hub](./media/iot-accelerators-connected-factory-gateway-deployment/image2.png)

    Kopírování **připojovací řetězec primární klíč** hodnotu.

1. Povolit komunikaci mezi docker kontejnery, je třeba síťový most definovaný uživatelem. Pokud chcete vytvořit síť most pro vaše kontejnery, spusťte následující příkazy v příkazovém řádku:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Chcete-li ověřit **iot_edge** síťový most byl vytvořen, spusťte následující příkaz:

    ```cmd/sh
    docker network ls
    ```

    Vaše **iot_edge** síťový most je obsažena v seznamu sítí.

Pokud chcete spustit OPC vydavatele, spusťte následující příkaz na příkazovém řádku:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.3 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- [OPC vydavatele Githubu](https://github.com/Azure/iot-edge-opc-publisher) a [docker spustit odkaz](https://docs.docker.com/engine/reference/run/) poskytují další informace o:

  - Možnosti příkazového řádku dockeru zadaný před název kontejneru (`microsoft/iot-edge-opc-publisher:2.1.3`).
  - Význam parametry příkazového řádku OPC vydavatele zadaný po název kontejneru (`microsoft/iot-edge-opc-publisher:2.1.3`).

- `<IoTHubOwnerConnectionString>` Je **iothubowner** sdílené připojovací řetězec s přístupem zásady z portálu Azure. Tento připojovací řetězec jste zkopírovali v předchozím kroku. Potřebujete jenom tento připojovací řetězec pro první spuštění OPC vydavatele. Při dalším spuštění by měl vynechejte ho zaškrtávací bezpečnostní riziko.

- `<SharedFolder>` Používáte a jeho syntaxe je popsaný v části [nainstalovat a nakonfigurovat Docker](#install-and-configure-docker). OPC vydavatel používá `<SharedFolder>` ke čtení a zápis do konfiguračního souboru OPC vydavatele, zapisovat do souboru protokolu a zpřístupnit oba tyto soubory mimo kontejneru.

- OPC vydavatele načte konfiguraci z **publishednodes.json** souboru, který je číst z a zapisovat do `<SharedFolder>/docker` složky. Tento konfigurační soubor definuje, která data uzlu OPC UA na daném serveru OPC UA, které se musí přihlásit k odběru OPC vydavatele. Úplná syntaxe **publishednodes.json** souboru je popsaný v [OPC vydavatele](https://github.com/Azure/iot-edge-opc-publisher) stránky na Githubu. Když přidáváte bránu, put prázdnou **publishednodes.json** do složky:

    ```json
    [
    ]
    ```

- Vždy, když server OPC UA upozorní OPC vydavatel změny dat, nová hodnota se odesílají do služby IoT Hub. V závislosti na nastavení dávkování nejprve OPC vydavatele shromažďovat data předtím, než odešle data do služby IoT Hub v dávce.

- Docker nepodporuje překlad názvů pro rozhraní NetBIOS, pouze překlad názvu DNS. Pokud nemáte DNS server v síti, můžete použít alternativní řešení v předchozím příkladu příkazového řádku. Předchozí příklad příkazového řádku používá `--add-host` parametr přidání položky do souboru hostitelů kontejnery. Umožňuje vyhledávání název hostitele pro tuto položku daném `<OpcServerHostname>`, řešení pro danou adresu IP `<IpAddressOfOpcServerHostname>`.

- OPC UA používá X.509 – certifikáty pro ověřování a šifrování. Je nutné umístit OPC vydavatel certifikátu na server OPC UA, ke kterému se připojujete, a zda že důvěřovat OPC vydavatele. Úložiště certifikátů OPC vydavatele je umístěn ve `<SharedFolder>/CertificateStores` složky. Můžete najít certifikát vydavatele OPC v `trusted/certs` složku `CertificateStores` složky.

  Postup konfigurace serveru OPC UA závisí na zařízení, které používáte. Tyto kroky jsou obvykle popsané v uživatelské příručce OPC UA serveru.

K instalaci OPC Proxy, spusťte na příkazovém řádku následující příkaz:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Potřebujete jenom jednou spustit instalaci v systému.

Ke spuštění OPC proxy serveru použijte následující příkaz:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -D /mapped/cs.db
```

OPC Proxy uloží připojovací řetězec během instalace. Při dalším spuštění by měl vynechejte připojovací řetězec, protože ji ale představuje riziko zabezpečení.

## <a name="enable-your-gateway"></a>Povolit bránu

Proveďte následující kroky k povolení bránu ve akcelerátoru připojen objekt pro vytváření řešení:

1. Když obě komponenty běží, přejdete **připojit vlastní OPC UA Server** na portálu řešení připojen objekt pro vytváření. Tato stránka je dostupný jenom pro správce v řešení. Zadejte adresu URL koncového bodu vydavatele (opc.tcp://publisher: 62222) a klikněte na tlačítko **Connect**.

1. Vytvořte vztah důvěryhodnosti mezi portálem připojen objekt pro vytváření a OPC vydavatele. Když se zobrazí varování týkající se certifikátu, klikněte na tlačítko **pokračovat**. V dalším kroku, zobrazí chyba, není OPC vydavateli důvěřujete webového uživatelský Agent klienta. Chcete-li tuto chybu vyřešit, zkopírujte **uživatelský Agent webového klienta** certifikátu z `<SharedFolder>/CertificateStores/rejected/certs` složku pro `<SharedFolder>/CertificateStores/trusted/certs` složky na bráně. Není nutné restartovat bránu.

Již se můžete připojit k bráně z cloudu a jste připraveni přidat servery OPC UA k řešení.

## <a name="add-your-own-opc-ua-servers"></a>Přidat vlastní OPC UA servery

Přidání vlastního OPC UA serverů do akcelerátor připojen objekt pro vytváření řešení:

1. Vyhledejte **připojit serverem OPC UA** na portálu řešení připojen objekt pro vytváření.

    1. Spuštění, které chcete připojit k serveru OPC UA. Ujistěte se, že váš server OPC UA dostupný z OPC vydavatele a Proxy OPC spuštěné v kontejneru (viz předchozí komentáře o překladu názvů).
    1. Zadejte adresu URL koncového bodu serveru OPC UA (`opc.tcp://<host>:<port>`) a klikněte na tlačítko **Connect**.
    1. Jako součást instalace připojení je vytvořen vztah důvěryhodnosti mezi portálem připojen objekt pro vytváření (OPC UA klienta) a OPC UA serveru, který se pokoušíte připojit. V řídicím panelu Factory připojený dostanete **nelze ověřit certifikát serveru se chcete připojit** upozornění. Když se zobrazí varování týkající se certifikátu, klikněte na tlačítko **pokračovat**.
    1. Obtížnější instalační program je konfiguraci certifikátu, který se pokoušíte připojit k serveru OPC UA. Pro počítače na základě OPC UA serverů, můžete v řídicím panelu můžete potvrdit obdržet právě dialog s upozorněním. Pro systémy embedded OPC UA server najdete v dokumentaci OPC UA serveru k vyhledání jak tento úkol probíhá. Tuto úlohu dokončit, musíte certifikát klienta OPC UA portálu připojen objekt pro vytváření. Správce můžete na stáhnout tento certifikát **připojit serverem OPC UA** stránky:

        ![Portál řešení](./media/iot-accelerators-connected-factory-gateway-deployment/image4.png)

1. Procházet uzlů stromu OPC UA OPC UA serveru, klikněte pravým tlačítkem na OPC uzly, které chcete odeslat hodnoty pro vytváření připojení a vyberte **publikování**.

1. Telemetrická data jsou nyní z zařízení brány. Můžete zobrazit telemetrii v **umístění objektu pro vytváření** zobrazení připojen objekt pro vytváření portálu pod **nový objekt pro vytváření**.

## <a name="next-steps"></a>Další postup

Další informace o architektuře akcelerátor připojen objekt pro vytváření řešení najdete v tématu [připojen objekt pro vytváření řešení akcelerátoru návod](iot-accelerators-connected-factory-sample-walkthrough.md).

Další informace o [OPC vydavatele odkaz na implementaci](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).