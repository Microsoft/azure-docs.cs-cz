---
title: Sestavení a nasazení aplikace Node.js Express do Azure Cloud Services
description: Vytvoření a nasazení Express.js aplikace v Node.js do Azure Cloud Services
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 24f8e7ef-e90d-4554-9b1e-a9b31d5824e5
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: ac62baae2ececf257d22f8220d472c1bcb5ee082
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009340"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Vytvoření a nasazení webové aplikace Node.js pomocí Expressu ve Azure Cloud Services

Node.js zahrnuje minimální sada funkcí v modulu runtime jádra.
3. stran moduly vývojáři často používají k poskytnutí dalších funkcí při vývoji aplikace v Node.js. V tomto kurzu vytvoříte novou aplikaci pomocí [Express](https://github.com/expressjs/express) modul, který poskytuje rozhraní MVC pro vytváření webových aplikací Node.js.

Snímek obrazovky dokončené aplikace je nižší než:

![Webový prohlížeč zobrazující uvítací Express v Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Vytvořit projekt cloudové služby
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Proveďte následující kroky a vytvoříte nový projekt cloudové služby s názvem "expressapp":

1. Z **nabídky Start** nebo **úvodní obrazovku**, vyhledejte **prostředí Windows PowerShell**. A konečně, klikněte pravým tlačítkem na **prostředí Windows PowerShell** a vyberte **spustit jako správce**.
   
    ![Ikona Azure Powershellu](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Přejděte do adresáře **c:\\uzel** adresář a potom zadejte následující příkazy k vytvoření nového řešení s názvem **expressapp** a webová role s názvem **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Ve výchozím nastavení **Add-AzureNodeWebRole** používá starší verzi Node.js. **Set-AzureServiceProjectRole** výše uvedeného příkazu instruuje Azure používat v0.10.21 uzlu.  Všimněte si, že parametry jsou malá a velká písmena.  Můžete ověřit kontrolou byla vybrána správná verze Node.js **moduly** vlastnost **WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Nainstalujte Express
1. Pomocí následujícího příkazu nainstalujte generátor Express:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    Výstup příkazu npm by měl vypadat podobně jako výsledek níže. 
   
    ![Prostředí Windows PowerShell zobrazení výstupu npm nainstalujte express příkazu.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Přejděte do adresáře **WebRole1** adresáře a použijte příkaz express vygenerujte novou aplikaci:
   
        PS C:\node\expressapp\WebRole1> express
   
    Zobrazí výzva k přepsání starší aplikace. Zadejte **y** nebo **Ano** pokračujte. Express bude generovat souboru app.js a strukturu složek pro tvorbu aplikací.
   
    ![Výstup příkazu express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Pokud chcete nainstalovat další závislosti, které jsou definovány v souboru package.json, zadejte následující příkaz:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![Příkaz instalovat výstup z npm](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Použijte následující příkaz pro kopírování **bin/www** do souboru **server.js**. Jedná se proto cloudovou službu můžete najít vstupní bod pro tuto aplikaci.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Po dokončení tohoto příkazu byste měli mít **server.js** soubor v adresáři řádku webová role 1.
5. Upravit **server.js** k odebrání jednoho z "." znaky z následujícího řádku.
   
       var app = require('../app');
   
   Po provedení této změny by měl vypadat následovně řádku.
   
       var app = require('./app');
   
   Tato změna se vyžaduje, protože jsme přešli soubor (dříve **bin/www**,) do stejného adresáře jako soubor aplikace je povinné. Po provedení této změny, uložte **server.js** souboru.
6. Pomocí následujícího příkazu spusťte aplikaci v emulátoru Azure:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Webová stránka obsahující Vítá vás express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Úprava zobrazení
Nyní upravte zobrazení tak, aby se zpráva "Vítejte do Express v Azure".

1. Zadejte následující příkaz pro otevření souboru index.jade:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![Obsah souboru index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade je výchozí modul zobrazení používané aplikacemi Express. Další informace o Jade zobrazovací modul, naleznete v tématu [ http://jade-lang.com ] [ http://jade-lang.com].
2. Upravit poslední řádek textu přidáním **v Azure**.
   
   ![Načte poslední řádek souboru index.jade: p Vítá vás \#{title} v Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Uložte soubor a ukončete Poznámkový blok.
4. Aktualizujte svůj prohlížeč a zobrazí se provedené změny.
   
   ![Okno prohlížeče, obsahuje na stránce Vítejte Express v Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Po otestování aplikace, použijte **Stop-AzureEmulator** rutiny pro ukončení emulátoru.

## <a name="publishing-the-application-to-azure"></a>Publikování aplikace do Azure
V okně prostředí Azure PowerShell použít **Publish-AzureServiceProject** rutiny pro tuto aplikaci nasadit do cloudové služby

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Po dokončení operace nasazení se prohlížeči otevřít a zobrazit webovou stránku.

![Webový prohlížeč zobrazující stránku Express. Adresa URL označuje, že teď je hostovaný v Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Další postup
Další informace najdete ve [Středisku pro vývojáře Node.js](https://docs.microsoft.com/en-us/javascript/azure/?view=azure-node-latest).

[Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: http://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com


