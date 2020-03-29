---
title: Začínáme s sadou Azure CDN SDK pro soubor Node.js | Dokumenty společnosti Microsoft
description: Přečtěte si, jak psát aplikace Node.js pro správu Azure CDN.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 18dbcbf93947306334ccc2c156d9266884198e19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594127"
---
# <a name="get-started-with-azure-cdn-development"></a>Začínáme s vývojem pro Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Sadazure [CDN SDK pro Node.js](https://www.npmjs.com/package/azure-arm-cdn) můžete použít k automatizaci vytváření a správy profilů CDN a koncových bodů.  Tento kurz provede vytvoření jednoduché aplikace konzoly Node.js, která ukazuje několik dostupných operací.  Tento kurz není určen k popisu všech aspektů sady Azure CDN SDK pro node.js podrobně.

Chcete-li dokončit tento kurz, měli byste již mít [node.js](https://www.nodejs.org) **4.x.x** nebo vyšší nainstalován a nakonfigurován.  Můžete použít libovolný textový editor, který chcete vytvořit aplikaci Node.js.  Chcete-li napsat tento výukový program, použil jsem [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> [Dokončený projekt z tohoto kurzu](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) je k dispozici ke stažení na MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Vytvoření projektu a přidání závislostí NPM
Teď, když jsme vytvořili skupinu prostředků pro naše profily CDN a udělili naše aplikace Azure AD oprávnění ke správě profilů CDN a koncových bodů v rámci této skupiny, můžeme začít vytvářet naši aplikaci.

Vytvořte složku pro uložení aplikace.  Z konzoly s nástroji Node.js v aktuální cestě nastavte aktuální umístění do této nové složky a inicializujte projekt spuštěním:

    npm init

Poté se zobrazí řada otázek pro inicializaci projektu.  Pro **vstupní bod**tento kurz používá soubor *app.js*.  Můžete vidět mé další volby v následujícím příkladu.

![Výstup init npm](./media/cdn-app-dev-node/cdn-npm-init.png)

Náš projekt je nyní inicializován se souborem *packages.json.*  Náš projekt bude používat některé knihovny Azure obsažené v balíčcích NPM.  Použijeme Azure Client Runtime pro Node.js (ms-rest-azure) a klientskou knihovnu Azure CDN pro Node.js (azure-arm-cd).  Přidáme je do projektu jako závislosti.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Po dokončení instalace balíčků by měl soubor *package.json* vypadat podobně jako v tomto příkladu (čísla verzí se mohou lišit):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Nakonec pomocí textového editoru vytvořte prázdný textový soubor a uložte jej do kořenového adresáře naší složky projektu jako *app.js*.  Nyní jsme připraveni začít psát kód.

## <a name="requires-constants-authentication-and-structure"></a>Vyžaduje, konstanty, ověřování a struktura
S *app.js* otevřené v našem editoru, pojďme si základní strukturu našeho programu písemného.

1. Přidejte "vyžaduje" pro naše npm balíčky v horní části s následujícími:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Musíme definovat některé konstanty, které naše metody budou používat.  Přidejte následující.  Nezapomeňte nahradit zástupné ** &lt;symboly, včetně&gt;úhlových závorek**, vlastními hodnotami podle potřeby.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Dále nastoupíme instance klienta pro správu CDN a dáme mu naše přihlašovací údaje.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Pokud používáte individuální ověřování uživatele, budou tyto dva řádky vypadat mírně odlišně.
   
   > [!IMPORTANT]
   > Tuto ukázku kódu použijte pouze v případě, že se rozhodnete mít individuální ověřování uživatele namísto instančního objektu.  Dávejte pozor, abyste chránili vaše individuální přihlašovací údaje uživatele a drželi je v tajnosti.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Nezapomeňte nahradit položky v ** &lt;úhlových&gt; závorkách** správnými informacemi.  Pro `<redirect URI>`použití identifikátoru URI přesměrování, který jste zadali při registraci aplikace ve službě Azure AD.
4. Naše aplikace konzoly Node.js bude trvat některé parametry příkazového řádku.  Ověřme, zda byl předán alespoň jeden parametr.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Tím se dostáváme do hlavní části našeho programu, kde se rozdělíme na další funkce na základě toho, jaké parametry byly předány.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. Na několika místech našeho programu se musíme ujistit, že správný počet parametrů byl předán a zobrazit nějakou pomoc, pokud nevypadají správně.  Pojďme vytvořit funkce k tomu, že.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Nakonec funkce, které budeme používat v klientovi pro správu CDN, jsou asynchronní, takže potřebují metodu pro volání zpět, když jsou hotové.  Udělejme ten, který může zobrazit výstup z klienta správy CDN (pokud existuje) a ukončit program elegantně.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Nyní, když je napsána základní struktura našeho programu, měli bychom vytvořit funkce nazývané na základě našich parametrů.

## <a name="list-cdn-profiles-and-endpoints"></a>Seznam profilů CDN a koncových bodů
Začněme s kódem pro seznam našich stávajících profilů a koncových bodů.  Můj kód komentáře poskytují očekávanou syntaxi, takže víme, kde každý parametr jde.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Vytvoření profilů a koncových bodů CDN
Dále napíšeme funkce pro vytvoření profilů a koncových bodů.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Vymazání koncového bodu
Za předpokladu, že koncový bod byl vytvořen, jeden společný úkol, který můžeme chtít provést v našem programu je vymazání obsahu v našem koncovém bodu.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Odstranění profilů a koncových bodů CDN
Poslední funkce, kterou zahrneme, odstraní koncové body a profily.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Spuštění programu
Nyní můžeme spustit náš program Node.js pomocí našeho oblíbeného ladicího programu nebo na konzoli.

> [!TIP]
> Pokud používáte Visual Studio Kód jako ladicí program, budete muset nastavit prostředí předat parametry příkazového řádku.  Visual Studio Code to v souboru **launch.json.**  Vyhledejte vlastnost s názvem **args** a přidejte pole řetězcových hodnot pro vaše `"args": ["list", "profiles"]`parametry, aby vypadala podobně jako tato: .
> 
> 

Začněme tím, že seznam našich profilů.

![Profily seznamu](./media/cdn-app-dev-node/cdn-list-profiles.png)

Máme zpátky prázdné pole.  Vzhledem k tomu, že nemáme žádné profily v naší skupině prostředků, to se očekává.  Nyní vytvoříme profil.

![Vytvořit profil](./media/cdn-app-dev-node/cdn-create-profile.png)

Nyní přidáme koncový bod.

![Vytvoření koncového bodu](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Nakonec smažme náš profil.

![Odstranit profil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Další kroky
Chcete-li zobrazit dokončený projekt z tohoto návodu, [stáhněte si ukázku](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Chcete-li zobrazit odkaz na azure CDN SDK pro Node.js, zobrazte [odkaz](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Chcete-li najít další dokumentaci k azure sdk pro Node.js, zobrazte [úplný odkaz](https://azure.github.io/azure-sdk-for-node/).

Spravujte prostředky CDN pomocí [prostředí PowerShell](cdn-manage-powershell.md).

