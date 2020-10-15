---
title: Sestavení a nasazení aplikace Node.js Express do Azure Cloud Services
titleSuffix: Azure Cloud Services
description: Tento kurz slouží k vytvoření nové aplikace pomocí modulu Express, který poskytuje architekturu MVC pro vytváření Node.js webových aplikací.
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.custom: devx-track-js
ms.openlocfilehash: 7ecaccf36988e94d0cb1114bd04c5d571d4fe86d
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071859"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Sestavení a nasazení webové aplikace Node.js pomocí Expressu v Azure Cloud Services

Node.js obsahuje minimální sadu funkcí v jádru modulu runtime.
Vývojáři často používají moduly třetích stran k poskytování dalších funkcí při vývoji aplikace Node.js. V tomto kurzu vytvoříte novou aplikaci pomocí modulu [Express](https://github.com/expressjs/express) , který poskytuje architekturu MVC pro vytváření Node.js webových aplikací.

Snímek obrazovky dokončené aplikace je níže:

![Webový prohlížeč, který zobrazuje úvodní přehledy pro Express v Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Vytvoření projektu cloudové služby
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Provedením následujících kroků vytvořte nový projekt cloudové služby s názvem ' expressapp ':

1. V **nabídce Start** nebo na **obrazovce Start**vyhledejte **Windows PowerShell**. Nakonec klikněte pravým tlačítkem myši na **Windows PowerShell** a vyberte **Spustit jako správce**.

    ![Ikona Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Přejděte do adresáře **c: \\ Node** a potom zadejte následující příkazy k vytvoření nového řešení s názvem **expressapp** a webovou roli s názvem **WebRole1**:

   ```powershell
   PS C:\node> New-AzureServiceProject expressapp
   PS C:\Node\expressapp> Add-AzureNodeWebRole
   PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   ```

   > [!NOTE]
   > Ve výchozím nastavení používá **příkaz Add-AzureNodeWebRole** starší verzi Node.js. Příkaz **set-AzureServiceProjectRole** výše dává pokyn pro Azure k použití v 0.10.21 uzlu.  Všimněte si, že v parametrech jsou rozlišována malá a velká písmena.  Správnou verzi Node.js můžete ověřit tak, že zkontrolujete vlastnost **Engines** v **WebRole1\package.jsna**.
>
>

## <a name="install-express"></a>Instalace aplikace Express
1. Nainstalujte generátor Express vyvoláním následujícího příkazu:

    ```powershell
    PS C:\node\expressapp> npm install express-generator -g
    ```

    Výstup příkazu npm by měl vypadat podobně jako v níže uvedeném výsledku.

    ![Prostředí Windows PowerShell zobrazující výstup příkazu npm Install Express](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Přejděte do adresáře **WebRole1** a pomocí příkazu Express Vygenerujte novou aplikaci:

    ```powershell
    PS C:\node\expressapp\WebRole1> express
    ```

    Zobrazí se výzva k přepsání předchozí aplikace. Pokračujte zadáním **hodnoty y** nebo **Ano** . Express vygeneruje soubor app.js a strukturu složek pro sestavení aplikace.

    ![Výstup příkazu Express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Chcete-li nainstalovat další závislosti definované v package.jssouboru, zadejte následující příkaz:

    ```powershell
    PS C:\node\expressapp\WebRole1> npm install
    ```

   ![Výstup příkazu pro instalaci npm](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. K zkopírování souboru **bin/www** do **server.js**použijte následující příkaz. To znamená, že cloudová služba může najít vstupní bod pro tuto aplikaci.

    ```powershell
    PS C:\node\expressapp\WebRole1> copy bin/www server.js
    ```

   Po dokončení tohoto příkazu byste měli mít soubor **server.js** v adresáři WebRole1.
5. Upravte **server.js** pro odebrání jednoho ze znaků '. ' z následujícího řádku.

    ```js
    var app = require('../app');
    ```

   Po provedení této změny by se čára měla zobrazit takto.

    ```js
    var app = require('./app');
    ```

   Tato změna je nutná, protože jsme přesunuli soubor (dříve **bin nebo na webové**stránce) do stejného adresáře, ve kterém je požadovaný soubor aplikace. Po provedení této změny uložte soubor **server.js** .
6. Pomocí následujícího příkazu spusťte aplikaci v emulátoru Azure:

    ```powershell
    PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
    ```

    ![Webová stránka obsahující úvodní stránku Express](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Úprava zobrazení
Teď upravte zobrazení tak, aby se zobrazila zpráva "Vítá vás Express v Azure".

1. Zadejte následující příkaz a otevřete soubor index. Jade:

    ```powershell
    PS C:\node\expressapp\WebRole1> notepad views/index.jade
    ```

   ![Obsah souboru index. Jade](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

   Jade je výchozí modul zobrazení používaný aplikacemi Express. Další informace o modulu zobrazení Jade naleznete v tématu [http://jade-lang.com][http://jade-lang.com] .
2. Upravte poslední řádek textu připojením **v Azure**.

   ![Soubor index. Jade, poslední řádek načte: p Vítejte na \# {title} v Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Uložte soubor a ukončete Poznámkový blok.
4. Aktualizujte si prohlížeč a zobrazí se vaše změny.

   ![Okno prohlížeče obsahuje úvodní stránku k expresnímu v Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Po otestování aplikace použijte k zastavení emulátoru rutinu **stop-AzureEmulator** .

## <a name="publishing-the-application-to-azure"></a>Publikování aplikace do Azure
V okně Azure PowerShell použijte k nasazení aplikace do cloudové služby rutinu **Publish-AzureServiceProject** .

```powershell
PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch
```

Po dokončení operace nasazení se v prohlížeči otevře a zobrazí webová stránka.

![Webový prohlížeč zobrazující stránku Express Adresa URL indikuje, že je teď hostovaná v Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Další kroky
Další informace najdete ve [Středisku pro vývojáře Node.js](/azure/developer/javascript/).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com