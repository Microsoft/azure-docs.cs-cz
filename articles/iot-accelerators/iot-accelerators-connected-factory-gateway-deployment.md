---
title: Nasazení brány připojené továrny – Azure | Dokumentace Microsoftu
description: Jak nasadit bránu na umožnění konektivity k akcelerátor řešení připojená továrna Windows nebo Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: ce2391014eb5e15650c0a81ebb8e2fd4a08ca96b
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139220"
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-solution-accelerator-on-windows-or-linux"></a>Nasazení brány edge pro akcelerátor řešení připojená továrna ve Windows nebo Linuxu

Budete potřebovat dvě součásti softwaru k nasazení brány edge pro *připojená továrna* akcelerátor řešení:

- Proxy  *serveru OPC* naváže připojení k připojené továrny. Proxy server OPC a čeká na příkazy a ovládání zpráv z rozhraní integrované OPC v prohlížeči, na kterém běží na portálu řešení připojené továrny.

- Vydavatel  *OPC* připojí k existující místní servery OPC UA a předává telemetrické zprávy z nich pro propojenou továrnu. Můžete připojit pomocí klasické zařízení OPC [OPC classic adaptér pro OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Obě komponenty jsou open source a jsou k dispozici jako source na Githubu a jako kontejnery Dockeru na Dockerhubu:

| GitHubu | Dockerhubu |
| ------ | --------- |
| [Vydavatel OPC](https://github.com/Azure/iot-edge-opc-publisher) | [Vydavatel OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [Proxy server OPC](https://github.com/Azure/iot-edge-opc-proxy)         | [Proxy server OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Není nutné veřejnou IP adresu nebo otevřené příchozí porty v bráně firewall pro žádné komponenty. Proxy server OPC a vydavatel OPC součásti používat jenom odchozí port 443.

Kroky v tomto článku ukazují, jak nasazení hraniční brány pomocí Docker ve Windows nebo Linuxu. Brána umožňuje připojení k akcelerátor řešení připojená továrna. Můžete také komponenty bez připojené továrny.

> [!NOTE]
> Obě komponenty je možné použít jako moduly v [Azure IoT Edge](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Vyberte zařízení brány

Pokud ještě nemáte zařízení brány, Microsoft doporučuje že koupit komerčních bran jednoho z jeho partnerů. Seznam zařízení brány, které jsou kompatibilní s řešení připojená továrna, přejděte [katalog zařízení Azure IoT](https://catalog.azureiotsuite.com/?q=opc). Postupujte podle pokynů, které jsou součástí zařízení nastavit bránu.

Pokud je potřeba ručně nakonfigurovat stávající zařízení brány, použijte následující pokyny.

## <a name="install-and-configure-docker"></a>Instalace a konfigurace Dockeru

Nainstalujte [Docker pro Windows](https://www.docker.com/docker-windows) na zařízení brány založené na Windows nebo pomocí Správce balíčků pro instalaci dockeru na vašem zařízení brány založené na Linuxu.

Během instalace Dockeru pro Windows vyberte jednotku na počítači hostitele se sdílet s Dockerem. Následující snímek obrazovky ukazuje sdílení **D** jednotky v systému Windows. Sdílení jednotku umožňuje přístup na hostitelskou jednotku z uvnitř kontejneru dockeru:

![Nainstalovat Docker for Windows](./media/iot-accelerators-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> Můžete také provést tento krok po instalaci dockeru z **nastavení** dialogového okna. Klikněte pravým tlačítkem na **Docker** ikonu na hlavním panelu systému Windows a zvolte **nastavení**. Pokud hlavní aktualizace Windows se nasadilo do systému, jako je Windows Fall Creators update, zrušení sdílení jednotek a sdílejte je znovu k aktualizaci přístupová práva.

Pokud používáte Linux, není nutná žádná další konfigurace k umožnění přístupu k systému souborů.

Na Windows vytvořte složku na disku, který sdílíte s Dockerem, v Linuxu vytvořte ve složce kořenovému systému souborů. Tento návod odkazuje na tuto složku jako `<SharedFolder>`.

Když odkazujete `<SharedFolder>` v příkazu Docker, nezapomeňte použít správnou syntaxi pro váš operační systém. Tady jsou dva příklady, jeden pro Windows a jeden pro Linux:

- Pokud se pomocí složky `D:\shared` na Windows jako vaše `<SharedFolder>`, je syntaxe příkazu Docker `d:/shared`.

- Pokud se pomocí složky `/shared` v Linuxu jako vaše `<SharedFolder>`, je syntaxe příkazu Docker `/shared`.

Další informace najdete v tématu [použít svazky](https://docs.docker.com/engine/admin/volumes/volumes/) odkaz na modul docker.

## <a name="configure-the-opc-components"></a>Konfigurace komponent OPC

Před instalací komponenty OPC, proveďte následující kroky k přípravě vašeho prostředí:

1. K dokončení nasazení brány, je potřeba **iothubowner** připojovací řetězec služby IoT Hub v nasazení připojené továrny. V [webu Azure portal](http://portal.azure.com/), přejděte do služby IoT Hub ve skupině prostředků vytvořili při nasazení řešení připojené továrny. Klikněte na tlačítko **zásady sdíleného přístupu** přístup **iothubowner** připojovací řetězec:

    ![Najděte připojovací řetězec služby IoT Hub](./media/iot-accelerators-connected-factory-gateway-deployment/image2.png)

    Kopírovat **připojovací řetězec – primární klíč** hodnotu.

1. Pokud chcete povolit komunikaci mezi kontejnery dockeru, musíte síťovým mostem uživatelem definované. K vytvoření síťového mostu pro vaše kontejnery, spusťte následující příkazy z příkazového řádku:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Chcete-li ověřit **iot_edge** síťového mostu byl vytvořen, spusťte následující příkaz:

    ```cmd/sh
    docker network ls
    ```

    Vaše **iot_edge** síťovým mostem je zahrnuta v seznamu sítí.

Pokud chcete spustit vydavatele OPC, spusťte následující příkaz z příkazového řádku:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.4 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- [Githubu vydavatele OPC](https://github.com/Azure/iot-edge-opc-publisher) a [docker spusťte odkaz](https://docs.docker.com/engine/reference/run/) poskytují další informace o:

  - Možnosti příkazového řádku dockeru zadané před název kontejneru (`microsoft/iot-edge-opc-publisher:2.1.4`).
  - Význam parametry příkazového řádku vydavatel OPC zadán za názvem kontejneru (`microsoft/iot-edge-opc-publisher:2.1.4`).

- `<IoTHubOwnerConnectionString>` Je **iothubowner** sdíleného přístupu zásad připojovací řetězec z portálu Azure portal. Tento připojovací řetězec jste si zkopírovali v předchozím kroku. Potřebujete pouze tento připojovací řetězec pro první spuštění vydavatele OPC. Při dalším spuštění by ho vynecháte protože představuje bezpečnostní riziko.

- `<SharedFolder>` Používáte a jeho syntaxe je popsaný v části [instalaci a konfiguraci Dockeru](#install-and-configure-docker). Vydavatel OPC používá `<SharedFolder>` na:

    - Čtení a zápis do konfiguračního souboru vydavatele OPC.
    - Zápis do souboru protokolu.
    - Oba tyto soubory zpřístupníte vně kontejneru.

- Vydavatel OPC načte konfiguraci z **publishednodes.json** soubor, který se čtou a zapisují do `<SharedFolder>/docker` složky. Tento konfigurační soubor definuje, která data uzlu OPC UA na daném serveru OPC UA, kterou vydavatel OPC Přihlaste se k odběru. Úplná syntaxe **publishednodes.json** souboru je popsaný na [vydavatel OPC](https://github.com/Azure/iot-edge-opc-publisher) stránku na Githubu. Když chcete přidat bránu, vložit prázdnou **publishednodes.json** do složky:

    ```json
    [
    ]
    ```

- Pokaždé, když se server OPC UA upozorní vydavatel OPC změny dat, nová hodnota je odeslána do služby IoT Hub. V závislosti na nastavení dávkování vydavatele OPC nejdřív shromažďují data předtím, než odešle data do služby IoT Hub v dávce.

- Docker nepodporuje překladu názvů NetBIOS, pouze překlad názvů DNS. Pokud nemáte DNS server v síti, můžete použít zástupné řešení uvedené v předchozím příkladu příkazového řádku. Předchozí příklad příkazového řádku používá `--add-host` parametru se přidá záznam do souboru hostitelů kontejnerů. Tato položka umožňuje vyhledávání názvu hostitele daném `<OpcServerHostname>`, řešení pro danou adresu IP `<IpAddressOfOpcServerHostname>`.

- OPC UA k ověřování a šifrování používá certifikáty X.509. Ustaví certifikát vydavatele OPC, které se připojujete k serveru OPC UA, tak, aby byl důvěřuje vydavatel OPC. Úložiště certifikátů vydavatel OPC se nachází v `<SharedFolder>/CertificateStores` složky. Můžete vyhledat certifikát vydavatele OPC v `trusted/certs` složky `CertificateStores` složky.

  Postup pro konfiguraci serveru OPC UA závisí na zařízení, které používáte. Tyto kroky jsou obvykle popsané v uživatelské příručce serveru OPC UA.

Pokud chcete nainstalovat proxy serveru OPC, spusťte následující příkaz z příkazového řádku:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Stačí jednou spustit instalaci v systému.

Použijte následující příkaz pro spuštění proxy serveru OPC:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -D /mapped/cs.db
```

Proxy server OPC uloží připojovací řetězec během instalace. Při dalším spuštění by měly vynechat připojovací řetězec, protože představuje bezpečnostní riziko.

## <a name="enable-your-gateway"></a>Povolit bránu

Proveďte následující kroky, aby vaše brány v akcelerátor řešení připojená továrna:

1. Když obě komponenty jsou spuštěné, přejděte **připojit svůj vlastní Server OPC UA** stránky na portálu řešení připojená továrna. Tato stránka je pouze pro správce dostupný v řešení. Zadejte adresu URL koncového bodu vydavatele (opc.tcp://publisher: 62222) a klikněte na tlačítko **připojit**.

1. Navázání vztahu důvěryhodnosti mezi portálem propojenou továrnu a vydavatel OPC. Když se zobrazí upozornění certifikátu, klikněte na tlačítko **pokračovat**. V dalším kroku se zobrazí chyba, že nedůvěřuje vydavatele OPC UA webového klienta. Chcete-li tuto chybu vyřešit, zkopírujte **UA webový klient** certifikátu `<SharedFolder>/CertificateStores/rejected/certs` složku `<SharedFolder>/CertificateStores/trusted/certs` složky na bráně. Není nutné restartovat bránu.

Je možné připojit se k bráně z cloudu a jste připraveni přidat servery OPC UA do řešení.

## <a name="add-your-own-opc-ua-servers"></a>Přidat vlastní servery OPC UA

Chcete-li přidat vlastní servery OPC UA na akcelerátor řešení připojená továrna:

1. Přejděte **připojit vlastní server OPC UA** stránky na portálu řešení připojená továrna.

    1. Spuštění, který chcete připojit k serveru OPC UA. Zkontrolujte, jestli váš server OPC UA dosažitelný z vydavatele OPC a proxy serveru OPC, které běží v kontejneru. Zobrazit předchozí poznámky o překlad názvů.
    1. Zadejte adresu URL koncového bodu serveru OPC UA (`opc.tcp://<host>:<port>`) a klikněte na tlačítko **připojit**.
    1. Proces instalace připojení vytváří vztah důvěryhodnosti mezi portálem připojená továrna (klienta OPC UA) a server OPC UA, které se pokoušíte připojit. Na řídicím panelu připojená továrna získáte **nelze ověřit certifikát serveru chcete připojit** upozornění. Když se zobrazí upozornění certifikátu, klikněte na tlačítko **pokračovat**.
    1. Obtížnější instalační program je konfiguraci certifikátu, který se snažíte připojit k serveru OPC UA. Pro servery založené na PC OPC UA se může jenom zobrazit dialogové okno upozornění na řídicím panelu můžete potvrdit. For embedded systems serveru OPC UA najdete v dokumentaci serveru OPC UA k vyhledání jak tuto úlohu lze provést. K dokončení této úlohy, budete potřebovat certifikát klienta OPC UA portál připojené továrny. Správce můžete stáhnout tento certifikát **připojit vlastní server OPC UA** stránky:

        ![Portál řešení](./media/iot-accelerators-connected-factory-gateway-deployment/image4.png)

1. Procházet stromu OPC UA uzlů serveru OPC UA, klikněte pravým tlačítkem na uzly OPC, které mají k odesílání hodnoty pro propojenou továrnu a vyberte **publikovat**.

1. Telemetrie toky teď ze zařízení brány. Můžete zobrazit telemetrii v **umístění továren** zobrazení portálu na připojená továrna **nový objekt pro vytváření**.

## <a name="next-steps"></a>Další postup

Další informace o architektuře akcelerátor řešení připojená továrna najdete v tématu [seznámení s akcelerátory řešení připojená továrna](iot-accelerators-connected-factory-sample-walkthrough.md).

Další informace o [referenční implementace vydavatele OPC](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).