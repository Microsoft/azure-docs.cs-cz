---
title: Aplikace Node.js pomocí Socket.io – Azure
description: Další informace o použití socket.io v aplikaci node.js hostované v Azure.
services: cloud-services
documentationcenter: nodejs
author: thraka
manager: timlt
editor: ''
ms.assetid: 7f9435e0-7732-4aa1-a4df-ea0e894b847f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: adegeo
ms.openlocfilehash: b5d706f82dd41f0c271aa5f67676bba08cceb3b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608589"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Vytvoření chatovací aplikace Node.js pomocí Socket.IO ve službě Azure Cloud Service

Socket.IO poskytuje v reálném čase komunikaci mezi serverem node.js a klienty. Tento kurz vás provede hostování soketu. Vstupně-výstupních operací na základě chatovací aplikace v Azure. Další informace o Socket.IO najdete v tématu [socket.io](http://socket.io).

Zde je snímek obrazovky dokončené aplikace:

![Okno prohlížeče zobrazující služby hostované v Azure][completed-app]  

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že jsou nainstalovány následující produkty a verze úspěšné dokončení v příkladu v tomto článku:

* Nainstalovat [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Instalovat [Node.js](https://nodejs.org/download/)
* Nainstalujte [Python verze 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Vytvoření projektu cloudové služby
Následující postup vytvořit projekt cloudové služby, který bude hostitelem Socket.IO aplikace.

1. Z **nabídce Start** nebo **obrazovce Start**, vyhledejte **prostředí Windows PowerShell**. Nakonec klikněte pravým tlačítkem na **prostředí Windows PowerShell** a vyberte **spustit jako správce**.
   
    ![Azure PowerShell – ikona][powershell-menu]
2. Vytvořte adresář s názvem **c:\\uzlu**. 
   
        PS C:\> md node
3. Přejděte do adresáře **c:\\uzlu** adresáře
   
        PS C:\> cd node
4. Zadejte následující příkazy k vytvoření nové řešení s názvem **chatapp** a roli pracovního procesu s názvem **WorkerRole1**:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    Zobrazí se následující odpověď:
   
    ![Výstup nové azureservice a přidat azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Stáhněte si příklad chatu
Pro tento projekt použijeme příklad chat z [úložiště Socket.IO GitHub]. Proveďte následující kroky ke stažení v příkladu a přidat jej do projektu, které jste vytvořili.

1. Vytvořit místní kopii úložiště pomocí **klon** tlačítko. Můžete také používat **ZIP** tlačítko Stáhnout projektu.
   
   ![Zobrazení okna prohlížeče https://github.com/LearnBoost/socket.io/tree/master/examples/chat, zvýrazněnou ikonou stáhnout ZIP][chat-example-view]
2. Až přijedete do přejděte struktura adresářů místní úložiště **příklady\\chat** adresáře. Kopírovat obsah tohoto adresáře na **C:\\uzlu\\chatapp\\WorkerRole1** adresář vytvořený.
   
   ![Průzkumník, zobrazení obsahu v příkladech\\directory chat extrahovat z archivu][chat-contents]
   
   Zvýrazněné položky v výše uvedený snímek obrazovky jsou souborům zkopírovaným ze **příklady\\chat** adresáře
3. V **C:\\uzlu\\chatapp\\WorkerRole1** adresáře, odstraňte **server.js** souboru a přejmenujte **app.js** do souboru **server.js**. Odebere výchozí **server.js** soubor vytvořený dříve **přidat AzureNodeWorkerRole** rutiny a nahradí soubor aplikace z příkladu konverzace.

### <a name="modify-serverjs-and-install-modules"></a>Upravit Server.js a nainstalujte moduly
Před testováním aplikace v Azure emulátoru, jsme musí provést některé menší změny. Proveďte následující kroky v souboru server.js:

1. Otevřete **server.js** souborů v sadě Visual Studio nebo libovolného textového editoru.
2. Najít **modulu závislosti** části na začátku server.js a změňte řádek obsahující **sio = require('.. //.. lib//Socket.IO')** k **sio = require('socket.io')** jak je uvedeno níže:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Aby aplikace naslouchá na správný port, otevřete server.js v poznámkovém bloku nebo svém oblíbeném editoru a potom změňte následující řádek tak, že nahradíte **3000** s **process.env.port** jak je uvedeno níže:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

Po uložení změn do **server.js**, použijte následující postup k instalaci požadované moduly a otestujte aplikaci v emulátoru Azure:

1. Pomocí **prostředí Azure PowerShell**, přejděte do adresáře **C:\\uzlu\\chatapp\\WorkerRole1** adresáře a použijte následující příkaz pro instalaci modulů Tato aplikace vyžaduje:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Nainstaluje uvedených v souboru package.json modulů. Po dokončení příkazu, byste měli vidět výstup podobný následujícímu:
   
   ![Příkaz instalovat výstup npm][The-output-of-the-npm-install-command]
2. Vzhledem k tomu, že v tomto příkladu byla původně součástí úložiště Socket.IO GitHub a knihovně Socket.IO přímo odkazuje relativní cestu, nebyl v souboru package.json, odkazuje Socket.IO, proto ji musíte nainstalovat po vydání následujícího příkazu:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Testování a nasazení
1. Spusťte emulátor po vydání následujícího příkazu:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Pokud narazíte na potíže se spuštěním emulátoru, např.: spuštění AzureEmulator: došlo k neočekávané chybě.  Podrobnosti: Došlo k neočekávané chybě objekt komunikace, System.ServiceModel.Channels.ServiceChannel, nelze použít pro komunikaci protože je v chybném stavu.
   
      Přeinstalujte AzureAuthoringTools v 2.7.1 a AzureComputeEmulator v 2.7 – Ujistěte se, že odpovídá této verze.
   >
   >


2. Otevřete prohlížeč a přejděte do **http://127.0.0.1**.
3. Když se otevře okno prohlížeče, zadejte přezdívku a potom stiskněte klávesu enter.
   To vám umožní k odeslání zprávy jako konkrétní přezdívka. K testování funkčnosti více uživatelů, spusťte další okna prohlížeče pomocí stejné adresy URL a zadejte jiné přezdívky.
   
   ![Zobrazení chatovací zprávy z uživatel1 a uživatel2 dvě okna prohlížeče](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Po testování aplikace, zastavte emulátoru vydání následujícího příkazu:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Chcete-li nasadit aplikaci do Azure, použijte **Publish-AzureServiceProject** rutiny. Příklad:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Nezapomeňte použít jedinečný název, jinak se proces publikování nezdaří. Po dokončení nasazení se v prohlížeči otevřete a přejděte do nasazené služby.
   > 
   > Pokud se zobrazí chyba oznamující, že název zadané předplatné neexistuje v profilu publikování importované, musíte stáhnout a naimportovat profil publikování pro vaše předplatné před nasazením do Azure. Najdete v článku **nasazení aplikace do Azure** části [sestavení a nasazení aplikace Node.js ve službě Azure Cloud Service](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Okno prohlížeče zobrazující služby hostované v Azure][completed-app]
   
   > [!NOTE]
   > Pokud se zobrazí chyba oznamující, že název zadané předplatné neexistuje v profilu publikování importované, musíte stáhnout a naimportovat profil publikování pro vaše předplatné před nasazením do Azure. Najdete v článku **nasazení aplikace do Azure** části [sestavení a nasazení aplikace Node.js ve službě Azure Cloud Service](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Vaše aplikace je nyní spuštěna v Azure a může přenášet zprávy chat mezi různé klienty pomocí Socket.IO.

> [!NOTE]
> Pro jednoduchost Tato ukázka je omezený na chatování mezi uživatele připojené ke stejné instanci. To znamená, že pokud cloudové službě vytvoří dvě instance role pracovního procesu, uživatele bude moci pouze chat s ostatními připojení na stejnou instanci role pracovního procesu. Škálování aplikace pro práci s více instancí role, můžete použít technologie, jako je Service Bus ke sdílení ukládání stavu Socket.IO napříč instancemi. Příklady najdete v tématu ukázky využití fronty služby Service Bus a témat v [Azure SDK pro Node.js Githubu úložiště](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak vytvořit základní chatovací aplikace hostované na cloudové služby Azure. Zjistěte, jak hostovat tuto aplikaci na webu Azure, najdete v tématu [sestavení aplikace Chat v Node.js se Socket.IO na webovou stránku Azure][chatwebsite].

Další informace naleznete také [středisku pro vývojáře Node.js](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

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


