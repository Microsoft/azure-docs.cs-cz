---
title: Nasazení aplikace Node. js, která používá MongoDB
description: Návod, jak zabalit více spustitelných souborů hosta k nasazení do clusteru Azure Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4538efc8a2426fc20dd20d1a85edaf6f76bfc649
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614464"
---
# <a name="deploy-multiple-guest-executables"></a>Nasazení několika hostujících spustitelných souborů
Tento článek popisuje, jak zabalit a nasadit více spustitelných souborů hosta do Azure Service Fabric. Pro sestavování a nasazování jednoho balíčku Service Fabric si přečtěte, jak [nasadit spustitelný soubor hosta pro Service Fabric](service-fabric-deploy-existing-app.md).

I když tento návod ukazuje, jak nasadit aplikaci s front-end Node. js, který používá MongoDB jako úložiště dat, můžete použít postup pro libovolnou aplikaci, která má závislosti na jiné aplikaci.   

Sadu Visual Studio můžete použít k tvorbě balíčku aplikace, který obsahuje více spustitelných souborů typu Host. Další informace najdete v tématu [použití sady Visual Studio k zabalení existující aplikace](service-fabric-deploy-existing-app.md). Po přidání prvního spustitelného souboru hosta klikněte pravým tlačítkem na projekt aplikace a vyberte **Add-> New Service Fabric Service** a přidejte do řešení druhý spustitelný soubor hosta. Poznámka: Pokud se rozhodnete propojit zdroj v projektu sady Visual Studio, sestavte řešení sady Visual Studio a ujistěte se, že je balíček aplikace aktuální se změnami ve zdroji. 

## <a name="samples"></a>ukázky
* [Ukázka balení a nasazení hostovaného spustitelného souboru](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázka dvou spustitelných souborů hostaC# (a NodeJS), které komunikují přes službu pojmenování pomocí REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Ruční zabalení aplikace s více spustitelnými soubory hosta
Případně můžete ručně zabalit spustitelný soubor hosta. Podrobnosti najdete v tématu [ruční balení a nasazení stávajícího spustitelného souboru](service-fabric-deploy-existing-app.md#manually-package-and-deploy-an-existing-executable).

### <a name="packaging-the-nodejs-application"></a>Balení aplikace Node. js
V tomto článku se předpokládá, že Node. js není nainstalovaný na uzlech v clusteru Service Fabric. V důsledku toho je před sbalením nutné přidat Node. exe do kořenového adresáře vaší aplikace uzlu. Adresářová struktura aplikace Node. js (pomocí nástroje Express Web Framework a Jade Template Engine) by měla vypadat podobně jako na následujícím obrázku:

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

V dalším kroku vytvoříte balíček aplikace pro aplikaci Node. js. Následující kód vytvoří balíček aplikace Service Fabric, který obsahuje aplikaci Node. js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Níže je uveden popis parametrů, které se používají:

* **/source** odkazuje na adresář aplikace, který by měl být zabalen.
* **/target** definuje adresář, ve kterém by měl být balíček vytvořen. Tento adresář musí být jiný než zdrojový adresář.
* **/AppName** definuje název aplikace existující aplikace. Je důležité pochopit, že se jedná o název služby v manifestu, a ne o Service Fabric název aplikace.
* **/exe** definuje spustitelný soubor, který by Service Fabric měl spustit, v tomto případě `node.exe`.
* **/ma** definuje argument, který se používá ke spuštění spustitelného souboru. Protože Node. js není nainstalován, Service Fabric musí spustit webový server Node. js spuštěním `node.exe bin/www`.  `/ma:'bin/www'` říká nástroji pro balení, aby používal `bin/www` jako argument pro Node. exe.
* **/AppType** definuje název typu aplikace Service Fabric.

Pokud přejdete do adresáře, který byl zadán v parametru/Target, vidíte, že nástroj vytvořil plně funkční balíček Service Fabric, jak je znázorněno níže:

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
Vygenerovaný ServiceManifest. XML teď obsahuje část, která popisuje, jak by se měl spustit webový server Node. js, jak je znázorněno v následujícím fragmentu kódu:

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
V této ukázce webový server Node. js naslouchá na portu 3000, takže je třeba aktualizovat informace koncového bodu v souboru ServiceManifest. XML, jak je znázorněno níže.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Balení aplikace MongoDB
Teď, když máte zabalenou aplikaci Node. js, můžete pokračovat a zabalit MongoDB. Jak bylo zmíněno dříve, kroky, které provedete nyní, nejsou specifické pro Node. js a MongoDB. Ve skutečnosti se vztahují na všechny aplikace, které mají být zabaleny společně jako jedna aplikace Service Fabric.  

Chcete-li zabalit MongoDB, měli byste se ujistit, že jste zabalily Mongod. exe a Mongo. exe. Oba binární soubory jsou umístěné v adresáři `bin` instalačního adresáře MongoDB. Adresářová struktura vypadá podobně jako v následujícím příkladu.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric musí začít MongoDB s příkazem podobným následujícímu, takže při vytváření balíčku MongoDB musíte použít parametr `/ma`.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Data nejsou zachována v případě selhání uzlu, pokud umístíte adresář dat MongoDB do místního adresáře uzlu. Aby se zabránilo ztrátě dat, měli byste buď použít trvalé úložiště, nebo implementovat sadu replik MongoDB.  
>
>

V PowerShellu nebo příkazovém prostředí spustíme Nástroj pro vytváření balíčků s následujícími parametry:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Aby bylo možné přidat MongoDB do balíčku aplikace Service Fabric, je nutné zajistit, aby parametr/Target odkazoval na stejný adresář, který již obsahuje manifest aplikace spolu s aplikací Node. js. Musíte také zkontrolovat, zda používáte stejný název typu ApplicationType.

Pojďme přejít do adresáře a ověřit, co nástroj vytvořil.

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
Jak vidíte, nástroj přidal novou složku MongoDB do adresáře, který obsahuje binární soubory MongoDB. Pokud otevřete `ApplicationManifest.xml` soubor, uvidíte, že balíček nyní obsahuje aplikaci Node. js i MongoDB. Následující kód ukazuje obsah manifestu aplikace.

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
Posledním krokem je publikování aplikace do místního clusteru Service Fabric pomocí následujících skriptů PowerShellu:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Po úspěšném publikování aplikace do místního clusteru můžete k aplikaci Node. js přistupovat na portu, který jsme zadali v manifestu služby aplikace Node. js – například http:\//localhost: 3000.

V tomto kurzu jste viděli, jak snadno zabalit dvě existující aplikace jako jednu Service Fabric aplikaci. Zjistili jste také, jak ho nasadit, aby bylo možné Service Fabric, aby mohl využívat některé z Service Fabric funkcí, jako je například vysoká dostupnost a integrace systému pro stav.

## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Přidání dalších spustitelných souborů hosta do existující aplikace pomocí Yeoman v systému Linux

Pokud chcete přidat další službu do aplikace již vytvořené pomocí `yo`, proveďte následující kroky: 
1. Změňte adresář na kořenovou složku stávající aplikace.  Například `cd ~/YeomanSamples/MyApplication`, pokud `MyApplication` je aplikace vytvořená pomocí Yeomanu.
2. Spusťte `yo azuresfguest:AddService` a zadejte potřebné podrobnosti.

## <a name="next-steps"></a>Další kroky
* Informace o nasazení kontejnerů pomocí [Service Fabric a kontejnerů – přehled](service-fabric-containers-overview.md)
* [Ukázka balení a nasazení hostovaného spustitelného souboru](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázka dvou spustitelných souborů hostaC# (a NodeJS), které komunikují přes službu pojmenování pomocí REST](https://github.com/Azure-Samples/service-fabric-containers)
