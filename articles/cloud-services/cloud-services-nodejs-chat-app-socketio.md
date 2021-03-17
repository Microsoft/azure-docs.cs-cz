---
title: Node.js aplikace s využitím Socket.io – Azure
description: Pomocí tohoto kurzu se naučíte hostovat soket. Vstupně-výstupní aplikace v Azure na bázi Socket.IO poskytuje komunikaci v reálném čase pro node.js Server a klienty.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: abc02769d7d978e14975d90ae0f98547bdc4faf7
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743317"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service-classic"></a>Vytvoření aplikace Node.js chatu pomocí Socket.IO v cloudové službě Azure (Classic)

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Socket.IO zajišťuje komunikaci v reálném čase mezi vaším node.js serverem a klienty. Tento kurz vás provede hostováním soketu. Aplikace v/v v Azure na základě konverzace Další informace o Socket.IO najdete v tématu [Socket.IO](https://socket.io).

Snímek obrazovky dokončené aplikace je níže:

![Okno prohlížeče zobrazující službu hostovanou v Azure][completed-app]

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že jsou nainstalovány následující produkty a verze, aby bylo možné úspěšně dokončit příklad v tomto článku:

* Nainstalovat [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Nainstalovat [Node.js](https://nodejs.org/download/)
* Instalace [Pythonu verze 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Vytvoření projektu cloudové služby
Následující kroky vytvoří projekt cloudové služby, který bude hostovat aplikaci Socket.IO.

1. V **nabídce Start** nebo na **obrazovce Start** vyhledejte **Windows PowerShell**. Nakonec klikněte pravým tlačítkem myši na **Windows PowerShell** a vyberte **Spustit jako správce**.

    ![Ikona Azure PowerShell][powershell-menu]
2. Vytvořte adresář nazvaný **c: \\ Node**.

    ```powershell
    PS C:\> md node
    ```

3. Změňte adresáře na adresář **c: \\ Node.**

    ```powershell
    PS C:\> cd node
    ```

4. Zadáním následujících příkazů vytvořte nové řešení s názvem `chatapp` a roli pracovního procesu s názvem `WorkerRole1` :

    ```powershell
    PS C:\node> New-AzureServiceProject chatapp
    PS C:\Node> Add-AzureNodeWorkerRole
    ```

    Zobrazí se následující odpověď:

    ![Výstup New-azureservice a Add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Příklad stažení chatu
Pro tento projekt použijeme příklad chatu z [úložiště GitHub Socket.IO]. Provedením následujících kroků stáhněte příklad a přidejte ho do projektu, který jste vytvořili dříve.

1. Vytvořte místní kopii úložiště pomocí tlačítka **klonování** . K stažení projektu můžete použít také tlačítko **zip** .

   ![Zobrazení okna prohlížeče https://github.com/LearnBoost/socket.io/tree/master/examples/chat se zvýrazněnou ikonou stažení souboru ZIP](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Přejděte do adresářové struktury místního úložiště, dokud se nedostanete do adresáře **příkladů \\ chatu** . Zkopírujte obsah tohoto adresáře do adresáře **C: \\ node \\ chatapp \\ WorkerRole1** , který jste vytvořili dříve.

   ![Průzkumník a zobrazení obsahu \\ adresáře příkladů chatu extrahované z archivu][chat-contents]

   Zvýrazněné položky na snímku obrazovky jsou soubory zkopírované z adresáře **příkladů \\ chatu** .
3. V adresáři **C: \\ node \\ chatapp \\ WorkerRole1** odstraňte soubor **server.js** a potom přejmenujte soubor **app.js** na **server.js**. Tím se odebere výchozí **server.js** dříve vytvořený pomocí rutiny **Add-AzureNodeWorkerRole** a nahradí se souborem aplikace z příkladu chatu.

### <a name="modify-serverjs-and-install-modules"></a>Upravit Server.js a nainstalovat moduly
Před testováním aplikace v emulátoru Azure je nutné provést několik menších úprav. V server.js souboru proveďte následující kroky:

1. Otevřete soubor **server.js** v aplikaci Visual Studio nebo v libovolném textovém editoru.
2. Vyhledejte část **závislosti modulu** na začátku server.js a změňte řádek obsahující **SIO = vyžadovat (. //.. lib//Socket. IO ')** do **SIO = vyžadovat (' Socket. IO ')** , jak je znázorněno níže:

    ```js
    var express = require('express')
      , stylus = require('stylus')
      , nib = require('nib')
    //, sio = require('..//..//lib//socket.io'); //Original
      , sio = require('socket.io');                //Updated
      var port = process.env.PORT || 3000;         //Updated
    ```

3. Pokud chcete zajistit, aby aplikace naslouchala na správném portu, otevřete server.js v programu Poznámkový blok nebo v oblíbeném editoru a pak změňte následující řádek tak, že nahradíte **3000** **. env. port** , jak je znázorněno níže:

    ```js
    //app.listen(3000, function () {            //Original
    app.listen(process.env.port, function () {  //Updated
      var addr = app.address();
      console.log('   app listening on http://' + addr.address + ':' + addr.port);
    });
    ```

Po uložení změn do **server.js** pomocí následujících kroků nainstalujte požadované moduly a pak otestujte aplikaci v emulátoru Azure:

1. Pomocí **Azure PowerShell** změňte adresáře na adresář **C: \\ node \\ chatapp \\ WorkerRole1** a pomocí následujícího příkazu nainstalujte moduly požadované touto aplikací:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> npm install
    ```

   Tím se nainstalují moduly, které jsou uvedené v package.jsv souboru. Po dokončení příkazu by se měl zobrazit výstup, který vypadá přibližně takto:

   ![Výstup příkazu pro instalaci npm][The-output-of-the-npm-install-command]
2. Vzhledem k tomu, že tento příklad byl původně součástí úložiště GitHubu Socket.IO a přímo odkazoval na knihovnu Socket.IO na základě relativní cesty, Socket.IO se na soubor nepackage.jsodkazuje, takže je potřeba ho nainstalovat vyvoláním následujícího příkazu:

   ```powershell
   PS C:\node\chatapp\WorkerRole1> npm install socket.io --save
    ```

### <a name="test-and-deploy"></a>Testování a nasazení
1. Spusťte emulátor vyvoláním následujícího příkazu:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
    ```

   > [!NOTE]
   > Pokud narazíte na problémy s emulátorem spouštění, například: Start-AzureEmulator: došlo k neočekávané chybě.  Podrobnosti: došlo k neočekávané chybě. objekt komunikace, System. ServiceModel. Channels. kanálu ServiceChannel, nelze použít pro komunikaci, protože je ve stavu Faulted.
   >
   > Přeinstalujte AzureAuthoringTools v 2.7.1 a AzureComputeEmulator v 2,7 – zajistěte shodu verzí.

2. Otevřete prohlížeč a přejděte na **http://127.0.0.1** .
3. Po otevření okna prohlížeče zadejte přezdívku a potom stiskněte klávesu ENTER.
   To vám umožní publikovat zprávy jako konkrétní přezdívku. Chcete-li otestovat funkci více uživatelů, otevřete další okna prohlížeče pomocí stejné adresy URL a zadejte jiné přezdívky.

   ![Dvě okna prohlížeče zobrazující zprávy chatu od uživatele User1 a uživatel2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Po otestování aplikace zastavte emulátor vydáním následujícího příkazu:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
    ```

5. Pokud chcete nasadit aplikaci do Azure, použijte rutinu **Publish-AzureServiceProject** . Například:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
    ```

   > [!IMPORTANT]
   > Nezapomeňte použít jedinečný název, jinak se proces publikování nezdaří. Po dokončení nasazení se otevře prohlížeč a přejde na nasazenou službu.
   >
   > Pokud se zobrazí chyba s oznámením, že zadaný název předplatného neexistuje v importovaném publikačním profilu, musíte si před nasazením do Azure stáhnout a importovat profil publikování pro vaše předplatné. Viz část **nasazení aplikace do Azure** v tématu [sestavení a nasazení Node.js aplikace do cloudové služby Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/) .
   >
   >

   ![Okno prohlížeče zobrazující službu hostovanou v Azure][completed-app]

   > [!NOTE]
   > Pokud se zobrazí chyba s oznámením, že zadaný název předplatného neexistuje v importovaném publikačním profilu, musíte si před nasazením do Azure stáhnout a importovat profil publikování pro vaše předplatné. Viz část **nasazení aplikace do Azure** v tématu [sestavení a nasazení Node.js aplikace do cloudové služby Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/) .
   >
   >

Vaše aplikace je teď spuštěná v Azure a může přenášet zprávy konverzace mezi různými klienty pomocí Socket.IO.

> [!NOTE]
> Pro zjednodušení je tato ukázka omezená na konverzaci mezi uživateli připojenými ke stejné instanci. To znamená, že pokud cloudová služba vytvoří dvě instance role pracovního procesu, budou moct uživatelé hovořit jenom s ostatními připojenými ke stejné instanci role pracovního procesu. Pokud chcete aplikaci škálovat tak, aby fungovala s více instancemi role, můžete použít technologii, jako je Service Bus ke sdílení stavu úložiště Socket.IO napříč instancemi. Příklady najdete v tématu věnovaném ukázkám použití Service Bus front a témat v [sadě Azure SDK pro Node.js úložiště GitHub](https://github.com/WindowsAzure/azure-sdk-for-node).
>
>

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak vytvořit základní chatovací aplikaci hostovanou v cloudové službě Azure. Informace o tom, jak hostovat tuto aplikaci na webu Azure, najdete v tématu [Vytvoření aplikace Node.js chatu pomocí Socket.IO na webu Azure][chatwebsite].

Další informace najdete také v centru pro [ vývojářeNode.js](/azure/developer/javascript/).

[chatwebsite]: ./cloud-services-nodejs-develop-deploy-app.md

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Úložiště GitHub Socket.IO]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png