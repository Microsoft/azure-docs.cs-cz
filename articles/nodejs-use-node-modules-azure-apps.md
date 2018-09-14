---
title: Práce s moduly Node.js
description: Zjistěte, jak pracovat s Node.js modulů při použití služby Azure App Service nebo cloudové služby.
services: ''
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: 045250f0b0f97cbefe05b36f1c8d4480244a172d
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45575843"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Používání modulů Node.js s aplikacemi Azure
Tento dokument obsahuje pokyny k používání modulů Node.js s aplikacemi hostovanými na Azure. Poskytuje rady, jak zajistit, aby aplikace používala konkrétní verzi modulu a jak používat nativní moduly s Azure.

Pokud jste už obeznámení s používání modulů Node.js **package.json** a **npm shrinkwrap.json** soubory, následující informace poskytují rychlý přehled, co je popsáno v tomto článku:

* Azure App Service rozumí **package.json** a **npm shrinkwrap.json** soubory a můžete nainstalovat moduly založené na položky v těchto souborech.

* Azure Cloud Services očekává, že všechny moduly nainstalují ve vývojovém prostředí a **uzel\_moduly** adresáře, které mají být zahrnuty jako součást balíčku pro nasazení. Je možné povolit podporu pro instalaci modulů pomocí **package.json** nebo **npm shrinkwrap.json** soubory na Cloud Services, ale tato konfigurace vyžaduje přizpůsobení výchozí skripty používané projektů cloudové služby. Příklad způsobu konfigurace tohoto prostředí, najdete v části [úloha spuštění Azure ke spuštění npm instalovat do čipu TPM nenasazujte moduly node](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Virtuální počítače Azure nejsou popsané v tomto článku, protože prostředí nasazení na virtuálním počítači je závislá na operační systém, který je hostitelem virtuálního počítače.
> 
> 

## <a name="nodejs-modules"></a>Moduly Node.js
Moduly jsou zaveditelný balíčky jazyka JavaScript, které poskytují konkrétní funkce pro vaši aplikaci. Moduly se obvykle instalují pomocí **npm** příkazového řádku nástroj, ale jsou k dispozici některé moduly (například modul pro http) jako součást balíčku core Node.js.

Pokud jsou nainstalované moduly, jsou uloženy v **uzel\_moduly** adresáře v kořenové složce adresářové struktury aplikace. Každý modul v rámci **uzel\_moduly** directory udržuje svůj vlastní adresáře, který obsahuje všechny moduly, které závisí na a toto chování se opakuje pro každý modul úplně řetězu závislostí. Toto prostředí umožňuje každý modul nainstalovaný má vlastní požadavky na verzi pro moduly, že závisí na oblasti, ale to může vést k poměrně velké adresářovou strukturu.

Nasazení **uzel\_moduly** adresář jako součást aplikace dojde ke zvětšení nasazení selhávajícími **package.json** nebo  **npm shrinkwrap.json** souboru; však zaručit, že verze moduly používané v produkčním prostředí jsou stejné jako moduly používané ve vývoji.

### <a name="native-modules"></a>Nativní moduly
Většina modulů jsou soubory jazyka JavaScript jednoduše prostého textu, některé moduly jsou specifické pro platformu binární imagí. Tyto moduly jsou kompilovány během instalace, obvykle pomocí Pythonu a gyp uzlu. Protože využívají služby Azure Cloud Services **uzel\_moduly** složky, které jsou nasazované jako součást aplikace, všechny nativní modul zahrnutý jako součást nainstalovaných modulů by měly fungovat v cloudové službě, jako je nainstalovaný a zkompilovaných na vývojovém systému Windows.

Azure App Service nepodporuje všechny nativní moduly a může selhat při kompilaci moduly s konkrétní požadavky. I když některé oblíbené moduly, například MongoDB závislostmi volitelné nativní a fungovat bez nich, dvě řešení program zaznamenal úspěch již s téměř všechny nativní moduly, které jsou k dispozici ještě dnes:

* Spustit **npm nainstalujte** na počítači s Windows, který má všechny nativního modulu nainstalovány požadované součásti. Potom nasaďte vytvořený **uzel\_moduly** složku jako součást aplikace do služby Azure App Service.

  * Před kompilací, zkontrolujte, že vaše místní instalace Node.js má odpovídající architekturu a verze není co nejblíže k jednomu použít v Azure (aktuální hodnoty můžete kontrolovat v modulu runtime z vlastností **process.arch** a **process.version**).

* Azure App Service lze nakonfigurovat pro spuštění vlastního prostředí bash nebo skripty prostředí během nasazení, získáte možnost spustit vlastní příkazy a přesně tak, jak nastavit **npm nainstalujte** je spuštěn. Video ukazuje, jak nakonfigurovat prostředí najdete v tématu [vlastní skripty nasazení webu pomocí Kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Použití souboru package.json

**Package.json** soubor je způsob, jak určit závislosti nejvyšší úrovně, vaše aplikace vyžaduje, aby hostující platforma můžete nainstalovat závislosti, spíše než by bylo potřeba zahrnout **uzlu\_ moduly** složky jako součást svého nasazení. Po nasazení aplikace **npm nainstalujte** příkaz slouží k analýze **package.json** souboru a nainstaluje všechny potřebné závislé uvedené.

Během vývoje, můžete použít **--Uložit**, **– vývoj Uložit**, nebo **– volitelné uložení** parametry při instalaci modulů k přidání položky modulu do vaší **package.json** soubor automaticky. Další informace najdete v tématu [npm install](https://docs.npmjs.com/cli/install).

Jeden možný problém s **package.json** soubor je, že pouze určuje verzi pro závislosti nejvyšší úrovně. Každý nainstalovaný modul může nebo nemůže určit verzi modulů, které závisí na a proto je možné, že můžete skončit s jinou kdekoliv než jeden použitý ve vývoji.

> [!NOTE]
> Při nasazování do služby Azure App Service, pokud vaše <b>package.json</b> soubor odkazuje na nativní modul při publikování aplikace pomocí Gitu, může se zobrazit chyba podobná následujícímu příkladu:
> 
> npm ERR! module-name@0.6.0 instalace: "uzel gyp sestavení můžete konfigurovat.
> 
> npm ERR! "cmd"/ c""sestavení konfigurace uzlu gyp"' se nezdařilo s 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Použití souboru npm shrinkwrap.json
**Npm shrinkwrap.json** soubor při pokusu o adres omezení správy verzí modulu **package.json** souboru. Zatímco **package.json** soubor obsahuje pouze verze pro moduly nejvyšší úrovně, **npm shrinkwrap.json** soubor obsahuje požadavky na verzi pro řetězec závislostí úplné modulu.

Když je aplikace připravená pro produkční prostředí, můžete zamezit požadavky na verzi a vytvoření **npm shrinkwrap.json** souboru pomocí **npm shrinkwrap** příkaz. Tento příkaz použije verze nainstalovaná v **uzel\_moduly** složky a zaznamenat tyto verze **npm shrinkwrap.json** souboru. Po nasazení aplikace do hostitelského prostředí **npm nainstalujte** příkaz slouží k analýze **npm shrinkwrap.json** souboru a nainstaluje všechny potřebné závislé uvedené. Další informace najdete v tématu [npm shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Při nasazování do služby Azure App Service, pokud vaše <b>npm shrinkwrap.json</b> soubor odkazuje na nativní modul při publikování aplikace pomocí Gitu, může se zobrazit chyba podobná následujícímu příkladu:
> 
> npm ERR! module-name@0.6.0 instalace: "uzel gyp sestavení můžete konfigurovat.
> 
> npm ERR! "cmd"/ c""sestavení konfigurace uzlu gyp"' se nezdařilo s 1
> 
> 

## <a name="next-steps"></a>Další postup
Teď, když rozumíte používání modulů Node.js s Azure, přečtěte si postup [určení verze Node.js](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md), [sestavovat a nasazovat webové aplikace Node.js](app-service/app-service-web-get-started-nodejs.md), a [použití příkazového řádku Azure Rozhraní pro Mac a Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Další informace najdete ve [Středisku pro vývojáře Node.js](/nodejs/azure/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
