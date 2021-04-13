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
ms.openlocfilehash: bfecc88dc0c504cee615f1a3d35f9208aeb724f8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309180"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Kurz: vytvoření hierarchie IoT Edgech zařízení

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Nasazení uzlů Azure IoT Edge napříč sítěmi uspořádanými do hierarchických vrstev. Každá vrstva v hierarchii je zařízení brány, které zpracovává zprávy a požadavky ze zařízení ve vrstvě pod ní.

Můžete strukturovat hierarchii zařízení, aby se ke cloudu mohla připojit jenom nejvyšší vrstva, a nižší vrstvy můžou komunikovat jenom s sousedními severními a jižními vrstvami. Tato vrstva sítě je základem většiny průmyslových sítí, které se řídí [standardem ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

Cílem tohoto kurzu je vytvořit hierarchii IoT Edge zařízení, která simulují zjednodušené provozní prostředí. Na konci nasadíte [modul simulovaného senzoru teploty](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) na zařízení nižší vrstvy bez přístupu k Internetu stažením imagí kontejneru v hierarchii.

Tento kurz vás provede vytvořením hierarchie IoT Edge zařízení, nasazením IoT Edge kontejnerů runtime na vaše zařízení a místní konfigurací zařízení. V tomto kurzu použijete automatizovaný konfigurační nástroj k těmto akcím:

> [!div class="checklist"]
>
> * Vytvořte a definujte relace v hierarchii IoT Edgech zařízení.
> * Nakonfigurujte modul runtime IoT Edge na zařízeních ve vaší hierarchii.
> * Nainstalujte konzistentní certifikáty napříč vaší hierarchií zařízení.
> * Přidejte úlohy do zařízení ve vaší hierarchii.
> * K bezpečnému směrování přenosů HTTP přes jeden port ze zařízení s nižší vrstvou použijte [modul Proxy IoT Edge API](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) .

>[!TIP]
>Tento kurz obsahuje kombinaci ručních a automatizovaných kroků, které poskytují předvedení vnořených IoT Edge funkcí.
>
>Pokud byste chtěli zcela automatizovaný pohled na nastavení hierarchie IoT Edge zařízení, můžete postupovat podle [Azure IoT Edge skriptu pro průmyslovou ukázku IoT](https://aka.ms/iotedge-nested-sample). Tento skriptový scénář nasadí virtuální počítače Azure jako předkonfigurovaná zařízení pro simulaci prostředí továrny.
>
>Pokud chcete prohloubit postup ručních kroků při vytváření a správě hierarchie IoT Edge zařízení, přečtěte si téma [Průvodce postupy pro IoT Edge hierarchií brány zařízení](how-to-connect-downstream-iot-edge-device.md).

V tomto kurzu jsou definovány následující vrstvy sítě:

* **Nejvyšší vrstva**: zařízení IoT Edge v této vrstvě se můžou připojit přímo ke cloudu.

* **Nižší vrstvy**: IoT Edge zařízení ve vrstvách pod horní vrstvou se nemohou připojit přímo ke cloudu. Musí projít jedním nebo více zprostředkujícími IoT Edge zařízeními pro odesílání a příjem dat.

V tomto kurzu se pro jednoduchost používá dvě hierarchie zařízení, která je naformátovaná níže. Jedno zařízení, **horní vrstva zařízení**, představuje zařízení v horní vrstvě hierarchie, které se může připojit přímo ke cloudu. Toto zařízení se bude také označovat jako **nadřazené zařízení**. Druhé zařízení, **nižší vrstva zařízení**, představuje zařízení v nižší vrstvě hierarchie, které se nemůže připojit přímo ke cloudu. V případě potřeby můžete přidat další zařízení nižší vrstvy, která reprezentují vaše provozní prostředí. Zařízení v nižších vrstvách budou také označována jako **podřízená zařízení**.

![Struktura hierarchie kurzu, která obsahuje dvě zařízení: horní vrstva zařízení a nižší vrstva](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit hierarchii IoT Edgech zařízení, budete potřebovat:

* Počítač (Windows nebo Linux) s připojením k Internetu.
* Účet Azure s platným předplatným. Pokud ještě nemáte [předplatné Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) v Azure úrovně Free nebo Standard.
* Prostředí bash v Azure Cloud Shell pomocí rozhraní příkazového řádku Azure CLI v 2.3.1 s nainstalovaným rozšířením Azure IoT v 0.10.6 nebo vyšším. V tomto kurzu se používá [Azure Cloud Shell](../cloud-shell/overview.md). Pokud Azure Cloud Shell neznáte, [Projděte si podrobnosti v rychlém](./quickstart-linux.md#prerequisites)startu.
  * Pokud chcete zobrazit aktuální verze modulů a rozšíření Azure CLI, spusťte příkaz [AZ Version](/cli/azure/reference-index?#az_version).
* Zařízení se systémem Linux, které se má nakonfigurovat jako zařízení IoT Edge pro každé zařízení ve vaší hierarchii. V tomto kurzu se používají dvě zařízení. Pokud nemáte dostupná zařízení, můžete vytvořit virtuální počítače Azure pro každé zařízení v hierarchii pomocí následujícího příkazu.

   Zástupný text v následujícím příkazu nahraďte a dvakrát ho spusťte pro každý virtuální počítač. Každý virtuální počítač potřebuje jedinečnou předponu DNS, která bude taky sloužit jako jeho název. Předpona DNS musí odpovídat následujícímu regulárnímu výrazu: `[a-z][a-z0-9-]{1,61}[a-z0-9]` .

   ```bash
   az deployment group create \
    --resource-group <REPLACE_WITH_YOUR_RESOURCE_GROUP> \
    --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
    --parameters dnsLabelPrefix='<REPLACE_WITH_UNIQUE_DNS_FOR_VIRTUAL_MACHINE>' \
    --parameters adminUsername='azureuser' \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/id_rsa.pub)" \
    --query "properties.outputs.[publicFQDN.value, publicSSH.value]" -o tsv
   ```

   Virtuální počítač používá klíče SSH k ověřování uživatelů. Pokud si nejste vědomi vytváření a používání klíčů SSH, můžete postupovat podle [pokynů pro páry klíčů veřejných privátních klíčů pro virtuální počítače se systémem Linux v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys).

   IoT Edge verze 1,2 je předinstalována s touto šablonou ARM a ukládá nutnost ruční instalace prostředků do virtuálních počítačů. Pokud instalujete IoT Edge na vlastní zařízení, přečtěte si téma [instalace Azure IoT Edge pro Linux (verze 1,2)](how-to-install-iot-edge.md) nebo [aktualizace IoT Edge na verzi 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

   Úspěšné vytvoření virtuálního počítače pomocí této šablony ARM vypíše popisovač vašeho virtuálního počítače `SSH` a plně kvalifikovaný název domény ( `FQDN` ). V pozdějších krocích použijete popisovač SSH a buď plně kvalifikovaný název domény nebo IP adresu každého virtuálního počítače. Tyto informace si můžete nechat sledovat. Ukázkový výstup je uvedený na obrázku níže.

   >[!TIP]
   >Můžete také najít IP adresu a plně kvalifikovaný název domény na Azure Portal. Pro IP adresu přejděte do seznamu virtuálních počítačů a poznamenejte si **pole veřejná IP adresa**. Pro plně kvalifikovaný název domény, navštivte stránku Přehled jednotlivých virtuálních počítačů a vyhledejte pole **název DNS** .

   ![Virtuální počítač při vytváření vytvoří výstup JSON, který obsahuje jeho popisovač SSH.](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Ujistěte se, že následující porty jsou otevřené příchozí pro všechna zařízení s výjimkou zařízení s nejnižší vrstvou: 8000, 443, 5671, 8883:
  * 8000: používá se k vyžádání imagí kontejneru Docker prostřednictvím proxy serveru rozhraní API.
  * 443: používá se mezi nadřazenými a podřízenými hraničními centry pro REST API volání.
  * 5671, 8883: používá se pro AMQP a MQTT.

  Další informace najdete v tématu [Postup otevření portů na virtuálním počítači s Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md).

## <a name="configure-your-iot-edge-device-hierarchy"></a>Konfigurace IoT Edge hierarchie zařízení

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Vytvoření hierarchie IoT Edgech zařízení

IoT Edge zařízení tvoří vrstvy vaší hierarchie. V tomto kurzu dojde k vytvoření hierarchie dvou IoT Edge zařízení: **horní vrstva zařízení** a její podřízenosti, což je **nižší vrstva**. V případě potřeby můžete vytvořit další podřízená zařízení.

Pokud chcete vytvořit a nakonfigurovat hierarchii IoT Edgech zařízení, použijte `iotedge-config` nástroj. Tento nástroj zjednodušuje konfiguraci hierarchie automatizací a zúžením několika kroků do dvou:

1. Nastavení konfigurace cloudu a příprava jednotlivých konfigurací zařízení, mezi které patří:

   * Vytváření zařízení v IoT Hub
   * Nastavení vztahů nadřazenosti a podřízenosti pro autorizaci komunikace mezi zařízeními
   * Generování řetězu certifikátů pro každé zařízení, aby se mezi nimi navázala zabezpečená komunikace
   * Generují se konfigurační soubory pro každé zařízení.

1. Instalace všech konfigurací zařízení, včetně:

   * Instalace certifikátů na každé zařízení
   * Použití konfiguračních souborů pro každé zařízení

`iotedge-config`Nástroj také provede nasazení modulu do zařízení IoT Edge automaticky.

Pokud chcete použít `iotedge-config` Nástroj k vytvoření a konfiguraci hierarchie, postupujte podle následujících kroků v rozhraní příkazového řádku Azure CLI:

1. V [Azure Cloud Shell](https://shell.azure.com/)vytvořte adresář pro prostředky vašeho kurzu:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Stáhněte si verzi konfiguračního nástroje a šablony konfigurace:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Tím se vytvoří `iotedge_config_cli_release` Složka v adresáři kurzu.

   Soubor šablony použitý k vytvoření hierarchie zařízení se nachází `iotedge_config.yaml` v souboru `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` . Ve stejném adresáři `deploymentLowerLayer.json` je soubor nasazení JSON, který obsahuje pokyny pro to, které moduly nasadíte do **nižší vrstvy zařízení**. `deploymentTopLayer.json`Soubor je stejný, ale pro **zařízení nejvyšší vrstvy**, protože moduly nasazené do jednotlivých zařízení nejsou stejné. `device_config.toml`Soubor je šablonou pro IoT Edge konfigurace zařízení a použije se k automatickému generování konfiguračních sad pro zařízení ve vaší hierarchii.

   Pokud se chcete podívat na zdrojový kód a skripty pro `iotedge-config` nástroj, podívejte [se na Azure-Samples úložiště na GitHubu](https://github.com/Azure-Samples/iotedge_config_cli).

1. Otevřete šablonu konfigurace kurzu a upravte ji pomocí vašich informací:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   V části **iothub** vyplňte `iothub_hostname` `iothub_name` pole a informacemi. Tyto informace najdete na stránce s přehledem IoT Hub na Azure Portal.

   V části volitelné **certifikáty** můžete naplnit pole pomocí absolutních cest k vašemu certifikátu a klíči. Pokud necháte tato pole prázdná, skript automaticky vygeneruje testovací certifikáty podepsané svým držitelem pro vaše použití. Pokud nejste obeznámeni s tím, jak se používají certifikáty ve scénáři brány, podívejte [se na část certifikát Průvodce postupy](how-to-connect-downstream-iot-edge-device.md#prepare-certificates).

   V části **Konfigurace** `template_config_path` je cesta k `device_config.toml` šabloně použité k vytvoření konfigurací zařízení. `default_edge_agent`Pole určuje, jakou image agenta Edge se budou počítat a kde se budou zařízení vyžádat.

   V části **edgedevices** můžete pro produkční scénář upravit strom hierarchie tak, aby odrážel požadovanou strukturu. Pro účely tohoto kurzu přijměte výchozí strom. Pro každé zařízení je k dispozici `device_id` pole, kde můžete pojmenovat zařízení. K dispozici je také `deployment` pole, které určuje cestu k nasazení JSON pro dané zařízení.

   Zařízení IoT Edge můžete v IoT Hub taky ručně zaregistrovat prostřednictvím Azure Portal nebo Azure Cloud Shell. Informace o tom, jak se dozvědět, najdete v [příručce k registraci zařízení IoT Edge](how-to-register-device.md).

   Relace nadřazený-podřízený můžete definovat také ručně. Další informace najdete v části [vytvoření hierarchie brány](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) v průvodci postupy.

   ![Část edgedevices konfiguračního souboru umožňuje definovat hierarchii.](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Soubor uložte a zavřete:

   `CTRL + S`, `CTRL + Q`

1. Vytvořte adresář výstupy pro konfigurační sady v adresáři vašich prostředků kurzu:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Přejděte do `iotedge_config_cli_release` adresáře a spusťte nástroj a vytvořte svou hierarchii IoT Edgech zařízení:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   Pomocí `--output` příznaku nástroj vytvoří certifikáty zařízení, sady certifikátů a soubor protokolu v adresáři dle vašeho výběru. Když `-f` nastavíte příznak, nástroj automaticky vyhledá existující IoT Edge zařízení v IoT Hub a odebere je, aby se předešlo chybám a zachovala se vaše centrum čistě.

   Konfigurační nástroj vytvoří IoT Edge zařízení a nastaví mezi nimi vztahy mezi nimi a mezi nimi. Volitelně vytvoří certifikáty, které se budou používat pro vaše zařízení. Pokud jsou k dispozici cesty k JSON pro nasazení, nástroj tato nasazení automaticky vytvoří na vaše zařízení, ale to se nevyžaduje. Nástroj nakonec vygeneruje konfigurační sady pro vaše zařízení a umístí je do výstupního adresáře. Podrobný přehled kroků provedených konfiguračním nástrojem najdete v souboru protokolu ve výstupním adresáři.

   ![Po provedení skriptu se zobrazí topologie vaší hierarchie.](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Ověřte, že výstup topologie ze skriptu vypadá správně. Jakmile budete spokojeni s tím, že je vaše hierarchie správně strukturovaná, budete připraveni pokračovat.

### <a name="configure-the-iot-edge-runtime"></a>Konfigurace modulu runtime IoT Edge

Kromě zřizování vašich zařízení postup konfigurace naváže důvěryhodnou komunikaci mezi zařízeními ve vaší hierarchii pomocí certifikátů, které jste vytvořili dříve. Kroky také zahájí vytvoření struktury sítě v hierarchii. Zařízení nejvyšší vrstvy bude udržovat připojení k Internetu, což umožňuje, aby si vyčetla image z cloudu, zatímco zařízení nižší vrstvy budou směrovat přes zařízení nejvyšší vrstvy, aby k nim měli přístup.

Chcete-li nakonfigurovat modul runtime IoT Edge, je nutné použít konfigurační sady vytvořené instalačním skriptem na vaše zařízení. Konfigurace se mírně liší mezi **zařízením horní vrstvy** a **nižší vrstvou zařízení**, takže je potřeba mít na vědomí, který konfigurační soubor zařízení se aplikuje na každé zařízení.

1. Každé zařízení potřebuje svůj odpovídající konfigurační svazek. Ke přesunutí konfiguračních sad na každé zařízení můžete použít jednotku USB nebo [zabezpečenou kopii souboru](https://www.ssh.com/ssh/scp/) .

   Nezapomeňte poslat do každého zařízení správnou konfigurační sadu.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   `:~`To znamená, že složka konfigurace bude umístěna do domovského adresáře na virtuálním počítači.

1. Přihlaste se k virtuálnímu počítači a použijte konfigurační sadu na zařízení:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. Na každém zařízení rozbalte konfigurační sadu. Nejdříve bude nutné nainstalovat zip:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. Na každém zařízení použijte konfigurační sadu na zařízení:

   ```bash
   sudo ./install.sh
   ```

   V **zařízení nejvyšší vrstvy** se zobrazí výzva k zadání názvu hostitele. Na **nižší vrstvě zařízení** se požádá o název hostitele a název hostitele nadřazeného objektu. Zadejte odpovídající IP adresu nebo plně kvalifikovaný název domény pro každé zobrazení výzvy. Můžete použít kteroukoli z těchto možností, ale v různých zařízeních je konzistentní. Výstup instalačního skriptu je uvedený na obrázku níže.

   Pokud se chcete blíže podívat, jaké úpravy konfiguračního souboru zařízení jsou, přečtěte si [část konfigurace IoT Edge na zařízeních v tématu Průvodce postupy](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

  ![Když nainstalujete konfigurační balíčky, aktualizují se na svém zařízení soubory Config. toml a automaticky se restartují všechny IoT Edge služby.](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Pokud jste výše uvedené kroky dokončili správně, můžete ověřit, jestli jsou vaše zařízení správně nakonfigurovaná.

Spusťte kontrolu konfigurace a připojení na vašich zařízeních. Pro **zařízení nejvyšší vrstvy**:

   ```bash
   sudo iotedge check
   ```

V případě **zařízení nižší vrstvy** musí být bitová kopie diagnostiky manuálně předána do příkazu:

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

Na **zařízení nejvyšší vrstvy** se očekává, že se zobrazí výstup s několika průchody. Můžou se zobrazit některá upozornění týkající se zásad protokolů a v závislosti na vaší síti a zásadách DNS.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

Jakmile budete spokojeni s konfigurací, které jsou na každém zařízení správné, budete připraveni pokračovat.

## <a name="deploy-modules-to-your-devices"></a>Nasazení modulů do zařízení

Nasazení modulu do vašich zařízení se automaticky vygenerovalo při vytvoření zařízení. `iotedge-config-cli`Nástroj nakrmené JSON nasazení pro **zařízení nejvyšší a nižší vrstvy** po jejich vytvoření. Nasazení modulu probíhalo v době, kdy jste nakonfigurovali IoT Edge runtime na každém zařízení. Po nakonfigurování modulu runtime nasazení na **nejvyšší vrstvě** začala. Po dokončení těchto nasazení by **zařízení nižší vrstvy** mohlo použít modul **proxy rozhraní IoT Edge API** k vyžádání potřebných imagí.

V [Azure Cloud Shell](https://shell.azure.com/)se můžete podívat na kód JSON pro nasazení na **nejvyšší vrstvě** , abyste zjistili, jaké moduly byly do vašeho zařízení nasazené:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Kromě modulů modulu runtime **IoT Edge agenta** a **centra IoT Edge** má **nejvyšší vrstva** k dis modul **registru docker** a modul **proxy rozhraní API IoT Edge** .

Modul **registru Docker** odkazuje na existující Azure Container Registry. V tomto případě `REGISTRY_PROXY_REMOTEURL` odkazuje na Microsoft Container Registry. V nástroji `createOptions` můžete vidět, že komunikuje na portu 5000.

Modul **Proxy IoT Edge API** SMĚRUJE požadavky HTTP na jiné moduly, což umožňuje zařízením nižší vrstvy načítat image kontejneru nebo doručovat objekty blob do úložiště. V tomto kurzu komunikuje na portu 8000 a je nakonfigurované tak, aby odesílaly požadavky na vyžádání obsahu kontejneru Docker na portu 5000 na vašem modulu **registru Docker** . Všechny požadavky na nahrávání do služby Blob Storage se také směrují do modulu AzureBlobStorageonIoTEdge na portu 11002. Další informace o modulu **proxy serveru rozhraní IoT Edge API** a jeho konfiguraci najdete v tématu [Průvodce](how-to-configure-api-proxy-module.md)modulem.

Pokud se chcete podívat, jak toto nasazení vytvořit pomocí Azure Portal nebo Azure Cloud Shell, přečtěte si téma [Průvodce postupy v části zařízení s nejvyšší vrstvou](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices).

V [Azure Cloud Shell](https://shell.azure.com/)se můžete podívat na kód JSON pro nasazení **nižší vrstvy** , abyste pochopili, které moduly byly na vaše zařízení nasazené:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

Můžete vidět, `systemModules` že běhové moduly **zařízení nižší vrstvy** jsou nastavené na načíst z, `$upstream:8000` namísto `mcr.microsoft.com` , jako **zařízení nejvyšší vrstvy** . **Nižší vrstva zařízení** odesílá image Docker žádá o **IoT Edge modul API Proxy** na portu 8000, protože nemůže přímo načíst image z cloudu. Druhý modul nasazený do **nižší vrstvy zařízení**, modul pro **simulaci teplotního snímače** , také vytváří svůj požadavek na obrázek `$upstream:8000` .

Pokud se chcete podívat, jak toto nasazení vytvořit pomocí Azure Portal nebo Azure Cloud Shell, přečtěte si [část zařízení nižší vrstvy tématu Průvodce postupy](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices).

Stav modulů můžete zobrazit pomocí příkazu:

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   Tento příkaz zobrazí výstup všech hlášených vlastností edgeAgent. Tady je několik užitečných pro monitorování stavu zařízení: *běhový stav*, *čas spuštění za běhu*, *čas posledního ukončení* za běhu, *počet restartování za běhu*.

Stav modulů můžete zobrazit také na [Azure Portal](https://ms.portal.azure.com/). Pokud chcete zobrazit vaše zařízení a moduly, přejděte do části **IoT Edge** v IoT Hub.

Až budete s nasazeními modulů spokojeni, budete připraveni pokračovat.

## <a name="view-generated-data"></a>Zobrazit vygenerovaná data

Vámi nabízený modul **snímače teploty** vygeneruje ukázková data prostředí. Posílá zprávy, které zahrnují okolní teplotu a vlhkost, teplotu a tlak počítače a časové razítko.

Tyto zprávy můžete zobrazit také pomocí [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Řešení potíží

Spuštěním `iotedge check` příkazu Ověřte konfiguraci a odstraňte chyby.

Můžete spustit `iotedge check` ve vnořené hierarchii, a to i v případě, že podřízené počítače nemají přímý přístup k Internetu.

Při spuštění `iotedge check` z nižší vrstvy se program pokusí načíst image z nadřazené položky přes port 443.

V tomto kurzu použijeme port 8000, proto ho musíme zadat:

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2.0-rc4
```

`azureiotedge-diagnostics`Hodnota je načtena z registru kontejneru, který je propojen s modulem registru. Tento kurz má ve výchozím nastavení nastavené na https://mcr.microsoft.com:

| Name | Hodnota |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Pokud používáte privátní registr kontejnerů, ujistěte se, že jsou v registru kontejneru přítomné všechny Image (IoTEdgeAPIProxy, edgeAgent, edgeHub, senzor teploty a Diagnostika).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete odstranit místní konfigurace a prostředky Azure, které jste vytvořili v tomto článku, abyste se vyhnuli poplatkům.

Odstranění prostředků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

2. Vyberte název skupiny prostředků, která obsahuje vaše testovací prostředky služby IoT Edge. 

3. Projděte si seznam prostředků ve vaší skupině prostředků. Pokud chcete odstranit všechny prostředky, můžete vybrat možnost **Odstranit skupinu prostředků**. Pokud chcete odstranit pouze některé prostředky, můžete na ně kliknout a odstranit je jednotlivě. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali dvě IoT Edge zařízení jako brány a nastavili je jako nadřazené zařízení druhého. Pak jste provedli navýšení image kontejneru do podřízeného zařízení prostřednictvím brány pomocí modulu proxy rozhraní IoT Edge API. Pokud chcete získat další informace, přečtěte si [příručku k používání modulu proxy](how-to-configure-api-proxy-module.md) .

Další informace o používání bran k vytváření hierarchických vrstev IoT Edgech zařízení najdete v tématu [Postup při připojování zařízení IoT Edge pro příjem dat](how-to-connect-downstream-iot-edge-device.md).

Pokud chcete zjistit, jak Azure IoT Edge může vytvořit další řešení pro firemní využití, pokračujte dalšími kurzy.

> [!div class="nextstepaction"]
> [Nasazení modelu Azure Machine Learning jako modulu](tutorial-deploy-machine-learning.md)
