---
title: Vizualizace dat ve službě IoT Hub v reálném čase ve webové aplikaci
description: Webovou aplikaci můžete použít k vizualizaci dat o teplotě a vlhkosti shromažďovaných ze senzorů a odeslání do služby IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 4f2f0678b421ac6965b2848cc25564b4e95c7c6b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567043"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Vizualizace dat snímačů v reálném čase z Azure IoT Hub ve webové aplikaci

![Komplexní diagram](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

V tomto článku se dozvíte, jak vizualizovat data senzorů v reálném čase, která vaše centrum IoT obdrží, pomocí node.js webové aplikace spuštěné v místním počítači. Po místní instalaci webové aplikace můžete podle pokynů použít postup hostování webové aplikace v Azure App Service. Pokud se chcete pokusit vizualizovat data ve službě IoT Hub pomocí Power BI, přečtěte si téma [použití Power BI k vizualizaci dat snímačů v reálném čase z Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="prerequisites"></a>Požadavky

* Dokončete kurz [online simulátoru malin](iot-hub-raspberry-pi-web-simulator-get-started.md) . nebo v některém z kurzů zařízení. Například můžete přejít na [malinu Pi pomocí node.js](iot-hub-raspberry-pi-kit-node-get-started.md) nebo do jednoho z rychlých startů pro [odeslání telemetrie](quickstart-send-telemetry-dotnet.md) . Tyto články se týkají následujících požadavků:

  * Aktivní předplatné Azure
  * Centrum IoT v rámci vašeho předplatného
  * Klientská aplikace, která odesílá zprávy do služby IoT Hub

* [Stáhnout Git](https://www.git-scm.com/downloads)

* Kroky v tomto článku předpokládají vývojový počítač s Windows. v upřednostňovaném prostředí ale můžete tyto kroky snadno provést v systému Linux.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidání skupiny příjemců do služby IoT Hub

[Skupiny příjemců](../event-hubs/event-hubs-features.md#event-consumers) poskytují nezávislá zobrazení do datového proudu událostí, který aplikacím a službám Azure umožňuje nezávisle spotřebovat data ze stejného koncového bodu centra událostí. V této části přidáte skupinu příjemců do integrovaného koncového bodu služby IoT Hub, který bude webová aplikace používat pro čtení dat.

Spuštěním následujícího příkazu přidejte skupinu příjemců do integrovaného koncového bodu služby IoT Hub:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Poznamenejte si název, který zvolíte, budete ho potřebovat později v tomto kurzu.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Získání připojovacího řetězce služby pro Centrum IoT

Centra IoT se vytvářejí s několika výchozími zásadami přístupu. Jedna z těchto zásad je zásada **služby** , která poskytuje dostatečná oprávnění pro službu ke čtení a zápisu koncových bodů služby IoT Hub. Spusťte následující příkaz, který načte připojovací řetězec pro službu IoT Hub, který dodržuje zásady služby:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

Připojovací řetězec by měl vypadat nějak takto:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Poznamenejte si připojovací řetězec služby, budete ho potřebovat později v tomto kurzu.

## <a name="download-the-web-app-from-github"></a>Stažení webové aplikace z GitHubu

Otevřete příkazové okno a zadejte následující příkazy ke stažení ukázky z GitHubu a přejděte do ukázkového adresáře:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Projděte si kód webové aplikace.

V adresáři Web-Apps-Node-IoT-Hub-data-vizualizace otevřete webovou aplikaci ve svém oblíbeném editoru. Následující příklad ukazuje strukturu souboru zobrazenou v VS Code:

![Struktura souborů webové aplikace](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Věnujte prosím chvilku, abyste prozkoumali následující soubory:

* **Server.js** je skript na straně služby, který inicializuje webový soket a třídu obálky centra událostí. Poskytuje zpětné volání do třídy obálky centra událostí, které třída používá pro vysílání příchozích zpráv do webového soketu.

* **Event-hub-reader.js** je skript na straně služby, který se připojuje k předdefinovanému koncovému bodu služby IoT Hub pomocí zadaného připojovacího řetězce a skupiny uživatelů. Extrahuje DeviceId a EnqueuedTimeUtc z metadat na příchozích zprávách a pak ji přenáší pomocí metody zpětného volání registrované server.js.

* **Chart-device-data.js** je skript na straně klienta, který naslouchá na webovém soketu, sleduje každý DeviceID a ukládá poslední 50 bodů příchozích dat pro každé zařízení. Pak váže vybraná data zařízení k objektu grafu.

* **Index.html** zpracovává rozložení uživatelského rozhraní pro webovou stránku a odkazuje na potřebné skripty pro logiku na straně klienta.

## <a name="configure-environment-variables-for-the-web-app"></a>Konfigurace proměnných prostředí pro webovou aplikaci

Aby webová aplikace mohla číst data ze služby IoT Hub, potřebuje připojovací řetězec služby IoT Hub a název skupiny uživatelů, do které by se měl číst. Získává tyto řetězce z prostředí procesu na následujících řádcích v server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Nastavte proměnné prostředí v příkazovém okně pomocí následujících příkazů. Nahraďte zástupné hodnoty připojovacím řetězcem služby pro službu IoT Hub a názvem skupiny uživatelů, kterou jste předtím vytvořili. Nepoužívejte uvozovky řetězců.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Spusťte webovou aplikaci

1. Ujistěte se, že vaše zařízení běží a odesílá data.

2. V příkazovém okně spusťte následující řádky a stáhněte a nainstalujte odkazované balíčky a spusťte web:

   ```cmd
   npm install
   npm start
   ```

3. V konzole byste měli vidět výstup, který indikuje, že se webová aplikace úspěšně připojila ke službě IoT Hub a naslouchá na portu 3000:

   ![Webová aplikace se spustila v konzole.](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Otevřete webovou stránku, kde se zobrazí data ze služby IoT Hub.

Otevřete prohlížeč `http://localhost:3000` .

V seznamu **Vybrat zařízení** vyberte zařízení, aby se zobrazilo běžící vykreslení poslední teploty 50 a datových bodů vlhkosti odesílané zařízením do služby IoT Hub.

![Stránka webové aplikace zobrazující teplotu a vlhkost v reálném čase](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Měl by se také zobrazit výstup v konzole, ve kterém se zobrazují zprávy, které webová aplikace vysílá do klienta prohlížeče:  

![Výstup všesměrového vysílání webové aplikace v konzole](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Hostování webové aplikace v App Service

[Funkce Web Apps v Azure App Service](../app-service/overview.md) poskytuje platformu jako službu (PaaS) pro hostování webových aplikací. Webové aplikace hostované ve službě Azure App Service můžou využívat výkonné funkce Azure, jako je další zabezpečení, Vyrovnávání zatížení a škálovatelnost, a také řešení Azure a partnerská řešení DevOps, jako je průběžné nasazování, Správa balíčků atd. Azure App Service podporuje webové aplikace vyvinuté v mnoha oblíbených jazycích a nasazené v infrastruktuře systému Windows nebo Linux.

V této části zřídíte webovou aplikaci v App Service a do ní nasadíte kód pomocí příkazů rozhraní příkazového řádku Azure CLI. Podrobnosti o příkazech, které se používají v dokumentaci [AZ WebApp](/cli/azure/webapp) , najdete v části. Než začnete, ujistěte se, že jste dokončili kroky pro [Přidání skupiny prostředků do služby IoT Hub](#add-a-consumer-group-to-your-iot-hub), [získání připojovacího řetězce služby pro službu IoT Hub](#get-a-service-connection-string-for-your-iot-hub)a [Stažení webové aplikace z GitHubu](#download-the-web-app-from-github).

1. [Plán App Service](../app-service/overview-hosting-plans.md) definuje sadu výpočetních prostředků pro aplikaci hostovanou v App Service, která se má spustit. V tomto kurzu používáme pro hostování webové aplikace úroveň vývojář/Free. S úrovní Free se vaše webová aplikace spouští na sdílených prostředcích Windows s jinými aplikacemi App Service, včetně aplikací jiných zákazníků. Azure také nabízí App Service plány pro nasazení webových aplikací do výpočetních prostředků Linux. Pokud již máte plán App Service, který chcete použít, můžete tento krok přeskočit.

   Pokud chcete vytvořit plán App Service pomocí bezplatné úrovně Windows, spusťte následující příkaz. Použijte stejnou skupinu prostředků, ve které je vaše centrum IoT. Název plánu služby může obsahovat velká a malá písmena, číslice a spojovníky.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Teď v plánu App Service zřídit webovou aplikaci. `--deployment-local-git`Parametr umožňuje nahrát a nasadit kód webové aplikace z úložiště Git na místním počítači. Název vaší webové aplikace musí být globálně jedinečný a může obsahovat velká a malá písmena, číslice a spojovníky. Nezapomeňte zadat uzel verze 10,6 nebo novější pro `--runtime` parametr v závislosti na verzi Node.js modulu runtime, který používáte. Pomocí `az webapp list-runtimes` příkazu můžete získat seznam podporovaných modulů runtime.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Teď přidejte nastavení aplikace pro proměnné prostředí, které určují připojovací řetězec služby IoT Hub a skupinu uživatelů centra událostí. Jednotlivá nastavení jsou oddělená mezerami v `-settings` parametru. Použijte připojovací řetězec služby pro Centrum IoT a skupinu uživatelů, kterou jste vytvořili dříve v tomto kurzu. Hodnoty nemusíte citovat.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Povolte pro webovou aplikaci protokol Web Sockets a nastavte webovou aplikaci tak, aby přijímala pouze požadavky HTTPS (požadavky HTTP se přesměrují na HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Chcete-li nasadit kód pro App Service, použijte [přihlašovací údaje pro nasazení na úrovni uživatele](../app-service/deploy-configure-credentials.md). Přihlašovací údaje pro nasazení na úrovni uživatele se liší od vašich přihlašovacích údajů Azure a používají se pro místní nasazení Git a FTP do webové aplikace. Po nastavení jsou platné napříč všemi vašimi aplikacemi App Service ve všech předplatných ve vašem účtu Azure. Pokud jste dříve nastavili přihlašovací údaje pro nasazení na úrovni uživatele, můžete je použít.

   Pokud jste dříve nenastavili přihlašovací údaje pro nasazení na úrovni uživatele nebo si nepamatujete heslo, spusťte následující příkaz. Vaše uživatelské jméno nasazení musí být v rámci Azure jedinečné a nesmí obsahovat \@ symbol pro místní nabízená oznámení Git. Po zobrazení výzvy zadejte a potvrďte nové heslo. Heslo musí mít délku alespoň osm znaků a dva z následujících tří prvků: písmena, číslice a symboly.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Získejte adresu URL Gitu, která se použije k nahrání kódu do App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Přidejte vzdálené úložiště k klonu, který odkazuje na úložiště Git webové aplikace v App Service. \<Git clone URL\>V případě použijte adresu URL vrácenou v předchozím kroku. Spusťte následující příkaz v příkazovém okně.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Chcete-li nasadit kód, který App Service, zadejte následující příkaz v příkazovém okně. Pokud se zobrazí výzva k zadání přihlašovacích údajů, zadejte přihlašovací údaje pro nasazení na úrovni uživatele, které jste vytvořili v kroku 5. Ujistěte se, že nahrajete do hlavní větve App Service vzdálené.

    ```cmd
    git push webapp main:main
    ```

9. Průběh nasazení se aktualizuje v příkazovém okně. Úspěšné nasazení skončí s řádky podobnými následujícímu výstupu:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  main -> main
    ```

10. Spuštěním následujícího příkazu se dotazuje na stav webové aplikace a ujistěte se, že je spuštěná:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. V prohlížeči přejděte na `https://<your web app name>.azurewebsites.net`. Webová stránka podobná té, kterou jste viděli při zobrazení webové aplikace v místním prostředí. Za předpokladu, že vaše zařízení běží a odesílá data, měl by se zobrazit běžící vykreslení 50 nejnovější teploty a čtení vlhkosti odesílané zařízením.

## <a name="troubleshooting"></a>Řešení potíží

Pokud v této ukázce provedete všechny problémy, zkuste postup v následujících částech. Pokud stále máte problémy, pošlete nám svůj názor na konci tohoto tématu.

### <a name="client-issues"></a>Problémy klienta

* Pokud se zařízení v seznamu nezobrazí nebo se žádný graf nevykresluje, ujistěte se, že je na vašem zařízení spuštěný Kód zařízení.

* V prohlížeči otevřete nástroje pro vývojáře (v mnoha prohlížečích ho otevře klávesa F12) a vyhledejte konzolu. Vyhledejte všechna upozornění nebo chyby, které tam byly vytištěny.

* Můžete ladit skript na straně klienta v/js/chat-device-data.js.

### <a name="local-website-issues"></a>Problémy s místním webem

* Sledujte výstup v okně, kde jste spustili uzel pro výstup konzoly.

* Ladění kódu serveru, konkrétně server.js a/Scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problémy s Azure App Service

* V Azure Portal přejdete do vaší webové aplikace. V části **monitorování** v levém podokně vyberte **protokoly App Service**. Zapněte **protokolování aplikace (systém souborů)** na zapnuto, nastavte **úroveň** na hodnotu chyba a pak vyberte **Uložit**. Pak otevřete **datový proud protokolu** (pod položkou **monitorování**).

* Z vaší webové aplikace v Azure Portal v části **vývojové nástroje**  vyberte  **konzolu** a ověřte verze node a npm pomocí `node -v` a `npm -v` .

* Pokud se zobrazí chyba týkající se nenalezení balíčku, možná jste spustili kroky mimo pořadí. Při nasazení lokality (s) je `git push` spuštěna služba App Service `npm install` , která běží na základě aktuální verze uzlu, který je nakonfigurován. Pokud se později v konfiguraci změní, budete muset provést nesmyslnou změnu kódu a znovu ho vložit.

## <a name="next-steps"></a>Další kroky

Úspěšně jste použili svou webovou aplikaci k vizualizaci dat snímačů v reálném čase ze služby IoT Hub.

Další způsob, jak vizualizovat data z Azure IoT Hub, najdete v tématu [použití Power BI k vizualizaci dat snímačů v reálném čase ze služby IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
