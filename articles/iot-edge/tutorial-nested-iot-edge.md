---
title: Kurz – vytvoření hierarchie IoT Edge zařízení – Azure IoT Edge
description: V tomto kurzu se dozvíte, jak vytvořit hierarchickou strukturu IoT Edge zařízení pomocí bran.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: a9591a394d80e7b4c60f28fda6c0a425ba3d0a4f
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180060"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>Kurz: vytvoření hierarchie zařízení IoT Edge (Preview)

Nasazení uzlů Azure IoT Edge napříč sítěmi uspořádanými do hierarchických vrstev. Každá vrstva v hierarchii je zařízení brány, které zpracovává zprávy a požadavky ze zařízení ve vrstvě pod ní.

>[!NOTE]
>Tato funkce vyžaduje IoT Edge verze 1,2, která je ve verzi Public Preview, spouští se kontejnery Linux.

Můžete strukturovat hierarchii zařízení, aby se ke cloudu mohla připojit jenom nejvyšší vrstva, a nižší vrstvy můžou komunikovat jenom s sousedními severními a jižními vrstvami. Tato vrstva sítě je základem většiny průmyslových sítí, které se řídí [standardem ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

Cílem tohoto kurzu je vytvořit hierarchii IoT Edge zařízení, která simulují produkční prostředí. Na konci nasadíte [modul simulovaného senzoru teploty](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) na zařízení nižší vrstvy bez přístupu k Internetu stažením imagí kontejneru v hierarchii.

Tento kurz vás provede vytvořením hierarchie IoT Edge zařízení, nasazením IoT Edge kontejnerů runtime na vaše zařízení a místní konfigurací zařízení. V tomto kurzu:

> [!div class="checklist"]
>
> * Vytvořte a definujte relace v hierarchii IoT Edgech zařízení.
> * Nakonfigurujte modul runtime IoT Edge na zařízeních ve vaší hierarchii.
> * Nainstalujte konzistentní certifikáty napříč vaší hierarchií zařízení.
> * Přidejte úlohy do zařízení ve vaší hierarchii.
> * Použijte modul API proxy k bezpečnému směrování přenosů HTTP přes jeden port ze zařízení se spodní vrstvou.

V tomto kurzu jsou definovány následující vrstvy sítě:

* **Nejvyšší vrstva**: zařízení IoT Edge v této vrstvě se můžou připojit přímo ke cloudu.

* **Nižší vrstva**: zařízení IoT Edge v této vrstvě se nemohou připojit přímo ke cloudu. Musí projít jedním nebo více zprostředkujícími IoT Edge zařízeními pro odesílání a příjem dat.

V tomto kurzu se pro jednoduchost používá dvě hierarchie zařízení. Jedno zařízení, **topLayerDevice**, představuje zařízení v horní vrstvě hierarchie, které se může připojit přímo ke cloudu. Toto zařízení se bude také označovat jako **nadřazené zařízení**. Druhé zařízení, **lowerLayerDevice**, představuje zařízení v nižší vrstvě hierarchie, které se nemůže připojit přímo ke cloudu. Toto zařízení se bude také označovat jako **podřízené zařízení**. Můžete přidat další zařízení nižší vrstvy, která reprezentují vaše provozní prostředí. Konfigurace dalších zařízení nižší vrstvy bude následovat po konfiguraci **lowerLayerDevice**.

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit hierarchii IoT Edgech zařízení, budete potřebovat:

* Počítač (Windows nebo Linux) s připojením k Internetu.
* Účet Azure s platným předplatným. Pokud ještě nemáte [předplatné Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) v Azure úrovně Free nebo Standard.
* Azure CLI v 2.3.1 s nainstalovaným rozšířením Azure IoT v 0.10.6 nebo novějším. V tomto kurzu se používá [Azure Cloud Shell](../cloud-shell/overview.md). Pokud Azure Cloud Shell neznáte, [Projděte si podrobnosti v rychlém](./quickstart-linux.md#prerequisites)startu.
* Dvě zařízení se systémem Linux nakonfigurovaná jako zařízení IoT Edge. Pokud nemáte dostupná zařízení, můžete vytvořit dva virtuální počítače Azure tak, že nahradíte zástupný text v následujícím příkazu a dvakrát ho spustíte:

   ```azurecli-interactive
   az vm create \
    --resource-group <REPLACE_WITH_RESOURCE_GROUP> \
    --name <REPLACE_WITH_UNIQUE_NAMES_FOR_EACH_VM> \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password <REPLACE_WITH_PASSWORD>
   ```

* Ujistěte se, že jsou otevřené příchozí porty: 8000, 443, 5671, 8883:
  * 8000: používá se k vyžádání imagí kontejneru Docker prostřednictvím proxy serveru rozhraní API.
  * 443: používá se mezi nadřazenými a podřízenými hraničními centry pro REST API volání.
  * 5671, 8883: používá se pro AMQP a MQTT.

  Další informace najdete v tématu věnovaném [otevření portů pro virtuální počítač s využitím webu Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md).

Tento scénář můžete vyzkoušet také pomocí skriptu [Azure IoT Edge pro průmyslovou ukázku IoT](https://aka.ms/iotedge-nested-sample), který nasadí virtuální počítače Azure jako předem nakonfigurovaná zařízení pro simulaci prostředí továrny.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Konfigurace IoT Edge hierarchie zařízení

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Vytvoření hierarchie IoT Edgech zařízení

První krok, vytvoření zařízení IoT Edge, se dá provést pomocí Azure Portal nebo Azure CLI. V tomto kurzu se vytvoří hierarchie dvou IoT Edgech zařízení: **topLayerDevice** a její podřízená **lowerLayerDevice**.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://ms.portal.azure.com/)přejděte na IoT Hub.

1. V nabídce v levém podokně v části **Automatická správa zařízení** vyberte **IoT Edge**.

1. Vyberte **+ Přidat zařízení IoT Edge**. Toto zařízení bude zařízení nejvyšší vrstvy, takže zadejte odpovídající jedinečné ID zařízení. Vyberte **Uložit**.

1. Znovu vyberte **+ přidat IoT Edge zařízení** . Toto zařízení bude hraniční zařízení nižší vrstvy, takže zadejte odpovídající jedinečné ID zařízení.

1. Zvolte možnost **nastavit nadřazené zařízení**, v seznamu zařízení vyberte zařízení nejvyšší vrstvy a vyberte **OK**. Vyberte **Uložit**.

   ![Nastavení nadřazené položky pro zařízení nižší vrstvy](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. V [Azure Cloud Shell](https://shell.azure.com/)zadejte následující příkaz, který vytvoří zařízení IoT Edge ve vašem centru. Toto zařízení bude zařízení nejvyšší vrstvy, takže zadejte příslušné jedinečné ID zařízení:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Zadejte následující příkaz pro vytvoření podřízeného IoT Edge zařízení a vytvořte vztah nadřazenosti-podřízenosti mezi zařízeními:

    ```azurecli-interactive
    az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --pd {top_layer_device_id} --hub-name {iothub_name}
    ```

---

Poznamenejte si připojovací řetězec každého zařízení IoT Edge. Budou použity později.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://ms.portal.azure.com/)přejděte do části **IoT Edge** IoT Hub.

1. V seznamu zařízení klikněte na ID zařízení jednoho ze zařízení.

1. V poli **primární připojovací řetězec** vyberte příkaz **Kopírovat** a zaznamenejte jej na místo dle vašeho výběru.

1. Opakujte pro všechna ostatní zařízení.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. V [Azure Cloud Shell](https://shell.azure.com/)pro každé zařízení zadejte následující příkaz, který načte připojovací řetězec vašeho zařízení a zaznamená jej na místo, kde jste zvolili:

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

### <a name="create-certificates"></a>Vytvoření certifikátů

Všechna zařízení ve [scénáři brány](iot-edge-as-gateway.md) potřebují sdílený certifikát pro nastavení zabezpečených připojení mezi nimi. K vytvoření ukázkových certifikátů pro obě zařízení v tomto scénáři použijte následující postup.

K vytvoření ukázkových certifikátů na zařízení se systémem Linux je nutné naklonovat skripty generace a nastavit je tak, aby byly místně spouštěny v bash.

1. Naklonujte IoT Edge úložiště Git, které obsahuje skripty pro generování ukázkových certifikátů.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. Přejděte do adresáře, ve kterém chcete pracovat. V tomto adresáři se vytvoří všechny soubory certifikátů a klíčů.

1. Zkopírujte soubory config a Script z naklonovaného IoT Edge úložiště do svého pracovního adresáře.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. Vytvořte certifikát kořenové certifikační autority a jeden zprostředkující certifikát.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů, ale používáme tento soubor jako **certifikát kořenové certifikační autority** pro hierarchii brány:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. Pomocí následujícího příkazu vytvořte dvě sady certifikátů CA a privátních klíčů IoT Edge zařízení: jednu sadu pro zařízení nejvyšší vrstvy a jednu sadu pro zařízení nižší vrstvy. Poskytněte jim snadno zapamatovatelné názvy certifikátů certifikační autority, abyste je vzájemně rozlišili.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "top-layer-device"
   ./certGen.sh create_edge_device_ca_certificate "lower-layer-device"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů, ale používáme následující certifikát a pár klíčů na každém IoT Edge zařízení a odkazuje se v souboru config. yaml:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Každé zařízení potřebuje kopii certifikátu kořenové certifikační autority a kopii vlastního certifikátu certifikační autority pro zařízení a privátního klíče. K přesunutí certifikátů na každé zařízení můžete použít jednotku USB nebo [zabezpečenou kopii souboru](https://www.ssh.com/ssh/scp/) .

1. Po přenesení certifikátů nainstalujte pro každé zařízení kořenovou certifikační autoritu.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   Tento příkaz by měl mít výstup, který v/etc/SSL/certs. přidal jeden certifikát.

### <a name="install-iot-edge-on-the-devices"></a>Instalace IoT Edge na zařízeních

Pomocí následujících kroků na obou zařízeních nainstalujte IoT Edge.

1. Nainstalujte konfiguraci úložiště, která odpovídá operačnímu systému vašeho zařízení.

   * **Ubuntu Server 16,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Ubuntu Server 18,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Malina Pi OS Stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

1. Zkopírujte vygenerovaný seznam do adresáře sources. list. d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Nainstalujte veřejný klíč Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```
   
1. Aktualizuje seznamy balíčků na vašem zařízení.

   ```bash
   sudo apt-get update
   ```

1. Nainstalujte modul Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

1. Nainstalujte démona hsmlib a IoT Edge. Pokud chcete zobrazit prostředky pro ostatní distribuce systému Linux, [navštivte verzi GitHub](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1). <!-- Update with proper image links on release -->

   ```bash
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/libiothsm-std_1.2.0_rc1-1-1_debian9_amd64.deb -o libiothsm-std.deb
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/iotedge_1.2.0_rc1-1_debian9_amd64.deb -o iotedge.deb
   sudo dpkg -i ./libiothsm-std.deb
   sudo dpkg -i ./iotedge.deb
   ```

### <a name="configure-the-iot-edge-runtime"></a>Konfigurace modulu runtime IoT Edge

Nakonfigurujte modul runtime IoT Edge pomocí následujících kroků na svých zařízeních. Konfigurace modulu runtime IoT Edge pro vaše zařízení se skládá ze čtyř kroků, které jsou dokončené úpravou konfiguračního souboru IoT Edge:

1. Ruční zřízení každého zařízení přidáním připojovacího řetězce zařízení do konfiguračního souboru.

1. Dokončete nastavení certifikátů zařízení tak, že přejdete na konfigurační soubor na certifikát certifikační autority zařízení, na privátní klíč certifikační autority zařízení a na certifikát kořenové certifikační autority.

1. Proveďte zavedení systému pomocí agenta IoT Edge.

1. Aktualizujte parametr **hostname** pro zařízení **nejvyšší vrstvy** a aktualizujte parametr **hostname** a parametr **parent_hostname** pro vaše zařízení **nižší vrstvy** .

Proveďte tyto kroky a restartujte službu IoT Edge pro konfiguraci zařízení.

1. Na každém zařízení otevřete konfigurační soubor IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Vyhledejte konfigurace zřizování souboru a zrušte komentář k **ruční konfiguraci zřizování pomocí oddílu připojovacího řetězce** .

1. Aktualizujte hodnotu **device_connection_string** připojovacím řetězcem ze zařízení IoT Edge. Ujistěte se, že jsou všechny ostatní oddíly pro zřizování zakomentovány. Ujistěte se, že **zřizování:** řádek neobsahuje žádné předchozí prázdné znaky a že vnořené položky jsou odsazeny dvěma mezerami.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

   >[!TIP]
   >Vložení obsahu schránky do nano `Shift+Right Click` nebo stisknutím klávesy `Shift+Insert` .

1. Vyhledejte část **certifikáty** . Odkomentujte a aktualizujte pole tří certifikátů tak, aby odkazovaly na certifikáty, které jste vytvořili v předchozí části, a přesunuli do zařízení IoT Edge. Zadejte cesty k identifikátorům URI souboru, které mají formát `file:///<path>/<filename>` .

   ```yml
   certificates:
     device_ca_cert: "<File URI path to the device CA certificate unique to this device.>"
     device_ca_pk: "<File URI path to the device CA private key unique to this device.>"
     trusted_ca_certs: "<File URI path to the root CA certificate shared by all devices in the gateway hierarchy.>"
   ```

1. Pro zařízení **nejvyšší vrstvy** Najděte parametr **hostname** . Aktualizujte hodnotu na plně kvalifikovaný název domény (FQDN) nebo IP adresu zařízení IoT Edge. Použijte jakoukoli hodnotu, kterou zvolíte konzistentně napříč zařízeními ve vaší hierarchii.

   ```yml
   hostname: <device fqdn or IP>
   ```

1. U zařízení IoT Edge v **nižších vrstvách** aktualizujte konfigurační soubor tak, aby odkazoval na plně kvalifikovaný název domény nebo IP adresu nadřazeného zařízení, a to podle toho, co je v poli **hostname** nadřazeného zařízení. U IoT Edge zařízení v **horní vrstvě** ponechte tento parametr Zakomentovat.

   ```yml
   parent_hostname: <parent device fqdn or IP>
   ```

   > [!NOTE]
   > V případě hierarchií s více než jednou nižší vrstvou aktualizujte pole *parent_hostname* se plně kvalifikovaným názvem domény zařízení ve vrstvě hned výše.

1. V případě zařízení **nejvyšší vrstvy** najděte část **Agent** YAML a aktualizujte hodnotu image na správnou verzi agenta IoT Edge. V tomto případě budeme nasměrovat agenta IoT Edge nejvyšší vrstvy na Azure Container Registry s dostupnou verzí Public Preview verze Preview agenta IoT Edge.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. U zařízení IoT Edge v **nižších vrstvách** aktualizujte název domény hodnoty image tak, aby odkazoval na plně kvalifikovaný název domény nebo IP adresu nadřazeného zařízení, a za ním port proxy serveru rozhraní API, 8000. Přidáte modul API proxy do další části.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "<parent_device_fqdn_or_ip>:8000/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Uložte soubor a zavřete ho.

   `CTRL + X`, `Y`, `Enter`

1. Po zadání informací o zřizování do konfiguračního souboru restartujte démona:

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="deploy-modules-to-the-top-layer-device"></a>Nasazení modulů do zařízení nejvyšší vrstvy

Zbývající kroky k dokončení konfigurace IoT Edge runtime a úloh nasazení se provádí z cloudu prostřednictvím Azure Portal nebo Azure CLI.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V [Azure Portal](https://ms.portal.azure.com/):

1. Přejděte na IoT Hub.

1. V nabídce v levém podokně v části **Automatická správa zařízení** vyberte **IoT Edge**.

1. V seznamu zařízení klikněte na ID zařízení v **horním** zařízení Edge vrstvy.

1. Na horním panelu vyberte možnost **nastavit moduly**.

1. Vedle ikony ozubeného kolečka vyberte **nastavení modulu runtime**.

1. V části **hraniční centrum** v poli obrázek zadejte `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` .

   ![Úprava obrázku hraničního centra](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Do modulu Edge hub přidejte následující proměnné prostředí:

    | Name | Hodnota |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Upravit proměnné prostředí hraničního centra](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. Pod položkou **Agent Edge** v poli Image zadejte `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2` . Vyberte **Uložit**.

1. Přidejte modul registru Docker do zařízení nejvyšší vrstvy. Vyberte **+ Přidat** a v rozevíracím seznamu vyberte **IoT Edge modul** . Zadejte název `registry` pro modul registru Docker a zadejte `registry:latest` pro identifikátor URI image. Dále přidejte proměnné prostředí a vytvořte možnosti, které odkazují na místní modul registru v registru Microsoft Container Registry ke stažení imagí kontejneru z a k obsluze těchto imagí v registru: 5000.

1. Na kartě proměnné prostředí zadejte následující dvojici název-hodnota proměnné prostředí:

    | Name | Hodnota |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. Na kartě Možnosti vytvoření kontejneru zadejte následující kód JSON:

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. V dalším kroku přidejte modul API proxy do zařízení nejvyšší vrstvy. V rozevíracím seznamu vyberte **+ Přidat** a vyberte **modul Marketplace** . Vyhledejte `IoT Edge API Proxy` a vyberte modul. Proxy IoT Edge API používá port 8000 a je nakonfigurovaný tak, aby ve výchozím nastavení používal váš modul registru s názvem `registry` na portu 5000.

1. Vyberte **zkontrolovat + vytvořit** a pak **vytvořit** a dokončete nasazení. Vaše zařízení IoT Edge modulu runtime nejvyšší vrstvy, které má přístup k Internetu, vyžádá a spustí konfigurace **verze Public Preview** pro IoT Edge centra a IoT Edge agenta.

   ![Kompletní nasazení obsahující centrum Edge, hraniční agent, modul registru a proxy modul rozhraní API](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. V [Azure Cloud Shell](https://shell.azure.com/)zadejte následující příkaz, který vytvoří deployment.jsv souboru:

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. Zkopírujte obsah JSON níže do deployment.jsna, uložte ho a zavřete.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Zadejte následující příkaz, který vytvoří nasazení na nejvyšší hraniční zařízení vrstvy:

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

## <a name="deploy-modules-to-the-lower-layer-device"></a>Nasaďte moduly do zařízení nižší vrstvy.

K nasazení úloh z cloudu do **nižších vrstev** můžete použít jak Azure Portal, tak Azure CLI.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V [Azure Portal](https://ms.portal.azure.com/):

1. Přejděte na IoT Hub.

1. V nabídce v levém podokně v části **Automatická správa zařízení** vyberte **IoT Edge**.

1. V seznamu zařízení IoT Edge klikněte na ID zařízení na nižší vrstvě.

1. Na horním panelu vyberte možnost **nastavit moduly**.

1. Vedle ikony ozubeného kolečka vyberte **nastavení modulu runtime**.

1. V části **hraniční centrum** v poli obrázek zadejte `$upstream:8000/azureiotedge-hub:1.2.0-rc2` .

1. Do modulu Edge hub přidejte následující proměnné prostředí:

    | Name | Hodnota |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Pod položkou **Agent Edge** v poli Image zadejte `$upstream:8000/azureiotedge-agent:1.2.0-rc2` . Vyberte **Uložit**.

1. Přidejte modul snímače teploty. V rozevíracím seznamu vyberte **+ Přidat** a vyberte **modul Marketplace** . Vyhledejte `Simulated Temperature Sensor` a vyberte modul.

1. V části **IoT Edge moduly** vyberte `Simulated Temperature Sensor` modul, který jste právě přidali, a aktualizujte jeho identifikátor URI obrázku tak, aby odkazoval na `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0` .

1. Kliknutím na **Uložit**, **zkontrolovat + vytvořit** a **vytvořit** dokončete nasazení.

   ![Kompletní nasazení, které obsahuje hraniční centrum, hraničního agenta a simulovaný senzor teploty](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. V [Azure Cloud Shell](https://shell.azure.com/)zadejte následující příkaz, který vytvoří deployment.jsv souboru:

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. Zkopírujte obsah JSON níže do deployment.jsna, uložte ho a zavřete.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Zadejte následující příkaz, který vytvoří nasazení set Module do spodního zařízení hraniční vrstvy:

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

On the device details page for your lower layer IoT Edge device, you should now see the temperature sensor module listed along the system modules as **Specified in deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by device**.

## IotEdge check

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc2
```
   
`azureiotedge-diagnostics`Hodnota je načtena z registru kontejneru, který je propojen s modulem registru. Tento kurz má ve výchozím nastavení nastavené na https://mcr.microsoft.com:

| Name | Hodnota |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Pokud používáte privátní registr kontejnerů, zajistěte, aby byly všechny Image (například IoTEdgeAPIProxy, edgeAgent, edgeHub a Diagnostika) přítomné v registru kontejneru.    
    
## <a name="view-generated-data"></a>Zobrazit vygenerovaná data

Vámi nabízený modul **snímače teploty** vygeneruje ukázková data prostředí. Posílá zprávy, které zahrnují okolní teplotu a vlhkost, teplotu a tlak počítače a časové razítko.

Zprávy doručené do služby IoT Hub můžete sledovat pomocí [rozšíření Azure IoT Hub pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Tyto zprávy můžete zobrazit také pomocí [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete odstranit místní konfigurace a prostředky Azure, které jste vytvořili v tomto článku, abyste se vyhnuli poplatkům.

Odstranění prostředků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

2. Vyberte název skupiny prostředků, která obsahuje vaše testovací prostředky služby IoT Edge. 

3. Projděte si seznam prostředků ve vaší skupině prostředků. Pokud chcete odstranit všechny prostředky, můžete vybrat možnost **Odstranit skupinu prostředků**. Pokud chcete odstranit pouze některé prostředky, můžete na ně kliknout a odstranit je jednotlivě. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali dvě IoT Edge zařízení jako brány a nastavili je jako nadřazené zařízení druhého. Pak jste si ukázali, že se k podřízenému zařízení z brány odhlásí image kontejneru.

Pokud chcete zjistit, jak Azure IoT Edge může vytvořit další řešení pro firemní využití, pokračujte dalšími kurzy.

> [!div class="nextstepaction"]
> [Nasazení modelu Azure Machine Learning jako modulu](tutorial-deploy-machine-learning.md)
