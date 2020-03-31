---
title: Vizualizace dat v reálném čase dat z centra IoT ve webové aplikaci
description: Pomocí webové aplikace můžete vizualizovat údaje o teplotě a vlhkosti, které jsou shromažďovány ze senzoru a odesílány do centra Iot Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 138e077f7b47fa9f38a4710db95eb7208cef78e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675319"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Vizualizace dat senzorů v reálném čase z centra Azure IoT hub ve webové aplikaci

![Diagram koncového bodu](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Co se naučíte

V tomto kurzu se dozvíte, jak vizualizovat data senzorů v reálném čase, která vaše centrum IoT hub přijímá pomocí webové aplikace node.js spuštěné v místním počítači. Po spuštění webové aplikace místně můžete volitelně postupovat podle pokynů pro hostování webové aplikace ve službě Azure App Service. Pokud se chcete pokusit vizualizovat data ve službě IoT hub pomocí Power BI, [přečtěte si, že pomocí Power BI můžete vizualizovat data senzorů v reálném čase z Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Co děláte

* Přidání skupiny spotřebitelů do centra IoT hubu, kterou webová aplikace použije ke čtení dat ze senzorů
* Stažení kódu webové aplikace z GitHubu
* Zkontrolujte kód webové aplikace
* Konfigurace proměnných prostředí pro uložení artefaktů služby IoT Hub, které vaše webová aplikace potřebuje
* Spuštění webové aplikace ve vývojovém počítači
* Otevření webové stránky zobrazíte data o teplotě a vlhkosti v reálném čase z centra IoT hub
* (Nepovinné) Použití azure cli k hostování webové aplikace ve službě Azure App Service

## <a name="what-you-need"></a>Co potřebujete

* Dokončete kurz [online simulátoru Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) nebo jeden z výukových programů zařízení; například [Raspberry Pi s node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Tyto požadavky se vztahují na tyto požadavky:

  * Aktivní předplatné Azure
  * Centrum služby Iot v rámci vašeho předplatného
  * Klientská aplikace, která odesílá zprávy do centra Služby Iot

* [Stažení Git](https://www.git-scm.com/downloads)

* Kroky v tomto článku předpokládají vývojový počítač systému Windows; tyto kroky však můžete snadno provést v systému Linux ve vašem preferovaném prostředí.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Spusťte následující příkaz a přidejte rozšíření Microsoft Azure IoT extension pro Azure CLI do instance Cloud Shellu. Rozšíření IOT přidá do rozhraní příkazového příkazu Azure CLI specifické pro služby IoT Hub, IoT Edge a Služby zřizování zařízení IoT (DPS).

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidání skupiny spotřebitelů do centra IoT hub

[Skupiny spotřebitelů](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) poskytují do datového proudu událostí nezávislá zobrazení, která umožňují aplikacím a službám Azure nezávisle využívat data ze stejného koncového bodu centra událostí. V této části přidáte skupinu spotřebitelů do integrovaného koncového bodu služby IoT hub, ze kterého bude webová aplikace používat ke čtení dat.

Spuštěním následujícího příkazu přidáte skupinu spotřebitelů do integrovaného koncového bodu centra IoT hub:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Poznamenejte si název, který si vyberete, budete ho potřebovat později v tomto kurzu.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Získání připojovacího řetězce služby pro centrum IoT hub

Centra IoT jsou vytvořena s několika výchozími zásadami přístupu. Jednou z takových zásad je zásada **služby,** která poskytuje dostatečná oprávnění pro službu ke čtení a zápisu koncových bodů služby IoT hub. Spuštěním následujícího příkazu získáte připojovací řetězec pro centrum IoT hub, který dodržuje zásady služby:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

Připojovací řetězec by měl vypadat podobně jako následující:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Poznamenejte si připojovací řetězec služby, budete ho potřebovat později v tomto kurzu.

## <a name="download-the-web-app-from-github"></a>Stažení webové aplikace z GitHubu

Otevřete příkazové okno a zadejte následující příkazy, abyste stáhli ukázku z GitHubu a přechyli do ukázkového adresáře:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Zkontrolujte kód webové aplikace

Z adresáře web-apps-node-iot-hub-data-visualization otevřete webovou aplikaci ve svém oblíbeném editoru. Následující ukazuje strukturu souborů zobrazenou v kódu VS:

![Struktura souborů webové aplikace](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Udělejte si chvilku a prohlédněte si následující soubory:

* **Server.js** je skript na straně služby, který inicializuje webový soket a obálkovou třídu Event Hub. Poskytuje zpětné volání do třídy obálky Centra událostí, kterou třída používá k vysílání příchozích zpráv do webového soketu.

* **Event-hub-reader.js** je skript na straně služby, který se připojuje k integrovanému koncovému bodu služby IoT hub pomocí zadaného připojovacího řetězce a skupiny spotřebitelů. Extrahuje DeviceId a EnqueuedTimeUtc z metadat na příchozí zprávy a potom předává zprávu pomocí metody zpětného volání registrované server.js.

* **Chart-device-data.js** je skript na straně klienta, který naslouchá na webovém soketu, sleduje každé DeviceId a ukládá posledních 50 bodů příchozích dat pro každé zařízení. Potom sváže vybraná data zařízení s objektem grafu.

* **Index.html** zpracovává rozložení uživatelského rozhraní pro webovou stránku a odkazuje na potřebné skripty pro logiku na straně klienta.

## <a name="configure-environment-variables-for-the-web-app"></a>Konfigurace proměnných prostředí pro webovou aplikaci

Ke čtení dat z centra IoT potřebuje webová aplikace připojovací řetězec služby IoT hub a název skupiny spotřebitelů, kterou by si měla přečíst. Získá tyto řetězce z prostředí procesu v následujících řádcích v souboru server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Nastavte proměnné prostředí v příkazovém okně pomocí následujících příkazů. Nahraďte zástupné hodnoty připojovacím řetězcem služby pro centrum IoT a názvem skupiny spotřebitelů, kterou jste vytvořili dříve. Necituj struny.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Spuštění webové aplikace

1. Zkontrolujte, zda je zařízení spuštěné a odesílá data.

2. V příkazovém okně spusťte následující řádky pro stažení a instalaci odkazovaných balíčků a spuštění webu:

   ```cmd
   npm install
   npm start
   ```

3. V konzole by měl vidět výstup, který označuje, že se webová aplikace úspěšně připojila k centru IoT hub a naslouchá na portu 3000:

   ![Webová aplikace spuštěna na konzoli](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Otevření webové stránky zobrazíte data z centra IoT hub

Otevřete prohlížeč `http://localhost:3000`aplikace .

V seznamu **Vybrat zařízení** vyberte zařízení, chcete-li zobrazit průběžný obrázek posledních 50 datových bodů teploty a vlhkosti odeslaných zařízením do vašeho centra IoT Hub.

![Stránka webové aplikace zobrazující teplotu a vlhkost v reálném čase](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Měli byste také vidět výstup v konzole, která zobrazuje zprávy, které vaše webová aplikace vysílá klientovi prohlížeče:  

![Výstup vysílání webové aplikace na konzoli](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Hostování webové aplikace ve službě App Service

[Funkce Webové aplikace služby Azure App Service](https://docs.microsoft.com/azure/app-service/overview) poskytuje platformu jako službu (PAAS) pro hostování webových aplikací. Webové aplikace hostované ve službě Azure App Service můžou využívat výkonné funkce Azure, jako je další zabezpečení, vyrovnávání zatížení a škálovatelnost, stejně jako řešení Azure a partnerdevOps, jako je průběžné nasazení, správa balíčků a tak dále. Azure App Service podporuje webové aplikace vyvinuté v mnoha populárních jazycích a nasazené na infrastruktuře Windows nebo Linuxu.

V této části zřídíte webovou aplikaci ve službě App Service a nasadíte do ní svůj kód pomocí příkazů Azure CLI. Podrobnosti o příkazech použitých naleznete v dokumentaci [az webapp.](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) Než začnete, ujistěte se, že jste dokončili kroky k [přidání skupiny prostředků do centra IoT hub](#add-a-consumer-group-to-your-iot-hub), [získejte připojovací řetězec služby pro centrum IoT](#get-a-service-connection-string-for-your-iot-hub)a [stáhněte si webovou aplikaci z GitHubu](#download-the-web-app-from-github).

1. Plán [služby App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) definuje sadu výpočetních prostředků pro aplikaci hostovoci ve službě App Service ke spuštění. V tomto kurzu používáme úroveň Developer/Free k hostování webové aplikace. S úrovní Free se vaše webová aplikace spouští na sdílených prostředcích Windows s dalšími aplikacemi služby App Service, včetně aplikací jiných zákazníků. Azure také nabízí plány služby App Service k nasazení webových aplikací na výpočetní prostředky Linuxu. Tento krok můžete přeskočit, pokud už máte plán služby App Service, který chcete použít.

   Chcete-li vytvořit plán služby App Service pomocí úrovně Windows free, spusťte následující příkaz. Použijte stejnou skupinu prostředků, ve které se nachází vaše centrum IoT. Název plánu služeb může obsahovat velká a malá písmena, čísla a pomlčky.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Teď zřizujte webovou aplikaci v plánu služby App Service. Parametr `--deployment-local-git` umožňuje nahrát a nasadit kód webové aplikace z úložiště Git v místním počítači. Název webové aplikace musí být globálně jedinečný a může obsahovat velká a malá písmena, čísla a pomlčky. Nezapomeňte zadat uzel verze 10.6 nebo `--runtime` novější pro parametr, v závislosti na verzi node.js runtime, který používáte. Pomocí příkazu `az webapp list-runtimes` můžete získat seznam podporovaných modulů runtimes.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Teď přidejte nastavení aplikace pro proměnné prostředí, které určují připojovací řetězec centra IoT a skupinu spotřebitelů centra událostí. Jednotlivá nastavení jsou v `-settings` parametru vymezena mezerami. Použijte připojovací řetězec služby pro centrum IoT a skupinu spotřebitelů, kterou jste vytvořili dříve v tomto kurzu. Necitovujte hodnoty.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Povolte protokol webových soketů pro webovou aplikaci a nastavte webovou aplikaci tak, aby přijímali pouze požadavky HTTPS (požadavky HTTP jsou přesměrovány na protokol HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Chcete-li nasadit kód do služby App Service, budete používat [přihlašovací údaje pro nasazení na úrovni uživatele](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). Vaše přihlašovací údaje pro nasazení na úrovni uživatele se liší od vašich přihlašovacích údajů azure a používají se pro místní nasazení Gitu a FTP do webové aplikace. Jakmile jsou nastaveny, jsou platné ve všech vašich aplikacích služby App Service ve všech předplatných ve vašem účtu Azure. Pokud jste dříve nastavili přihlašovací údaje pro nasazení na úrovni uživatele, můžete je použít.

   Pokud jste dříve nenastavili přihlašovací údaje pro nasazení na úrovni uživatele nebo si nepamatujete heslo, spusťte následující příkaz. Uživatelské jméno nasazení musí být v rámci Azure jedinečné a nesmí obsahovat symbol @pro místní nabízená oznámení Git. Po zobrazení výzvy zadejte a potvrďte nové heslo. Heslo musí mít trvat nejméně osm znaků, přičemž dva z následujících tří prvků: písmena, číslice a symboly.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Získejte adresu URL Git, kterou chcete použít k nabízení kódu do služby App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Přidejte ke svému klonu dálkové ovládání, které odkazuje na úložiště Git pro webovou aplikaci ve službě App Service. Pro \<adresu URL\>klonování Gitu použijte adresu URL vrácenou v předchozím kroku. V příkazovém okně spusťte následující příkaz.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Chcete-li nasadit kód do služby App Service, zadejte do příkazového okna následující příkaz. Pokud se zobrazí výzva k zadání pověření, zadejte přihlašovací údaje nasazení na úrovni uživatele, které jste vytvořili v kroku 5. Ujistěte se, že push do hlavní větve vzdálené služby App Service.

    ```cmd
    git push webapp master:master
    ```

9. Průběh nasazení se aktualizuje ve vašem příkazovém okně. Úspěšné nasazení bude zakončeno řádky podobnými následujícímu výstupu:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Spusťte následující příkaz, abyste se dotazují na stav webové aplikace a ujistili se, že je spuštěná:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. V prohlížeči přejděte na `https://<your web app name>.azurewebsites.net`. Webová stránka podobná té, kterou jste viděli při spuštění webové aplikace místně. Za předpokladu, že vaše zařízení běží a odesílá data, měli byste vidět běžecký obrázek 50 nejnovějších údajů o teplotě a vlhkosti odeslaných zařízením.

## <a name="troubleshooting"></a>Řešení potíží

Pokud narazíte na nějaké problémy s touto ukázkou, vyzkoušejte kroky v následujících částech. Pokud potíže přetrvávají, pošlete nám zpětnou vazbu v dolní části tohoto tématu.

### <a name="client-issues"></a>Problémy s klientem

* Pokud se zařízení v seznamu nezobrazuje nebo se nekreslí žádný graf, zkontrolujte, zda je v zařízení spuštěn kód zařízení.

* V prohlížeči otevřete vývojářské nástroje (v mnoha prohlížečích jej otevře klávesa F12) a najděte konzolu. Vyhledejte zde vytištěná varování nebo chyby.

* Skript na straně klienta můžete ladit v souboru /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problémy s místními webovými stránkami

* Podívejte se na výstup v okně, kde jste spustili uzel pro výstup konzoly.

* Ladění kódu serveru, konkrétně server.js a /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problémy se službou Azure App Service

* Na webu Azure Portal přejděte do webové aplikace. V části **Monitorování** v levém podokně vyberte **protokoly služby App Service**. Zapněte **protokolování aplikací (Systém souborů),** nastavte **úroveň** na chybu a pak vyberte **Uložit**. Pak otevřete **Log stream** (v části **Monitorování).**

* Ve své webové aplikaci na webu Azure Portal vyberte v části `node -v` `npm -v`Nástroje **pro vývoj** **konzolu** a ověřte verze uzlů a npm pomocí a .

* Pokud se zobrazí chyba o nenalezení balíčku, je možné, že jste kroky neprovedli. Když je web nasazen `git push`(s) služba `npm install`aplikace běží , který běží na základě aktuální verze uzlu, který nakonfiguroval. Pokud se to později změní v konfiguraci, budete muset provést bezvýznamnou změnu kódu a znovu tlačit.

## <a name="next-steps"></a>Další kroky

Úspěšně jste použili webovou aplikaci k vizualizaci dat senzorů v reálném čase z centra IoT hub.

Další způsob vizualizace dat z Azure IoT Hub najdete v [tématu Visualize dat senzorů v reálném čase z centra IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
