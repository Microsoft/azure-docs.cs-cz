---
title: Nejčastější dotazy k řešení připojené továrny – Azure | Dokumenty společnosti Microsoft
description: Tento článek odpovídá na nejčastější dotazy pro akcelerátor řešení připojené továrny. Obsahuje odkazy na úložiště GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: f2f8c08176f80436a339924adb4b2a09338a548a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313590"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Nejčastější dotazy k akcelerátoru řešení Connected Factory

Viz také obecné [faq](iot-accelerators-faq.md) pro akcelerátory řešení IoT.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Kde najdu zdrojový kód akcelerátoru řešení?

Zdrojový kód je uložen v následujícím úložišti GitHub:

* [Urychlovač řešení připojené továrny](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Co je OPC UA?

OPC Unified Architecture (UA), která byla vydána v roce 2008, je standard interoperability nezávislý na platformě a orientovaný na služby. OPC UA se používá v různých průmyslových systémech a zařízeních, jako jsou průmyslové počítače, PLC a senzory. OPC UA integruje funkce specifikací OPC Classic do jednoho rozšiřitelného rámce s integrovaným zabezpečením. Je to standard, který je řízen Nadací OPC. [Nadace OPC](https://opcfoundation.org/) je nezisková organizace s více než 440 členy. Cílem organizace je použití specifikací OPC k usnadnění multi-vendor, multi-platformní, bezpečné a spolehlivé interoperability prostřednictvím:

* Infrastruktura
* Specifikace
* Technologie
* Procesy

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Proč microsoft vybral OPC UA pro akcelerátor řešení připojené továrny?

Společnost Microsoft zvolila OPC UA, protože se jedná o otevřený, neproprietární, nezávislý na platformě, uznávaný a osvědčený standard. Jedná se o požadavek na řešení referenční architektury Industrie 4.0 (RAMI4.0), která zajišťují interoperabilitu mezi širokou sadou výrobních procesů a zařízení. Společnost Microsoft vidí poptávku od svých zákazníků po sestavení řešení Industrie 4.0. Podpora OPC UA pomáhá snížit bariéru pro zákazníky k dosažení jejich cílů a poskytuje okamžitou obchodní hodnotu pro ně.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Jak přidám veřejnou IP adresu do simulačního virtuálního virtuálního montovního virtuálního montovna?

Máte dvě možnosti přidání IP adresy:

* Použijte skript `Simulation/Factory/Add-SimulationPublicIp.ps1` Prostředí PowerShell v [úložišti](https://github.com/Azure/azure-iot-connected-factory). Předejte název nasazení jako parametr. Pro místní nasazení `<your username>ConnFactoryLocal`použijte . Skript vytiskne IP adresu virtuálního počítačů.

* Na webu Azure Portal vyhledejte skupinu prostředků vašeho nasazení. S výjimkou místního nasazení má skupina prostředků název, který jste zadali jako název řešení nebo nasazení. Pro místní nasazení pomocí skriptu sestavení je `<your username>ConnFactoryLocal`název skupiny prostředků . Nyní přidejte nový prostředek **veřejné IP adresy** do skupiny prostředků.

> [!NOTE]
> V obou případech se ujistěte, že nainstalujete nejnovější záplaty podle pokynů na [webových stránkách Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Udržujte instalaci aktuální tak dlouho, dokud váš virtuální počítač je přístupný prostřednictvím veřejné IP adresy.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Jak odeberu veřejnou IP adresu do simulačního virtuálního virtuálního montovního virtuálního montova?

Máte dvě možnosti odebrání adresy IP:

* Použijte skript PowerShell Simulation/Factory/Remove-SimulationPublicIp.ps1 [úložiště](https://github.com/Azure/azure-iot-connected-factory). Předejte název nasazení jako parametr. Pro místní nasazení `<your username>ConnFactoryLocal`použijte . Skript vytiskne IP adresu virtuálního počítačů.

* Na webu Azure Portal vyhledejte skupinu prostředků vašeho nasazení. S výjimkou místního nasazení má skupina prostředků název, který jste zadali jako název řešení nebo nasazení. Pro místní nasazení pomocí skriptu sestavení je `<your username>ConnFactoryLocal`název skupiny prostředků . Nyní odeberte prostředek **veřejné IP adresy** ze skupiny prostředků.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Jak se přihlásím k simulačnímu virtuálnímu virtuálnímu virtuálnímu mísu?

Přihlášení k simulačnímu virtuálnímu počítači je podporované jenom `build.ps1` v případě, že jste nasadili řešení pomocí skriptu PowerShell v [úložišti](https://github.com/Azure/azure-iot-connected-factory).

Pokud jste nasadili řešení z www.azureiotsolutions.com, nemůžete se přihlásit k virtuálnímu virtuálnímu soudu. Nelze se přihlásit, protože heslo je generováno náhodně a nelze jej obnovit.

1. Přidejte veřejnou IP adresu do virtuálního počítačů. Podívejte se [na how do i add a public IP address to the simulation VM?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Vytvořte relaci SSH pro váš virtuální počítač pomocí IP adresy virtuálního počítače.
1. Uživatelské jméno, které `docker`chcete použít, je: .
1. Heslo, které chcete použít, závisí na verzi, kterou jste použili k nasazení:
    * Pro řešení nasazená pomocí skriptu build.ps1 před 1. `Passw0rd`
    * Pro řešení nasazená pomocí skriptu build.ps1 po 1. `<name of your deployment>.config.user` Heslo je uloženo v nastavení **VmAdminPassword.** Heslo je generováno náhodně v době nasazení, `build.ps1` pokud ho nezadáte pomocí parametru skriptu.`-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Jak zastavím a spustím všechny procesy dockeru v simulačním virtuálním virtuálním ms?

1. Přihlaste se k simulačnímu virtuálnímu virtuálnímu mísu. Podívejte se [na how do i sign in to the simulation VM?](#how-do-i-sign-in-to-the-simulation-vm)
1. Chcete-li zkontrolovat, které `docker ps`kontejnery jsou aktivní, spusťte: .
1. Chcete-li zastavit všechny `./stopsimulation`kontejnery simulace, spusťte: .
1. Spuštění všech simulačních kontejnerů:
    * Exportujte proměnnou prostředí s názvem **IOTHUB_CONNECTIONSTRING**. Použijte hodnotu nastavení **IotHubOwnerConnectionString** `<name of your deployment>.config.user` v souboru. Příklad:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Spusťte `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Jak můžu aktualizovat simulaci ve virtuálním ms?

Pokud jste v simulaci provedli nějaké změny, `build.ps1` můžete pomocí příkazu `updatedimulation` použít skript Prostředí PowerShell v [úložišti.](https://github.com/Azure/azure-iot-connected-factory) Tento skript vytvoří všechny součásti simulace, zastaví simulaci ve virtuálním ms, nahraje, nainstaluje a spustí je.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Jak zjistím připojovací řetězec centra IoT používaný mým řešením?

Pokud jste nasadili řešení `build.ps1` se skriptem v [úložišti](https://github.com/Azure/azure-iot-connected-factory), připojovací řetězec je `<name of your deployment>.config.user` hodnota **IotHubOwnerConnectionString** v souboru.

Připojovací řetězec můžete také najít pomocí portálu Azure. V prostředku služby IoT Hub ve skupině prostředků vašeho nasazení vyhledejte nastavení připojovacího řetězce.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Která zařízení ioT hubu používá simulace připojené továrny?

Simulace sama registruje následující zařízení:

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* vydavatel.beijing.corp.contoso
* vydavatel.capetown.corp.contoso
* vydavatel.mumbai.corp.contoso
* vydavatel.munich0.corp.contoso
* vydavatel.rio.corp.contoso
* vydavatel.seattle.corp.contoso

Pomocí [deviceexploreru](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) nebo [rozšíření IoT pro](https://github.com/Azure/azure-iot-cli-extension) nástroj Azure CLI můžete zkontrolovat, která zařízení jsou registrovaná v centru IoT, které vaše řešení používá. Chcete-li použít Průzkumník zařízení, budete potřebovat připojovací řetězec pro službu IoT hub ve vašem nasazení. Chcete-li použít rozšíření IoT pro Azure CLI, budete potřebovat název služby IoT Hub.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Jak lze získat data protokolu z komponent simulace?

Všechny součásti v informacích protokolu simulace do souborů protokolu. Tyto soubory se nacházejí ve virtuálním `home/docker/Logs`virtuálním ms ve složce . Chcete-li načíst protokoly, můžete `Simulation/Factory/Get-SimulationLogs.ps1` použít skript Prostředí PowerShell v [úložišti](https://github.com/Azure/azure-iot-connected-factory).

Tento skript se musí přihlásit k virtuálnímu virtuálnímu soudu. Možná budete muset zadat přihlašovací údaje pro přihlášení. Podívejte [se na how do i sign in to the simulation VM?](#how-do-i-sign-in-to-the-simulation-vm)

Skript přidá nebo odebere veřejnou IP adresu do virtuálního počítačů, pokud ještě nemá a odebere ji. Skript umístí všechny soubory protokolu do archivu a stáhne archiv do vývojové pracovní stanice.

Případně se přihlaste k virtuálnímu virtuálnímu provozu přes SSH a zkontrolujte soubory protokolu za běhu.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Jak lze zkontrolovat, zda simulace odesílá data do cloudu?

Pomocí [deviceexploreru](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) nebo příkazu [události monitorování rozšíření Azure IoT CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-monitor-events) můžete zkontrolovat data odeslaná do služby IoT Hub z určitých zařízení. Chcete-li použít tyto nástroje, musíte znát připojovací řetězec pro službu IoT hub ve vašem nasazení. Viz [Jak zjistím připojovací řetězec centra IoT, který používá moje řešení?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Zkontrolujte data odeslaná jedním ze zařízení vydavatele:

* vydavatel.beijing.corp.contoso
* vydavatel.capetown.corp.contoso
* vydavatel.mumbai.corp.contoso
* vydavatel.munich0.corp.contoso
* vydavatel.rio.corp.contoso
* vydavatel.seattle.corp.contoso

Pokud nevidíte žádná data odeslaná do ioT hubu, pak je problém se simulací. Jako první krok analýzy byste měli analyzovat soubory protokolu součástí simulace. Podívejte se na [části Jak lze získat data protokolu z komponent simulace?](#how-can-i-get-log-data-from-the-simulation-components) Dále se pokuste zastavit a spustit simulaci a pokud stále nejsou odeslána žádná data, aktualizujte simulaci úplně. Podívejte se [na how do i update the simulation in the VM?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Jak povolím interaktivní mapu v řešení připojené továrny?

Chcete-li povolit interaktivní mapu v řešení připojené továrny, musíte mít účet Azure Maps.

Při nasazování z [www.azureiotsolutions.com](https://www.azureiotsolutions.com)přidá proces nasazení účet Azure Maps do skupiny prostředků, která obsahuje služby akcelerátoru řešení.

Když nasadíte pomocí skriptu `build.ps1` v úložišti GitHub připojené továrny, nastavte proměnnou `$env:MapApiQueryKey` prostředí v okně sestavení na klíč vašeho účtu Azure [Maps](../azure-maps/how-to-manage-account-keys.md). Interaktivní mapa je pak automaticky povolena.

Můžete také přidat klíč účtu Azure Maps do akcelerátoru řešení po nasazení. Přejděte na portál Azure a získejte přístup k prostředku služby App Service v nasazení připojené továrny. Přejděte do **nastavení aplikace**, kde najdete oddíl **Nastavení aplikace**. Nastavte **mapapiquerykey** na [klíč svého účtu Azure Maps](../azure-maps/how-to-manage-account-keys.md). Uložte nastavení a přejděte na **Přehled** a restartujte službu App Service.

### <a name="how-do-i-create-an-azure-maps-account"></a>Jak si vytvořím účet Azure Maps?

Přečtěte si, [jak spravovat svůj účet a klíče Azure Maps](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Jak získat klíč účtu Azure Maps

Přečtěte si, [jak spravovat svůj účet a klíče Azure Maps](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Jak povolit interaktivní mapu při místním ladění?

Chcete-li povolit interaktivní mapu při místním ladění, nastavte hodnotu `MapApiQueryKey` nastavení `local.user.config` `<yourdeploymentname>.user.config` v souborech a v kořenovém adresáři nasazení na hodnotu **querykey,** kterou jste dříve zkopírovali.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Jak se na domovské stránce řídicího panelu použije jiný obrázek?

Chcete-li změnit statický obrázek zobrazený io domovskou stránku řídicího panelu, nahraďte obrázek `WebApp\Content\img\world.jpg`. Potom znovu a znovu nasaďte webapp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Jak se používají zařízení bez OPC UA s připojenou továrnou?

Odesílání telemetrických dat ze zařízení, která nejsou OPC UA, do připojené továrny:

1. [Nakonfigurujte novou stanici v topologii připojené továrny](iot-accelerators-connected-factory-configure.md) v souboru. `ContosoTopologyDescription.json`

1. Ingestujte telemetrická data ve formátu JSON kompatibilním s připojenou továrnou:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. Formát `<timestamp>` je:`2017-12-08T19:24:51.886753Z`

1. Restartujte službu Connected Factory App Service.

### <a name="next-steps"></a>Další kroky

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Přehled akcelerátorů řešení prediktivní údržby](iot-accelerators-predictive-overview.md)
* [Nasazení akcelerátoru řešení připojené továrny](quickstart-connected-factory-deploy.md)
* [Zabezpečení IoT od počátku](/azure/iot-fundamentals/iot-security-ground-up)
