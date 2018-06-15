---
title: Určení verze Node.js
description: Zjistěte, jak k určení verze Node.js používané weby Azure a cloudové služby
services: ''
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: ''
ms.assetid: d0e15278-2ab4-4ec8-8256-913839c6d5ef
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: a20179c72b227deb14df442bea7b80cf31728aa7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864298"
---
# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Určení verze Node.js v aplikaci Azure
Při hostování aplikace Node.js, můžete zajistit, že vaše aplikace používá na konkrétní verzi Node.js. Existuje několik způsobů k tomu pro aplikace hostované v Azure.

## <a name="default-versions"></a>Výchozí verze
Verze Node.js poskytovaný platformou Azure jsou neustále aktualizovány. Pokud není uvedeno jinak, je výchozí verze, který je uveden v `WEBSITE_NODE_DEFAULT_VERSION` proměnné prostředí se použije. Chcete-li tuto výchozí hodnotu přepsat, postupujte podle kroků v následující části tohoto článku

> [!NOTE]
> Pokud je hostitelem vaší aplikace v cloudové službě Azure (role web nebo worker), a je při prvním nasazení aplikace, se pokusí použít stejnou verzi nástroje Node.js, jako jste nainstalovali na vývojového prostředí, pokud odpovídá jednomu z t Azure Výchozí verze he dostupné v Azure.
>
>

## <a name="versioning-with-packagejson"></a>Správa verzí pomocí package.json
Můžete zadat verzi Node.js, který se má použít přidáním následující příkaz pro vaše **package.json** souboru:

    "engines":{"node":version}

Kde *verze* je konkrétní verzi číslo k použití. Složitější podmínky pro verzi, můžete zadat například:

    "engines":{"node": "0.6.22 || 0.8.x"}

Vzhledem k tomu, že 0.6.22 není některá z verzí, které jsou k dispozici v hostitelském prostředí, nejvyšší verze 0,8 série, která je k dispozici bude místo toho použít - 0.8.4.

## <a name="versioning-websites-with-app-settings"></a>Správa verzí webů s nastavení aplikace
Pokud hostujete aplikace na webu, můžete nastavit proměnné prostředí **WEBSITE_NODE_DEFAULT_VERSION** na požadovanou verzi.

## <a name="versioning-cloud-services-with-powershell"></a>Správa verzí cloudové služby pomocí prostředí PowerShell
Pokud jsou hostiteli aplikace v cloudové službě a nasazení aplikace pomocí Azure PowerShell, můžete přepsat výchozí Node.js verze pomocí **Set-AzureServiceProjectRole** rutiny prostředí PowerShell. Například:

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Všimněte si, že jsou parametry v předchozím prohlášením malá a velká písmena.  Můžete ověřit kontrolou byla vybrána správná verze Node.js **moduly** vlastnost vaší role **package.json**.

Můžete také **Get-AzureServiceProjectRoleRuntime** načíst seznam verzí Node.js pro aplikace hostované jako cloudová služba k dispozici.  Vždycky ověřte, zda je verze Node.js projektu závisí na v tomto seznamu.

## <a name="using-a-custom-version-with-azure-websites"></a>Použití vlastní verzi s weby Azure
Zatímco Azure poskytuje několik verzí výchozí Node.js, můžete použít na verzi, která není uvedena ve výchozím nastavení. Pokud je vaše aplikace hostována jako web Azure, můžete dosáhnout pomocí **iisnode.yml** souboru. Následující kroky provede procesem vlastní verzi Node.Js pomocí webu Azure:

1. Vytvořte nový adresář a pak vytvořte **server.js** soubor v adresáři. **Server.js** soubor by měl obsahovat následující:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Tato akce zobrazí verze Node.js používá při procházení webu.
2. Vytvoření nového webu a poznamenejte si název webu. Například následující používá [nástroje příkazového řádku Azure] k vytvoření nového webu Azure s názvem **mywebsite**a pak povolte úložiště Git pro web.

        azure site create mywebsite --git
3. Vytvořte nový adresář s názvem **bin** jako podřízený adresář obsahující **server.js** souboru.
4. Stáhněte si konkrétní verzi **node.exe** (verze systému Windows), kterou chcete použít s vaší aplikací. Například následující používá **curl** ke stažení verze 0.8.1:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Uložit **node.exe** soubor do **bin** složky, které jste vytvořili dříve.
5. Vytvoření **iisnode.yml** souboru ve stejném adresáři jako **server.js** souboru a poté přidejte následující obsah **iisnode.yml** souboru:

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Kde je tato cesta **node.exe** soubor v projektu budou umístěné po publikování aplikace na webu Azure.
6. Publikování aplikace. Například vzhledem k tomu, že jste vytvořili nový web pomocí parametru – git dříve, následující příkazy se přidání souborů aplikací do mé místní úložiště Git a vložit je do úložiště webu:

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Po aplikaci publikovali, otevřete stránku v prohlížeči. Měla by se zobrazit zpráva s oznámením "Hello z Azure spuštěné verze uzlu: v0.8.1".

## <a name="next-steps"></a>Další kroky
Teď, když chápete, jak k určení verze Node.js používá vaše aplikace, zjistěte, jak [práce s moduly], [sestavení a nasazení webu Node.js](app-service/app-service-web-get-started-nodejs.md), a [jak používat Azure Nástroje příkazového řádku pro Mac a Linux].

Další informace najdete ve [Středisku pro vývojáře Node.js](https://azure.microsoft.com/develop/nodejs/).

[jak používat Azure Nástroje příkazového řádku pro Mac a Linux]:cli-install-nodejs.md
[nástroje příkazového řádku Azure]:cli-install-nodejs.md
[práce s moduly]: nodejs-use-node-modules-azure-apps.md
[build and deploy a Node.js Web Site]: app-service/app-service-web-get-started-nodejs.md
