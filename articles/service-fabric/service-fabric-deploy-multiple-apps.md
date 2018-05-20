---
title: Nasazení aplikace Node.js, která používá MongoDB do Azure Service Fabric | Microsoft Docs
description: Návod o tom, jak balíček více spustitelné soubory hosta k nasazení clusteru služby Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 9a7ab3881cd1058a60ff7d5f6e50c296f042e76e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="deploy-multiple-guest-executables"></a>Nasazení několika hostujících spustitelných souborů
Tento článek ukazuje, jak pro balíček a nasazení více spustitelné soubory hosta pro Azure Service Fabric. Vytváření a nasazování jeden balíček Service Fabric najdete v návodu k [nasadit do Service Fabric Host spustitelný soubor](service-fabric-deploy-existing-app.md).

Když tento návod ukazuje, jak nasadit aplikaci s Node.js front-end, který jako úložiště dat používá MongoDB, můžete použít kroky pro každou aplikaci, která má závislosti na jinou aplikaci.   

Visual Studio můžete použít k vytvoření balíček aplikace, který obsahuje více spustitelné soubory hosta. V tématu [pomocí sady Visual Studio balíčku existující aplikaci](service-fabric-deploy-existing-app.md). Po přidání první hosta spustitelný soubor, klikněte pravým tlačítkem na projekt aplikace a vyberte **Přidat -> Nový Service Fabric service** přidat druhý hosta spustitelný projekt do řešení. Poznámka: Pokud zvolíte možnost propojit zdroje v projektu sady Visual Studio, vytváření řešení sady Visual Studio, budou se, že je aktuální pomocí změn ve zdroji balíčku aplikace. 

## <a name="samples"></a>Ukázky
* [Ukázka pro balení a nasazení spustitelný soubor hosta](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázka dvěma hosta spustitelné soubory (C# a nodejs) komunikaci přes službu Naming pomocí REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Ručně balíček více hosta spustitelná aplikace
Případně můžete ručně balíček Host spustitelný soubor. Pro ruční vytváření balíčků, tento článek používá nástroj balení Service Fabric, která je k dispozici v [ http://aka.ms/servicefabricpacktool ](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Zabalení aplikace Node.js
Tento článek předpokládá, že Node.js není nainstalována na uzly v clusteru Service Fabric. V důsledku toho je nutné přidat do kořenového adresáře aplikace uzlu před balení Node.exe. Struktura adresářů aplikace Node.js (s použitím expresního webová architektura a modulu Jade šablon) by měl vypadat podobná té následující:

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

Jako další krok vytvoříte balíček aplikace pro aplikaci Node.js. Následující kód vytvoří balíček aplikace Service Fabric, která obsahuje aplikaci Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Níže je uveden popis parametry, které jsou používány:

* **/ source** odkazuje na adresář aplikace, která by měla být zabalena.
* **/ target** definuje adresář, ve kterém by měl být balíček vytvořen. Tento adresář musí být odlišný od zdrojového adresáře.
* **příkazy/appname** definuje název aplikace existující aplikace. Je důležité si uvědomit, že to znamená, že k názvu služby v manifestu a nikoliv k názvu aplikace Service Fabric.
* **/exe** definuje spustitelný soubor, který by měl Service Fabric spustíte v tomto případě `node.exe`.
* **/Ma** definuje argument, který slouží ke spuštění spustitelného souboru. Jako Node.js není nainstalovaná, Service Fabric musí spusťte webový server Node.js spuštěním `node.exe bin/www`.  `/ma:'bin/www'` informuje nástroj balení používat `bin/www` jako argument pro node.exe.
* **Nebo typ aplikace** definuje název typu aplikace Service Fabric.

Pokud přejdete do adresáře, která byla zadaná v parametru/Target, uvidíte, že nástroj vytvořil plně funkční balíček Service Fabric, jak je uvedeno níže:

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Vygenerovaný ServiceManifest.xml teď má oddíl, který popisuje, jak Node.js webového serveru musí být spuštěna, jak je znázorněno v následujícím fragmentu kódu:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
V této ukázce Node.js webový server naslouchá na port 3000, proto musíte aktualizovat informace o koncový bod v souboru ServiceManifest.xml, jak je uvedeno níže.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Balení aplikací MongoDB
Teď, když máte zabalené aplikace Node.js, můžete přejít k tématu a balíček MongoDB. Jak je uvedeno nahoře, kroky, které teď projít nejsou specifické pro Node.js a MongoDB. Ve skutečnosti se vztahují na všechny aplikace, které jsou určené pro zabalené společně jako jednu aplikaci Service Fabric.  

Chcete-li balíček MongoDB, budete chtít Ujistěte se, že balíček Mongod.exe a Mongo.exe. Obě binární soubory jsou umístěny ve `bin` adresář adresáře instalace MongoDB. Vypadá podobná té následující adresářovou strukturu.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric musí začínat MongoDB podobné tomu příkaz níže, takže budete muset použít `/ma` parametr při balení MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Data není se zachovají v případě selhání uzlu, když vložíte do adresáře dat MongoDB v místním adresáři uzlu. Buď použijte odolná úložiště nebo implementovat prevence ztráty dat sady replik MongoDB.  
>
>

V prostředí PowerShell nebo příkazové okno jsme spustit nástroj balení s následujícími parametry:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Aby bylo možné přidat do balíčku aplikace Service Fabric MongoDB, budete muset Ujistěte se, že odkazuje parametr/target na stejný adresář, který již obsahuje aplikaci manifestu spolu s aplikací Node.js. Budete také muset Ujistěte se, že používáte stejný název ApplicationType.

Pojďme vyhledejte adresář a zkontrolujte, co nástroj vytvořil.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Jak vidíte, nástroj Přidat novou složku, MongoDB, k adresáři, který obsahuje binární soubory MongoDB. Pokud otevřete `ApplicationManifest.xml` souboru, zobrazí se balíček teď obsahuje aplikace Node.js i MongoDB. Následující kód ukazuje obsah manifestu aplikace.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>Publikování aplikace
Posledním krokem je k publikování aplikace pro místní cluster Service Fabric pomocí skriptů prostředí PowerShell, níže:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Jakmile se k místnímu clusteru se daná aplikace úspěšně publikuje, dostanete aplikace Node.js na portu, který jsme zadali v service manifest aplikace Node.js – například http://localhost:3000.

V tomto kurzu jste viděli, jak snadno balíček dvě existující aplikace jako jednu aplikaci Service Fabric. Naučili jste postup nasazení na Service Fabric, takže můžete využít některé funkce Service Fabric, jako například vysokou dostupnost a stavu systému integrace.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Přidání další spustitelné soubory hosta do existující aplikace pomocí Yeoman v systému Linux

Pokud chcete přidat další službu do aplikace již vytvořené pomocí `yo`, proveďte následující kroky: 
1. Změňte adresář na kořenovou složku stávající aplikace.  Například `cd ~/YeomanSamples/MyApplication`, pokud `MyApplication` je aplikace vytvořená pomocí Yeomanu.
2. Spustit `yo azuresfguest:AddService` a zadejte potřebné detaily.

## <a name="next-steps"></a>Další postup
* Další informace o nasazení kontejnery s [Service Fabric a kontejnery – přehled](service-fabric-containers-overview.md)
* [Ukázka pro balení a nasazení spustitelný soubor hosta](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázka dvěma hosta spustitelné soubory (C# a nodejs) komunikaci přes službu Naming pomocí REST](https://github.com/Azure-Samples/service-fabric-containers)
