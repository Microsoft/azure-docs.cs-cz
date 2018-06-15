---
title: Začínáme s Azure CDN SDK pro Node.js | Microsoft Docs
description: Informace o psaní aplikací Node.js ke správě Azure CDN.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 46ae8cd9775432d126cbde856c1fb06ea319297e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23843193"
---
# <a name="get-started-with-azure-cdn-development"></a>Začínáme s vývojem pro Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Můžete použít [Azure CDN SDK pro Node.js](https://www.npmjs.com/package/azure-arm-cdn) k automatizaci vytváření a Správa profilů CDN a koncové body.  Tento kurz vás provede vytvoření jednoduché konzolovou aplikaci Node.js, která ukazuje několik operací, k dispozici.  V tomto kurzu není určeno k podrobně popisují všechny aspekty CDN Azure SDK pro Node.js.

K dokončení tohoto kurzu, byste již měli mít [Node.js](http://www.nodejs.org) **4.x.x** nebo vyšší nainstalována a nakonfigurována.  Můžete použít libovolný textový editor, kterou chcete vytvořit aplikace Node.js.  Zápis v tomto kurzu, použití [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> [Dokončený projekt z tohoto kurzu](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) je k dispozici ke stažení na webu MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Vytvořte projekt a přidejte NPM závislosti
Teď, když jsme vytvořili skupinu prostředků pro naše profily CDN a získá naše Azure AD aplikace oprávnění ke správě profilů CDN a koncové body v rámci dané skupiny, můžeme začít vytváření aplikace.

Vytvořte složku pro uložení vaší aplikace.  Z konzoly nástroje Node.js v aktuální cestě nastavte vaše aktuální umístění do této nové složky a inicializovat spuštěním projektu:

    npm init

Potom zobrazí řadu otázek, k chybě při inicializaci projektu.  Pro **vstupní bod**, tento kurz používá *app.js*.  Můžete zobrazit Moje další možnosti v následujícím příkladu.

![Init – výstupní NPM](./media/cdn-app-dev-node/cdn-npm-init.png)

Naše projektu je nyní inicializován s *packages.json* souboru.  Naše projektu bude používat některé knihovny Azure obsažené v NPM balíčky.  Použijeme modulu Runtime klienta Azure pro platformu Node.js (ms-rest azure) a Azure CDN klientské knihovny pro Node.js (azure arm cd).  Umožňuje přidat do projektu jako závislosti.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Poté, co jsou balíčky dokončí instalaci, *package.json* soubor by měl vypadat podobně jako tento příklad (verze čísla se mohou lišit):

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

Nakonec pomocí textového editoru, vytvořte prázdný textový soubor a uložte jej do kořenové složky naše projektu jako *app.js*.  Nyní připraveni začít psaní kódu.

## <a name="requires-constants-authentication-and-structure"></a>Vyžaduje, konstanty, ověřování a struktury
S *app.js* otevřete v našem editoru, Pojďme základní strukturu naše programu zapsána.

1. Přidejte "vyžaduje" pro naše balíčky NPM v horní části s následujícími službami:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Je potřeba definovat některé konstanty, který bude používat naše metody.  Přidejte následující.  Ujistěte se, zda jste nahradili zástupné symboly, včetně  **&lt;lomené závorky&gt;**, s vlastní hodnoty, podle potřeby.
   
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
3. V dalším kroku jsme budete doložit klient správy CDN a dejte mu naše přihlašovací údaje.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Pokud používáte ověřování jednotlivých uživatelů, bude vypadat tyto dva řádky mírně lišit.
   
   > [!IMPORTANT]
   > Tato ukázka kódu použijte, pouze pokud zvolíte ověřování jednotlivého uživatele místo objekt služby.  Pečlivě chránit pověření jednotlivé uživatele a bezpečně je tajný.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Nezapomeňte nahradit položky v  **&lt;lomené závorky&gt;**  se správnými informacemi.  Pro `<redirect URI>`, použijte identifikátor URI, které jste zadali při registraci aplikace ve službě Azure AD přesměrování.
4. Naše konzolovou aplikaci Node.js bude trvat některé parametry příkazového řádku.  Umožňuje ověřit, že aspoň jeden parametr byl předán.
   
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
5. Která přináší nám pro hlavní část naší programu, které jsme větev na jiné funkce založené na jaké parametry byly předány.
   
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
6. Na několika místech v našem programu potřebujeme zkontrolujte, zda číslo správné parametry byly předány v a zobrazit pomoc, pokud jejich nevypadají správné.  Umožňuje vytvořit kvůli tomu funkce.
   
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
7. Nakonec jsou asynchronní, funkcích, které budeme používat v klientovi správu CDN, takže potřebují metodu volat zpět při jejich hotovi.  Pojďme si ho, můžete zobrazit výstup z klienta správy CDN (pokud existuje) a ukončení programu řádně.
   
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

Teď, když je zapsán základní struktura naše programu, vytvoříme by měl funkce volané založené na našem parametrů.

## <a name="list-cdn-profiles-and-endpoints"></a>Koncové body a seznam profilů CDN
Začněme s kódem k zobrazení seznamu naše stávající profily a koncové body.  Vlastní kód komentář poskytují očekávaný syntaxe, abychom věděli, kde každý parametr přejde.

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

## <a name="create-cdn-profiles-and-endpoints"></a>Vytvoření profilů CDN a koncové body
V dalším kroku jsme budete napsat funkce vytvářet profily a koncové body.

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

## <a name="purge-an-endpoint"></a>Vyprázdnění koncového bodu
Za předpokladu, že byl vytvořen koncový bod, jeden běžný úkol, který jsme může být třeba provést v našem programu je mazání obsahu v našem koncový bod.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Odstranit profily CDN a koncové body
Poslední funkci, kterou jsme bude obsahovat odstraní koncových bodů a profily.

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
Můžeme spouštět naše program Node.js pomocí našich oblíbených ladicí program nyní nebo v konzole.

> [!TIP]
> Pokud používáte Visual Studio Code jako ladicí program, budete muset nastavit svoje prostředí předávat parametry příkazového řádku.  Visual Studio Code tomu **lanuch.json** souboru.  Vyhledejte vlastnost s názvem **argumentů** a přidejte řetězcové hodnoty pro parametry, pole, aby vypadal podobně jako tento: `"args": ["list", "profiles"]`.
> 
> 

Začněme tak, že uvedete naše profily.

![Seznam profilů](./media/cdn-app-dev-node/cdn-list-profiles.png)

My zpět prázdné pole.  Vzhledem k tomu, že nemáme žádné profily v naší skupiny prostředků, která se očekává.  Nyní vytvoříme profil.

![Vytvoření profilu](./media/cdn-app-dev-node/cdn-create-profile.png)

Nyní Pojďme přidat koncový bod.

![Vytvoření koncového bodu](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Umožňuje odstranit nakonec naše profil.

![Odstranit profil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Další kroky
Zobrazíte dokončený projekt z tohoto návodu [stáhnout vzorek](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Pokud chcete zobrazit odkaz pro Azure CDN SDK pro Node.js, zobrazit [odkaz](http://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Chcete-li zjistit další dokumentaci týkající se sady Azure SDK pro Node.js, podívejte se [úplné referenční](http://azure.github.io/azure-sdk-for-node/).

Spravovat prostředky CDN s [prostředí PowerShell](cdn-manage-powershell.md).

