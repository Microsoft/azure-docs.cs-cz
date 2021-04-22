---
title: Nejčastější dotazy k řešení propojené továrny – Azure | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy k akcelerátoru řešení propojené továrny. Obsahuje odkazy na úložiště GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 038403743caf13087655066f4acbec4dcee598c7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874202"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Nejčastější dotazy k akcelerátoru řešení propojené továrny

Viz také obecné [Nejčastější dotazy](iot-accelerators-faq.md) pro akcelerátory řešení IoT.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Kde můžu najít zdrojový kód pro akcelerátor řešení?

Zdrojový kód je uložený v následujícím úložišti GitHub:

* [Akcelerátor řešení propojené továrny](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Co je OPC UA?

OPC Unified Architecture (UA), která je vydaná v 2008, je standardně závislá na platformě orientované na platformu. OPC UA používají různé průmyslové systémy a zařízení, jako jsou oborové počítače, PLCs a senzory. OPC UA integruje funkce specifikací OPC Classic do jedné rozšiřitelné architektury s integrovaným zabezpečením. Je to standard, který je založený na OPC Foundation. [OPC Foundation](https://opcfoundation.org/) je nezisková organizace s více než 440 členy. Cílem organizace je použití specifikací OPC k usnadnění více dodavatelů, zabezpečené a spolehlivé interoperability prostřednictvím:

* Infrastruktura
* Specifikace
* Technologie
* Procesy

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Proč Microsoft pro akcelerátor řešení propojené továrny zvolí OPC UA?

Společnost Microsoft zvolila OPC UA, protože se jedná o otevřené, neproprietární, nevlastní platformu, která je nezávislá na platformě, která je známá a prověřená Standard. Je potřeba, aby řešení referenční architektury průmysl 4,0 (RAMI 4.0) zajišťovaly interoperabilitu mezi širokou škálou výrobních procesů a vybavení. Microsoft uvidí od svých zákazníků poptávku za účelem sestavení řešení průmysl 4,0. Podpora OPC UA pomáhá snížit bariéru pro zákazníky, aby dosáhli svých cílů a poskytovala jim okamžitou obchodní hodnotu.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Návody přidat veřejnou IP adresu na simulaci virtuálního počítače?

IP adresu můžete přidat dvěma způsoby:

* Použijte skript prostředí PowerShell `Simulation/Factory/Add-SimulationPublicIp.ps1` v [úložišti](https://github.com/Azure/azure-iot-connected-factory). Jako parametr předejte název nasazení. Pro místní nasazení použijte `<your username>ConnFactoryLocal` . Skript vytiskne IP adresu virtuálního počítače.

* V Azure Portal vyhledejte skupinu prostředků vašeho nasazení. S výjimkou místního nasazení má skupina prostředků název, který jste zadali jako řešení nebo název nasazení. Pro místní nasazení pomocí skriptu sestavení je název skupiny prostředků `<your username>ConnFactoryLocal` . Nyní do skupiny prostředků přidejte nový prostředek **veřejné IP adresy** .

> [!NOTE]
> V obou případech se ujistěte, že nainstalujete nejnovější opravy podle pokynů na [webu Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Pokud je váš virtuální počítač přístupný prostřednictvím veřejné IP adresy, udržujte si instalaci v aktuálním stavu.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Návody odebrat veřejnou IP adresu na virtuálním počítači simulace?

IP adresu můžete odebrat dvěma způsoby:

* Použijte PowerShellový simulaci/tovární nebo Remove-SimulationPublicIp.ps1 [úložiště](https://github.com/Azure/azure-iot-connected-factory). Jako parametr předejte název nasazení. Pro místní nasazení použijte `<your username>ConnFactoryLocal` . Skript vytiskne IP adresu virtuálního počítače.

* V Azure Portal vyhledejte skupinu prostředků vašeho nasazení. S výjimkou místního nasazení má skupina prostředků název, který jste zadali jako řešení nebo název nasazení. Pro místní nasazení pomocí skriptu sestavení je název skupiny prostředků `<your username>ConnFactoryLocal` . Nyní ze skupiny prostředků odeberte prostředek **veřejné IP adresy** .

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Návody se přihlašovat k simulaci virtuálního počítače?

Přihlášení k virtuálnímu počítači simulace se podporuje jenom v případě, že jste řešení nasadili pomocí skriptu PowerShellu `build.ps1` v [úložišti](https://github.com/Azure/azure-iot-connected-factory).

Pokud jste řešení nasadili z www.azureiotsolutions.com, nemůžete se přihlásit k virtuálnímu počítači. Nemůžete se přihlásit, protože heslo je vygenerované náhodně a nemůžete ho resetovat.

1. Přidejte veřejnou IP adresu k virtuálnímu počítači. Viz [návody přidání veřejné IP adresy na simulaci virtuálního počítače?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Vytvořte relaci SSH k VIRTUÁLNÍmu počítači pomocí IP adresy virtuálního počítače.
1. Uživatelské jméno, které se má použít, je: `docker` .
1. Heslo, které se má použít, závisí na verzi, kterou jste použili k nasazení:
    * Pro řešení nasazená pomocí skriptu build.ps1 do 1. června 2017 je heslo: `Passw0rd` .
    * V případě řešení nasazených pomocí skriptu build.ps1 po 1. června 2017 můžete v souboru najít heslo `<name of your deployment>.config.user` . Heslo je uloženo v nastavení **VmAdminPassword** . Heslo se vygeneruje náhodně v době nasazení, pokud ho neurčíte pomocí `build.ps1` parametru skriptu. `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Návody zastavit a spustit všechny procesy Docker v simulaci virtuálního počítače?

1. Přihlaste se k virtuálnímu počítači simulace. Přečtěte si téma [návody Přihlaste se k simulaci virtuálního počítače?](#how-do-i-sign-in-to-the-simulation-vm)
1. Chcete-li zjistit, které kontejnery jsou aktivní, spusťte příkaz: `docker ps` .
1. Chcete-li zastavit všechny kontejnery simulace, spusťte příkaz: `./stopsimulation` .
1. Chcete-li spustit všechny kontejnery simulace:
    * Exportujte proměnnou prostředí s názvem **IOTHUB_CONNECTIONSTRING**. Použijte hodnotu nastavení **IotHubOwnerConnectionString** v `<name of your deployment>.config.user` souboru. Například:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Spusťte `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Návody aktualizovat simulaci na virtuálním počítači?

Pokud jste provedli nějaké změny v simulaci, můžete použít skript prostředí PowerShell `build.ps1` v [úložišti](https://github.com/Azure/azure-iot-connected-factory) pomocí `updatedimulation` příkazu. Tento skript vytvoří všechny komponenty simulace, zastaví simulaci ve virtuálním počítači, nahraje, nainstaluje a spustí je.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Návody zjistit připojovací řetězec centra IoT, které používá moje řešení?

Pokud jste řešení nasadili pomocí `build.ps1` skriptu v [úložišti](https://github.com/Azure/azure-iot-connected-factory), připojovací řetězec je hodnota **IotHubOwnerConnectionString** v `<name of your deployment>.config.user` souboru.

Připojovací řetězec můžete také najít pomocí Azure Portal. V prostředku IoT Hub ve skupině prostředků vašeho nasazení vyhledejte nastavení připojovacího řetězce.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Které IoT Hub zařízení používá simulaci propojené továrny?

Simulace samo registruje následující zařízení:

* proxy. Peking. corp. contoso
* proxy. Capetown. corp. contoso
* proxy. Bombaj. corp. contoso
* proxy. munich0. corp. contoso
* proxy. Rio. corp. contoso
* proxy. Seattle. corp. contoso
* Publisher. Peking. corp. contoso
* Publisher. Capetown. corp. contoso
* Publisher. Bombaj. corp. contoso
* Publisher. munich0. corp. contoso
* Publisher. Rio. corp. contoso
* Publisher. Seattle. corp. contoso

Pomocí nástroje [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/) nebo [rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) můžete zjistit, která zařízení jsou zaregistrovaná ve službě IoT Hub, které vaše řešení používá. Chcete-li použít Průzkumníka zařízení, potřebujete připojovací řetězec pro Centrum IoT v nasazení. Pokud chcete používat rozšíření IoT pro Azure CLI, budete potřebovat název IoT Hub.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Jak mohu získat data protokolu z komponent simulace?

Všechny součásti v protokolu simulace informace v nástroji do souborů protokolu. Tyto soubory najdete ve složce na virtuálním počítači `home/docker/Logs` . K načtení protokolů můžete použít skript prostředí PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` v [úložišti](https://github.com/Azure/azure-iot-connected-factory).

Tento skript musí být přihlášený k virtuálnímu počítači. Možná budete muset zadat přihlašovací údaje pro přihlášení. Pokud chcete najít přihlašovací údaje, přečtěte si téma [návody přihlášení k virtuálnímu počítači pro simulaci?](#how-do-i-sign-in-to-the-simulation-vm)

Skript přidá nebo odebere veřejnou IP adresu virtuálního počítače, pokud ho ještě neobsahuje, a odebere ho. Skript umístí všechny soubory protokolu do archivu a stáhne archiv do pracovní stanice pro vývoj.

Případně se přihlaste k virtuálnímu počítači přes SSH a zkontrolujte soubory protokolu za běhu.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Jak se dá ověřit, jestli simulace odesílá data do cloudu?

Pomocí příkazu monitorovat rozšíření Azure [IoT Explorer](https://github.com/Azure/azure-iot-explorer) nebo [Azure IoT CLI – události](/cli/azure/iot/hub#az_iot_hub_monitor_events) můžete zkontrolovat data odesílaná do IoT Hub z určitých zařízení. Chcete-li použít tyto nástroje, musíte znát připojovací řetězec pro Centrum IoT v nasazení. Přečtěte si téma [návody zjištění připojovacího řetězce centra IoT, kterou používá moje řešení?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Zkontrolujte data odesílaná v jednom ze zařízení vydavatele:

* Publisher. Peking. corp. contoso
* Publisher. Capetown. corp. contoso
* Publisher. Bombaj. corp. contoso
* Publisher. munich0. corp. contoso
* Publisher. Rio. corp. contoso
* Publisher. Seattle. corp. contoso

Pokud se nezobrazí žádná data, která by mohla IoT Hub, dojde k problému s simulací. Jako první krok analýzy byste měli analyzovat soubory protokolů součástí simulace. Přečtěte si [, jak mohu získat data protokolu z komponent simulace?](#how-can-i-get-log-data-from-the-simulation-components) Potom se pokuste zastavit a spustit simulaci a pokud se ještě neodesílají žádná data, aktualizujte simulaci kompletně. Přečtěte si téma [návody aktualizace simulace ve virtuálním počítači?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Návody povolit interaktivní mapování v řešení moje připojení k výrobě?

Pokud chcete povolit interaktivní mapování v řešení propojené továrny, musíte mít účet Azure Maps.

Při nasazování z [www.azureiotsolutions.com](https://www.azureiotsolutions.com)proces nasazení přidá účet Azure Maps do skupiny prostředků, která obsahuje služby akcelerátoru řešení.

Při nasazení pomocí `build.ps1` skriptu v úložišti GitHub připojené továrny nastavte proměnnou prostředí `$env:MapApiQueryKey` v okně sestavení na [klíč vašeho účtu Azure Maps](../azure-maps/how-to-manage-account-keys.md). Interaktivní mapování se pak povolí automaticky.

Po nasazení můžete také přidat klíč účtu Azure Maps do akcelerátoru řešení. Přejděte do Azure Portal a přihlaste se k prostředku App Service ve vašem nasazení propojené továrny. Přejděte do **nastavení aplikace**, kde najdete část **nastavení aplikace**. Nastavte **MapApiQueryKey** na [klíč účtu Azure Maps](../azure-maps/how-to-manage-account-keys.md). Uložte nastavení a potom přejděte na **Přehled** a restartujte App Service.

### <a name="how-do-i-create-an-azure-maps-account"></a>Návody vytvořit Azure Maps účet?

Přečtěte si, [jak spravovat účet Azure Maps a klíče](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Jak získat klíč účtu Azure Maps

Přečtěte si, [jak spravovat účet Azure Maps a klíče](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Jak povolit interaktivní mapování při místním ladění?

Chcete-li povolit interaktivní mapování při místním ladění, nastavte hodnotu nastavení `MapApiQueryKey` v souborech `local.user.config` a `<yourdeploymentname>.user.config` v kořenu vašeho nasazení na hodnotu **QueryKey** , kterou jste zkopírovali dříve.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Návody použít na domovské stránce řídicího panelu jiný obrázek?

Chcete-li změnit statický obrázek v/v na domovské stránce řídicího panelu, nahraďte obrázek `WebApp\Content\img\world.jpg` . Pak znovu sestavte a znovu nasaďte rozhraní WebApp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Návody používat zařízení OPC UA s propojenou továrnou?

Odeslání dat telemetrie ze zařízení, která nejsou OPC UA, do propojené továrny:

1. V souboru [nakonfigurujte novou stanici v topologii připojené továrny](iot-accelerators-connected-factory-configure.md) `ContosoTopologyDescription.json` .

1. Ingestujte data telemetrie ve formátu JSON propojené továrny, který je kompatibilní:

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

1. Restartujte App Service propojené továrny.

### <a name="next-steps"></a>Další kroky

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Nasadit akcelerátor řešení propojené továrny](quickstart-connected-factory-deploy.md)
* [Zabezpečení IoT od počátku](../iot-fundamentals/iot-security-ground-up.md)