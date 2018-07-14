---
title: Aplikace Node.js pomocí Socket.io – Azure
description: Zjistěte, jak pomocí socket.io v aplikaci node.js hostované v Azure.
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7f9435e0-7732-4aa1-a4df-ea0e894b847f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: 0fae47f248d5662b69a0d1a12c82b7ded33badd6
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001979"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Vytvoření chatovací aplikace v Node.js pomocí Socket.IO na cloudové službě Azure

Socket.IO nabízí komunikaci v reálném čase mezi node.js serverem a klienty. Tento kurz vás provede hostování soketu. Vstupně-výstupních operací na základě chatovací aplikace v Azure. Další informace o Socket.IO najdete v tématu [socket.io](http://socket.io).

Snímek obrazovky dokončené aplikace je nižší než:

![Okno prohlížeče zobrazující služby hostované v Azure][completed-app]  

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že jsou nainstalované následující produktů a verzí k úspěšnému dokončení příkladu v tomto článku:

* Nainstalovat [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Instalovat [Node.js](https://nodejs.org/download/)
* Nainstalujte [Python verze 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Vytvořit projekt cloudové služby
Následujícím postupem se vytvoří projekt cloudové služby, který bude hostitelem aplikace Socket.IO.

1. Z **nabídky Start** nebo **úvodní obrazovku**, vyhledejte **prostředí Windows PowerShell**. A konečně, klikněte pravým tlačítkem na **prostředí Windows PowerShell** a vyberte **spustit jako správce**.
   
    ![Ikona Azure Powershellu][powershell-menu]
2. Vytvořte adresář s názvem **c:\\uzel**. 
   
        PS C:\> md node
3. Přejděte do adresáře **c:\\uzel** adresáře
   
        PS C:\> cd node
4. Zadejte následující příkazy k vytvoření nového řešení s názvem **chatapp** a role pracovního procesu s názvem **WorkerRole1**:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    Zobrazí se následující odpověď:
   
    ![Výstup nové azureservice a přidat azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Stáhnout příklad chatu
Pro tento projekt, použijeme příklad chat z [úložiště Socket.IO GitHub]. Proveďte následující kroky stáhnout příklad a přidat do projektu, který jste předtím vytvořili.

1. Vytvořit místní kopii úložiště pomocí **klonování** tlačítko. Můžete také použít **ZIP** tlačítko a stáhněte si projektu.
   
   ![Zobrazení okna prohlížeče https://github.com/LearnBoost/socket.io/tree/master/examples/chat, zvýrazněnou ikonou stahování ZIP][chat-example-view]
2. Až přijedete přejděte struktury adresářů v místním úložišti **příklady\\chat** adresáře. Kopírovat obsah tohoto adresáře **C:\\uzel\\chatapp\\WorkerRole1** adresář vytvořený dříve.
   
   ![Průzkumník zobrazení obsahu v příkladech\\chat directory extrahují z archivu][chat-contents]
   
   Souborům zkopírovaným ze jsou zvýrazněné položky ve výše uvedeném snímku obrazovky **příklady\\chat** adresáře
3. V **C:\\uzel\\chatapp\\WorkerRole1** adresáře, odstranit **server.js** souboru a potom přejmenujte **app.js** do souboru **server.js**. Tato operace odebere výchozí **server.js** souboru vytvořené dříve **přidat AzureNodeWorkerRole** rutiny a nahradí soubor aplikace z příkladu konverzace.

### <a name="modify-serverjs-and-install-modules"></a>Upravit Server.js a nainstalujte moduly
Před testováním aplikace spustila v emulátoru Azure, musíte Usnadňujeme malými změnami. Proveďte následující kroky v souboru server.js:

1. Otevřít **server.js** souboru v sadě Visual Studio nebo libovolného textového editoru.
2. Najít **závislostí modulů** na začátku server.js a u řádku obsahujícím operátor **sio = require('.. //.. lib//Socket.IO ")** k **sio = require('socket.io')** jak je znázorněno níže:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Pokud chcete mít jistotu, aplikace naslouchá na správný port, otevřete server.js v programu Poznámkový blok nebo svém oblíbeném editoru a potom změňte následující řádek tak, že nahradíte **3000** s **process.env.port** jak je znázorněno níže:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

Po uložení změn **server.js**, postupujte následovně pro instalaci požadované moduly a otestujte aplikaci v emulátoru Azure:

1. Pomocí **prostředí Azure PowerShell**, přejděte do adresáře **C:\\uzel\\chatapp\\WorkerRole1** adresáře a použijte zadáním následujícího příkazu nainstalujte moduly vyžaduje tato aplikace:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Tím se nainstaluje moduly uvedené v souboru package.json. Po dokončení příkazu byste měli vidět výstup podobný následujícímu:
   
   ![Příkaz instalovat výstup z npm][The-output-of-the-npm-install-command]
2. Protože v tomto příkladu byla původně součástí úložiště Socket.IO GitHub a přímo odkazovanými Socket.IO knihovny pomocí relativní cesty, Socket.IO neexistuje odkaz v souboru package.json, proto jsme musí nainstalovat pomocí následujícího příkazu:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Testování a nasazení
1. Spusťte emulátor pomocí následujícího příkazu:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Pokud narazíte na problémy se spuštěním emulátor, např.: Start-AzureEmulator: došlo k neočekávané chybě.  Podrobnosti: Došlo k neočekávané chybě objekt komunikace, System.ServiceModel.Channels.ServiceChannel, nelze použít pro komunikaci vzhledem k tomu, že je v chybovém stavu.
   
      znovu nainstalujte AzureAuthoringTools v 2.7.1 a AzureComputeEmulator v 2.7 – Ujistěte se, že odpovídá této verzi.
   >
   >


2. Otevřete prohlížeč a přejděte do **http://127.0.0.1**.
3. Když se otevře okno prohlížeče, zadat přezdívku a pak stiskněte enter.
   To vám umožní publikovat zprávy jako konkrétní přezdívku. K otestování funkce více uživatelů, otevřete další okna prohlížeče pomocí stejné adresy URL a zadejte jiné zástupné názvy.
   
   ![Zobrazení zprávy chatu z uživatel1, uživatel2 tak dvě okna prohlížeče](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Po otestování aplikace, zastavte emulátor vydání následujícího příkazu:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Chcete-li nasadit aplikaci do Azure, použijte **Publish-AzureServiceProject** rutiny. Příklad:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Nezapomeňte použít jedinečný název, jinak se proces publikování nezdaří. Po dokončení nasazení se v prohlížeči otevřete a přejděte do nasazené služby.
   > 
   > Pokud obdržíte chybu s informacemi o tom, že název zadaného předplatného neexistuje v profilu publikování importované, musíte stáhnout a naimportovat profil publikování pro vaše předplatné před nasazením do Azure. Najdete v článku **nasazení aplikace do Azure** část [sestavovat a nasazovat aplikace v Node.js do cloudové služby Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Okno prohlížeče zobrazující služby hostované v Azure][completed-app]
   
   > [!NOTE]
   > Pokud obdržíte chybu s informacemi o tom, že název zadaného předplatného neexistuje v profilu publikování importované, musíte stáhnout a naimportovat profil publikování pro vaše předplatné před nasazením do Azure. Najdete v článku **nasazení aplikace do Azure** část [sestavovat a nasazovat aplikace v Node.js do cloudové služby Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Vaše aplikace je nyní spuštěna v Azure a může přenášet zprávy chatu mezi různé klienty pomocí Socket.IO.

> [!NOTE]
> Pro jednoduchost je omezený na konverzace mezi uživateli, které jsou připojené do stejné instance této ukázce. To znamená, že pokud cloudovou službu vytvoří dvě instance role pracovního procesu, uživatele pouze by se do konverzace s ostatními připojené do stejné instance role pracovního procesu. Škálování aplikace pro práci s více instancemi role, můžete použít technologie, jako je Service Bus ke sdílení ukládání stavu Socket.IO napříč instancemi. Příklady najdete v ukázkách využití fronty služby Service Bus a témat v [sady Azure SDK pro úložiště Node.js GitHub](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak vytvořit základní chatovací aplikaci hostované v cloudové službě Azure. Zjistěte, jak hostovat tuto aplikaci na webu Azure, najdete v článku [sestavení aplikace Chat v Node.js pomocí Socket.IO na webu Azure][chatwebsite].

Další informace najdete v tématu taky [středisko pro vývojáře Node.js](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: http://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Úložiště Socket.IO GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png


