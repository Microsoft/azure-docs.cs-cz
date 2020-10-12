---
title: Nasazení Live video Analytics na Azure Stack Edge
description: V tomto článku jsou uvedené kroky, které vám pomůžou nasadit živé video analýzy na Azure Stack hraničních zařízeních.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: b13bb779a5a780b21f2d5d96ed8831ef5c26564d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935265"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>Nasazení Live video Analytics na Azure Stack Edge

V tomto článku jsou uvedené kroky, které vám pomůžou nasadit živé video analýzy na Azure Stack hraničních zařízeních. Jakmile se zařízení nastaví a aktivuje, bude připraveno pro nasazení ve službě Live video Analytics. 

Pro Live video Analytics nasadíme prostřednictvím IoT Hub, ale prostředky Azure Stack Edge zveřejňují rozhraní Kubernetes API, které umožňuje zákazníkům nasazovat další řešení nesouvisející s IoT Hub, která se můžou používat s živým analýzou videí. 

> [!TIP]
> Použití rozhraní API Kubernetes (K8s) pro vlastní nasazení je pokročilý případ. Doporučuje se, aby zákazník vytvořil hraniční moduly a nasadil prostřednictvím IoT Hub do každého prostředku Azure Stack Edge místo používání rozhraní Kubernetes API. V tomto článku vám ukážeme, jak nasadit modul Live video Analytics pomocí IoT Hub.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure, ke kterému máte [oprávnění vlastníka](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner).
* Prostředek [Azure Stack Edge](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep)
   
* [IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)
* [Instanční objekt](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/create-custom-azure-resource-manager-role-how-to#create-service-principal) pro modul Live video Analytics.

   Použijte jednu z těchto oblastí, kde je IoT Hub k dispozici: Východní USA 2, Střed USA, Střed USA – sever, Japonsko – východ, Západní USA 2, Středozápadní USA, Kanada – východ, Velká Británie – jih, Francie – střed, Francie – jih, Švýcarsko – sever, Švýcarsko – západ a Japonsko – západ.
* Účet úložiště

    Doporučuje se používat účty úložiště pro obecné účely v2 (GPv2).  
    Přečtěte si další informace o [účtu úložiště pro obecné účely verze 2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade?tabs=azure-portal).
* [Visual Studio Code](https://code.visualstudio.com/) ve vývojovém počítači. Ujistěte se, že máte [rozšíření Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Ujistěte se, že je síť, ke které je připojený váš vývojový počítač, povolená prostřednictvím portu 5671 rozšířený protokol front zpráv. Tato instalace umožňuje, aby nástroje Azure IoT komunikovaly se službou Azure IoT Hub.

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>Konfigurace Azure Stack Edge pro používání služby Live video Analytics

Azure Stack Edge je řešení typu hardware jako služba a hraniční výpočetní zařízení s podporou AI s možnostmi přenosu dat v síti. Přečtěte si další informace o [Azure Stack Edge a podrobné pokyny k instalaci](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep). Chcete-li začít, postupujte podle pokynů v následujících odkazech:

* [Azure Stack vytváření prostředků Edge/Data Box Gateway](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep)
* [Instalace a nastavení](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-install)
* [Připojení a aktivace](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate)

### <a name="attach-an-iot-hub-to-azure-stack-edge"></a>Připojení IoT Hub k Azure Stack Edge

1. V [Azure Portal](https://ms.portal.azure.com)přejděte na prostředek Azure Stack Edge a klikněte na přehled. V pravém podokně na dlaždici výpočty vyberte Začínáme.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge.png" alt-text="Azure Stack Edge":::
1. Na dlaždici konfigurace hraničních výpočtů vyberte Konfigurovat výpočetní prostředky.
1. V okně Konfigurace hraničních výpočtů zadejte následující:
    
    | Pole|Hodnota|
    |---|---|
    |IoT Hub|Vyberte z nových nebo existujících.<br/>Ve výchozím nastavení se k vytváření prostředků IoT používá úroveň Standard (S1). Pokud chcete použít prostředek IoT úrovně Free, vytvořte ho a pak vyberte existující prostředek.<br/>V každém případě IoT Hub prostředek používá stejné předplatné a skupinu prostředků, které používají prostředek Azure Stack Edge.|
    |Name|Zadejte název prostředku IoT Hub.|

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-get-started.png" alt-text="Azure Stack Edge":::
1. Vyberte **Vytvořit**. Vytvoření prostředku IoT Hub trvá několik minut. Po vytvoření prostředku IoT Hub se aktualizuje a zobrazí **se konfigurace výpočtů na dlaždici** . Pokud chcete ověřit, jestli je role hraničního výpočtu nakonfigurovaná, vyberte na dlaždici **Konfigurovat výpočty** možnost **Zobrazit výpočetní** prostředky.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/edge-compute-config.png" alt-text="Azure Stack Edge":::

    > [!NOTE]
    > Pokud je dialogové okno Konfigurovat výpočetní prostředí uzavřeno, než je IoT Hub přidružen k prostředku Azure Stack Edge, IoT Hub vytvořen, ale není zobrazen v konfiguraci výpočtů. Znovu načtěte stránku po několika minutách a podívejte se, jak se zobrazí.
    
    Když se na hraničním zařízení nastaví role hraničního zpracování, vytvoří se dvě zařízení: zařízení IoT a zařízení IoT Edge. Obě zařízení je možné zobrazit v prostředku IoT Hub. V zařízení IoT Edge je spuštěn také modul runtime IoT Edge. V tomto okamžiku je k dispozici pouze Platforma Linux pro vaše zařízení IoT Edge.
    
    Jakmile budou všechny informace vyplněny, zobrazí se na kartě Konfigurace hraničních služeb něco podobného:
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/configure-edge-compute.png" alt-text="Azure Stack Edge":::
 
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Povolení výpočetních požadavků v místním uživatelském rozhraní Azure Stack Edge

Než budete pokračovat, ujistěte se, že:

* Aktivovali jste svůj prostředek Azure Stack Edge.
* K přístupu k prostředku Azure Stack Edge máte přístup k klientskému systému Windows, na kterém běží PowerShell 5,0 nebo novější.
* Chcete-li nasadit cluster Kubernetes, je nutné nakonfigurovat Azure Stack hraniční prostředky prostřednictvím [místního webového uživatelského rozhraní](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate#connect-to-the-local-web-ui-setup). 
    
    * Pokud chcete povolit výpočetní výkon, v místním webovém uživatelském rozhraní zařízení, navštivte stránku Compute.
    
        * Vyberte síťové rozhraní, které chcete povolit pro výpočetní prostředky. Vyberte Povolit. Povolení výpočetních výsledků při vytváření virtuálního přepínače na zařízení v tomto síťovém rozhraní.
        * Kubernetes testovací IP adresy uzlu a IP adresy Kubernetes externích služeb ponechte prázdné.
        * Vybrat použít – tato operace by měla trvat přibližně 2 minuty.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text="Azure Stack Edge":::

        * Pokud není DNS nakonfigurovaný pro Kubernetes rozhraní API a Azure Stack hraniční prostředek, můžete aktualizovat soubor hostitele vašeho okna.
        
            * Otevřete textový editor jako správce.
            * Otevřít soubor ' do C:\Windows\System32\drivers\etc\hosts '
            * Do souboru přidejte adresu IPv4 a název hostitele rozhraní Kubernetes API. (Tyto informace najdete na portálu Azure Stack Edge v části zařízení.)
            * Uložit a zavřít

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>Nasazení služby Live video Analytics Edge pomocí Azure Portal

V tomto případě provedeme pouze konkrétní kroky od [nasazení živých videí Analytics prostřednictvím IoT Hub](deploy-iot-edge-device.md).

1. Přeskočte kroky vytváření uživatelů a skupin a přejděte k části [nasazení Live video Analytics Edge](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module) . Postupujte podle kroků uvedených tady.
1. V kontejneru vytvořte možnosti, které nemusíte nastavovat proměnné prostředí. Proto tento krok přeskočte.
1. Otevřete kartu možnosti vytvoření kontejneru.

   * Zkopírujte a vložte následující JSON do pole, abyste omezili velikost souborů protokolu vytvořených modulem.
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > Pokud používáte protokol gRPC s přenosem sdílené paměti, použijte pro přístup ke sdílené paměti mezi živými a odvozenými řešeními hostitelský režim IPC.
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > Oddíl "vazby" ve formátu JSON má 2 položky. Nebojte se aktualizovat vazby hraničních zařízení, ale ujistěte se, že tyto adresáře existují.
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices": používá se k navázání trvalých konfiguračních dat aplikace z kontejneru a jeho uložení na hraničním zařízení.
    * "/var/Media:/var/Media": Tato akce váže mediální složky mezi hraničním zařízením a kontejnerem. Slouží k ukládání nahrávek videa při spuštění topologie mediálního grafu, která podporuje ukládání videoklipů do hraničního zařízení.
        
1. Pokračujte v krocích v dokumentu a vyplňte nastavení s dvojitým podmodulem.
1. Vyberte **Další**: trasy pro pokračování v části trasy. Zadejte trasy.

    Ponechte výchozí trasy a vyberte Další: zkontrolovat + vytvořit, abyste mohli pokračovat v části revize.
1. [Kontrola a ověření nasazení](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>Volitelné Nastavení přípojných svazků Docker

Pokud chcete zobrazit data v pracovních adresářích, postupujte podle těchto kroků, abyste před nasazením nastavili připojení ke svazku Docker. 

Tento postup popisuje vytvoření uživatele brány a nastavení sdílených složek pro zobrazení obsahu Live video Analytics Work Directory a mediální složky Live video Analytics.

> [!NOTE]
> Připojení vazby jsou podporovaná, ale připojení svazků umožňují zobrazit data a v případě potřeby vzdáleně kopírovat. Je možné použít vazby i připojení svazků, ale nemohou odkazovat na stejnou cestu kontejneru.

1. Otevřete Azure Portal a přejít na prostředek Azure Stack Edge.
1. Vytvořte **uživatele brány** , který bude mít přístup ke sdíleným složkám.
    
    1. V levém navigačním podokně klikněte na **Brána – >uživatelům**.
    1. Klikněte na **+ Přidat uživatele** do nastavení uživatelské jméno a heslo. (Doporučeno: `lvauser` ).
    1. Klikněte na **Přidat**.
    
1. Vytvořte **místní sdílenou složku** pro zachování živé analýzy videí.

    1. Klikněte na **Brána – >sdílené složky**.
    1. Klikněte na **+ Přidat sdílené složky**.
    1. Nastavte název sdílené složky. (Doporučeno: `lva` ).
    1. Ponechte typ sdílení jako SMB.
    1. Zajistěte, aby byla zaškrtnuta **možnost použít sdílenou složku s hraničními výpočty** .
    1. Ujistěte se, že je zaškrtnuté políčko **Konfigurovat jako hraniční místní sdílení** .
    1. V části Podrobnosti o uživateli udělte přístup ke sdílené složce nedávno vytvořenému uživateli.
    1. Klikněte na **vytvořit**.
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="Azure Stack Edge":::
    
1. Vytvořte vzdálenou sdílenou složku pro úložiště synchronizace souborů.

    1. Nejdřív vytvořte účet úložiště objektů BLOB ve stejné oblasti.
    1. Klikněte na **Brána – >sdílené složky**.
    1. Klikněte na **+ Přidat sdílené složky**.
    1. Nastavte název sdílené složky. (Doporučeno: médium).
    1. Ponechte typ sdílení jako SMB.
    1. Zajistěte, aby byla zaškrtnuta **možnost použít sdílenou složku s hraničními výpočty** .
    1. Ujistěte se, že není zaškrtnutá možnost **Konfigurovat jako místní sdílení Edge** .
    1. Vyberte nedávno vytvořený účet úložiště.
    1. Nastavte název kontejneru.
    1. Nastavte typ úložiště na objekt blob bloku.
    1. V části Podrobnosti o uživateli udělte přístup ke sdílené složce nedávno vytvořenému uživateli.
    1. Klikněte na **vytvořit**.    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="Azure Stack Edge"
            }]
        }
    }
    ```

### <a name="verify-that-the-module-is-running"></a>Ověřte, že je modul spuštěný.

Posledním krokem je ujistit se, že je modul připojený a spuštěný podle očekávání. Běhový stav modulu by měl být spuštěný pro vaše IoT Edge zařízení v prostředku IoT Hub.

Pokud chcete ověřit, že je modul spuštěný, udělejte toto:

1. V Azure Portal se vraťte k prostředku Azure Stack Edge.
1. Vyberte dlaždici moduly. Tím přejdete do okna moduly. V seznamu modulů Identifikujte modul, který jste nasadili. Běhový stav modulu, který jste přidali, by měl být spuštěný.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="Azure Stack Edge":::

### <a name="configure-the-azure-iot-tools-extension"></a>Konfigurace rozšíření Azure IoT Tools

Podle těchto pokynů se připojte ke službě IoT Hub pomocí rozšíření Azure IoT Tools.

1. V Visual Studio Code vyberte Zobrazit > Průzkumník. Nebo vyberte CTRL + SHIFT + E.
1. V levém dolním rohu karty Průzkumník vyberte Azure IoT Hub.
1. Kliknutím na ikonu Další možnosti zobrazíte kontextovou nabídku. Pak vyberte nastavit IoT Hub připojovací řetězec.
1. Po zobrazení vstupního pole zadejte připojovací řetězec IoT Hub. 

   * Připojovací řetězec získáte tak, že přejdete na IoT Hub v Azure Portal a v levém navigačním podokně kliknete na zásady sdíleného přístupu.
   * Klikněte na iothubowner získat sdílené přístupové klíče.
   * Zkopírujte připojovací řetězec – primární klíč a vložte ho do vstupního pole na VSCode.

   Připojovací řetězec bude vypadat takto:<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Pokud je připojení úspěšné, zobrazí se seznam hraničních zařízení. Měl by se zobrazit Azure Stack Edge. Pomocí místní nabídky teď můžete spravovat zařízení IoT Edge a pracovat s Azure IoT Hub. Chcete-li zobrazit moduly nasazené na hraničním zařízení, v části zařízení Azure Stack rozbalte uzel moduly.
    
## <a name="troubleshooting"></a>Řešení potíží

* Přístup k rozhraní Kubernetes API (kubectl).

    * Podle dokumentace nakonfigurujte počítač pro [přístup ke clusteru Kubernetes](https://review.docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-create-kubernetes-cluster?toc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Ftoc.json&bc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Fbreadcrumb%2Ftoc.json&branch=release-tzl#debug-kubernetes-issues).
    * Všechny nasazené IoT Edge moduly používají `iotedge` obor názvů. Nezapomeňte při použití kubectl použít.
* Protokoly modulů

    `iotedge`Nástroj není přístupný pro získání protokolů. K zobrazení protokolů nebo kanálu do souboru je nutné použít [protokoly kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)  . Příklad: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`
* Metriky pod a Node

    Pomocí [kubectl shora](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)  Zobrazte metriky pod a uzlem. (Tato funkce bude k dispozici v další verzi Azure Stack Edge. >v2007)<br/>`kubectl top pods -n iotedge`
* Sítě modulů pro zjišťování modulu v Azure Stack hraničních zařízeních je nutné, aby modul měl vazbu portu hostitele v createOptions. Modul bude pak možné adresovat `moduleName:hostport` .
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* Připojení svazku

    Modul se nepodaří spustit, pokud se kontejner pokouší připojit svazek k existujícímu a neprázdnému adresáři.
* Sdílená paměť

    Sdílená paměť v Azure Stackch hraničních prostředcích je podporována napříč lusky v jakémkoli oboru názvů pomocí hostitele IPC.
    Konfigurace sdílené paměti v modulu Edge pro nasazení prostřednictvím IoT Hub.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    (Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API.
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* Upřesnit Pod společným umístěním

    Při použití K8s k nasazení vlastních řešení pro odvození, která komunikují se službou Live video Analytics prostřednictvím gRPC, je nutné zajistit, aby se lusky nasadily na stejných uzlech jako v modulech Live video Analytics.

    * Možnost 1 – použijte spřažení uzlů a předdefinované popisky uzlů pro společné umístění.

    V současné době se NodeSelector vlastní konfigurace nejedná o možnost, protože uživatelé nemají přístup k nastavení popisků na uzlech. V závislosti na topologii a konvenci pojmenování zákazníka ale můžou být schopné použít [popisky integrovaných uzlů](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels). Část nodeAffinity, která se odkazuje na Azure Stack hraničních prostředků s živou analýzou videa, se dá přidat do odvozených manifestů pod, aby bylo možné dosáhnout společného umístění.
    * Možnost 2 – pro společné umístění použijte spřažení pod (doporučeno).
Kubernetes má podporu pro [spřažení pod](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)  , která může naplánovat lusky na stejném uzlu. Oddíl podAffinity odkazující na modul Live video Analytics se dá přidat do odvozených manifestů pod, aby bylo možné dosáhnout společného umístění.

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```

## <a name="next-steps"></a>Další kroky

Pomocí modulu můžete analyzovat živé streamy videa vyvoláním přímých metod. [Vyvolat přímé metody](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls) v modulu.
