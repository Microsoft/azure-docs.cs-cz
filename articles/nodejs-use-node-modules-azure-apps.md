---
title: Práce s moduly Node.js
description: Zjistěte, jak pracovat s moduly Node.js při používání azure app služby nebo cloudových služeb.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70309277"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Používání modulů Node.js s aplikacemi Azure
Tento dokument obsahuje pokyny k používání modulů Node.js s aplikacemi hostovanými v Azure. Poskytuje pokyny k zajištění, že vaše aplikace používá konkrétní verzi modulu, stejně jako pomocí nativní moduly s Azure.

Pokud jste již obeznámeni s použitím modulů Node.js, **package.json** a **npm-shrinkwrap.json,** následující informace poskytují stručný přehled toho, co je popsáno v tomto článku:

* Azure App Service rozumí **package.json** a **npm-shrinkwrap.json** soubory a můžete nainstalovat moduly na základě položek v těchto souborech.

* Azure Cloud Services očekává, že všechny moduly, které mají být nainstalovány ve vývojovém prostředí a adresář **modulů uzlů,\_** které mají být zahrnuty jako součást balíčku nasazení. Je možné povolit podporu pro instalaci modulů pomocí **package.json** nebo **npm-shrinkwrap.json** soubory na cloudservices; tato konfigurace však vyžaduje přizpůsobení výchozích skriptů používaných projekty cloudové služby. Příklad konfigurace tohoto prostředí najdete v [tématu Úloha po spuštění Azure pro spuštění instalace npm, abyste se vyhnuli nasazení modulů uzlů.](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Virtuální počítače Azure nejsou popsané v tomto článku, protože prostředí nasazení ve virtuálním počítači závisí na operačním systému hostovaném virtuálním počítačem.
> 
> 

## <a name="nodejs-modules"></a>Moduly Node.js
Moduly jsou načítatelné balíčky JavaScriptu, které poskytují specifické funkce pro vaši aplikaci. Moduly jsou obvykle instalovány pomocí nástroje příkazového řádku **npm,** ale některé moduly (například modul http) jsou k dispozici jako součást balíčku Core Node.js.

Při instalaci modulů jsou uloženy v adresáři **modulů uzlů\_** v kořenovém adresáři adresáře aplikace. Každý modul v adresáři **modulů uzlů\_** udržuje svůj vlastní adresář, který obsahuje všechny moduly, na kterých závisí, a toto chování se opakuje pro každý modul úplně dolů v řetězci závislostí. Toto prostředí umožňuje každému nainstalovanému modulu mít vlastní požadavky na verzi modulů, na kterých závisí, může však mít za následek poměrně rozsáhlou adresářovou strukturu.

Nasazení adresáře **modulů\_uzlů** jako součást aplikace zvyšuje velikost nasazení ve srovnání s použitím souboru **package.json** nebo **npm-shrinkwrap.json;** zaručuje však, že verze modulů používaných ve výrobě jsou stejné jako moduly používané ve vývoji.

### <a name="native-modules"></a>Nativní moduly
Zatímco většina modulů jsou jednoduše soubory JavaScript ve formátu prostého textu, některé moduly jsou binární obrázky specifické pro platformu. Tyto moduly jsou kompilovány v době instalace, obvykle pomocí Pythonu a node-gyp. Vzhledem k tomu, že Azure Cloud Services spoléhají na složku **modulů uzlů,\_** která se nasazuje jako součást aplikace, měl by jakýkoli nativní modul zahrnutý jako součást nainstalovaných modulů fungovat v cloudové službě, pokud byla nainstalována a zkompilována ve vývojovém systému Windows.

Azure App Service nepodporuje všechny nativní moduly a může selhat při kompilaci modulů s konkrétní mise. Zatímco některé populární moduly, jako je MongoDB, mají volitelné nativní závislosti a fungují bez nich dobře, dvě řešení se ukázala jako úspěšná s téměř všemi nativními moduly, které jsou dnes k dispozici:

* Spusťte **instalaci npm** na počítači se systémem Windows, který má nainstalovány všechny požadavky nativního modulu. Potom nasadit vytvořené **moduly uzlů\_** složky jako součást aplikace do služby Azure App Service.

  * Před kompilací zkontrolujte, zda má místní instalace Node.js odpovídající architekturu a verze je co nejblíže té, která se používá v Azure (aktuální hodnoty lze zkontrolovat za běhu z vlastností **process.arch** a **process.version**).

* Azure App Service lze nakonfigurovat tak, aby spouštět vlastní bash nebo shell skripty během nasazení, což vám dává možnost spouštět vlastní příkazy a přesně nakonfigurovat způsob, jakým se spouští **instalace npm.** Video, které ukazuje, jak toto prostředí konfigurovat, najdete v [tématu Vlastní skripty pro nasazení webu s kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Použití souboru package.json

Soubor **package.json** je způsob, jak určit nejvyšší úroveň závislostí, které vaše aplikace vyžaduje, aby hostitelská platforma mohla nainstalovat závislosti, spíše než vyžadovat, abyste jako součást nasazení zahrnuli složku **modulů uzlů.\_** Po nasazení aplikace se příkaz **npm install** použije k analýzě souboru **package.json** a k instalaci všech uvedených závislostí.

Během vývoje můžete při instalaci modulů použít parametry **--save**, **--save-dev**nebo **--save-save-optional** pro automatické přidání položky modulu do souboru **package.json.** Další informace naleznete v [tématu npm-install](https://docs.npmjs.com/cli/install).

Jeden potenciální problém se souborem **package.json** je, že určuje pouze verzi pro závislosti nejvyšší úrovně. Každý nainstalovaný modul může nebo nemusí určit verzi modulů, na kterých závisí, a proto je možné, že můžete skončit s jiným řetězcem závislostí, než který se používá ve vývoji.

> [!NOTE]
> Při nasazování do služby Azure App Service, pokud váš soubor <b>package.json</b> odkazuje na nativní modul, může se při publikování aplikace pomocí Gitu zobrazit chyba podobná následujícímu příkladu:
> 
> npm ERR! module-name@0.6.0instalace: 'node-gyp konfigurovat sestavení'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build" se nezdařilo s 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Použití souboru npm-shrinkwrap.json
Soubor **npm-shrinkwrap.json** je pokus o řešení omezení správy verzí modulu souboru **package.json.** Zatímco soubor **package.json** obsahuje pouze verze pro moduly nejvyšší úrovně, soubor **npm-shrinkwrap.json** obsahuje požadavky na verzi pro celý řetězec závislostí modulu.

Když je vaše aplikace připravena k produkčnímu prostředí, můžete uzamknout požadavky na verzi a vytvořit soubor **npm-shrinkwrap.json** pomocí příkazu **npm shrinkwrap.** Tento příkaz použije verze aktuálně nainstalované ve složce **modulů uzlů\_** a zaznamená tyto verze do souboru **npm-shrinkwrap.json.** Po nasazení aplikace do hostitelského prostředí se příkaz **instalace npm** používá k analýzě souboru **npm-shrinkwrap.json** a k instalaci všech uvedených závislostí. Další informace naleznete v tématu [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Při nasazování do služby Azure App Service, pokud váš soubor <b>npm-shrinkwrap.json</b> odkazuje na nativní modul, může se při publikování aplikace pomocí Gitu zobrazit chyba podobná následujícímu příkladu:
> 
> npm ERR! module-name@0.6.0instalace: 'node-gyp konfigurovat sestavení'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build" se nezdařilo s 1
> 
> 

## <a name="next-steps"></a>Další kroky
Teď, když rozumíte tomu, jak používat moduly Node.js s Azure, se dozvíte, jak [určit verzi Node.js](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md), [sestavit a nasadit webovou aplikaci Node.js](app-service/app-service-web-get-started-nodejs.md)a [jak používat rozhraní Příkazového řádku Azure pro Mac a Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Další informace najdete ve [Středisku pro vývojáře Node.js](/azure/javascript/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
