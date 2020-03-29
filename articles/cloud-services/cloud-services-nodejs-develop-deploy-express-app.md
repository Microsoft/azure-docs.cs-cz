---
title: Vytvoření a nasazení aplikace Node.js Express do Cloudových služeb Azure
titleSuffix: Azure Cloud Services
description: Vytvoření a nasazení aplikace Express.js v souboru Node.js do Cloudových služeb Azure
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 79a998930a384420b22add8825ee4b2269eb4539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360748"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Vytvoření a nasazení webové aplikace Node.js pomocí expressu v cloudových službách Azure

Node.js obsahuje minimální sadu funkcí v core runtime.
Vývojáři často používají moduly třetích stran k poskytování dalších funkcí při vývoji aplikace Node.js. V tomto kurzu vytvoříte novou aplikaci pomocí [express](https://github.com/expressjs/express) modulu, který poskytuje MVC framework pro vytváření webových aplikací Node.js.

Snímek obrazovky dokončené aplikace je níže:

![Webový prohlížeč zobrazující vítejte v aplikaci Express v Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Vytvoření projektu cloudových služeb
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Chcete-li vytvořit nový projekt cloudových služeb s názvem expressapp, proveďte následující kroky:

1. V **nabídce Start** nebo **na úvodní obrazovce**vyhledejte prostředí Windows **PowerShell**. Nakonec klepněte pravým tlačítkem myši na **prostředí Windows PowerShell** a vyberte **příkaz Spustit jako správce**.
   
    ![Ikona Azure PowerShellu](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Změňte adresáře na adresář **uzlu c:\\** a zadejte následující příkazy a vytvořte nové řešení s názvem **expressapp** a webovou roli s názvem **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Ve výchozím nastavení **add-AzureNodeWebRole** používá starší verzi Node.js. Příkaz **Set-AzureServiceProjectRole** výše dává Azure pokyn k použití v0.10.21 uzlu.  Všimněte si, že parametry rozlišují malá a velká písmena.  Můžete ověřit, že byla vybrána správná verze souboru Node.js, a to kontrolou **vlastnosti engines** v **souboru WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Instalace aplikace Express
1. Nainstalujte generátor Express vydáním následujícího příkazu:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    Výstup příkazu npm by měl vypadat podobně jako výsledek níže. 
   
    ![Prostředí Windows PowerShell zobrazující výstup příkazu npm install express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Změňte adresáře do adresáře **WebRole1** a pomocí příkazu express vygenerujte novou aplikaci:
   
        PS C:\node\expressapp\WebRole1> express
   
    Budete vyzváni k přepsání dřívější aplikace. Chcete-li pokračovat, zadejte **y** nebo **ano.** Express vygeneruje soubor app.js a strukturu složek pro vytváření aplikace.
   
    ![Výstup expresního příkazu](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Chcete-li nainstalovat další závislosti definované v souboru package.json, zadejte následující příkaz:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![Výstup příkazu npm install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Pomocí následujícího příkazu zkopírujte soubor **bin/www** do **souboru server.js**. Je to proto, aby cloudová služba mohla najít vstupní bod pro tuto aplikaci.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Po dokončení tohoto příkazu byste měli mít soubor **server.js** v adresáři WebRole1.
5. Upravte **soubor server.js** a odeberte jeden ze znaků z následujícího řádku.
   
       var app = require('../app');
   
   Po provedení této změny by měl řádek vypadat následovně.
   
       var app = require('./app');
   
   Tato změna je vyžadována, protože jsme soubor (dříve **bin/www,)** přesunuli do stejného adresáře, jako je vyžadován soubor aplikace. Po provedení této změny uložte soubor **server.js.**
6. Ke spuštění aplikace v emulátoru Azure použijte následující příkaz:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Webová stránka obsahující vítejte vyjádřit.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Úprava zobrazení
Nyní upravte zobrazení tak, aby se zobrazila zpráva "Vítejte v expresu v Azure".

1. Chcete-li otevřít soubor index.jade, zadejte následující příkaz:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![Obsah souboru index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade je výchozí modul zobrazení používaný aplikacemi Express. Další informace o modulu zobrazení [http://jade-lang.com][http://jade-lang.com]Jade naleznete v tématu .
2. Upravte poslední řádek textu připojením **v Azure**.
   
   ![Soubor index.jade, poslední řádek zní: \#p Vítejte na {title} v Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Uložte soubor a ukončete poznámkový blok.
4. Aktualizujte prohlížeč a uvidíte změny.
   
   ![Okno prohlížeče, stránka obsahuje Vítejte na Express v Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Po testování aplikace použijte rutinu **Stop-AzureEmulator** k zastavení emulátoru.

## <a name="publishing-the-application-to-azure"></a>Publikování aplikace do Azure
V okně Azure PowerShell použijte rutinu **Publish-AzureServiceProject** k nasazení aplikace do cloudové služby.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Po dokončení operace nasazení se prohlížeč otevře a zobrazí webovou stránku.

![Webový prohlížeč zobrazující stránku Express. Adresa URL označuje, že je teď hostovaná v Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Další kroky
Další informace najdete ve [Středisku pro vývojáře Node.js](https://docs.microsoft.com/azure/javascript/).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com





