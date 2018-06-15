---
title: Práce s moduly Node.js
description: Naučte se pracovat s modulů Node.js při použití služby Azure App Service nebo cloudové služby.
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
ms.openlocfilehash: 76679ea0ff2c1e88d1923488717a245351437165
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864053"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Používání modulů Node.js s aplikacemi Azure
Tento dokument obsahuje pokyny k používání modulů Node.js s aplikacemi, které jsou hostované v Azure. Poskytuje rady, jak zajistit, aby aplikace používala konkrétní verzi modulu, a jak používat nativní moduly s Azure.

Pokud jste již obeznámeni s použitím modulů Node.js **package.json** a **npm shrinkwrap.json** soubory, následující informace poskytují rychlý přehled co je popsané v tomto článku:

* Aplikační služba Azure rozumí **package.json** a **npm shrinkwrap.json** soubory a můžete nainstalovat moduly založené na položky v těchto souborech.

* Azure Cloud Services očekávat všechny moduly být nainstalovány na vývojového prostředí a **uzlu\_moduly** adresáři, který bude součástí balíčku pro nasazení. Je možné povolit podporu pro instalaci modulů pomocí **package.json** nebo **npm shrinkwrap.json** soubory na cloudových služeb, ale tato konfigurace vyžaduje vlastní skripty výchozí používané projekty cloudových služeb. Příklad způsobu konfigurace tohoto prostředí naleznete v části [úloha spuštění Azure ke spuštění instalace npm předejdete nasazení uzlu moduly](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Virtuální počítače Azure nejsou popsané v tomto článku se jako možnosti nasazení do virtuálního počítače, je závislá na operační systém, který je hostitelem virtuálního počítače.
> 
> 

## <a name="nodejs-modules"></a>Modulů Node.js
Moduly jsou načíst JavaScript balíčky, které poskytují funkce specifická pro vaši aplikaci. Moduly jsou obvykle nainstalován pomocí **npm** příkazového řádku nástroje, ale jsou uvedeny některé moduly (jako je například modul http) jako součást balíčku Node.js jádra.

Při instalaci moduly jsou uložené ve **uzlu\_moduly** adresář v kořenovém adresáři aplikace adresářové struktury. Každý modul v rámci **uzlu\_moduly** directory udržuje vlastní **uzlu\_moduly** adresář, který obsahuje všechny moduly, které závisí na a toto chování se opakuje pro každý modul zcela v řetězu závislostí. Toto prostředí umožňuje každý modul nainstalovaný mít vlastní požadavky verze pro moduly, že závisí na, ale může vést k poměrně velké adresářové struktury.

Nasazení **uzlu\_moduly** adresáři jako součást aplikace zvětšuje velikost nasazení ve srovnání s pomocí **package.json** nebo **npm shrinkwrap.json** soubor; však zaručit, že verze moduly používané v produkčním prostředí jsou stejné jako moduly používané v vývoj.

### <a name="native-modules"></a>Nativní moduly
Většina moduly jsou soubory JavaScript jednoduše prostého textu, některé moduly jsou specifické pro platformu binární bitové kopie. Tyto moduly jsou kompilovány během instalace, obvykle pomocí Pythonu a gyp uzlu. Vzhledem k tomu, že Azure Cloud Services spoléhají na **uzlu\_moduly** složky, které jsou nasazované jako součást aplikace, všechny nativní modul, jsou součástí nainstalovaných modulů by měla fungovat v cloudové službě Pokud byl nainstalován a kompilovat v systému Windows, vývoj.

Aplikační služba Azure nepodporuje všechny nativní moduly a může selhat, když kompilujete modulů s konkrétní požadavky. I když některé oblíbené moduly jako MongoDB mají volitelné nativní závislosti a bez problémů fungují bez nich, prokázat dvě řešení úspěšné s téměř všechny nativní moduly, které jsou k dispozici dnes:

* Spustit **npm nainstalujte** na počítači s Windows, který má nainstalovány požadované součásti všechny nativní modul na. Potom nasaďte vytvořený **uzlu\_moduly** složku v rámci aplikace do služby Azure App Service.

  * Před kompilování, zkontrolujte, zda vaše místní instalaci Node.js má odpovídající architekturu a verze je co nejblíže k jednomu používá v Azure (aktuální hodnoty lze zkontrolovat na runtime z vlastností **process.arch** a **process.version**).

* Aplikační služba Azure je možné nakonfigurovat na provedení vlastní bash nebo skripty prostředí během nasazení, budete moci provést vlastní příkazy a přesně nastavit způsob **npm nainstalujte** je spuštěn. Video znázorňující postup konfigurace prostředí najdete v tématu [skriptů nasazení vlastní web s Kudu].

### <a name="using-a-packagejson-file"></a>Pomocí souboru package.json

**Package.json** soubor je způsob, jak určit závislosti nejvyšší úrovně, vaše aplikace vyžaduje, aby hostující platforma můžete nainstalovat závislosti, nikoli by bylo potřeba zahrnout **uzlu\_ moduly** složku jako součást nasazení. Po nasazení aplikace **npm nainstalujte** příkaz slouží k analýze **package.json** souboru a nainstalujte všechny uvedené závislosti.

Během vývoje, můžete použít **– Uložit**, **– uložit dev**, nebo **– uložit volitelné** parametry při instalaci modulů, které chcete přidat záznam pro modulu vaše **package.json** souboru automaticky. Další informace najdete v tématu [npm install](https://docs.npmjs.com/cli/install).

Jedním z možných problémů s **package.json** soubor je, že pouze určuje verzi nejvyšší úrovně závislosti. Každý modul nainstalovaný může nebo nemusí zadejte moduly, které závisí na verzi, a proto je možné, že můžete dospět řetězem závislostí jiný než ten, který je používán vývoj.

> [!NOTE]
> Při nasazování do služby Azure App Service, pokud vaše <b>package.json</b> soubor odkazuje na nativní modul při publikování aplikace pomocí Git, může se zobrazit chyba podobná v následujícím příkladu:
> 
> npm ERR! module-name@0.6.0Nainstalujte: 'uzlu gyp konfigurace buildu.
> 
> npm ERR! ' cmd "/ c" "uzlu gyp konfigurace sestavení" ' se nezdařilo s 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Pomocí npm shrinkwrap.json souboru.
**Npm shrinkwrap.json** soubor je pokus o adres omezení verze modulu **package.json** souboru. Při **package.json** soubor obsahuje pouze verze pro moduly nejvyšší úrovně, **npm shrinkwrap.json** soubor obsahuje požadavky na verzi řetězu závislostí úplné modulu.

Když je aplikace připravená pro vydání, můžete uzamknout požadavky verze a vytvořit **npm shrinkwrap.json** soubor pomocí **npm shrinkwrap** příkaz. Tento příkaz bude používat na aktuálně nainstalovaná verze **uzlu\_moduly** složku a zaznamenejte tyto verze **npm shrinkwrap.json** souboru. Po nasazení aplikace do hostitelského prostředí **npm nainstalujte** příkaz slouží k analýze **npm shrinkwrap.json** souboru a nainstalujte všechny uvedené závislosti. Další informace najdete v tématu [npm shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Při nasazování do služby Azure App Service, pokud vaše <b>npm shrinkwrap.json</b> soubor odkazuje na nativní modul při publikování aplikace pomocí Git, může se zobrazit chyba podobná v následujícím příkladu:
> 
> npm ERR! module-name@0.6.0Nainstalujte: 'uzlu gyp konfigurace buildu.
> 
> npm ERR! ' cmd "/ c" "uzlu gyp konfigurace sestavení" ' se nezdařilo s 1
> 
> 

## <a name="next-steps"></a>Další kroky
Teď, když chápete, jak používání modulů Node.js s Azure, se naučíte, jak [určení verze Node.js], [sestavení a nasazení webové aplikace Node.js](app-service/app-service-web-get-started-nodejs.md), a [jak používat rozhraní příkazového řádku Azure pro Mac a Linux].

Další informace najdete ve [Středisku pro vývojáře Node.js](/nodejs/azure/).

[určení verze Node.js]: nodejs-specify-node-version-azure-apps.md
[jak používat rozhraní příkazového řádku Azure pro Mac a Linux]:cli-install-nodejs.md
[skriptů nasazení vlastní web s Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
