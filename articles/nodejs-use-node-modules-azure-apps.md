---
title: Práce s moduly Node.js
description: Naučte se pracovat s Node.js moduly při použití Azure App Service nebo Cloud Services.
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
ms.openlocfilehash: f61f08f32ebb2b721846d3c3017405af99421104
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542214"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Používání modulů Node.js s aplikacemi Azure
Tento dokument poskytuje pokyny k používání Node.jsch modulů s aplikacemi hostovanými v Azure. Poskytuje pokyny, jak zajistit, aby vaše aplikace používala konkrétní verzi modulu a aby používala nativní moduly s Azure.

Pokud jste již obeznámeni s používáním Node.jsch modulů, **package.jsna** souborech a **npm-shrinkwrap.js** , poskytují následující informace rychlý přehled toho, co je popsáno v tomto článku:

* Azure App Service rozumí **package.js** a **npm-shrinkwrap.js** souborů a může instalovat moduly založené na položkách v těchto souborech.

* Azure Cloud Services očekává, že se všechny moduly nainstalují do vývojového prostředí, a adresáře **\_ modulů uzlů** , které se mají zahrnout jako součást balíčku pro nasazení. Je možné povolit podporu pro instalaci modulů pomocí **package.js** nebo **npm-shrinkwrap.js** souborů na Cloud Services. Tato konfigurace ale vyžaduje přizpůsobení výchozích skriptů používaných projekty cloudových služeb. Příklad toho, jak nakonfigurovat toto prostředí, najdete v tématu [úloha po spuštění Azure pro spuštění instalace NPM, aby nedocházelo k nasazení modulů uzlů](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown) .

> [!NOTE]
> V tomto článku se nezabývá Azure Virtual Machines, protože prostředí pro nasazení na VIRTUÁLNÍm počítači závisí na operačním systému hostovaném virtuálním počítačem.
>
>

## <a name="nodejs-modules"></a>Moduly Node.js
Moduly jsou spustitelný balíčky JavaScriptu, které poskytují konkrétní funkce pro vaši aplikaci. Moduly se obvykle instalují pomocí nástroje příkazového řádku **npm** , ale některé moduly (například modul HTTP) jsou k dispozici jako součást balíčku základní Node.js.

Když jsou moduly nainstalované, ukládají se do adresáře **\_ modulů uzlů** v kořenové složce adresářové struktury vaší aplikace. Každý modul v adresáři **\_ modulů uzlů** udržuje svůj vlastní adresář, který obsahuje všechny moduly, na kterých závisí, a toto chování se opakuje u každého modulu v závislosti na tom, jak se nachází v řetězu závislostí. Toto prostředí umožňuje, aby každý modul měl nainstalované vlastní požadavky na verzi pro moduly, na kterých závisí, ale může mít za následek velkou rozsáhlou adresářovou strukturu.

Nasazení adresáře **\_ modulů uzlů** v rámci vaší aplikace zvyšuje velikost nasazení v porovnání s použitím **package.js** nebo **npm-shrinkwrap.jsv** souboru. zaručuje však, že verze modulů, které jsou používány v produkčním prostředí, jsou stejné jako moduly používané při vývoji.

### <a name="native-modules"></a>Nativní moduly
I když většina modulů je jednoduše prostým textem souborů JavaScriptu, některé moduly jsou binární obrázky specifické pro platformu. Tyto moduly jsou kompilovány v době instalace, obvykle pomocí Pythonu a Node-gyp. Vzhledem k tomu, že Azure Cloud Services spoléhá na nasazování složky **\_ modulů uzlů** v rámci aplikace, všechny nativní moduly, které jsou součástí instalovaných modulů, by měly fungovat v cloudové službě, pokud byla nainstalována a kompilována v systému Windows Development.

Azure App Service nepodporuje všechny nativní moduly a může selhat při kompilování modulů s konkrétními požadavky. I když některé oblíbené moduly, jako je MongoDB, mají volitelné nativní závislosti a fungují bez nich, dvě alternativní řešení se ukázala úspěšně s téměř všemi dostupnými nativními moduly, které jsou dnes dostupné:

* Spusťte **instalaci npm** na počítači s Windows, na kterém jsou nainstalované všechny požadované součásti nativního modulu. Pak nasaďte složku vytvořených **\_ modulů uzlů** jako součást aplikace na Azure App Service.

  * Před kompilací ověřte, že vaše místní instalace Node.js má odpovídající architekturu a verze je co nejblíže k verzi používané v Azure (aktuální hodnoty je možné zkontrolovat za běhu z vlastností **Process. arch** a **Process. Version**).

* Azure App Service je možné nakonfigurovat tak, aby během nasazení prováděla vlastní skripty pro bash nebo prostředí. díky němu máte možnost provádět vlastní příkazy a přesně nakonfigurovat způsob, jakým se spouští **instalace npm** . Video, které ukazuje, jak nakonfigurovat toto prostředí, najdete v tématu [vlastní skripty pro nasazení webu pomocí Kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Použití package.jsv souboru

**package.jsv** souboru je způsob, jak určit závislosti nejvyšší úrovně, které vaše aplikace vyžaduje, aby hostující platforma mohla instalovat závislosti, nikoli vyžadovat, abyste jako součást nasazení zahrnuli **složku \_ moduly uzlu** . Po nasazení aplikace se pomocí příkazu **npm Install** analyzuje **package.jsv** souboru a nainstaluje všechny uvedené závislosti.

Během vývoje můžete při instalaci modulů použít parametry **--Save**, **--Save-dev**, nebo **--Save-Option** , abyste mohli přidat položku pro modul **package.js** do souboru automaticky. Další informace najdete v tématu [npm-Install](https://docs.npmjs.com/cli/install).

Jedním z možných problémů s **package.jsv** souboru je, že určuje pouze verzi pro závislosti nejvyšší úrovně. Každý instalovaný modul může nebo nemusí určovat verzi modulů, na kterých závisí, a proto je možné, že můžete končit jiným řetězem závislostí, než který se používá při vývoji.

> [!NOTE]
> Při nasazení na Azure App Service, pokud <b>package.jsna</b> soubor odkazuje na nativní modul, může se při publikování aplikace pomocí Gitu zobrazit chybová zpráva podobná následujícímu příkladu:
>
> NPM ERR! module-name@0.6.0 instalace: node-gyp Configure Build
>
> NPM ERR! příkaz "cmd"/c "Node-gyp Configure Build" se nezdařil. 1
>
>

### <a name="using-a-npm-shrinkwrapjson-file"></a>Použití npm-shrinkwrap.jsv souboru
**npm-shrinkwrap.jsv** souboru je pokus o řešení omezení verzí modulu **package.jsv** souboru. I když **package.jsv** souboru obsahuje jenom verze modulů nejvyšší úrovně, **npm-shrinkwrap.jsv** souboru obsahuje požadavky na verzi pro úplný řetěz závislostí modulu.

Když je vaše aplikace připravená na produkční prostředí, můžete uzamknout požadavky na verzi a vytvořit **npm-shrinkwrap.jspro** soubor pomocí příkazu **shrinkwrap pro npm** . Tento příkaz použije verze, které jsou aktuálně nainstalované ve **složce \_ modulů uzlů** , a zaznamená tyto verze do **npm-shrinkwrap.js** souboru. Po nasazení aplikace do hostitelského prostředí se pomocí příkazu **npm Install** analyzuje **npm-shrinkwrap.jsv** souboru a nainstaluje všechny uvedené závislosti. Další informace najdete v tématu [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Při nasazení na Azure App Service, pokud <b>npm-shrinkwrap.jsna</b> soubor odkazuje na nativní modul, může se při publikování aplikace pomocí Gitu zobrazit chybová zpráva podobná následujícímu příkladu:
>
> NPM ERR! module-name@0.6.0 instalace: node-gyp Configure Build
>
> NPM ERR! příkaz "cmd"/c "Node-gyp Configure Build" se nezdařil. 1
>
>

## <a name="next-steps"></a>Další kroky
Teď, když jste se seznámili s používáním modulů Node.js s Azure, se naučíte, jak [určit Node.js verzi](https://github.com/squillace/nodejs-microservice), [sestavit a nasadit Node.js webovou aplikaci](app-service/quickstart-nodejs.md)a [Jak používat rozhraní Azure Command-Line pro Mac a Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Další informace najdete ve [Středisku pro vývojáře Node.js](/azure/developer/javascript/).

[specify the Node.js version]: ./app-service/overview.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo