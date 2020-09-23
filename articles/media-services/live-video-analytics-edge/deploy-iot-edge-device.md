---
title: Nasazení Live video Analytics na zařízení IoT Edge – Azure
description: V tomto článku jsou uvedené kroky, které vám pomůžou nasadit Live video Analytics na zařízení IoT Edge. To byste měli udělat například v případě, že máte přístup k místnímu počítači se systémem Linux nebo jste vytvořili účet Azure Media Services.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 211dd0d61bbca39c4f4ec2f388d950c4615bb023
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90887227"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>Nasazení Live video Analytics na zařízení IoT Edge

V tomto článku jsou uvedené kroky, které vám pomůžou nasadit Live video Analytics na zařízení IoT Edge. To byste měli udělat například v případě, že máte přístup k místnímu počítači se systémem Linux nebo jste vytvořili účet Azure Media Services.

> [!NOTE]
> Podpora pro zařízení ARM64 je k dispozici v Live video Analytics na IoT Edge sestaveních `1.0.4` a novějších.
> Podpora spouštění Azure IoT Edge runtime na zařízeních ARM64 je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

* Zařízení s platformou X86-64 nebo ARM64, které používá některý z [podporovaných operačních systémů Linux](https://docs.microsoft.com/azure/iot-edge/support#operating-systems) .
* Předplatné Azure, ke kterému máte [oprávnění vlastníka](../../role-based-access-control/built-in-roles.md#owner)
* [Vytvoření a nastavení IoT Hub](../../iot-hub/iot-hub-create-through-portal.md)
* [Registrace zařízení IoT Edge](../../iot-edge/how-to-register-device.md)
* [Instalace modulu runtime Azure IoT Edge v systémech Linux založených na distribuci Debian](../../iot-edge/how-to-install-iot-edge-linux.md)
* [Vytvoření účtu Azure Media Services](../latest/create-account-howto.md)

    * Použijte jednu z těchto oblastí: Východní USA 2, Východní USA, Střed USA, Střed USA – sever, Japonsko – východ, Západní USA, Západní USA 2, Středozápadní USA, Kanada – východ, Velká Británie – jih, Francie – střed, Francie – jih, Švýcarsko – sever, Švýcarsko – západ a Japonsko – západ.
    * Doporučuje se používat účty úložiště pro obecné účely v2 (GPv2).

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>Konfigurace prostředků Azure pro používání živé analýzy videí

### <a name="create-custom-azure-resource-manager-role"></a>Vytvoření vlastní role Azure Resource Manageru

Přečtěte si téma [Vytvoření vlastní role Azure Resource Manager](create-custom-azure-resource-manager-role-how-to.md) a přiřazení k instančnímu objektu pro použití ve službě Live video Analytics.

### <a name="set-up-a-premium-streaming-endpoint"></a>Nastavení koncového bodu streamování Premium

Pokud máte v úmyslu používat Live video Analytics k nepřetržitému nahrávání videa do cloudu a následně před jejich přehráním používat [rozhraní API pro dotazování](playback-recordings-how-to.md#query-api) , doporučujeme, abyste službu Media Service aktualizovali tak, aby používala [koncový bod streamování Premium](../latest/streaming-endpoint-concept.md#types).  

Toto je volitelný krok. K tomu můžete použít tento příkaz Azure CLI:

```azurecli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

Pomocí tohoto příkazu můžete spustit koncový bod streamování. 

> [!IMPORTANT]
> Vaše předplatné se začne účtovat v tomto okamžiku.

```azurecli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

Podle kroků v tomto článku získáte přihlašovací údaje pro přístup k rozhraním API služby Media Service: Získejte [přístup k rozhraním API služby Media Service](../latest/access-api-howto.md?tabs=portal) a vyberte kartu portál.

## <a name="create-and-use-local-user-account-for-deployment"></a>Vytvoření a použití místního uživatelského účtu pro nasazení
Pokud chcete spustit video Analytics v modulu IoT Edge, vytvořte místní uživatelský účet s co nejmenším možným oprávněním. Například spusťte na počítači se systémem Linux následující příkazy:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

## <a name="granting-permissions-to-device-storage"></a>Udělení oprávnění pro úložiště zařízení

Teď, když jste vytvořili místní uživatelský účet, 

* K uložení konfiguračních dat aplikace budete potřebovat místní složku. Vytvořte složku a udělte jí oprávnění k zápisu účtu LocalUser do této složky pomocí následujících příkazů:

```
sudo mkdir /var/lib/azuremediaservices
sudo chown -R edgeuser /var/lib/azuremediaservices
```

* Budete také potřebovat složku pro [záznam videí do místního souboru](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources). Pomocí následujících příkazů vytvořte místní složku pro stejné:

```
sudo mkdir /var/media
sudo chown -R edgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>Nasadit modul Edge pro video Analytics v provozu

Live video Analytics na IoT Edge zveřejňuje moduly s dvojitou vlastností, které jsou zdokumentovány ve [schématu konfigurace s dvojitou](module-twin-configuration-schema.md)platností. 

### <a name="deploy-using-the-azure-portal"></a>Nasazení pomocí webu Azure Portal

Azure Portal vás provede vytvořením manifestu nasazení a vložením nasazení do IoT Edgeho zařízení.  
#### <a name="select-your-device-and-set-modules"></a>Vyberte zařízení a nastavte moduly.

1. Přihlaste se k [Azure Portal](https://ms.portal.azure.com/) a přejděte do služby IoT Hub.
1. V nabídce vyberte **IoT Edge** .
1. V seznamu zařízení klikněte na ID cílového zařízení.
1. Vyberte možnost **nastavit moduly**.

#### <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly se mají nasadit, způsob, jakým jsou toky dat mezi moduly a požadované vlastnosti v modulu vlákna. Azure Portal má průvodce, který vás provede vytvořením manifestu nasazení. Obsahuje tři kroky uspořádané do karet: **moduly**, **trasy**a **Revize + vytvořit**.

#### <a name="add-modules"></a>Přidat moduly

1. V části **IoT Edge moduly** na stránce klikněte na rozevírací seznam **přidat** a vyberte **IoT Edge modul** . zobrazí se stránka **Přidat IoT Edge modul** .
1. Na kartě **nastavení modulu** zadejte název modulu a pak zadejte identifikátor URI image kontejneru:   
    Příklady:
    
    * **Název modulu IoT Edge**: lvaEdge
    * **Identifikátor URI image**: MCR.Microsoft.com/Media/Live-video-Analytics:1.0    
    
    ![Snímek obrazovky se zobrazí na kartě nastavení modulu.](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > Nevybírejte možnost **Přidat** , dokud neurčíte hodnoty v **nastavení modulu**, **možnosti vytvoření kontejneru**a **Dvojitá nastavení modulu** , jak je popsáno v tomto postupu.
    
    > [!WARNING]
    > Azure IoT Edge rozlišuje velká a malá písmena, když provádíte volání modulů. Poznamenejte si přesný řetězec, který použijete jako název modulu.

1. Otevřete kartu **proměnné prostředí** .
   
   Přidejte do vstupních polí následující hodnoty, které vidíte ![ proměnné prostředí.](./media/deploy-iot-edge-device/environment-variables.png) 

1. Otevřete kartu **možnosti vytvoření kontejneru** .

    ![Možnosti vytvoření kontejneru](./media/deploy-iot-edge-device/container-create-options.png)
 
    Zkopírujte a vložte následující JSON do pole, abyste omezili velikost souborů protokolu vytvořených modulem.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   Oddíl "vazby" ve formátu JSON má 2 položky:
   1. "/var/lib/azuremediaservices:/var/lib/azuremediaservices": používá se k navázání trvalých konfiguračních dat aplikace z kontejneru a jeho uložení na hraničním zařízení.
   1. "/var/Media:/var/Media": Tato akce váže mediální složky mezi hraničním zařízením a kontejnerem. Slouží k ukládání nahrávek videa při spuštění topologie mediálního grafu, která podporuje ukládání videoklipů do hraničního zařízení.
   
1. Na kartě **nastavení s dvojitým modulem** ZKOPÍRUJTE následující JSON a vložte ho do pole.
 
    ![Dvojitá nastavení](./media/deploy-iot-edge-device/twin-settings.png)

    Live video Analytics na IoT Edge vyžaduje sadu povinných vlastností, které je třeba spustit, jak je uvedeno v [modulu s dvojitou konfigurací schématu konfigurace](module-twin-configuration-schema.md).  

    KÓD JSON, který je třeba zadat do modulu nastavení s dvojitým nastavením, bude vypadat takto:    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    Tyto vlastnosti jsou **požadované** a pro JSON výše.  
    * {subscriptionID} – Toto je vaše ID předplatného Azure.
    * {resourceGroupName} – Tato skupina prostředků, do které patří váš účet Media Service
    * {AMS – Account-Name} – Toto je název vašeho účtu Media Services.
    
    Pokud chcete získat další hodnoty, přečtěte si téma [přístup Azure Media Services API](../latest/access-api-howto.md?tabs=portal) a vyberte kartu portál.  
    * aadTenantId – Toto je ID tenanta a je stejné jako "AadTenantId" z výše uvedeného odkazu.
    * aadServicePrincipalAppId – Toto je ID aplikace instančního objektu pro váš účet Media Service a je stejné jako "AadClientId" z výše uvedeného odkazu.
    * aadServicePrincipalSecret – Toto je heslo k instančnímu objektu a je stejné jako "AadSecret" z výše uvedeného odkazu.

    Níže jsou uvedeny některé další **Doporučené** vlastnosti, které je možné přidat do formátu JSON a které vám pomůžou monitorovat modul. Další informace najdete v tématu [monitorování a protokolování](monitoring-logging.md):
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    Tady je několik **volitelných** vlastností, které můžete přidat ve formátu JSON:
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > Dvojitá vlastnost **allowUnsecuredEndpoints** je nastavena na hodnotu true pro účely kurzů a rychlých startů.   
   Tuto vlastnost byste měli nastavit na **hodnotu false** při spuštění v produkčním prostředí. Tím se zajistí, že aplikace bude blokovat všechny nezabezpečené koncové body, a aby se daly spustit topologie grafu, budou potřeba platné přihlašovací údaje pro připojení.  
   
    Vyberte Přidat a přidejte do něj vlastnosti modulu.
1. Vyberte **Další: trasy** pro pokračování v části trasy.
    Zadejte trasy.

Ponechte výchozí trasy a vyberte **Další: zkontrolovat + vytvořit** , abyste mohli pokračovat v části revize.

#### <a name="review-deployment"></a>Zkontrolovat nasazení

V části Kontrola se dozvíte, který manifest nasazení JSON byl vytvořen na základě vašich výběrů v předchozích dvou částech. K dispozici jsou také dva moduly, které jste nepřidali: $edgeAgent a $edgeHub. Tyto dva moduly tvoří modul runtime IoT Edge a jsou požadované výchozí hodnoty v každém nasazení.

Zkontrolujte informace o svém nasazení a pak vyberte vytvořit.

### <a name="verify-your-deployment"></a>Ověření nasazení

Po vytvoření nasazení se vrátíte na stránku IoT Edge služby IoT Hub.

1. Vyberte zařízení IoT Edge, na které cílíte nasazení, a otevřete jeho podrobnosti.
2. V podrobnostech o zařízení ověřte, že je modul BLOB Storage uveden v části **nasazení a nahlášený zařízením**.

Může chvíli trvat, než se modul na zařízení spustí a pak se znovu oznámí IoT Hub. Aktualizujte stránku, aby se zobrazil aktualizovaný stav.
Stavový kód: 200 – OK znamená, že [modul runtime IoT Edge](../../iot-edge/iot-edge-runtime.md) je v pořádku a pracuje správně.

![Snímek obrazovky zobrazuje stavovou hodnotu pro modul runtime IoT Edge.](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>Vyvolání přímé metody

Dále umožňuje otestovat ukázku vyvoláním přímé metody. Přečtěte si [přímé metody pro Live video Analytics na IoT Edge](direct-methods.md) , abyste pochopili všechny přímé metody, které poskytuje náš modul lvaEdge.

1. Kliknutím na modul Edge, který jste vytvořili, přejdete na stránku konfigurace.  

    ![Snímek obrazovky ukazuje stránku konfigurace modulu Edge.](./media/deploy-iot-edge-device/modules.png)
1. Klikněte na možnost nabídky přímé metody.

    > [!NOTE] 
    > V sekcích připojovacího řetězce budete muset přidat hodnotu, jak vidíte na aktuální stránce. V části **název nastavení** není nutné skrývat ani měnit žádné údaje. Je to v pořádku, aby bylo možné ho zveřejnit.

    ![Direct – metoda](./media/deploy-iot-edge-device/module-details.png)
1. Potom do pole název metody zadejte "GraphTopologyList".
1. Potom zkopírujte a vložte níže uvedenou datovou část JSON do pole datová část.
    
    ```
    {
        "@apiVersion" : "1.0"
    }
    ```
1. V horní části stránky klikněte na možnost vyvolat metodu.

    ![Přímé metody](./media/deploy-iot-edge-device/direct-method.png)
1. V poli výsledek by se měla zobrazit zpráva o stavu 200.

    ![Stavová zpráva 200](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>Další kroky

Vyzkoušejte [rychlý Start: Začínáme – Live video Analytics na IoT Edge](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)

> [!TIP]
> Pokud budete pokračovat s výše uvedeným rychlým startem, při vyvolání přímých metod pomocí Visual Studio Code použijete místo výchozího nastavení zařízení, které bylo přidáno do IoT Hub prostřednictvím tohoto článku `lva-sample-device` .
