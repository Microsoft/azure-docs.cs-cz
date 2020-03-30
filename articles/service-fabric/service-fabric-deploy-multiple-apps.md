---
title: Nasazení aplikace Node.js, která používá MongoDB
description: Návod, jak zabalit více spustitelných souborů hosta k nasazení do clusteru Azure Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4538efc8a2426fc20dd20d1a85edaf6f76bfc649
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614464"
---
# <a name="deploy-multiple-guest-executables"></a>Nasazení několika hostujících spustitelných souborů
Tento článek ukazuje, jak zabalit a nasadit více spustitelných souborů hosta do Azure Service Fabric. Při vytváření a nasazování jednoho balíčku Service Fabric se přečtěte, jak [nasadit spustitelný soubor hosta do service fabric](service-fabric-deploy-existing-app.md).

Zatímco tento návod ukazuje, jak nasadit aplikaci s front-endem Node.js, který používá MongoDB jako úložiště dat, můžete použít kroky pro všechny aplikace, které mají závislosti na jiné aplikaci.   

Pomocí sady Visual Studio můžete vytvořit balíček aplikace, který obsahuje více spustitelných souborů hosta. Viz [Použití sady Visual Studio k zabalení existující aplikace](service-fabric-deploy-existing-app.md). Po přidání prvního spustitelného souboru hosta klikněte pravým tlačítkem myši na projekt aplikace a vyberte **službu Add->New Service Fabric** a přidejte do řešení druhý spustitelný projekt hosta. Poznámka: Pokud se rozhodnete propojit zdroj v projektu sady Visual Studio, sestavení řešení Sady Visual Studio, zajistí, že balíček aplikace je aktuální se změnami ve zdroji. 

## <a name="samples"></a>ukázky
* [Ukázka pro balení a nasazení spustitelného souboru hosta](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázka dvou spustitelných souborů hosta (C# a nodejs), které komunikují prostřednictvím služby Pojmenování pomocí rest](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Ruční balíček více spustitelných aplikací hosta
Případně můžete ručně zabalit spustitelný soubor hosta. Podrobnosti naleznete [v tématu Ruční balíček a nasazení existující spustitelný soubor](service-fabric-deploy-existing-app.md#manually-package-and-deploy-an-existing-executable).

### <a name="packaging-the-nodejs-application"></a>Balení aplikace Node.js
Tento článek předpokládá, že soubor Node.js není nainstalován na uzlech v clusteru Service Fabric. V důsledku toho je třeba přidat Node.exe do kořenového adresáře aplikace uzlu před balením. Adresářová struktura aplikace Node.js (pomocí webového rámce Express a modulu šablony Jade) by měla vypadat podobně jako v následujících vlastnostech:

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

Jako další krok vytvoříte balíček aplikace pro aplikaci Node.js. Níže uvedený kód vytvoří balíček aplikace Service Fabric, který obsahuje aplikaci Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Níže je uveden popis parametrů, které se používají:

* **/source** odkazuje na adresář aplikace, která by měla být zabalena.
* **/target** definuje adresář, ve kterém má být balíček vytvořen. Tento adresář se musí lišit od zdrojového adresáře.
* **/appname** definuje název aplikace existující aplikace. Je důležité si uvědomit, že se to překládá na název služby v manifestu a nikoli na název aplikace Service Fabric.
* **/exe** definuje spustitelný soubor, který service fabric má `node.exe`spustit, v tomto případě .
* **/ma** definuje argument, který se používá ke spuštění spustitelného souboru. Protože soubor Node.js není nainstalován, je třeba aplikaci Service Fabric `node.exe bin/www`spustit webový server Node.js spuštěním souboru .  `/ma:'bin/www'`říká nástroj pro `bin/www` balení použít jako argument pro node.exe.
* **/AppType** definuje název typu aplikace Service Fabric.

Pokud přejdete do adresáře, který byl zadán v parametru /target, uvidíte, že nástroj vytvořil plně funkční balíček Service Fabric, jak je znázorněno níže:

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
Vygenerovaný soubor ServiceManifest.xml má nyní oddíl, který popisuje, jak by měl být spuštěn webový server Node.js, jak je znázorněno ve fragmentu kódu níže:

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
V této ukázce webový server Node.js naslouchá portu 3000, takže je třeba aktualizovat informace o koncovém bodu v souboru ServiceManifest.xml, jak je znázorněno níže.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Balení aplikace MongoDB
Nyní, když jste zabalili aplikaci Node.js, můžete pokračovat a zabalit MongoDB. Jak již bylo zmíněno dříve, kroky, které procházíte nyní nejsou specifické pro Node.js a MongoDB. Ve skutečnosti platí pro všechny aplikace, které jsou určeny k balení společně jako jedna aplikace Service Fabric.  

Chcete-li zabalit MongoDB, chcete se ujistit, že balíček Mongod.exe a Mongo.exe. Oba binární soubory jsou `bin` umístěny v adresáři instalačního adresáře MongoDB. Adresářová struktura vypadá podobně jako ta níže.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric musí spustit MongoDB s příkazem podobným tomu níže, `/ma` takže je třeba použít parametr při balení MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Data nejsou zachována v případě selhání uzlu, pokud vložíte datový adresář MongoDB do místního adresáře uzlu. Měli byste použít trvalé úložiště nebo implementovat sadu replik MongoDB, aby se zabránilo ztrátě dat.  
>
>

V prostředí PowerShell nebo v příkazovém prostředí spustíme balicí nástroj s následujícími parametry:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Chcete-li přidat MongoDB do balíčku aplikace Service Fabric, musíte se ujistit, že parametr /target odkazuje na stejný adresář, který již obsahuje manifest aplikace spolu s aplikací Node.js. Musíte se také ujistit, že používáte stejný název ApplicationType.

Pojďme se podívat do adresáře a prozkoumat, co nástroj vytvořil.

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
Jak můžete vidět, nástroj přidal novou složku, MongoDB, do adresáře, který obsahuje binární soubory MongoDB. Pokud `ApplicationManifest.xml` soubor otevřete, uvidíte, že balíček nyní obsahuje aplikaci Node.js i MongoDB. Níže uvedený kód zobrazuje obsah manifestu aplikace.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Posledním krokem je publikování aplikace do místního clusteru Service Fabric pomocí skriptů prostředí PowerShell níže:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Jakmile je aplikace úspěšně publikována do místního clusteru, můžete získat přístup k aplikaci Node.js na portu, který jsme\/zadali do manifestu služby aplikace Node.js-- například http: /localhost:3000.

V tomto kurzu jste viděli, jak snadno zabalit dvě existující aplikace jako jednu aplikaci Service Fabric. Také jste se naučili, jak ji nasadit do Service Fabric tak, aby mohl těžit z některých funkcí Service Fabric, jako je vysoká dostupnost a integrace systému stavu.

## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Přidání dalších spustitelných souborů hosta do existující aplikace pomocí Yeoman na Linuxu

Pokud chcete přidat další službu do aplikace již vytvořené pomocí `yo`, proveďte následující kroky: 
1. Změňte adresář na kořenovou složku stávající aplikace.  Například `cd ~/YeomanSamples/MyApplication`, pokud `MyApplication` je aplikace vytvořená pomocí Yeomanu.
2. Spusťte `yo azuresfguest:AddService` a poskytněte potřebné podrobnosti.

## <a name="next-steps"></a>Další kroky
* Informace o nasazení kontejnerů pomocí [service fabric a kontejnerů přehled](service-fabric-containers-overview.md)
* [Ukázka pro balení a nasazení spustitelného souboru hosta](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázka dvou spustitelných souborů hosta (C# a nodejs), které komunikují prostřednictvím služby Pojmenování pomocí rest](https://github.com/Azure-Samples/service-fabric-containers)
