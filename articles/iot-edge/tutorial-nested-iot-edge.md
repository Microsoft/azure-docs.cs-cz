---
title: Kurz – vytvoření hierarchie IoT Edge zařízení – Azure IoT Edge
description: V tomto kurzu se dozvíte, jak vytvořit hierarchickou strukturu IoT Edge zařízení pomocí bran.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: f1b1a94dc1d96e625947eef5730c24f080fc155a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721408"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>Kurz: vytvoření hierarchie zařízení IoT Edge (Preview)

Nasazení uzlů Azure IoT Edge napříč sítěmi uspořádanými do hierarchických vrstev. Každá vrstva v hierarchii je zařízení brány, které zpracovává zprávy a požadavky ze zařízení ve vrstvě pod ní.

>[!NOTE]
>Tato funkce vyžaduje IoT Edge verze 1,2, která je ve verzi Public Preview, spouští se kontejnery Linux.

Můžete strukturovat hierarchii zařízení, aby se ke cloudu mohla připojit jenom nejvyšší vrstva, a nižší vrstvy můžou komunikovat jenom s sousedními severními a jižními vrstvami. Tato vrstva sítě je základem většiny průmyslových sítí, které se řídí [standardem ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

Cílem tohoto kurzu je vytvořit hierarchii IoT Edge zařízení, která simulují produkční prostředí. Na konci nasadíte [modul simulovaného senzoru teploty](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) na zařízení nižší vrstvy bez přístupu k Internetu stažením imagí kontejneru v hierarchii.

Tento kurz vás provede vytvořením hierarchie IoT Edge zařízení, nasazením IoT Edge kontejnerů runtime na vaše zařízení a místní konfigurací zařízení. V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvořte a definujte relace v hierarchii IoT Edgech zařízení.
> * Nakonfigurujte modul runtime IoT Edge na zařízeních ve vaší hierarchii.
> * Nainstalujte konzistentní certifikáty napříč vaší hierarchií zařízení.
> * Přidejte úlohy do zařízení ve vaší hierarchii.
> * K bezpečnému směrování přenosů HTTP přes jeden port ze zařízení s nižší vrstvou použijte [modul Proxy IoT Edge API](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) .

V tomto kurzu jsou definovány následující vrstvy sítě:

* **Nejvyšší vrstva**: zařízení IoT Edge v této vrstvě se můžou připojit přímo ke cloudu.

* **Nižší vrstvy**: IoT Edge zařízení ve vrstvách pod horní vrstvou se nemohou připojit přímo ke cloudu. Musí projít jedním nebo více zprostředkujícími IoT Edge zařízeními pro odesílání a příjem dat.

V tomto kurzu se pro jednoduchost používá dvě hierarchie zařízení, která je naformátovaná níže. Jedno zařízení, **horní vrstva zařízení**, představuje zařízení v horní vrstvě hierarchie, které se může připojit přímo ke cloudu. Toto zařízení se bude také označovat jako **nadřazené zařízení**. Druhé zařízení, **nižší vrstva zařízení**, představuje zařízení v nižší vrstvě hierarchie, které se nemůže připojit přímo ke cloudu. V případě potřeby můžete přidat další zařízení nižší vrstvy, která reprezentují vaše provozní prostředí. Zařízení v nižších vrstvách budou také označována jako **podřízená zařízení**. Konfigurace dalších zařízení nižší vrstvy bude následovat po konfiguraci **zařízení nižší vrstvy**.

![Struktura hierarchie kurzu, která obsahuje dvě zařízení: horní vrstva zařízení a nižší vrstva](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit hierarchii IoT Edgech zařízení, budete potřebovat:

* Počítač (Windows nebo Linux) s připojením k Internetu.
* Účet Azure s platným předplatným. Pokud ještě nemáte [předplatné Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) v Azure úrovně Free nebo Standard.
* Azure CLI v 2.3.1 s nainstalovaným rozšířením Azure IoT v 0.10.6 nebo novějším. V tomto kurzu se používá [Azure Cloud Shell](../cloud-shell/overview.md). Pokud Azure Cloud Shell neznáte, [Projděte si podrobnosti v rychlém](./quickstart-linux.md#prerequisites)startu.
  * Pokud chcete zobrazit aktuální verze modulů a rozšíření Azure CLI, spusťte příkaz [AZ Version](/cli/azure/reference-index?#az_version).
* Zařízení se systémem Linux, které se má nakonfigurovat jako zařízení IoT Edge pro každé zařízení ve vaší hierarchii. V tomto kurzu se používají dvě zařízení. Pokud nemáte dostupná zařízení, můžete vytvořit virtuální počítače Azure pro každé zařízení v hierarchii tak, že v následujícím příkazu nahradíte zástupný text a spustíte ho:

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

>[!TIP]
>Pokud chcete při vytváření hierarchie IoT Edge zařízení automaticky sledovat, můžete postupovat podle [ukázkových Azure IoT Edge pro průmyslový příklad IoT](https://aka.ms/iotedge-nested-sample). Tento skriptový scénář nasadí virtuální počítače Azure jako předkonfigurovaná zařízení pro simulaci prostředí továrny.
>
>Pokud budete chtít pokračovat, i když vytvoříte ukázkovou hierarchii krok za krokem, pokračujte níže uvedenými kroky kurzu.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Konfigurace IoT Edge hierarchie zařízení

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Vytvoření hierarchie IoT Edgech zařízení

IoT Edge zařízení tvoří vrstvy vaší hierarchie. V tomto kurzu dojde k vytvoření hierarchie dvou IoT Edge zařízení: **horní vrstva zařízení** a její podřízenosti, což je **nižší vrstva**. V případě potřeby můžete vytvořit další podřízená zařízení.

Pokud chcete vytvořit zařízení IoT Edge, můžete použít Azure Portal nebo Azure CLI.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://ms.portal.azure.com/)přejděte na IoT Hub.

1. V nabídce v levém podokně v části **Automatická správa zařízení** vyberte **IoT Edge**.

1. Vyberte **+ Přidat zařízení IoT Edge**. Toto zařízení bude zařízení nejvyšší vrstvy, takže zadejte odpovídající jedinečné ID zařízení. Vyberte **Uložit**.

1. Znovu vyberte **+ přidat IoT Edge zařízení** . Toto zařízení bude zařízením nižší vrstvy, takže zadejte odpovídající jedinečné ID zařízení.

1. Zvolte možnost **nastavit nadřazené zařízení**, v seznamu zařízení vyberte zařízení nejvyšší vrstvy a vyberte **OK**. Vyberte **Uložit**.

   ![Nastavení nadřazené položky pro zařízení nižší vrstvy](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. V [Azure Cloud Shell](https://shell.azure.com/)zadejte následující příkaz, který vytvoří zařízení IoT Edge ve vašem centru. Toto zařízení bude zařízení nejvyšší vrstvy, takže zadejte příslušné jedinečné ID zařízení:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

   Po úspěšném vytvoření zařízení dojde ke výstupu konfigurace JSON zařízení.

   ![Výstup JSON úspěšného vytvoření zařízení](./media/tutorial-nested-iot-edge/json-success-output.png)

1. Zadejte následující příkaz, který vytvoří nižší vrstvu IoT Edge zařízení. Pokud chcete ve své hierarchii více vrstev, můžete vytvořit více než jedno zařízení nižší vrstvy. Ujistěte se, že jsou k dispozici jedinečné identity zařízení.

   ```azurecli-interactive
   az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Zadejte následující příkaz k definování jednotlivých vztahů mezi nadřazenými a podřízenými **zařízeními mezi zařízením nejvyšší vrstvy** a všemi **zařízeními nižší vrstvy**. Nezapomeňte spustit tento příkaz pro každé zařízení nižší vrstvy ve vaší hierarchii.

   ```azurecli-interactive
   az iot hub device-identity parent set --device-id {lower_layer_device_id} --parent-device-id {top_layer_device_id} --hub-name {hub_name}
   ```

   Tento příkaz neobsahuje explicitní výstup.

---

V dalším kroku si poznamenejte připojovací řetězec každého zařízení IoT Edge. Budou použity později.

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

Pokud jste výše uvedené kroky dokončili správně, můžete pomocí následujícího postupu ověřit, jestli jsou vaše vztahy nadřazenosti a podřízenosti v Azure Portal nebo Azure CLI správné.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://ms.portal.azure.com/)přejděte do části **IoT Edge** IoT Hub.

1. V seznamu zařízení klikněte na ID zařízení s **nižší vrstvou zařízení** .

1. Na stránce s podrobnostmi o zařízení by se měla zobrazit identita **zařízení nejvyšší vrstvy** uvedená vedle pole **nadřazené zařízení** .

   [Nadřazené zařízení potvrzené podřízeným zařízením](./media/tutorial-nested-iot-edge/lower-layer-device-parent.png)

Můžete také začlenit relaci hierarchie do svého **zařízení nejvyšší vrstvy**.

1. V seznamu zařízení klikněte na ID zařízení **nejvyšší vrstvy** .

1. Vyberte kartu **Spravovat podřízená zařízení** v horní části.

1. V seznamu zařízení byste měli vidět vaše **zařízení nižší vrstvy**.

   [Podřízené zařízení potvrzené nadřazeným zařízením](./media/tutorial-nested-iot-edge/top-layer-device-child.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. V [Azure Cloud Shell](https://shell.azure.com/) můžete ověřit, že se některá z vašich podřízených zařízení úspěšně navázala jako vztah k nadřazenému zařízení tím, že získá identitu potvrzeného nadřazeného zařízení podřízeného zařízení. Tím se vytvoří výstup konfigurace JSON nadřazeného zařízení, které se navýšely na obrázek:

   ```azurecli-interactive
   az iot hub device-identity parent show --device-id {lower_layer_device_id} --hub-name {hub_name}
   ```

Můžete také začlenit relaci vaší hierarchie se dotazem na **zařízení nejvyšší vrstvy**.

1. Seznam podřízených zařízení, která Nadřazená zařízení zná:

    ```azurecli-interactive
    az iot hub device-identity children list --device-id {top_layer_device_id} --hub-name {hub_name}
    ```

---

Jakmile budete spokojeni s tím, že je vaše hierarchie správně strukturovaná, budete připraveni pokračovat.

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
   ./certGen.sh create_edge_device_ca_certificate "{top_layer_certificate_name}"
   ./certGen.sh create_edge_device_ca_certificate "{lower_layer_certificate_name}"
   ```

   Tento příkaz skriptu vytvoří několik souborů certifikátů a klíčů, ale na každém zařízení IoT Edge používáme následující certifikát a pár klíčů a odkazuje v konfiguračním souboru:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Každé zařízení potřebuje kopii certifikátu kořenové certifikační autority a kopii vlastního certifikátu certifikační autority pro zařízení a privátního klíče. K přesunutí certifikátů na každé zařízení můžete použít jednotku USB nebo [zabezpečenou kopii souboru](https://www.ssh.com/ssh/scp/) .

1. Po přenesení certifikátů nainstalujte pro každé zařízení kořenovou certifikační autoritu.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   Tento příkaz by měl mít výstup, ve kterém byl přidán jeden certifikát `/etc/ssl/certs` .

   [Úspěšná zpráva o instalaci certifikátu](./media/tutorial-nested-iot-edge/certificates-success-output.png)

Pokud jste výše uvedené kroky dokončili správně, můžete se podívat na to, že se vaše certifikáty nainstalují, a to tak, že `/etc/ssl/certs` přejdete do tohoto adresáře a vyhledáte nainstalované certifikáty.

1. Přejít na `/etc/ssl/certs` :

   ```bash
   cd /etc/ssl/certs
   ```

1. Seznam nainstalovaných certifikátů a `grep` pro `azure` :

   ```bash
   ll | grep azure
   ```

   Měla by se zobrazit hodnota hash certifikátu propojená s certifikátem kořenové certifikační autority a certifikátem kořenové certifikační autority propojenou s kopií ve vašem `usr/local/share` adresáři.

   [Výsledky hledání certifikátů Azure](./media/tutorial-nested-iot-edge/certificate-list-results.png)

Jakmile budete spokojeni s tím, že se certifikáty nainstalují na každé zařízení, budete připraveni pokračovat.

### <a name="install-iot-edge-on-the-devices"></a>Instalace IoT Edge na zařízeních

Instalace imagí IoT Edge verze 1,2 runtime poskytuje vašim zařízením přístup k funkcím, které jsou nezbytné k tomu, aby fungovaly jako hierarchie zařízení.

Chcete-li nainstalovat IoT Edge, je nutné nainstalovat příslušnou konfiguraci úložiště, nainstalovat požadované součásti a nainstalovat potřebné prostředky vydaných verzí.

1. Nainstalujte konfiguraci úložiště, která odpovídá operačnímu systému vašeho zařízení.

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

1. Nainstalujte IoT Edge a službu IoT identity.

   ```bash
   sudo apt-get install aziot-edge
   ```

Pokud jste výše uvedené kroky dokončili správně, zobrazila se zpráva Azure IoT Edge banneru s požadavkem, abyste aktualizovali konfigurační soubor Azure IoT Edge, `/etc/aziot/config.toml` na každém zařízení ve vaší hierarchii. Pokud ano, budete připraveni pokračovat.

### <a name="configure-the-iot-edge-runtime"></a>Konfigurace modulu runtime IoT Edge

Kromě zřizování vašich zařízení postup konfigurace naváže důvěryhodnou komunikaci mezi zařízeními ve vaší hierarchii pomocí certifikátů, které jste vytvořili dříve. Kroky také zahájí vytvoření struktury sítě v hierarchii. Zařízení nejvyšší vrstvy bude udržovat připojení k Internetu, což umožňuje, aby si vyčetla image z cloudu, zatímco zařízení nižší vrstvy budou směrovat přes zařízení nejvyšší vrstvy, aby k nim měli přístup.

Chcete-li konfigurovat modul runtime IoT Edge, je nutné upravit několik součástí konfiguračního souboru. Konfigurace se mírně liší mezi **zařízením horní vrstvy** a **nižší vrstvou zařízení**, takže je důležité mít na vědomí, který konfigurační soubor zařízení upravujete pro každý krok. Konfigurace modulu runtime IoT Edge pro vaše zařízení se skládá ze čtyř kroků, které jsou dokončené úpravou konfiguračního souboru IoT Edge:

* Ruční zřízení každého zařízení přidáním připojovacího řetězce zařízení do konfiguračního souboru.

* Dokončete nastavení certifikátů zařízení tak, že přejdete na konfigurační soubor na certifikát certifikační autority zařízení, na privátní klíč certifikační autority zařízení a na certifikát kořenové certifikační autority.

* Proveďte zavedení systému pomocí agenta IoT Edge.

* Aktualizujte parametr **hostname** pro zařízení **nejvyšší vrstvy** a aktualizujte parametr **hostname** a parametr **parent_hostname** pro vaše zařízení **nižší vrstvy** .

Proveďte tyto kroky a restartujte službu IoT Edge pro konfiguraci zařízení.

>[!TIP]
>Při procházení konfiguračního souboru v nano můžete pomocí **kombinace kláves CTRL + W** vyhledat klíčová slova v souboru.

1. Na každém zařízení vytvořte konfigurační soubor založený na zahrnuté šabloně.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml

1. On each device, open the IoT Edge configuration file.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Pro zařízení **nejvyšší vrstvy** Najděte oddíl **hostname** . Odkomentujte řádek `hostname` parametrem a aktualizujte hodnotu na plně kvalifikovaný název domény (FQDN) nebo IP adresu zařízení IoT Edge. Použijte jakoukoli hodnotu, kterou zvolíte konzistentně napříč zařízeními ve vaší hierarchii.

   ```toml
   hostname = "<device fqdn or IP>"
   ```

   >[!TIP]
   >Vložení obsahu schránky do nano `Shift+Right Click` nebo stisknutím klávesy `Shift+Insert` .

1. Pro všechna IoT Edge zařízení v **nižších vrstvách** Najděte oddíl **nadřazený název hostitele** . Odkomentujte řádek `parent_hostname` parametrem a aktualizujte hodnotu tak, aby odkazovala na plně kvalifikovaný název domény nebo IP adresu nadřazeného zařízení. Použijte přesnou hodnotu, kterou vložíte do pole **hostname** nadřazeného zařízení. Pro IoT Edge zařízení v **horní vrstvě** nechejte tento parametr Zakomentovat.

   ```toml
   parent_hostname = "<parent device fqdn or IP>"
   ```

   > [!NOTE]
   > V případě hierarchií s více než jednou nižší vrstvou aktualizujte pole *parent_hostname* se plně kvalifikovaným názvem domény zařízení ve vrstvě hned výše.

1. V souboru vyhledejte část **certifikát skupiny důvěryhodných certifikátů** . Odkomentujte řádek `trust_bundle_cert` parametrem a aktualizujte hodnotu pomocí cesty identifikátoru URI souboru na kořenový certifikát certifikační autority, který sdílí všechna zařízení v hierarchii brány.

   ```toml
   trust_bundle_cert = "<root CA certificate>"
   ```

1. Vyhledejte oddíl **zřizování** souboru. Odkomentujte řádky pro **Ruční zřizování pomocí připojovacího řetězce**. Pro každé zařízení ve vaší hierarchii aktualizujte hodnotu **connection_string** připojovacím řetězcem ze zařízení IoT Edge.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string: "<Device connection string>"
   ```

1. Najděte **výchozí oddíl Edge agent** .

   * V případě zařízení **nejvyšší vrstvy** aktualizujte hodnotu image edgeAgent na verzi Public Preview modulu v Azure Container Registry.
   
     ```toml
     [agent.config]
     image = "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4"
     ```

   * Pro každé zařízení IoT Edge v **nižších vrstvách** aktualizujte image edgeAgent tak, aby odkazovala na nadřazené zařízení následované portem, na kterém proxy API naslouchá. V další části nasadíte modul proxy API do nadřazeného zařízení.
   
     ```toml
     [agent.config]
     image = "<parent hostname value>:8000/azureiotedge-agent:1.2.0-rc4"
     ```

1. Vyhledejte část **certifikát hraniční autority** . Odkomentujte první tři řádky této části. Pak aktualizujte dva parametry tak, aby odkazovaly na soubory certifikátů certifikační autority zařízení a soukromé klíče certifikační autority zařízení, které jste vytvořili v předchozí části, a přesunuli do zařízení IoT Edge. Zadejte cesty identifikátorů URI souboru, které mají formát `file:///<path>/<filename>` , například `file:///certs/iot-edge-device-ca-top-layer-device.key.pem` .

   ```yml
   [edge_ca]
   cert = "<File URI path to the device full chain CA certificate unique to this device.>"
   pk = "<File URI path to the device CA private key unique to this device.>"
   ```

   >[!NOTE]
   >Nezapomeňte k naplnění pole použít **cestu k úplnému řetězení** certifikátu certifikační autority a název souboru `device_ca_cert` .

1. Uložte soubor a zavřete ho.

   `CTRL + X`, `Y`, `Enter`

1. Po zadání informací o zřizování do konfiguračního souboru použijte tyto změny:

   ```bash
   sudo iotedge config apply
   ```

Než budete pokračovat, ujistěte se, že jste aktualizovali konfigurační soubor každého zařízení v hierarchii. V závislosti na struktuře hierarchie jste nakonfigurovali jedno **zařízení nejvyšší vrstvy** a jedno nebo více **zařízení s nižší vrstvou**.

Pokud jste výše uvedené kroky dokončili správně, můžete ověřit, jestli jsou vaše zařízení správně nakonfigurovaná.

1. Spusťte kontrolu konfigurace a připojení na vašich zařízeních:

   ```bash
   sudo iotedge check
   ```

Na **zařízení nejvyšší vrstvy** se očekává, že se zobrazí výstup s několika zkušebními hodnoceními a alespoň jedním upozorněním. Při kontrole se `latest security daemon` zobrazí upozornění, že další verze IoT Edge je nejnovější stabilní verze, protože IoT Edge verze 1,2 je ve verzi Public Preview. Můžou se zobrazit další upozornění týkající se zásad protokolů a v závislosti na vaší síti a zásadách DNS.

Na **nižší vrstvě zařízení** je třeba zobrazit výstup podobný zařízení nejvyšší vrstvy, ale s dalším upozorněním, že nelze z nadřazeného objektu načíst modul EdgeAgent. To je přijatelné, protože modul proxy IoT Edge rozhraní API a modul Docker Container Registry, který na nižší vrstvě přebírá image, se ještě nasazují do **zařízení nejvyšší vrstvy**.

Ukázkový výstup `iotedge check` je zobrazen níže:

[Ukázková konfigurace a výsledky připojení](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png)

Jakmile budete spokojeni s konfigurací, které jsou na každém zařízení správné, budete připraveni pokračovat.

## <a name="deploy-modules-to-the-top-layer-device"></a>Nasazení modulů do zařízení nejvyšší vrstvy

Moduly slouží k dokončení nasazení a IoT Edge běhového prostředí pro vaše zařízení a dále k definování struktury vaší hierarchie. Modul proxy rozhraní IoT Edge API bezpečně routs přenos HTTP přes jeden port ze zařízení s nižší vrstvou. Modul Docker Registry umožňuje úložiště Docker imagí, ke kterým mají vaše zařízení nižší vrstvy přístup, pomocí směrování image načte do nejvyšší vrstvy zařízení.

Pokud chcete nasadit moduly do zařízení nejvyšší vrstvy, můžete použít Azure Portal nebo Azure CLI.

>[!NOTE]
>Zbývající kroky k dokončení konfigurace IoT Edge runtime a úloh nasazení se na vašich IoT Edge zařízeních neprovádí.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V [Azure Portal](https://ms.portal.azure.com/):

1. Přejděte na IoT Hub.

1. V nabídce v levém podokně v části **Automatická správa zařízení** vyberte **IoT Edge**.

1. V seznamu zařízení klikněte na ID zařízení v **horním** zařízení Edge vrstvy.

1. Na horním panelu vyberte možnost **nastavit moduly**.

1. Vedle ikony ozubeného kolečka vyberte **nastavení modulu runtime**.

1. V části **hraniční centrum** v poli obrázek zadejte `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4` .

   ![Úprava obrázku hraničního centra](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Do modulu Edge hub přidejte následující proměnné prostředí:

    | Název | Hodnota |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Upravit proměnné prostředí hraničního centra](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. Pod položkou **Agent Edge** v poli Image zadejte `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4` . Vyberte **Uložit**.

1. Přidejte modul registru Docker do zařízení nejvyšší vrstvy. Vyberte **+ Přidat** a v rozevíracím seznamu vyberte **IoT Edge modul** . Zadejte název `registry` pro modul registru Docker a zadejte `registry:latest` pro identifikátor URI image. Dále přidejte proměnné prostředí a vytvořte možnosti, které odkazují na místní modul registru v registru Microsoft Container Registry ke stažení imagí kontejneru z a k obsluze těchto imagí v registru: 5000.

1. Na kartě proměnné prostředí zadejte následující dvojici název-hodnota proměnné prostředí:

    | Název | Hodnota |
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
                               } 
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
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4",
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

Pokud jste výše uvedené kroky dokončili správně, vaše **zařízení nejvyšší vrstvy** by mělo hlásit čtyři moduly: IoT Edge modul proxy rozhraní API, modul docker Container registry a systémové moduly, jak je **uvedeno v nasazení**. Může trvat několik minut, než zařízení dostanou nové nasazení a spustí moduly. Aktualizujte stránku, dokud neuvidíte modul snímače teploty uvedený jako **hlášený zařízením**. Jakmile zařízení ohlásí moduly, budete připraveni pokračovat.

## <a name="deploy-modules-to-the-lower-layer-device"></a>Nasaďte moduly do zařízení nižší vrstvy.

Moduly také slouží jako úlohy pro vaše zařízení nižší vrstvy. Modul pro simulaci teplotního senzoru vytváří ukázková data telemetrie, která poskytují funkční tok dat prostřednictvím vaší hierarchie zařízení.

Pokud chcete nasadit moduly do zařízení s nižší vrstvou, můžete použít Azure Portal nebo Azure CLI.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V [Azure Portal](https://ms.portal.azure.com/):

1. Přejděte na IoT Hub.

1. V nabídce v levém podokně v části **Automatická správa zařízení** vyberte **IoT Edge**.

1. V seznamu zařízení IoT Edge klikněte na ID zařízení na nižší vrstvě.

1. Na horním panelu vyberte možnost **nastavit moduly**.

1. Vedle ikony ozubeného kolečka vyberte **nastavení modulu runtime**.

1. V části **hraniční centrum** v poli obrázek zadejte `$upstream:8000/azureiotedge-hub:1.2.0-rc4` .

1. Do modulu Edge hub přidejte následující proměnné prostředí:

    | Název | Hodnota |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Pod položkou **Agent Edge** v poli Image zadejte `$upstream:8000/azureiotedge-agent:1.2.0-rc4` . Vyberte **Uložit**.

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
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc4",
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

If you completed the above steps correctly, your **lower layer device** should report three modules: the temperature sensor module and the system modules, as **Specified in Deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by Device**. Once the modules are reported by the device, you are ready to continue.

## Troubleshooting

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc4
```

`azureiotedge-diagnostics`Hodnota je načtena z registru kontejneru, který je propojen s modulem registru. Tento kurz má ve výchozím nastavení nastavené na https://mcr.microsoft.com:

| Název | Hodnota |
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
