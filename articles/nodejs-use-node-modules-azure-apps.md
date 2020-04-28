---
title: Práce s moduly node. js
description: Naučte se pracovat s moduly node. js při použití Azure App Service nebo Cloud Services.
services: ''
documentationcenter: nodejs
author: rloutlaw
manager: rloutlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: routlaw
ms.openlocfilehash: 61be6bcd957a4e81147d5ef472b8f850e5605e41
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "70309277"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Používání modulů Node.js s aplikacemi Azure
Tento dokument poskytuje pokyny k používání modulů Node. js s aplikacemi hostovanými v Azure. Poskytuje pokyny, jak zajistit, aby vaše aplikace používala konkrétní verzi modulu a aby používala nativní moduly s Azure.

Pokud jste již obeznámeni s používáním modulů Node. js, soubory **Package. JSON** a **npm-shrinkwrap. JSON** , poskytují následující informace rychlý přehled toho, co je popsáno v tomto článku:

* Azure App Service rozumí souborům **Package. JSON** a **npm-shrinkwrap. JSON** a může instalovat moduly založené na položkách v těchto souborech.

* Azure Cloud Services očekává, že se všechny moduly nainstalují do vývojového prostředí, a adresáře **modulů uzlů\_** , které se mají zahrnout jako součást balíčku pro nasazení. Je možné povolit podporu pro instalaci modulů pomocí souborů **Package. JSON** nebo **npm-shrinkwrap. JSON** na Cloud Services; Tato konfigurace ale vyžaduje přizpůsobení výchozích skriptů používaných projekty cloudových služeb. Příklad toho, jak nakonfigurovat toto prostředí, najdete v tématu [úloha po spuštění Azure pro spuštění instalace NPM, aby nedocházelo k nasazení modulů uzlů](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown) .

> [!NOTE]
> V tomto článku se nezabývá Azure Virtual Machines, protože prostředí pro nasazení na VIRTUÁLNÍm počítači závisí na operačním systému hostovaném virtuálním počítačem.
> 
> 

## <a name="nodejs-modules"></a>Moduly node. js
Moduly jsou spustitelný balíčky JavaScriptu, které poskytují konkrétní funkce pro vaši aplikaci. Moduly se obvykle instalují pomocí nástroje příkazového řádku **npm** , ale některé moduly (například modul HTTP) jsou k dispozici jako součást základního balíčku Node. js.

Když jsou moduly nainstalované, ukládají se do adresáře **modulů uzlů\_** v kořenové složce adresářové struktury vaší aplikace. Každý modul v adresáři **modulů\_uzlů** udržuje svůj vlastní adresář, který obsahuje všechny moduly, na kterých závisí, a toto chování se opakuje u každého modulu v závislosti na tom, jak se nachází v řetězu závislostí. Toto prostředí umožňuje, aby každý modul měl nainstalované vlastní požadavky na verzi pro moduly, na kterých závisí, ale může mít za následek velkou rozsáhlou adresářovou strukturu.

Nasazení adresáře **modulů\_uzlů** v rámci aplikace zvyšuje velikost nasazení v porovnání s použitím souboru **Package. JSON** nebo **npm-shrinkwrap. JSON** . zaručuje však, že verze modulů používaných v produkčním prostředí jsou stejné jako moduly používané při vývoji.

### <a name="native-modules"></a>Nativní moduly
I když většina modulů je jednoduše prostým textem souborů JavaScriptu, některé moduly jsou binární obrázky specifické pro platformu. Tyto moduly jsou kompilovány v době instalace, obvykle pomocí Pythonu a Node-gyp. Vzhledem k tomu, že Azure Cloud Services spoléhá na nasazování složky **modulů uzlů\_** v rámci aplikace, všechny nativní moduly, které jsou součástí instalovaných modulů, by měly fungovat v cloudové službě, pokud byla nainstalována a kompilována v systému Windows Development.

Azure App Service nepodporuje všechny nativní moduly a může selhat při kompilování modulů s konkrétními požadavky. I když některé oblíbené moduly, jako je MongoDB, mají volitelné nativní závislosti a fungují bez nich, dvě alternativní řešení se ukázala úspěšně s téměř všemi dostupnými nativními moduly, které jsou dnes dostupné:

* Spusťte **instalaci npm** na počítači s Windows, na kterém jsou nainstalované všechny požadované součásti nativního modulu. Pak nasaďte složku vytvořených **modulů\_uzlů** jako součást aplikace na Azure App Service.

  * Před kompilací ověřte, že vaše místní instalace Node. js má odpovídající architekturu a verze je co nejblíže k verzi používané v Azure (aktuální hodnoty je možné zkontrolovat za běhu z vlastností **Process. arch** a **Process. Version**).

* Azure App Service je možné nakonfigurovat tak, aby během nasazení prováděla vlastní skripty pro bash nebo prostředí. díky němu máte možnost provádět vlastní příkazy a přesně nakonfigurovat způsob, jakým se spouští **instalace npm** . Video, které ukazuje, jak nakonfigurovat toto prostředí, najdete v tématu [vlastní skripty pro nasazení webu pomocí Kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Použití souboru Package. JSON

Soubor **Package. JSON** je způsob, jak určit závislosti nejvyšší úrovně, které vaše aplikace vyžaduje, aby hostující platforma mohla instalovat závislosti, a ne vyžadovat, abyste jako součást nasazení zahrnuli složku **modules node\_** . Po nasazení aplikace se k analýze souboru **Package. JSON** a instalaci všech uvedených závislostí použije příkaz **npm Install** .

Během vývoje můžete při instalaci modulů použít parametry **--Save**, **--Save-dev**, nebo **--Save-Option** , abyste mohli přidat položku pro modul do souboru **Package. JSON** automaticky. Další informace najdete v tématu [npm-Install](https://docs.npmjs.com/cli/install).

Jedním z možných problémů se souborem **Package. JSON** je, že určuje pouze verzi pro závislosti nejvyšší úrovně. Každý instalovaný modul může nebo nemusí určovat verzi modulů, na kterých závisí, a proto je možné, že můžete končit jiným řetězem závislostí, než který se používá při vývoji.

> [!NOTE]
> Pokud soubor <b>Package. JSON</b> odkazuje na nativní modul, při nasazení do Azure App Service se může při publikování aplikace pomocí Gitu zobrazit chybová zpráva podobná následujícímu příkladu:
> 
> NPM ERR! module-name@0.6.0instalace: node-gyp Configure Build
> 
> NPM ERR! příkaz "cmd"/c "Node-gyp Configure Build" se nezdařil. 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Použití souboru npm-shrinkwrap. JSON
Soubor **npm-shrinkwrap. JSON** je pokus o řešení omezení verzí modulu souboru **Package. JSON** . I když soubor **Package. JSON** obsahuje jenom verze modulů nejvyšší úrovně, soubor **npm-shrinkwrap. JSON** obsahuje požadavky na verzi pro úplný řetěz závislostí modulu.

Když je vaše aplikace připravená na produkční prostředí, můžete uzamknout požadavky na verzi a vytvořit soubor **npm-shrinkwrap. JSON** pomocí příkazu **npm shrinkwrap** . Tento příkaz použije verze, které jsou aktuálně nainstalované ve **složce\_modulů uzlů** , a zaznamená tyto verze do souboru **npm-shrinkwrap. JSON** . Po nasazení aplikace do hostitelského prostředí se k analýze souboru **npm-shrinkwrap. JSON** a instalaci všech uvedených závislostí použije příkaz **npm Install** . Další informace najdete v tématu [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Pokud soubor <b>npm-shrinkwrap. JSON</b> odkazuje na nativní modul, při jeho nasazování do Azure App Service se může při publikování aplikace pomocí Gitu zobrazit chybová zpráva podobná následujícímu příkladu:
> 
> NPM ERR! module-name@0.6.0instalace: node-gyp Configure Build
> 
> NPM ERR! příkaz "cmd"/c "Node-gyp Configure Build" se nezdařil. 1
> 
> 

## <a name="next-steps"></a>Další kroky
Teď, když jste se seznámili s používáním modulů Node. js s Azure, se naučíte, jak [určit verzi Node. js](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md), [Sestavit a nasadit webovou aplikaci Node. js](app-service/app-service-web-get-started-nodejs.md)a [Jak používat rozhraní příkazového řádku Azure pro Mac a Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Další informace najdete ve [Středisku pro vývojáře Node.js](/azure/javascript/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
