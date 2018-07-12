---
title: Začínáme s Azure CDN SDK pro Node.js | Dokumentace Microsoftu
description: Zjistěte, jak psát aplikace Node.js a spravovat Azure CDN.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38301560"
---
# <a name="get-started-with-azure-cdn-development"></a>Začínáme s vývojem pro Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Můžete použít [Azure CDN SDK pro Node.js](https://www.npmjs.com/package/azure-arm-cdn) automatizovat vytváření a správa koncových bodů a profilů CDN.  Tento kurz vás provede vytvořením jednoduchou konzolovou aplikaci Node.js, který ukazuje několik dostupných operací.  V tomto kurzu není určená k podrobnému popisu, všechny aspekty Azure CDN SDK pro Node.js.

K dokončení tohoto kurzu byste už měli mít [Node.js](http://www.nodejs.org) **4.x.x** nebo novější nainstalován a nakonfigurován.  Můžete použít libovolný textový editor, kterou chcete vytvořit aplikaci Node.js.  Zápis v tomto kurzu, můžu použít [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> [Dokončený projekt z tohoto kurzu](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) je k dispozici ke stažení na webu MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Vytvoření projektu a přidání závislosti na NPM
Teď, když jsme vytvořili skupinu prostředků pro naše profily CDN a naše oprávnění aplikace Azure AD ke správě profilů CDN a koncové body v rámci dané skupiny, můžeme začít vytváření naši aplikaci.

Vytvořte složku pro uložení vaší aplikace.  Z konzoly s nástroji Node.js v aktuální cestě nastavte své aktuální polohy do této nové složky a inicializovat spuštěním projektu:

    npm init

Pak zobrazí na několik otázek k inicializaci vašeho projektu.  Pro **vstupní bod**, tento kurz používá *app.js*.  Zobrazí se Moje jiné možnosti v následujícím příkladu.

![Výstup init NPM](./media/cdn-app-dev-node/cdn-npm-init.png)

Naše projektu je teď inicializují se *packages.json* souboru.  Náš projekt bude používat některé knihovny Azure součástí balíčků NPM.  Použijeme modul Runtime klienta Azure pro Node.js (ms-rest azure) a Azure CDN klientské knihovny pro Node.js (azure-arm-cd).  Přidejme do projektu jako závislosti.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Po dokončení balíčky instalace, *package.json* soubor by měl vypadat podobně jako tento příklad (verze čísel se může lišit):

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

Nakonec pomocí textového editoru, vytvořte prázdný textový soubor a uložte jej do kořenové složky naše projektu jako *app.js*.  Máme teď můžete začít s psaním kódu.

## <a name="requires-constants-authentication-and-structure"></a>Vyžaduje, konstanty, ověřování a struktury
S *app.js* otevřete v našem editoru, pojďme si základní struktura naše programu.

1. Přidejte "vyžaduje" pro naše balíčky NPM v horní části následujícími způsoby:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Budeme muset definovat některé konstanty, které bude používat naše metody.  Přidejte následující.  Nezapomeňte nahradit zástupné symboly, včetně  **&lt;ostrých závorek&gt;**, vlastními hodnotami podle potřeby.
   
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
3. V dalším kroku vytvoříme vytvořit instanci klienta správy CDN a jí naše přihlašovací údaje.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Pokud používáte ověřování jednotlivých uživatelů, bude vypadat trochu jinak tyto dva řádky.
   
   > [!IMPORTANT]
   > Tento vzorový kód použijte, pouze pokud zvolíte ověřování jednotlivých uživatelů místo instančního objektu služby.  Buďte opatrní chránit vaše přihlašovací údaje jednotlivých uživatelů a udržujte je v tajnosti.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Nezapomeňte nahradit položky v **&lt;ostrých závorek&gt;** se správnými informacemi.  Pro `<redirect URI>`, použijte identifikátor URI, které jste zadali při registraci aplikace ve službě Azure AD pro přesměrování.
4. Naše aplikace konzoly Node.js bude trvat několik parametrů příkazového řádku.  Pojďme ověřit, že nejméně jedna byl předán parametr.
   
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
5. Který přináší do hlavní část našeho programu, ve kterém jsme větví dalších funkcí založené na jaké parametry byly předány.
   
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
6. Na několika místech v naší aplikaci potřebujeme zajistit správný počet parametrů předané a zobrazí nějaké Nápověda, pokud je jejich nevypadají správné.  Pojďme vytvořit funkce, které provedete.
   
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
7. Nakonec jsou asynchronní, funkce, které budeme používat na straně klienta pro správu CDN, takže potřebují metodu volat zpět při nepřenesete.  Vytvoříme ten, který může zobrazit výstup z CDN správy klienta (pokud existuje) a řádně ukončit program.
   
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

Teď, když je základní struktura náš program napsán, vytvoříme by to funkce volané založené na na našich parametry.

## <a name="list-cdn-profiles-and-endpoints"></a>Seznam profilů CDN a koncových bodů
Začněme s kódem, když chci zveřejnit naší stávající profily a koncových bodů.  Moje komentáře ke kódu poskytují očekávaná syntaxe, abychom věděli, kde každý parametr odkazuje.

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

## <a name="create-cdn-profiles-and-endpoints"></a>Vytvořte profily CDN a koncových bodů
V dalším kroku budeme psát funkce k vytvoření koncových bodů a profilů.

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
Za předpokladu, že se vytvořil koncový bod, je jednou z běžných úloh, která chceme v naší aplikaci provést vymazání obsahu v našich koncový bod.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Odstranění koncových bodů a profilů CDN
Poslední funkci, kterou zavedeme odstraní koncových bodů a profilů.

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
Teď můžeme spouštět náš program Node.js pomocí našich oblíbených ladicího programu, nebo na konzole.

> [!TIP]
> Pokud používáte Visual Studio Code jako ladicí program, bude nutné nastavit prostředí a zajistěte tak předání parametrů příkazového řádku.  Visual Studio Code to dělá **lanuch.json** souboru.  Vyhledejte vlastnost s názvem **args** a přidat pole řetězcových hodnot pro parametry, aby vypadá podobně jako tento: `"args": ["list", "profiles"]`.
> 
> 

Začněme tím, že výpis našich profilů.

![Seznam profilů](./media/cdn-app-dev-node/cdn-list-profiles.png)

My zpět prázdné pole.  Protože nemáme žádné profily v naší skupiny prostředků, to je očekáváno.  Pojďme teď vytvořit profil.

![Vytvoření profilu](./media/cdn-app-dev-node/cdn-create-profile.png)

Nyní Pojďme přidat koncový bod.

![Vytvoření koncového bodu](./media/cdn-app-dev-node/cdn-create-endpoint.png)

A konečně můžeme odstranit naše profil.

![Odstranit profil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Další kroky
Dokončený projekt z tohoto návodu, zobrazíte [Stáhněte ukázku](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Chcete-li zobrazit odkaz pro Azure CDN SDK pro Node.js, zobrazte [odkaz](http://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Chcete-li vyhledat další dokumentaci na sadu Azure SDK pro Node.js, podívejte se [úplné referenční](http://azure.github.io/azure-sdk-for-node/).

Správa prostředků CDN pomocí [Powershellu](cdn-manage-powershell.md).

