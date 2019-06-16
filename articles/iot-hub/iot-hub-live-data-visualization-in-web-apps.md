---
title: Vizualizace dat v reálném čase s daty ze snímačů ze služby Azure IoT hub ve webové aplikaci | Dokumentace Microsoftu
description: Použijte webové aplikace k vizualizaci dat teploty a vlhkosti, která se shromažďují ze senzoru a odesílají do služby Iot hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 22b15a95e529d4f09560e9b7e59d9f78f70651bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476000"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Vizualizace dat snímače v reálném čase ze služby Azure IoT hub ve webové aplikaci

![Diagram začátku do konce](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Co se naučíte

V tomto kurzu se dozvíte, jak k vizualizaci dat snímačů v reálném čase, který IoT hub přijímá s webové aplikace node.js spuštěná v místním počítači. Po spuštění webovou aplikaci místně, můžete volitelně postupujte podle kroků pro hostování webové aplikace ve službě Azure App Service. Pokud chcete zkusit k vizualizaci dat ve službě IoT hub s využitím Power BI, najdete v článku [pomocí Power BI k vizualizaci dat snímačů v reálném čase ze služby Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Co můžete dělat

* Přidat skupinu uživatelů do služby IoT hub, který webová aplikace bude používat ke čtení dat ze snímačů
* Stáhněte si kód webové aplikace z Githubu
* Prozkoumejte kód webové aplikace
* Konfigurace proměnných prostředí pro uchování artefaktů vyžadované vaší webové aplikace služby IoT Hub
* Spuštění webové aplikace na svém vývojovém počítači
* Otevřít webovou stránku, pokud chcete zobrazit data v reálném čase teploty a vlhkosti ze služby IoT hub
* (Volitelné) Hostujte svoji webovou aplikaci ve službě Azure App Service pomocí Azure CLI

## <a name="what-you-need"></a>Co potřebujete

* Dokončení [online simulátor Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) kurzu nebo jeden z kurzů zařízení; například [Raspberry Pi s node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ty zahrnují následující požadavky:

  * Aktivní předplatné Azure
  * Služby Iot hub v rámci vašeho předplatného
  * Klientská aplikace, která odesílá zprávy do služby Iot hub

* [Stáhněte si Git](https://www.git-scm.com/downloads)

* Kroky v tomto článku předpokládají vývojový počítač s Windows; ale snadno provedením těchto kroků v systému Linux v preferované příkazové.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Spusťte následující příkaz pro přidání rozšíření Microsoft Azure IoT pro Azure CLI do instance služby Cloud Shell. Rozšíření IOT přidá služby IoT Hub, IoT Edge a IoT zařízení zřizování služby (DPS) konkrétní příkazy rozhraní příkazového řádku Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidat skupinu uživatelů do služby IoT hub

[Skupiny příjemců](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) poskytují nezávislé zobrazení streamu událostí, která umožňují aplikací a služeb Azure s nezávisle na sobě využívají data z stejný koncový bod centra událostí. V této části přidáte do integrovaný koncový bod služby IoT hub, který webová aplikace bude používat ke čtení dat z skupiny příjemců.

Spusťte následující příkaz pro přidání skupiny příjemců na integrovaný koncový bod služby IoT hub:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Poznámka: dolů název, který zvolíte, budete je potřebovat později v tomto kurzu.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Získání připojovacího řetězce služby pro službu IoT hub

Centra IoT hub se vytvoří s několika výchozí zásady přístupu. Jeden takový zásady **služby** zásady, které poskytuje dostatečná oprávnění pro službu pro čtení a zápis koncové body IoT hubu. Spuštěním následujícího příkazu získejte připojovací řetězec služby IoT hub, který splňuje zásady služby:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

Připojovací řetězec by měl vypadat nějak takto:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Poznamenejte si připojovací řetězec služby, budete je potřebovat později v tomto kurzu.

## <a name="download-the-web-app-from-github"></a>Stáhněte si webové aplikace z Githubu

Otevřete okno příkazového řádku a zadejte následující příkazy, abyste Stáhnout ukázku z Githubu a přejděte do adresáře, ukázka:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Prozkoumejte kód webové aplikace

V adresáři web-apps-node-iot-hub-data-visualization otevřete webovou aplikaci ve svém oblíbeném editoru. Následuje ukázka struktura souborů zobrazit v nástroji VS Code:

![Struktura souborů webové aplikace](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Za chvíli zkontrolujte následující soubory:

* **Server.js** je skript straně služby, který inicializuje objekt websocket a obálkovou třídu centra událostí. Poskytuje zpětné volání pro obálkovou třídu centra událostí, který používá třídu k vysílání příchozí zprávy pro objekt websocket.

* **Události hub-reader.js** je skript straně služby, který se připojí k centru IoT integrovaný koncový bod, pomocí zadaného připojovacího řetězce a příjemce skupiny. Extrahuje ID zařízení a EnqueuedTimeUtc z metadat na příchozí zprávy a poté předává zprávy pomocí metody zpětného volání registrovaných server.js.

* **Graf. zařízení data.js** je skript na straně klienta, který naslouchá na objekt websocket, uchovává informace o každé ID zařízení a uloží poslední 50 bodů příchozích dat pro jednotlivá zařízení. Data vybrané zařízení se pak váže na objekt grafu.

* **Index.HTML** zpracovává rozložení uživatelského rozhraní pro webové stránky a odkazuje na nutných skriptů pro logiku na straně klienta.

## <a name="configure-environment-variables-for-the-web-app"></a>Konfigurace proměnných prostředí pro webovou aplikaci

Číst data ze služby IoT hub, webové aplikace musí připojovací řetězec služby IoT hub a název, který by měl pročtěte skupinu příjemců. Získá tyto řetězce z prostředí procesu v následujících řádcích v server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

V příkazovém okně pomocí následujících příkazů nastavte proměnné prostředí. Hodnoty zástupných symbolů nahraďte připojovací řetězec služby pro službu IoT hub a název skupiny uživatelů, kterou jste vytvořili dříve. Není uvozovek řetězce.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Spuštění webové aplikace

1. Ujistěte se, že vaše zařízení je spuštěná a odesílá data.

2. V příkazovém řádku spusťte následující řádky do stáhnout a nainstalovat odkazované balíčky a spusťte web:

   ```cmd
   npm install
   npm start
   ```

3. Byste měli vidět výstup do konzoly, která označuje, že tato webová aplikace se úspěšně připojil ke službě IoT hub a naslouchá na portu 3000:

   ![Webová aplikace spuštěna v konzole](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Otevřít webovou stránku zobrazit data ze služby IoT hub

Otevřete prohlížeč na `http://localhost:3000`.

V **vyberte zařízení** seznamu, vyberte zařízení zobrazíte spuštěné vykreslení poslední 50 teploty a vlhkosti datových bodů, které zařízení odeslalo do služby IoT hub.

![Zobrazení v reálném čase teploty a vlhkosti na stránce webové aplikace](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Také byste měli vidět výstup do konzoly, která zobrazuje zprávy, které vysílá vaší webové aplikace do prohlížeče klienta:  

![Výstup na konzole vysílání webové aplikace](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Hostitel webové aplikace ve službě App Service

[Funkce Web Apps služby Azure App Service](https://docs.microsoft.com/azure/app-service/overview) poskytuje platforma jako služba (PAAS) pro hostování webových aplikací. Webové aplikace hostované ve službě Azure App Service můžete využívat výkonné funkce Azure jako dodatečné zabezpečení, Vyrovnávání zatížení, a škálovatelnost, jakou najdete, jakož i řešení, jako jsou průběžné nasazování, správě balíčků, Azure a jeho partnerů DevOps a tak dále. Azure App Service podporuje webové aplikace vyvinuté v mnoha oblíbených jazyků a nasazeny na infrastruktuře Windows nebo Linux.

V této části zřízení webové aplikace ve službě App Service a nasazení kódu do něj s použitím příkazů Azure CLI. Můžete najít podrobnosti o příkazech použitých v [az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) dokumentaci. Než začnete, ujistěte se, že jste dokončili postup [přidat skupinu prostředků pro službu IoT hub](#add-a-consumer-group-to-your-iot-hub), [získat připojovací řetězec služby pro službu IoT hub](#get-a-service-connection-string-for-your-iot-hub), a [stáhnout z webu GitHubwebovéaplikace](#download-the-web-app-from-github).

1. [Plán služby App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) definuje sadu výpočetní prostředky pro aplikaci hostovanou ve službě App Service pro spuštění. V tomto kurzu používáme k hostování webové aplikace na úrovni Developer/Free. U bezplatné úrovně vaše webová aplikace běží na sdílených prostředků Windows s jinými aplikacemi App Service, včetně aplikací od ostatních zákazníků. Azure také nabízí plány služby App Service k nasazování webových aplikací v Linuxu výpočetní prostředky. Pokud už máte plán služby App Service, kterou chcete použít, můžete tento krok přeskočit.

   Chcete-li vytvořit plán služby App Service pomocí bezplatné úrovně Windows, spusťte následující příkaz. Použijte stejnou skupinu prostředků, služby IoT hub se. Název plánu služby může obsahovat velká a malá písmena, číslice a pomlčky.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Nyní zřízení webové aplikace v plánu služby App Service. `--deployment-local-git` Parametr povoluje kódu webové aplikace k nahrání a nasazení z úložiště Git na svém místním počítači. Název vaší webové aplikace musí být globálně jedinečný a může obsahovat velká a malá písmena, číslice a pomlčky.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --deployment-local-git
   ```

3. Nyní přidejte nastavení aplikace pro objekt environment variables, které určují připojovacího řetězce centra IoT a skupina uživatelů centra událostí. Jednotlivá nastavení jsou v oddělených mezerami `-settings` parametru. Použijte připojovací řetězec služby IoT hub a skupinu příjemců, který jste vytvořili dříve v tomto kurzu. Není nabídka hodnoty.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Pouze požadavky webové sokety protokol pro webové aplikace a nastavení webové aplikace pro příjem HTTPS (HTTP požadavky se přesměrují do HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Nasazení kódu do služby App Service, budete používat vaše [přihlašovací údaje pro nasazení na úrovni uživatele](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). Přihlašovací údaje pro nasazení na úrovni uživatele se liší od přihlašovacích údajů Azure a používají se pro místní Git a FTP nasazení do webové aplikace. Po nastavení jsou platné ve všech svých aplikací služby App Service v rámci všech předplatných ve svém účtu Azure. Pokud jste dříve nastavili přihlašovací údaje pro nasazení na úrovni uživatele, můžete je použít.

   Pokud jste ještě nenastavili přihlašovací údaje pro nasazení na úrovni uživatele nebo si nepamatujete heslo, spusťte následující příkaz. Vaše uživatelské jméno pro nasazení musí být v rámci Azure jedinečný a nesmí obsahovat "@" symbol pro místní Git nabízených oznámení. Když se zobrazí výzva, zadejte a potvrďte nové heslo. Heslo musí obsahovat alespoň osm znaků dlouhá a dva z následujících: písmena, číslice a symboly.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Získání adresy URL pro Git použít tak, aby nabízel kódu až do služby App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Přidáte vzdálené na vašem klonu, který odkazuje na úložiště Git pro webovou aplikaci ve službě App Service. Pro \<adresa URL klonu Git\>, použití adresy URL vrácené v předchozím kroku. Spusťte následující příkaz v příkazovém okně.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Nasazení kódu do služby App Service, zadejte následující příkaz v příkazovém okně. Pokud se výzva k zadání přihlašovacích údajů, zadejte přihlašovací údaje nasazení na úrovni uživatele, které jste vytvořili v kroku 5. Ujistěte se, že vložíte změny do hlavní větve vzdálené služby App Service.

    ```cmd
    git push webapp master:master
    ```

9. Průběh nasazení aktualizuje v příkazovém okně. Úspěšné nasazení bude končit řádky, podobně jako následující výstup:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Spusťte následující příkaz k dotazování na stav vaší webové aplikace a ujistěte se, že je spuštěná:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. V prohlížeči přejděte na `https://<your web app name>.azurewebsites.net`. Webové stránky podobně jako ten, který jste viděli, když jste spustili webovou aplikaci místně zobrazí. Za předpokladu, že vaše zařízení je spuštěná a odesílání dat, měli byste vidět spuštěné vykreslení 50 nejnovější teploty a vlhkosti údajů odeslané ze zařízení.

## <a name="troubleshooting"></a>Řešení potíží

Pokud narazíte na problémy s touto ukázkou, opakujte kroky v následujících částech. Pokud stále máte problémy, pošlete nám zpětnou vazbu v dolní části tohoto tématu.

### <a name="client-issues"></a>Problémy s klientem

* Pokud se zařízení nezobrazí v seznamu, nebo je právě vykreslovány žádný graf, ujistěte se, že kód zařízení běží na vašem zařízení.

* V prohlížeči, otevřete vývojářské nástroje (v mnoha prohlížečů F12 klíč otevřete ji) a vyhledejte konzolu. Vyhledejte žádná upozornění ani chyby tisku došlo.

* Můžete ladit skript na straně klienta v /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problémy s místní web

* Podívejte se na výstup v okně, ve kterém jste spustili uzel pro výstup na konzole.

* Ladění kódu serveru, konkrétně server.js a /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problémy s Azure App Service

* Na webu Azure portal přejděte do své webové aplikace. V části **monitorování** v levém podokně vyberte **protokoly služby App Service**. Zapnout **protokolování aplikace (systém souborů)** , nastavit **úroveň** chyby a pak vyberte **Uložit**. Pak otevřete **stream protokolů** (v části **monitorování**).

* Z vaší webové aplikace na webu Azure portal v části **nástroje pro vývoj** vyberte **konzoly** a ověření verze v node a npm pomocí `node -v` a `npm -v`.

* Pokud se zobrazí chyba týkající se nedaří se vám najít balíček, je může spustit kroky mimo pořadí. Při nasazení webu (s `git push`) spuštění služby aplikace `npm install`, které běží na základě aktuální verze uzlu je nakonfigurované. Ke změně, která je v konfiguraci později, budete muset význam měnit kód a znovu změny nasdílet.

## <a name="next-steps"></a>Další postup

Úspěšně jste použili vaší webové aplikace k vizualizaci dat snímačů v reálném čase ze služby IoT hub.

Dalším způsobem, jak vizualizovat data ze služby Azure IoT Hub, najdete v části [pomocí Power BI k vizualizaci dat snímačů v reálném čase ze služby IoT hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
