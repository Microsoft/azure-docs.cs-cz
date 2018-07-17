---
title: Připojená řešení továrny – nejčastější dotazy – Azure | Dokumentace Microsoftu
description: Nejčastější dotazy pro akcelerátor řešení připojená továrna
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 6d1002fd5131ca97f7bc1b9d081ae1471191a6cf
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076018"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Nejčastější dotazy pro akcelerátor řešení připojená továrna

Viz také Obecné [nejčastější dotazy k](iot-accelerators-faq.md) pro akcelerátory řešení IoT.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Kde lze najít zdrojový kód pro akcelerátor řešení?

Zdrojový kód je uložen v následujícím úložišti Githubu:

* [Akcelerátor řešení propojené továrny](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Co je OPC UA?

OPC Unified architektury (UA), vydáno 2008, je nezávislá na platformě, orientovaných na služby interoperability standard. OPC UA používají různé průmyslové systémů a zařízení, jako jsou odvětví počítače, plc a senzorů. OPC UA funkce OPC Classic specifikace integruje do jednoho rozšiřitelné rozhraní s integrovaným zabezpečením. To je standard, který řídíte nadací OPC Foundation. [OPC Foundation](http://opcfoundation.org/) je pro neziskové organizace s více než 440 členy. Cílem organizace je pomocí OPC specifikací pro usnadnění více dodavatele, více platforem, bezpečné a spolehlivé vzájemná funkční spolupráce prostřednictvím:

* Infrastruktura
* Specifikace
* Technologie
* Procesy

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Proč Microsoft zvolit OPC UA pro akcelerátor řešení připojená továrna?

Microsoft zvolili OPC UA, protože je standard otevřít a jiných nechráněný, platformě nezávislá, rozpoznat odvětví a osvědčené. Je to požadavek pro přechod 4.0 (RAMI4.0) referenční architektury řešení zajistit interoperabilitu mezi širokou škálu výrobní procesy a vybavení. Microsoft vnímá vyžádání od svých zákazníků a vytvářet tak řešení přechod 4.0. Podpora OPC UA pomáhá snížit odbourejte překážky bránící zákazníkům dosáhli svých cílů a poskytuje okamžitou přidanou hodnotu na ně.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Jak přidat veřejnou IP adresu pro simulaci virtuálního počítače?

Máte dvě možnosti, jak přidat IP adresu:

* Pomocí skriptu prostředí PowerShell `Simulation/Factory/Add-SimulationPublicIp.ps1` v [úložiště](https://github.com/Azure/azure-iot-connected-factory). Název vašeho nasazení se předá jako parametr. Pro místní nasazení, použijte `<your username>ConnFactoryLocal`. Skript vytiskne IP adresu virtuálního počítače.

* Na webu Azure Portal vyhledejte skupinu prostředků vašeho nasazení. Kromě místního nasazení skupina prostředků má název, který jste zadali jako řešení nebo název nasazení. Pro místní nasazení pomocí skriptu sestavení, je název skupiny prostředků `<your username>ConnFactoryLocal`. Nyní přidejte nový **veřejnou IP adresu** prostředků do skupiny prostředků.

> [!NOTE]
> V obou případech zajistěte instalaci nejnovějších oprav podle pokynů v [Ubuntu webu](https://wiki.ubuntu.com/Security/Upgrades). Instalace udržujte v aktuálním stavu pro váš virtuální počítač je přístupný prostřednictvím veřejné IP adresy.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Jak odstranit veřejnou IP adresu pro simulaci virtuálního počítače?

Máte dvě možnosti, jak odebrat IP adresa:

* Použití skriptů prostředí PowerShell Simulation/Factory/Remove-SimulationPublicIp.ps1 z [úložiště](https://github.com/Azure/azure-iot-connected-factory). Název vašeho nasazení se předá jako parametr. Pro místní nasazení, použijte `<your username>ConnFactoryLocal`. Skript vytiskne IP adresu virtuálního počítače.

* Na webu Azure Portal vyhledejte skupinu prostředků vašeho nasazení. Kromě místního nasazení skupina prostředků má název, který jste zadali jako řešení nebo název nasazení. Pro místní nasazení pomocí skriptu sestavení, je název skupiny prostředků `<your username>ConnFactoryLocal`. Teď odebrat **veřejnou IP adresu** prostředků ze skupiny prostředků.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Jak se můžu přihlásit k simulaci virtuálního počítače?

Přihlášení k simulaci virtuálního počítače je podporována, pouze pokud jste nasadili řešení pomocí skriptu prostředí PowerShell `build.ps1` v [úložiště](https://github.com/Azure/azure-iot-connected-factory).

Pokud jste nasadili řešení od www.azureiotsolutions.com, nemůžete se přihlásit k virtuálnímu počítači. Nemůžete se přihlásit, protože je náhodně vygenerované heslo a nelze ho obnovit.

1. Přidejte veřejnou IP adresu k virtuálnímu počítači. Zobrazit [jak mohu přidat veřejnou IP adresu pro simulaci virtuálního počítače?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Vytvořte relaci SSH k virtuálnímu počítači pomocí IP adresy virtuálního počítače.
1. Je uživatelské jméno pro použití: `docker`.
1. Heslo pro použití závisí na verzi, kterou jste použili k nasazení:
    * Pro řešení nasazená pomocí skriptu build.ps1 před 1. června 2017, je heslo: `Passw0rd`.
    * Pro řešení nasazená pomocí skriptu build.ps1 po 1. června 2017, můžete najít v heslo `<name of your deployment>.config.user` souboru. Heslo je uloženo v **VmAdminPassword** nastavení. Heslo se vygeneruje náhodně v době nasazení pomocí neurčíte `build.ps1` parametrů skriptu `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Jak zastavení a spuštění všech procesů dockeru v simulaci virtuálního počítače?

1. Přihlaste se k simulaci virtuálního počítače. Zobrazit [jak se přihlásím k simulaci virtuálního počítače?](#how-do-i-sign-in-to-the-simulation-vm)
1. Chcete-li zkontrolovat, které kontejnery jsou aktivní, spusťte: `docker ps`.
1. Chcete-li ukončit všechny kontejnery simulace, spusťte: `./stopsimulation`.
1. Chcete-li spustit všechny kontejnery simulace:
    * Exportovat do proměnné prostředí s názvem **IOTHUB_CONNECTIONSTRING**. Použijte hodnotu **IotHubOwnerConnectionString** nastavení `<name of your deployment>.config.user` souboru. Příklad:

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Spusťte `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Jak můžu aktualizovat simulace ve virtuálním počítači?

Pokud jste provedli všechny změny na simulaci, můžete použít skript prostředí PowerShell `build.ps1` v [úložiště](https://github.com/Azure/azure-iot-connected-factory) pomocí `updatedimulation` příkazu. Tento skript vytvoří všechny simulované komponenty, zastaví simulace ve virtuálním počítači, nahraje, nainstaluje a spustí je.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Jak můžu zjistit připojovací řetězec služby IoT hub používá Moje řešení?

Pokud jste nasadili s řešením `build.ps1` skript v [úložiště](https://github.com/Azure/azure-iot-connected-factory), připojovací řetězec je hodnota **IotHubOwnerConnectionString** v `<name of your deployment>.config.user` souboru.

Můžete také najít připojovací řetězec pomocí webu Azure portal. Ve službě IoT Hub prostředků ve skupině prostředků vašeho nasazení vyhledejte nastavení připojovacího řetězce.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Zařízení IoT Hub, která používá simulace připojená továrna?

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

Použití [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) nebo [rozšíření IoT pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) nástroj, můžete zkontrolovat zařízení, která jsou zaregistrovaná v centru IoT vaše řešení používá. Pokud chcete použít Průzkumník zařízení, potřebujete připojovací řetězec pro službu IoT hub ve vašem nasazení. Použití rozšíření IoT pro Azure CLI 2.0, potřebujete název vašeho centra IoT.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Jak lze získat data protokolu z simulované komponenty?

Všechny součásti v simulaci přihlášení informace do souborů protokolu. Tyto soubory lze najít ve virtuálním počítači ve složce `home/docker/Logs`. Pokud chcete načíst protokoly, můžete použít skript prostředí PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` v [úložiště](https://github.com/Azure/azure-iot-connected-factory).

Tento skript, musíte se přihlásit k virtuálnímu počítači. Budete muset zadat přihlašovací údaje pro přihlášení. Zobrazit [jak se přihlásím k simulaci virtuálního počítače?](#how-do-i-sign-in-to-the-simulation-vm) najít přihlašovací údaje.

Skript přidá/odebere veřejnou IP adresu pro virtuální počítač, pokud ještě nemá a odstraní ji. Skript umístí všechny soubory protokolu v archivu a stáhne archivu na stanici vývoje.

Můžete také přihlásit k virtuálnímu počítači přes SSH a kontrola souborů protokolu v době běhu.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Jak můžu zkontrolovat, pokud simulace odesílá data do cloudu?

S [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) nebo [iothub-explorer](https://github.com/azure/iothub-explorer) nástroj, můžete si prohlédnout data odeslaná do služby IoT Hub z některých zařízení. Tyto nástroje používat, musíte znát připojovací řetězec služby IoT hub ve vašem nasazení. Zobrazit [Jak zjistím připojovací řetězec služby IoT hub používá Moje řešení?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Kontrolovat data zaslaná z jednoho zařízení vydavatele:

* publisher.beijing.corp.contoso
* Publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Pokud se nezobrazují žádná data odeslaných do služby IoT Hub, je problém s simulace. Jako první krok analýzy je vhodné analyzovat soubory protokolů simulované komponenty. Zobrazit [jak lze získat data protokolu z simulované komponenty?](#how-can-i-get-log-data-from-the-simulation-components) V dalším kroku akci k zastavení a spuštění simulace a pokud nebude probíhat žádná data odeslat, aktualizovat simulace úplně. Zobrazit [jak aktualizovat simulace ve virtuálním počítači?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Jak povolit interaktivní mapu v mém řešení připojená továrna

Povolit interaktivní mapu ve vašem řešení připojená továrna, musíte mít existující API map Bing pro podnikový plán.

Při nasazení z [www.azureiotsolutions.com](http://www.azureiotsolutions.com), procesu nasazení ověřuje, že vaše předplatné má povoleno API map Bing pro podnikový plán a automaticky nasadí do připojená továrna interaktivní mapu. Pokud to není tento případ, můžete stále povolit interaktivní mapu ve vašem nasazení následujícím způsobem:

Při nasazení pomocí `build.ps1` skript v Githubu připojené továrny úložiště a můžete mít rozhraní API map Bing pro plán Enterprise, nastavte proměnnou prostředí `$env:MapApiQueryKey` v okně sestavení na klíč dotazu vašeho plánu. Interaktivní mapu se pak povolí automaticky.

Pokud nemáte k dispozici rozhraní API map Bing pro plán Enterprise, nasaďte řešení připojené továrny ze [www.azureiotsolutions.com](http://www.azureiotsolutions.com) nebo pomocí `build.ps1` skriptu. Pak přidejte rozhraní API map Bing pro podnikový plán do vašeho předplatného, jak je vysvětleno v [jak vytvořit rozhraní API map Bing pro účet organizace?](#how-do-i-create-a-bing-maps-api-for-enterprise-account). Vyhledání klíč dotazu tohoto účtu, jak je vysvětleno v [získání rozhraní API map Bing pro Enterprise QueryKey](#how-to-obtain-your-bing-maps-api-for-enterprise-querykey) a uložte tento klíč. Přejděte na web Azure Portal a přístup k prostředku služby App Service v nasazení připojené továrny. Přejděte do **nastavení aplikace**, kde najít oddíl **nastavení aplikace**. Nastavte **MapApiQueryKey** na klíč dotazu, který jste získali. Uložte nastavení a potom přejděte na **přehled** a restartujte služby App Service.

### <a name="how-do-i-create-a-bing-maps-api-for-enterprise-account"></a>Jak vytvořit rozhraní API map Bing pro účet organizace

Můžete získat bezplatné *vnitřní transakce úrovně 1 mapy Bing pro podniky* plánu. Ale můžete jenom přidat dvě z těchto plánů s předplatným Azure. Pokud nemáte k dispozici rozhraní API map Bing pro účet organizace, vytvořit na webu Azure Portal kliknutím **+ vytvořit prostředek**. Vyhledejte **rozhraní API map Bing pro podniky** a postupujte podle výzev a vytvořte ho.

![Klíč Bing](./media/iot-accelerators-faq-cf/bing.png)

### <a name="how-to-obtain-your-bing-maps-api-for-enterprise-querykey"></a>Postup získání rozhraní API map Bing pro Enterprise QueryKey

Po vytvoření rozhraní API map Bing pro plán Enterprise, přidejte do skupiny prostředků vaše řešení připojená továrna na webu Azure Portal mapy Bing pro podnikových zdrojů.

1. Na webu Azure Portal přejděte do skupiny prostředků, který obsahuje rozhraní API map Bing pro podnikový plán.

1. Klikněte na tlačítko **všechna nastavení**, pak **Správa klíčů**.

1. Existují dva klíče: **MasterKey** a **QueryKey**. Kopírovat **QueryKey** hodnotu.

1. Klíč neexistoval `build.ps1` skriptu, nastavte proměnnou prostředí `$env:MapApiQueryKey` ve vašem prostředí PowerShell a **QueryKey** vašeho plánu. Skript sestavení automaticky přidá hodnotu nastavení služby App Service.

1. Spusťte místní nebo cloudové nasazení s použitím `build.ps1` skriptu.

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Jak povolit interaktivní mapu při místním ladění?

Povolit interaktivní mapu při místním ladění, nastavte hodnotu nastavení `MapApiQueryKey` v souborech `local.user.config` a `<yourdeploymentname>.user.config` v kořenové složce nasazení tak, aby hodnota **QueryKey** jste zkopírovali, dříve.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Jak použít jinou image na domovské stránce Můj řídicí panel?

Chcete-li změnit statický obrázek ukazuje vstupně-výstupních operací domovské stránce řídicího panelu, nahraďte image `WebApp\Content\img\world.jpg`. Potom znovu sestavili a webové aplikace.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Použití zařízení bez OPC UA s připojená továrna

Neodesílat telemetrická data z jiných OPC UA zařízení pro propojenou továrnu:

1. [Konfigurace nové stanice topologie pro propojenou továrnu](iot-accelerators-connected-factory-configure.md) v `ContosoTopologyDescription.json` souboru.

1. Ingestování telemetrických dat ve formátu JSON kompatibilní propojenou továrnu:

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

1. Restartujte službu aplikace propojené továrny.

### <a name="next-steps"></a>Další postup

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Přehled akcelerátorů řešení prediktivní údržby](iot-accelerators-predictive-overview.md)
* [Nasazení akcelerátoru řešení připojená továrna](quickstart-connected-factory-deploy.md)
* [Zabezpečení IoT od počátku](/azure/iot-fundamentals/iot-security-ground-up)
