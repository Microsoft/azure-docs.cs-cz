---
title: Připojen objekt pro vytváření řešení časté otázky – Azure | Microsoft Docs
description: Nejčastější dotazy pro akcelerátoru řešení připojen objekt pro vytváření
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 4ed0cd413480e717e686f7e52123102e1a838f19
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Nejčastější dotazy pro připojení objektu pro vytváření řešení akcelerátoru

Viz také Obecné [– nejčastější dotazy](iot-accelerators-faq.md) pro akcelerátorů řešení IoT.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Kde najdu zdrojového kódu pro řešení akcelerátoru

Zdrojový kód je uložen v úložišti GitHub následující:

* [Připojené objekt pro vytváření řešení akcelerátoru](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Co je OPC UA?

OPC Unified architektura (uživatelský Agent), vydané v 2008, je nezávislý na platformě, orientované na služby interoperabilita standardní. OPC UA používají různé průmyslových systémů a zařízení, jako jsou odvětví počítačů, plc a snímače. OPC UA funkce OPC Classic specifikace integruje do jednoho rozšiřitelná architektura integrované zabezpečení. Je standard, který doprovází OPC Foundation. [OPC Foundation](http://opcfoundation.org/) je pro neziskové organizace s víc než 440 členy. Cílem organizace je používat OPC specifikace usnadňuje interoperabilitu více dodavatele, více platformami, zabezpečený a spolehlivý prostřednictvím:

* Infrastruktura
* Specifikace
* Technologie
* Procesy

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Proč Microsoft zvolit OPC UA pro akcelerátor připojen objekt pro vytváření řešení?

Microsoft zvolili OPC UA, protože je otevřený, bez chráněné, platformu nezávislé, oborových rozpoznána a osvědčené standardem. Je požadavek na Industrie 4.0 (RAMI4.0) referenční Architektura řešení zajistit interoperabilitu mezi širokou škálu výrobní procesy a vybavení. Microsoft setkává vyžádání z jeho zákazníci vytvářet řešení Industrie 4.0. Podpora OPC UA pomáhá snížit bariéry pro zákazníky, abyste dosáhli svých cílů a poskytuje okamžité obchodní hodnotu k nim.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Jak přidat veřejnou IP adresu pro simulaci virtuálního počítače?

Máte dvě možnosti, které chcete přidat IP adresu:

* Pomocí skriptu prostředí PowerShell `Simulation/Factory/Add-SimulationPublicIp.ps1` v [úložiště](https://github.com/Azure/azure-iot-connected-factory). Název nasazení předat jako parametr. Pro místní nasazení použijte `<your username>ConnFactoryLocal`. Skript vypíše adresu IP virtuálního počítače.

* Na portálu Azure vyhledejte skupinu prostředků vašeho nasazení. S výjimkou místní nasazení skupiny prostředků má název, který jste zadali jako řešení nebo název nasazení. Pro místní nasazení pomocí skriptu buildu, název skupiny prostředků je `<your username>ConnFactoryLocal`. Nyní přidejte nový **veřejnou IP adresu** prostředků do skupiny prostředků.

> [!NOTE]
> V obou případech zajistěte instalaci nejnovějších oprav podle pokynů [Ubuntu webu](https://wiki.ubuntu.com/Security/Upgrades). Aktuálnost instalace pro tak dlouho, dokud virtuální počítač je přístupný prostřednictvím veřejnou IP adresu.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Odebrání veřejnou IP adresu pro simulaci virtuálních počítačů

Máte dvě možnosti pro IP adresu odeberte:

* Použití skriptu prostředí PowerShell Simulation/Factory/Remove-SimulationPublicIp.ps1 z [úložiště](https://github.com/Azure/azure-iot-connected-factory). Název nasazení předat jako parametr. Pro místní nasazení použijte `<your username>ConnFactoryLocal`. Skript vypíše adresu IP virtuálního počítače.

* Na portálu Azure vyhledejte skupinu prostředků vašeho nasazení. S výjimkou místní nasazení skupiny prostředků má název, který jste zadali jako řešení nebo název nasazení. Pro místní nasazení pomocí skriptu buildu, název skupiny prostředků je `<your username>ConnFactoryLocal`. Nyní odebrat **veřejnou IP adresu** prostředků ze skupiny prostředků.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Jak se mohu přihlásit k simulaci virtuálního počítače?

Přihlášení k simulaci virtuálního počítače je podporována, pouze pokud jste nasadili řešení pomocí skriptu prostředí PowerShell `build.ps1` v [úložiště](https://github.com/Azure/azure-iot-connected-factory).

Pokud jste nasadili řešení od www.azureiotsuite.com, nemůžete se přihlásit k virtuálnímu počítači. Nemůžete se přihlásit, protože je náhodně vygenerované heslo a nelze ho obnovit.

1. Přidejte veřejnou IP adresu do virtuálního počítače. V tématu [jak přidám veřejnou IP adresu pro simulaci virtuálního počítače?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Vytvořte relace SSH do virtuálního počítače pomocí IP adresy virtuálního počítače.
1. Je uživatelské jméno používané k: `docker`.
1. K použití hesla, závisí na verzi, kterou jste použili k nasazení:
    * Pro řešení nasazuje pomocí skriptu build.ps1 před 1. června 2017, je heslo: `Passw0rd`.
    * Řešení nasazuje pomocí skriptu build.ps1 po 1. června 2017, můžete najít v heslo `<name of your deployment>.config.user` souboru. Heslo je uloženo v **VmAdminPassword** nastavení. Heslo je generována náhodně v době nasazení nezadáte pomocí `build.ps1` skript parametr `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Jak zastavení a spuštění všech procesů docker v simulaci virtuálního počítače?

1. Přihlaste se k simulaci virtuálních počítačů. V tématu [jak se přihlásím k simulaci virtuálního počítače?](#how-do-i-sign-in-to-the-simulation-vm)
1. Chcete-li zkontrolovat, které kontejnery jsou aktivní, spusťte: `docker ps`.
1. Chcete-li ukončit všechny kontejnery simulace, spusťte: `./stopsimulation`.
1. Chcete-li spustit všechny kontejnery simulace:
    * Export proměnnou prostředí s názvem **IOTHUB_CONNECTIONSTRING**. Použijte hodnotu **IotHubOwnerConnectionString** nastavení v `<name of your deployment>.config.user` souboru. Příklad:

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Spusťte `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Jak aktualizovat simulace ve virtuálním počítači?

Pokud jste udělali změny simulace, můžete použít skript prostředí PowerShell `build.ps1` v [úložiště](https://github.com/Azure/azure-iot-connected-factory) pomocí `updatedimulation` příkaz. Tento skript vytvoří všechny součásti simulace, zastaví simulace ve virtuálním počítači, odešle, nainstaluje a spustí je.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Jak zjistím připojovací řetězec služby IoT hub používá mém řešení?

Pokud jste nasadili s řešením `build.ps1` skript v [úložiště](https://github.com/Azure/azure-iot-connected-factory), připojovací řetězec je hodnota **IotHubOwnerConnectionString** v `<name of your deployment>.config.user` souboru.

Můžete také získat připojovací řetězec pomocí portálu Azure. V rámci služby IoT Hub prostředku ve skupině prostředků vašeho nasazení vyhledejte nastavení připojovacího řetězce.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Zařízení IoT Hub, která používá simulace připojen objekt pro vytváření?

Simulace vlastní zaregistruje následující zařízení:

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* Publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Pomocí [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) nebo [IoT rozšíření pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) nástroj, můžete zkontrolovat zařízení, která jsou zaregistrovaná v centru IoT používá vaše řešení. Použití Průzkumníka zařízení, budete potřebovat připojovací řetězec pro službu IoT hub ve vašem nasazení. Použití rozšíření IoT pro Azure CLI 2.0, je třeba název vaší služby IoT Hub.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Načtení dat protokolu z komponenty simulace?

Všechny součásti v simulaci přihlášení informace do souborů protokolu. Tyto soubory můžete najít ve virtuálním počítači ve složce `home/docker/Logs`. Pokud chcete načíst protokoly, můžete použít skript prostředí PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` v [úložiště](https://github.com/Azure/azure-iot-connected-factory).

Tento skript, musí se přihlásit k virtuálnímu počítači. Musíte zadat přihlašovací údaje pro přihlášení. V tématu [jak se přihlásím k simulaci virtuální počítač?](#how-do-i-sign-in-to-the-simulation-vm) najít přihlašovací údaje.

Skript přidá nebo odebere veřejnou IP adresu pro virtuální počítač, pokud ho ještě nemá a odstraní ji. Skript vloží všech souborů protokolů v rámci archivu a stáhne archivu na stanici vývoje.

Případně Přihlaste se k virtuálnímu počítači pomocí protokolu SSH a zkontrolujte soubory protokolu v době běhu.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Jak můžete zkontrolovat, pokud simulaci odesílá data do cloudu?

S [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) nebo [iothub-explorer](https://github.com/azure/iothub-explorer) nástroj, si můžete prohlédnout data odeslaná do služby IoT Hub z některých zařízení. Tyto nástroje používat, musíte znát připojovací řetězec pro službu IoT hub ve vašem nasazení. V tématu [Jak zjistím připojovací řetězec služby IoT hub používá mém řešení?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Zkontrolujte data odeslaná vydavatele zařízení:

* publisher.beijing.corp.contoso
* Publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Pokud se žádná data, odeslané do služby IoT Hub, nastane problém s simulace. Jako první krok analysis byste měli provést analýzu souborů protokolu součásti simulace. V tématu [načtení dat protokolu z komponenty simulace?](#how-can-i-get-log-data-from-the-simulation-components) V dalším kroku pokusí zastavit a spustit simulaci a v případě stále nejsou žádná data odesílá, aktualizujte simulaci úplně. V tématu [jak aktualizovat simulace ve virtuálním počítači?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Jak povolit interaktivní mapu v mém řešení připojen objekt pro vytváření?

Pokud chcete povolit interaktivní mapu ve vašem řešení připojen objekt pro vytváření, musí mít existující rozhraní API map Bing pro plán Enterprise.

Při nasazení z [www.azureiotsuite.com](http://www.azureiotsuite.com), proces nasazení ověří, že vaše předplatné má povoleno rozhraní API map Bing pro plán Enterprise a interaktivní mapu automaticky nasadí do připojen objekt pro vytváření. Pokud tomu tak není, můžete stále povolit interaktivní mapu ve vašem nasazení následujícím způsobem:

Při nasazení pomocí `build.ps1` skript v Githubu objekt pro vytváření připojení úložiště a mít rozhraní API map Bing pro plán Enterprise, nastavte proměnnou prostředí `$env:MapApiQueryKey` v okně sestavení na klíč dotazu plánu. Interaktivní mapu se pak automaticky povolí.

Pokud nemáte rozhraní API map Bing pro plán Enterprise, nasaďte řešení připojen objekt pro vytváření z [www.azureiotsuite.com](http://www.azureiotsuite.com) nebo pomocí `build.ps1` skriptu. Pak přidejte rozhraní API map Bing pro plánu podnikového do vašeho předplatného, jak je popsáno v [vytvoření rozhraní API map Bing pro účet organizace?](#how-do-i-create-a-bing-maps-api-for-enterprise-account). Vyhledání klíče dotazu tohoto účtu, jak je popsáno v [získání rozhraní API map Bing pro Enterprise QueryKey](#how-to-obtain-your-bing-maps-api-for-enterprise-querykey) a uložte tento klíč. Přejděte na portál Azure a přístup k prostředku služby App Service ve vašem nasazení připojen objekt pro vytváření. Přejděte na **nastavení aplikace**, kde najít oddíl **nastavení aplikace**. Nastavte **MapApiQueryKey** na klíč dotazu, který jste získali. Uložte nastavení a potom přejděte na **přehled** a restartujte službu aplikace.

### <a name="how-do-i-create-a-bing-maps-api-for-enterprise-account"></a>Vytvoření rozhraní API map Bing pro účet Enterprise

Můžete získat bezplatný *vnitřní transakce úroveň 1 mapy Bing pro podniky* plán. Ale pouze přidáním dva z těchto plánů k předplatnému Azure. Pokud nemáte rozhraní API map Bing pro účet Enterprise, vytvořit na portálu Azure kliknutím **+ vytvořit prostředek**. Poté vyhledejte **rozhraní API map Bing pro podniky** a postupujte podle výzev a vytvořte ho.

![Klíč Bing](./media/iot-accelerators-faq-cf/bing.png)

### <a name="how-to-obtain-your-bing-maps-api-for-enterprise-querykey"></a>Získání rozhraní API map Bing pro Enterprise QueryKey

Po vytvoření rozhraní API map Bing pro plánu podnikového Přidání mapy Bing pro prostředek Enterprise do skupiny prostředků vašeho připojen objekt pro vytváření řešení na portálu Azure.

1. Na portálu Azure přejděte do skupiny prostředků, která obsahuje vaše rozhraní API map Bing pro plán Enterprise.

1. Klikněte na tlačítko **všechna nastavení**, pak **Správa klíčů**.

1. Existují dva klíče: **MasterKey** a **QueryKey**. Kopírování **QueryKey** hodnotu.

1. Klíč zachyceny pomocí `build.ps1` skriptu, nastavte proměnnou prostředí `$env:MapApiQueryKey` ve vašem prostředí PowerShell a **QueryKey** plánu. Sestavení skript pak automaticky přidá hodnotu nastavení služby App Service.

1. Spusťte místní nebo cloudové nasazení pomocí `build.ps1` skriptu.

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Jak povolit interaktivní mapu při ladění místně?

Chcete-li povolit interaktivní mapu při ladění místně, nastavte hodnotu nastavení `MapApiQueryKey` v souborech `local.user.config` a `<yourdeploymentname>.user.config` v kořenu vašeho nasazení na hodnotu **QueryKey** jste zkopírovali, dříve.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Jak použít jinou bitovou kopii na domovské stránce Můj řídicí panel?

Chcete-li změnit statický obrázek ukazuje vstupně-výstupní operace domovské stránky řídicího panelu, nahraďte image `WebApp\Content\img\world.jpg`. Pak znovu sestavili a nasadili webové aplikace.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Jak používat jiný OPC UA zařízení s připojen objekt pro vytváření?

Chcete-li Neodesílat telemetrická data z jiných OPC UA zařízení k vytváření připojení:

1. [Konfigurace nové stanice v topologii připojen objekt pro vytváření](iot-accelerators-connected-factory-configure.md) v `ContosoTopologyDescription.json` souboru.

1. Ingestování telemetrická data ve formátu JSON kompatibilní Factory připojení:

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

1. Formát `<timestamp>` je: `2017-12-08T19:24:51.886753Z`

1. Restartujte službu aplikace připojené objekt pro vytváření.

### <a name="next-steps"></a>Další postup

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Přehled akcelerátorů řešení prediktivní údržby](../iot-suite/iot-suite-predictive-overview.md)
* [Připojené přehled akcelerátoru objekt pro vytváření řešení](iot-accelerators-connected-factory-overview.md)
* [Zabezpečení IoT od základů](../iot-suite/securing-iot-ground-up.md)
