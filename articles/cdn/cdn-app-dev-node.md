---
title: Začínáme s Azure CDN SDK pro Node.js | Microsoft Docs
description: Naučte se vytvořit jednoduchou Node.js konzolovou aplikaci, která ukazuje, jak automatizovat vytváření a správu profilů Azure CDN a koncových bodů.
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
ms.topic: how-to
ms.date: 04/02/2021
ms.author: mazha
ms.custom: devx-track-js
ms.openlocfilehash: 386a424e45d1b718b68cbbf53322fd704317a06b
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285213"
---
# <a name="get-started-with-azure-cdn-development"></a>Začínáme s vývojem pro Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Pomocí [sady Azure CDN SDK pro JavaScript](https://www.npmjs.com/package/@azure/arm-cdn) můžete automatizovat vytváření a správu profilů a koncových bodů CDN.  Tento kurz vás provede vytvořením jednoduché aplikace Node.js konzolou, která ukazuje několik dostupných operací.  Tento kurz není určený k podrobnému popisu všech aspektů Azure CDN SDK pro JavaScript.

Pro dokončení tohoto kurzu byste už měli mít nainstalovanou a nakonfigurovanou [Node.js](https://www.nodejs.org) **6. x. x** nebo vyšší.  Můžete použít libovolný textový editor, ve kterém chcete vytvořit aplikaci Node.js.  Pro zápis tohoto kurzu jsem použili [Visual Studio Code](https://code.visualstudio.com).  


[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Vytvoření projektu a přidání závislostí NPM
Teď, když jsme vytvořili skupinu prostředků pro profily CDN a udělili jí oprávnění aplikace Azure AD ke správě profilů a koncových bodů CDN v této skupině, můžeme začít vytvářet aplikace.

Vytvořte složku pro uložení vaší aplikace.  Z konzoly nástroje Node.js nástrojů v aktuální cestě nastavte aktuální umístění do této nové složky a inicializujte projekt spuštěním:

```console
npm init
```

Pak budete mít k dispozici řadu otázek pro inicializaci projektu.  V případě **vstupního bodu** používá tento kurz *app.js*.  V následujícím příkladu vidíte své další volby.

![Výstup inicializace NPM](./media/cdn-app-dev-node/cdn-npm-init.png)

Náš projekt je nyní inicializován pomocí *packages.jsv* souboru.  Náš projekt bude používat některé knihovny Azure, které jsou obsaženy v balíčcích NPM.  Knihovnu použijeme pro ověřování Azure Active Directory v Node.js ( @azure/ms-rest-nodeauth ) a v klientské knihovně Azure CDN pro JavaScript ( @azure/arm-cdn ).  Pojďme je přidat do projektu jako závislosti.

```console
npm install --save @azure/ms-rest-nodeauth
npm install --save @azure/arm-cdn
```

Po dokončení instalace balíčků by *package.jsv* souboru mělo vypadat podobně jako v tomto příkladu (čísla verzí se můžou lišit):

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
    "@azure/arm-cdn": "^5.2.0",
    "@azure/ms-rest-nodeauth": "^3.0.0"
  }
}
```

Nakonec pomocí textového editoru vytvořte prázdný textový soubor a uložte ho do kořenové složky projektu jako *app.js*.  Teď jsme připraveni začít psát kód.

## <a name="requires-constants-authentication-and-structure"></a>Vyžaduje, konstanty, ověřování a strukturu.
Když *app.js* otevřete v našem editoru, pojďme získat základní strukturu našeho programu.

1. Přidejte v horní části "vyžaduje" pro naše balíčky NPM následující:
   
    ``` javascript
    var msRestAzure = require('@azure/ms-rest-nodeauth');
    const { CdnManagementClient } = require('@azure/arm-cdn');
    ```
2. Musíme definovat některé konstanty, které budou používat naše metody.  Přidejte následující.  Zástupné symboly, včetně **&lt; lomených závorek &gt;**, nahraďte požadovanými hodnotami.
   
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
3. V dalším kroku vytvoříme instanci klienta pro správu CDN a udělíme mu přihlašovací údaje.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new CdnManagementClient(credentials, subscriptionId);
    ```

4. Naše Node.js Konzolová aplikace bude brát v úvahu některé parametry příkazového řádku.  Pojďme ověřit, že byl předán alespoň jeden parametr.
   
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
5. Tím se přineseme k hlavní části našeho programu, kde jsme uzavřeli jiné funkce na základě toho, jaké parametry byly předány.
   
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
6. Na několika místech našeho programu musíme zajistit, aby byl předán správný počet parametrů, a zobrazila se některá z nich, pokud nevypadají správně.  Pojďme k tomu vytvořit funkce.
   
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
7. A konečně, funkce, které budeme používat na klientovi pro správu CDN, jsou asynchronní, takže potřebují metodu, která bude volat zpět, jakmile se dokončí.  Pojďme to udělat, abyste mohli zobrazit výstup z klienta pro správu CDN (pokud existuje) a program ukončit bez problémů.
   
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

Teď, když je napsaná základní struktura našeho programu, máme na základě našich parametrů vytvořit funkce s názvem.

## <a name="list-cdn-profiles-and-endpoints"></a>Výpis profilů a koncových bodů CDN
Pojďme začít s kódem pro výpis našich stávajících profilů a koncových bodů.  Můj komentář ke kódu poskytuje očekávanou syntaxi, abychom věděli, kde každý parametr přechází.

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
            cdnClient.endpoints.listByProfile(resourceGroupName, parms[2], callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Vytvoření profilů a koncových bodů CDN
Potom napíšeme funkce pro vytváření profilů a koncových bodů.

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

    cdnClient.profiles.create( resourceGroupName, parms[2], standardCreateParameters, callback);
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

    cdnClient.endpoints.create(resourceGroupName, parms[2], parms[3], endpointProperties, callback);
}
```

## <a name="purge-an-endpoint"></a>Vyprázdnit koncový bod
Za předpokladu, že byl koncový bod vytvořen, může být v našem programu v tomto koncovém bodě jeden běžný úkol, který bychom mohli provést.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(resourceGroupName, parms[2], parms[3], purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Odstranění profilů a koncových bodů CDN
Poslední funkce, kterou budeme zahrnovat, budou odstraňovat koncové body a profily.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteMethod(resourceGroupName, parms[2], callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteMethod(resourceGroupName, parms[2], parms[3], callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Spuštění programu
Náš Node.js program teď můžeme spustit pomocí našeho oblíbeného ladicího programu nebo v konzole.

> [!TIP]
> Pokud používáte Visual Studio Code jako ladicí program, budete muset nastavit prostředí tak, aby se předávalo v parametrech příkazového řádku.  Visual Studio Code to provádí v **launch.jsv** souboru.  Vyhledejte vlastnost s názvem **args** a přidejte pole řetězcových hodnot pro parametry, aby vypadalo podobně jako toto:  `"args": ["list", "profiles"]` .
> 
> 

Pojďme začít zobrazením našich profilů.

![Seznam profilů](./media/cdn-app-dev-node/cdn-list-profiles.png)

Vrátili jsme prázdné pole.  Vzhledem k tomu, že v naší skupině prostředků nemáme žádné profily, je to očekávané.  Pojďme teď vytvořit profil.

![Vytvořit profil](./media/cdn-app-dev-node/cdn-create-profile.png)

Teď přidáme koncový bod.

![Vytvořit koncový bod](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Nakonec odstraňte náš profil.

![Odstranit profil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Další kroky

Chcete-li zobrazit referenční informace o sadě Azure CDN SDK pro JavaScript, podívejte se na [odkaz](https://docs.microsoft.com/javascript/api/@azure/arm-cdn).

Pokud chcete najít další dokumentaci k sadě Azure SDK pro JavaScript, zobrazte si [úplný odkaz](https://docs.microsoft.com/javascript/api/?view=azure-node-latest).

Spravujte své prostředky CDN pomocí [PowerShellu](cdn-manage-powershell.md).
