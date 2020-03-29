---
title: Aplikace Node.js pomocí Socket.io – Azure
description: Přečtěte si, jak používat socket.io v aplikaci node.js hostované v Azure.
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 0b515c630d8a3539cdab1df64b1925e9fcaf206e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360765"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Vytvoření chatovací aplikace Node.js s Socket.IO ve cloudové službě Azure

Socket.IO poskytuje komunikaci v reálném čase mezi serverem node.js a klienty. Tento kurz vás provede hostováním soketu. Aplikace chatu založená na iO v Azure. Další informace o Socket.IO naleznete v [tématu socket.io](https://socket.io).

Snímek obrazovky dokončené aplikace je níže:

![Okno prohlížeče zobrazující službu hostovací v Azure][completed-app]  

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že jsou nainstalovány následující produkty a verze, které úspěšně dokončí příklad v tomto článku:

* Instalace [sady Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Instalace [souboru Node.js](https://nodejs.org/download/)
* Instalace [Pythonu verze 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Vytvoření projektu cloudových služeb
Následující kroky vytvořit projekt cloudové služby, který bude hostitelem Socket.IO aplikace.

1. V **nabídce Start** nebo **na úvodní obrazovce**vyhledejte prostředí Windows **PowerShell**. Nakonec klepněte pravým tlačítkem myši na **prostředí Windows PowerShell** a vyberte **příkaz Spustit jako správce**.
   
    ![Ikona Azure PowerShellu][powershell-menu]
2. Vytvořte adresář s názvem **\\c: node**. 
   
        PS C:\> md node
3. Změna adresářů do adresáře **uzlu c:\\**
   
        PS C:\> cd node
4. Zadáním následujících příkazů vytvořte nové řešení s názvem **chatapp** a roli pracovního procesu s názvem **WorkerRole1**:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    Zobrazí se následující odpověď:
   
    ![Výstup nových azureservice a add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Stáhněte si příklad chatu
Pro tento projekt použijeme příklad chatu z [úložiště Socket.IO GitHub]. Provedením následujících kroků stáhněte příklad a přidejte jej do projektu, který jste dříve vytvořili.

1. Vytvořte místní kopii úložiště pomocí tlačítka **Klonování.** Projekt můžete stáhnout také pomocí tlačítka **ZIP.**
   
   ![Zobrazení https://github.com/LearnBoost/socket.io/tree/master/examples/chatokna prohlížeče se zvýrazněnou ikonou stahování ZIP](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Procházet adresářovou strukturu místního úložiště, dokud nedorazíte k **adresáři\\chatu příkladů.** Zkopírujte obsah tohoto adresáře do dříve vytvořeného adresáře **\\\\chatapp\\WorkerRole1 uzlu C.**
   
   ![Explorer, zobrazující obsah příkladů adresáře\\chatu extrahovaného z archivu][chat-contents]
   
   Zvýrazněné položky na obrázku výše jsou soubory zkopírované z **adresáře chatu\\příklady**
3. V adresáři **\\C:\\node\\chatapp WorkerRole1** odstraňte soubor **server.js** a přejmenujte soubor **app.js** na **server.js**. Tím odeberete výchozí soubor **server.js** vytvořený dříve rutinou **Add-AzureNodeWorkerRole** a nahradíte jej souborem aplikace z příkladu chatu.

### <a name="modify-serverjs-and-install-modules"></a>Úprava souboru Server.js a instalačních modulů
Před testováním aplikace v emulátoru Azure, musíme provést některé drobné úpravy. V souboru server.js proveďte následující kroky:

1. Otevřete soubor **server.js** v sadě Visual Studio nebo v libovolném textovém editoru.
2. Nazačátku souboru server.js vyhledejte oddíl **Závislosti modulu** a změňte řádek obsahující **sio = require('.. //.. lib//socket.io')** na **sio = require('socket.io'),** jak je znázorněno níže:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Chcete-li zajistit, aby aplikace naslouchala na správném portu, otevřete server.js v poznámkovém bloku nebo oblíbeném editoru a změňte následující řádek nahrazením **3000** **process.env.port,** jak je znázorněno níže:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

Po uložení změn na **server.js**, použijte následující kroky k instalaci požadovaných modulů a otestujte aplikaci v emulátoru Azure:

1. Pomocí **Azure PowerShellu**změňte adresáře na adresář **\\\\ChatApp\\WorkerRole1 uzlu C** a nainstalujte moduly vyžadované touto aplikací pomocí následujícího příkazu:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Tím nainstalujete moduly uvedené v souboru package.json. Po dokončení příkazu by se měl zobrazit výstup, který vypadá přibližně takto:
   
   ![Výstup příkazu npm install][The-output-of-the-npm-install-command]
2. Vzhledem k tomu, že tento příklad byl původně součástí Socket.IO úložiště GitHub a přímo odkazoval na knihovnu Socket.IO relativní cestou, Socket.IO nebyl odkazován v souboru package.json, takže jej musíme nainstalovat vydáním následujícího příkazu:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Testování a nasazení
1. Spusťte emulátor vydáním následujícího příkazu:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Pokud narazíte na problémy se spuštěním emulátoru, například:Start-AzureEmulator : Došlo k neočekávané chybě.  Podrobnosti: Došlo k neočekávané chybě Komunikační objekt System.ServiceModel.Channels.ServiceChannel nelze použít pro komunikaci, protože je ve stavu Faulted.
   > 
   > Přeinstalujte AzureAuthoringTools v 2.7.1 a AzureComputeEmulator v 2.7 – ujistěte se, že verze odpovídá.

2. Otevřete prohlížeč a **http://127.0.0.1**přejděte na .
3. Po otevření okna prohlížeče zadejte přezdívku a stiskněte klávesu ENTER.
   To vám umožní posílat zprávy jako konkrétní přezdívku. Chcete-li otestovat funkce pro více uživatelů, otevřete další okna prohlížeče pomocí stejné adresy URL a zadejte různé přezdívky.
   
   ![Dvě okna prohlížeče zobrazující zprávy chatu od uživatele 1 a uživatele2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Po testování aplikace zastavte emulátor vydáním následujícího příkazu:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Chcete-li nasadit aplikaci do Azure, použijte rutinu **Publish-AzureServiceProject.** Například:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Ujistěte se, že používáte jedinečný název, jinak se proces publikování nezdaří. Po dokončení nasazení se prohlížeč otevře a přejde na nasazenou službu.
   > 
   > Pokud se zobrazí chyba oznamující, že zadaný název předplatného neexistuje v importovaném profilu publikování, musíte před nasazením do Azure stáhnout a importovat profil publikování pro vaše předplatné. Podívejte se na část **Nasazení aplikace do Azure** v [buildu a nasazení aplikace Node.js do cloudové služby Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Okno prohlížeče zobrazující službu hostovací v Azure][completed-app]
   
   > [!NOTE]
   > Pokud se zobrazí chyba oznamující, že zadaný název předplatného neexistuje v importovaném profilu publikování, musíte před nasazením do Azure stáhnout a importovat profil publikování pro vaše předplatné. Podívejte se na část **Nasazení aplikace do Azure** v [buildu a nasazení aplikace Node.js do cloudové služby Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Vaše aplikace je teď spuštěná v Azure a může přenášet chatové zprávy mezi různými klienty pomocí Socket.IO.

> [!NOTE]
> Pro jednoduchost je tato ukázka omezena na chatování mezi uživateli připojenými ke stejné instanci. To znamená, že pokud cloudová služba vytvoří dvě instance role pracovního procesu, uživatelé budou moci chatovat pouze s ostatními připojenými ke stejné instanci role pracovního procesu. Chcete-li škálovat aplikaci tak, aby fungovala s více instancemi rolí, můžete použít technologii, jako je Service Bus, ke sdílení stavu Socket.IO úložiště mezi instancemi. Příklady naleznete v ukázkách využití fronty a témata služby Service Bus v azure [sdk pro úložiště Node.js GitHub](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili, jak vytvořit základní chatovací aplikaci hostovací v cloudové službě Azure. Informace o tom, jak tuto aplikaci hostovat na webu Azure, najdete v [tématu Vytvoření chatovací aplikace Node.js s Socket.IO na webu Azure][chatwebsite].

Další informace naleznete také v [Centru pro vývojáře node.js](https://docs.microsoft.com/azure/javascript/).

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO úložiště GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png





