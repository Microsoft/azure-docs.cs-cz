---
title: Telemetrie a protokolování pro kontejnery prostorových analýz
titleSuffix: Azure Cognitive Services
description: Prostorová analýza poskytuje každému kontejneru společné přehledy, protokolování a nastavení zabezpečení pro konfigurační rozhraní.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: aahi
ms.openlocfilehash: dd1b6d216f6225a13d86aa2435b5b1c807547ec3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014573"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetrie a řešení potíží

Prostorová analýza zahrnuje sadu funkcí pro monitorování stavu systému, které pomáhají s diagnostikou problémů.

## <a name="enable-visualizations"></a>Povolit vizualizace

Pokud chcete povolit vizualizaci událostí AI Insights ve snímku videa, musíte použít `.debug` verzi [operace prostorové analýzy](spatial-analysis-operations.md) na stolním počítači. Vizualizace není možná na Azure Stack hraničních zařízeních. K dispozici jsou čtyři operace ladění.

Pokud vaše zařízení není Azure Stack hraniční zařízení, upravte soubor manifestu nasazení pro [stolní počítače](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) tak, aby se pro proměnnou prostředí používala správná hodnota `DISPLAY` . Musí se shodovat s `$DISPLAY` proměnnou v hostitelském počítači. Po aktualizaci manifestu nasazení znovu nasaďte kontejner.

Po dokončení nasazení bude pravděpodobně nutné zkopírovat `.Xauthority` soubor z hostitelského počítače do kontejneru a restartovat jej. V následující ukázce `peopleanalytics` je název kontejneru v hostitelském počítači.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Shromažďování telemetrie stavu systému

Telegraf je open source obrázek, který pracuje s prostorovou analýzou a je k dispozici v Microsoft Container Registry. Přebírá následující vstupy a odesílá je do Azure Monitor. Modul telegraf se dá sestavit s požadovanými vlastními vstupy a výstupy. Konfigurace modulu telegraf v prostorové analýze je součástí manifestu nasazení (odkaz výše). Tento modul je nepovinný a je možné ho odebrat z manifestu, pokud ho nepotřebujete. 

Vztahují 
1. Metriky prostorové analýzy
2. Metriky disku
3. Metriky procesoru
4. Metriky Docker
5. Metriky GPU

Činnosti
1. Azure Monitor

Zadaný modul telegrafa prostorových analýz bude publikovat všechna data telemetrie vygenerovaná kontejnerem prostorové analýzy do Azure Monitor. Informace o přidání Azure Monitor do předplatného najdete v [Azure monitor](../../azure-monitor/overview.md) .

Po nastavení Azure Monitor budete muset vytvořit přihlašovací údaje, které modulu umožní odeslat telemetrii. K vytvoření nového instančního objektu můžete použít Azure Portal, nebo ho vytvořit pomocí příkazu Azure CLI níže.

> [!NOTE] 
> Tento příkaz vyžaduje, abyste měli k předplatnému oprávnění vlastníka. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/...”
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

V manifestu nasazení [Azure Stack hraničního zařízení](https://go.microsoft.com/fwlink/?linkid=2142179) nebo jiném [stolním počítači](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)vyhledejte modul *telegraf* a nahraďte následující hodnoty informace o instančním objektu z předchozího kroku a znovu proveďte nasazení.

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

Po nasazení modulu telegraf můžete k nahlášeným metrikám přistup prostřednictvím služby Azure Monitor, nebo výběrem **monitorování** v IoT Hub na Azure Portal.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Sestava telemetrie Azure Monitor":::

### <a name="system-health-events"></a>Události stavu systému

| Název události | Popis|
|------|---------|
|archon_exit    |Odesílá se, když uživatel změní stav modulu prostorové analýzy ze *spuštěno* na *Zastaveno*.  |
|archon_error   |Odesílá se v případě, že dojde k chybě kontejneru v případě jakéhokoli procesu. Toto je kritická chyba.  |
|InputRate  |Rychlost, s jakou graf zpracovává vstup videa Nahlášeno každých 5 minut. | 
|OutputRate     |Rychlost, s jakou má graf výstup AI Insights Nahlášeno každých 5 minut. |
|archon_allGraphsStarted | Odesílá se, když se spustí všechny grafy. |
|archon_configchange    | Odesílá se, když se změní konfigurace grafu. |
|archon_graphCreationFailed     |Odesílá se, když se nepovede spustit graf s nahlášeným řetězcem `graphId` . |
|archon_graphCreationSuccess    |Odesílá se, když se graf s nahlášeným `graphId` spuštěním úspěšně spustí. |
|archon_graphCleanup    | Odesílá se, když se graf s nahlášeným `graphId` čištěním vyčistí a ukončí. |
|archon_graphHeartbeat  |Prezenční signál se odesílá každou minutu každého grafu dovednosti. |
|archon_apiKeyAuthFail |Odesílá se, když klíč prostředku Počítačové zpracování obrazu nedokáže ověřit kontejner po dobu delší než 24 hodin z následujících důvodů: mimo kvótu, neplatné, offline. |
|VideoIngesterHeartbeat     |Odesílá se každou hodinu, která indikuje, že video se streamuje ze zdroje videa, a to s počtem chyb v této hodině. Oznamuje se pro každý graf. |
|VideoIngesterState | Sestavy se *zastavily* nebo se *spustily* pro streamování videa. Oznamuje se pro každý graf. |

##  <a name="troubleshooting-an-iot-edge-device"></a>Řešení potíží s IoT Edge zařízením

`iotedge`Nástroj příkazového řádku můžete použít ke kontrole stavu a protokolů spuštěných modulů. Například:
* `iotedge list`: Oznamuje seznam spuštěných modulů. 
  Chyby můžete dále kontrolovat pomocí `iotedge logs edgeAgent` . Pokud se `iotedge` zablokuje, můžete ho zkusit restartovat pomocí. `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` restartování určitého modulu 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Shromáždění souborů protokolu pomocí kontejneru diagnostiky

Prostorová analýza vygeneruje protokoly ladění Docker, které můžete použít k diagnostice běhových problémů, nebo zahrnout do lístků podpory. Modul pro diagnostiku prostorových analýz je k dispozici na Container Registry Microsoftu, abyste si ho stáhli. V souboru nasazení manifestu pro [Azure Stack hraniční zařízení](https://go.microsoft.com/fwlink/?linkid=2142179) nebo v jiném [stolním počítači](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)vyhledejte modul *diagnostiky* .

V části "ENV" přidejte následující konfiguraci:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

K optimalizaci protokolů odeslaných do vzdáleného koncového bodu, jako je například Azure Blob Storage, doporučujeme zachovat malou velikost souboru. Doporučené konfigurace protokolů Docker najdete v níže uvedeném příkladu.

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>Konfigurace úrovně protokolu

Konfigurace úrovně protokolu umožňuje řídit podrobnosti vygenerovaných protokolů. Podporované úrovně protokolu jsou: `none` , `verbose` , `info` , `warning` a `error` . Výchozí úroveň podrobností protokolu pro uzly i platformu je `info` . 

Úrovně protokolu lze globálně upravit nastavením `ARCHON_LOG_LEVEL` proměnné prostředí na jednu z povolených hodnot.
Dá se taky nastavit prostřednictvím vlákna s dvojitým odkazem IoT Edge modulu buď globálně, pro všechny nasazené dovednosti, nebo pro každou konkrétní dovednost nastavením hodnot pro `platformLogLevel` a `nodeLogLevel` , jak je uvedeno níže.

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodeLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Shromažďování protokolů

> [!NOTE]
> `diagnostics`Modul nemá vliv na obsah protokolování, pomáhá při shromažďování, filtrování a nahrávání stávajících protokolů.
> Chcete-li použít tento modul, musíte mít rozhraní Docker API verze 1,40 nebo vyšší.

Ukázkový soubor manifestu nasazení pro [zařízení Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179) nebo jiný [stolní počítač](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)  obsahuje modul s názvem `diagnostics` , který shromažďuje a odesílá protokoly. Tento modul je ve výchozím nastavení zakázán a měl by být povolený prostřednictvím konfigurace IoT Edge modulu, pokud potřebujete přístup k protokolům. 

`diagnostics`Kolekce je na vyžádání a ovládána prostřednictvím IoT Edge přímé metody a může odesílat protokoly do Azure Blob Storage.

### <a name="configure-diagnostics-upload-targets"></a>Konfigurace cílů nahrávání diagnostiky

Na portálu IoT Edge vyberte zařízení a pak modul **diagnostiky** . V souboru manifestu ukázkového nasazení pro [Azure Stack hraniční zařízení](https://go.microsoft.com/fwlink/?linkid=2142179) nebo v jiných [stolních počítačích](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)vyhledejte oddíl **proměnné prostředí** pro diagnostiku s názvem `env` a přidejte následující informace:

**Konfigurace nahrávání do Azure Blob Storage**

1. Vytvořte si vlastní účet Azure Blob Storage, pokud jste to ještě neudělali.
2. Získejte **připojovací řetězec** pro váš účet úložiště z Azure Portal. Bude se nacházet v **přístupových klíčích**.
3. Protokoly prostorové analýzy se automaticky nahrají do kontejneru Blob Storage s názvem *rtcvlogs* s následujícím formátem názvu souboru: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Nahrávají se protokoly prostorových analýz.

Protokoly se nahrávají na vyžádání pomocí `getRTCVLogs` metody IoT Edge v `diagnostics` modulu. 


1. Přejít na stránku IoT Hub portálu, vyberte **hraniční zařízení** a pak vyberte zařízení a modul diagnostiky. 
2. Přejděte na stránku podrobností modulu a klikněte na kartu **_Přímá metoda_* _.
3. `getRTCVLogs`V datové části zadejte název metody a řetězec formátu JSON. Můžete zadat `{}` , což je prázdná datová část. 
4. Nastavte časový limit připojení a metody a klikněte na _ * vyvolat metodu * *.
5. Vyberte cílový kontejner a vytvořte řetězec JSON datové části pomocí parametrů popsaných v části **syntaxe protokolování** . Pro provedení žádosti klikněte na **vyvolat metodu** .

>[!NOTE]
> Vyvoláním `getRTCVLogs` metody s prázdnou datovou částí se vrátí seznam všech kontejnerů nasazených v zařízení. Název metody rozlišuje velká a malá písmena. Pokud je zadán nesprávný název metody, zobrazí se chyba 501.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="Vyvolání metody getRTCVLogs ":::
![getRTCVLogs Direct – stránka metody](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Syntaxe protokolování

Následující tabulka obsahuje seznam parametrů, které můžete použít při dotazování protokolů.

| Klíčové slovo | Popis | Výchozí hodnota |
|--|--|--|
| StartTime | Čas spuštění požadovaných protokolů v milisekundách UTC | `-1`, začátek modulu runtime kontejneru. Když `[-1.-1]` se použije jako časový rozsah, rozhraní API vrátí protokoly za poslední hodinu.|
| EndTime | Požadovaný čas ukončení protokolů v milisekundách UTC. | `-1`, aktuální čas. Když `[-1.-1]` se použije časový rozsah, rozhraní API vrátí protokoly za poslední hodinu. |
| ContainerId | Cílový kontejner pro načítání protokolů| `null`, pokud není k dispozici žádné ID kontejneru. Rozhraní API vrátí všechny dostupné informace o kontejnerech s ID.|
| DoPost | Proveďte operaci odeslání. Pokud je tato hodnota nastavena na `false` , provede požadovanou operaci a vrátí velikost nahrávání bez provedení odeslání. Když se nastaví na `true` , zahájí asynchronní nahrávání vybraných protokolů. | `false`, Nenahrávat.|
| Omezení | Určete, kolik řádků protokolů se má nahrát na dávku. | `1000`, Použijte tento parametr pro úpravu rychlosti post. |
| Filtry | Filtruje protokoly, které se mají nahrát. | `null`, filtry je možné zadat jako páry klíč-hodnota na základě struktury protokolů prostorové analýzy: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . Příklad: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

V následující tabulce jsou uvedeny atributy v odpovědi na dotaz.

| Klíčové slovo | Popis|
|--|--|
|DoPost| Buď *hodnotu true* , nebo *false*. Určuje, jestli jsou protokoly nahrané nebo ne. Pokud se rozhodnete Nenahrávat protokoly, rozhraní API vrátí informace ***synchronně** _. Pokud se rozhodnete odeslat protokoly, rozhraní API vrátí 200, pokud je požadavek platný, a spustí odesílání protokolů _*_asynchronně_*_.|
|TimeFilter| Filtr času aplikovaný na protokoly.|
|ValueFilters| Filtry klíčových slov použité pro protokoly. |
|Časové razítko| Čas spuštění metody |
|ContainerId| ID cílového kontejneru |
|FetchCounter| Celkový počet řádků protokolu |
|FetchSizeInByte| Celková velikost dat protokolu v bajtech |
|MatchCounter| Platný počet řádků protokolu. |
|MatchSizeInByte| Platné množství dat protokolu v bajtech. |
|FilterCount| Celkový počet řádků protokolu po použití filtru |
|FilterSizeInByte| Celková velikost dat protokolu v bajtech po použití filtru |
|FetchLogsDurationInMiliSec| Doba trvání operace načtení |
|PaseLogsDurationInMiliSec| Doba trvání operace filtrování |
|PostLogsDurationInMiliSec| Doba trvání operace post |

#### <a name="example-request"></a>Příklad požadavku 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>Příklad odpovědi 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

Zkontrolujte řádky, časy a velikosti protokolu načtení a v případě, že tato nastavení vypadají dobře, nahraďte _*_DoPost_*_ do `true` a tím, že protokoly budou nabízeny stejným filtrům do cílových umístění. 

Při řešení potíží můžete exportovat protokoly z Azure Blob Storage. 

## <a name="common-issues"></a>Běžné problémy

Pokud se v protokolech modulů zobrazí následující zpráva, může to znamenat, že vaše předplatné Azure musí být schválené: 

Kontejner není v platném stavu. Ověření předplatného se nezdařilo se stavem neshoda. Klíč rozhraní API není určen pro daný typ kontejneru. "

Další informace najdete v tématu [schválení žádosti o spuštění kontejneru](spatial-analysis-container.md#request-approval-to-run-the-container).

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Řešení potíží s Azure Stack hraničním zařízením

Následující část je k dispozici pro nápovědu k ladění a ověřování stavu zařízení Azure Stack Edge.

### <a name="access-the-kubernetes-api-endpoint"></a>Přístup ke koncovému bodu rozhraní Kubernetes API. 

1. V místním uživatelském rozhraní zařízení, otevřete stránku _ *zařízení**. 
2. V části **koncové body zařízení** zkopírujte koncový bod služby API Kubernetes. Tento koncový bod je řetězec v následujícím formátu: `https://compute..[device-IP-address]` .
3. Uložte řetězec koncového bodu. Později to budete používat při konfiguraci `kubectl` pro přístup ke clusteru Kubernetes.

### <a name="connect-to-powershell-interface"></a>Připojení k rozhraní PowerShell

Vzdáleně se připojte z klienta Windows. Po vytvoření clusteru Kubernetes můžete spravovat aplikace přes tento cluster. Budete se muset připojit k rozhraní PowerShell zařízení. V závislosti na operačním systému klienta se postupy pro vzdálené připojení k zařízení můžou lišit. Následující postup se používá pro klienta Windows, na kterém běží PowerShell.

> [!TIP]
> * Než začnete, ujistěte se, že na vašem klientovi Windows běží Windows PowerShell 5,0 nebo novější.
> * PowerShell je také [k dispozici v systému Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. Spusťte relaci Windows PowerShellu jako správce. 
    1. Ujistěte se, že je ve vašem klientovi spuštěná služba Vzdálená správa systému Windows. Do příkazového řádku zadejte `winrm quickconfig` .

2. Přiřaďte proměnnou pro IP adresu zařízení. Například, `$ip = "<device-ip-address>"`.

3. Pomocí následujícího příkazu přidejte IP adresu vašeho zařízení do seznamu důvěryhodných hostitelů klienta. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Na zařízení spusťte relaci prostředí Windows PowerShell. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Po zobrazení výzvy zadejte heslo. Použijte stejné heslo, které se používá pro přihlášení k místnímu webovému rozhraní. Výchozí heslo místního webového rozhraní je `Password1` . 

### <a name="access-the-kubernetes-cluster"></a>Přístup ke clusteru Kubernetes

Po vytvoření clusteru Kubernetes můžete použít `kubectl` Nástroj příkazového řádku pro přístup ke clusteru.

1. Vytvořte nový obor názvů. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Vytvoří uživatele a získá konfigurační soubor. Tento příkaz zobrazí výstup informací o konfiguraci pro cluster Kubernetes. Zkopírujte tyto informace a uložte je do souboru s názvem *config*. Neukládat soubor s příponou souboru.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Přidejte *konfigurační* soubor do složky *. Kube* v profilu uživatele na místním počítači.   

4. Přidružte obor názvů k uživateli, který jste vytvořili.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Nainstalujte `kubectl` na klienta Windows pomocí následujícího příkazu:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Přidejte položku DNS do souboru Hosts ve vašem systému. 
    1. Spusťte Poznámkový blok jako správce a otevřete soubor *hosts* v umístění `C:\windows\system32\drivers\etc\hosts` . 
    2. V souboru Hosts vytvořte záznam s IP adresou zařízení a doménou DNS, kterou jste získali ze stránky **zařízení** v místním uživatelském rozhraní. Koncový bod, který byste měli použít, bude vypadat přibližně takto: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Ověřte, že se můžete připojit k Kubernetes luskům.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

Chcete-li získat protokoly kontejneru, spusťte následující příkaz:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Užitečné příkazy

|Příkaz  |Popis  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Vygeneruje konfigurační soubor Kubernetes. Při použití příkazu zkopírujte informace do souboru s názvem *config*. Neukládat soubor s příponou souboru.        |
| `Get-HcsApplianceInfo` | Vrátí informace o zařízení. |
| `Enable-HcsSupportAccess` | Generuje přístup k přihlašovacím údajům, aby bylo možné spustit relaci podpory. |

## <a name="next-steps"></a>Další kroky

* [Nasazení webové aplikace počítající lidi](spatial-analysis-web-app.md)
* [Konfigurace operací prostorových analýz](./spatial-analysis-operations.md)
* [Průvodce umístěním kamery](spatial-analysis-camera-placement.md)
* [Průvodce umístěním zóny a linky](spatial-analysis-zone-line-placement.md)