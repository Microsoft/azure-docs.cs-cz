---
title: Nasazení aplikace Node.js využívající MongoDB do Azure Service Fabric | Dokumentace Microsoftu
description: Návod na tom, jak několika hostujících spustitelných souborů pro balíček pro nasazení do clusteru Azure Service Fabric
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
ms.openlocfilehash: ea2f27069ca445a4d74ddc634f5c396ab13564a1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248992"
---
# <a name="deploy-multiple-guest-executables"></a>Nasazení několika hostujících spustitelných souborů
Tento článek ukazuje, jak balení a nasazení několika hostujících spustitelných souborů do Azure Service Fabric. Vytváření a nasazování jednoho balíčku Service Fabric najdete v návodu k [nasazení spustitelný soubor typu Host do Service Fabric](service-fabric-deploy-existing-app.md).

Zatímco tento návod ukazuje, jak nasadit aplikaci s front-endu na Node.js, která jako úložiště dat používá MongoDB, můžete provést kroky pro každou aplikaci, která obsahuje závislosti na jinou aplikaci.   

Visual Studio můžete použít k vytvoření balíčku aplikace, která obsahuje několika hostujících spustitelných souborů. Zobrazit [pomocí sady Visual Studio zabalit existující aplikaci](service-fabric-deploy-existing-app.md). Po přidání první spustitelný soubor typu Host, klikněte pravým tlačítkem na projekt aplikace a vyberte **Přidat -> Nový Service Fabric service** přidáte druhý projekt spustitelný soubor typu Host do řešení. Poznámka: Pokud budete chtít propojit zdroje v projektu sady Visual Studio, vytvářet řešení sady Visual Studio, budete mít jistotu, že je aktuální změny ve zdroji balíčku aplikace. 

## <a name="samples"></a>Ukázky
* [Ukázka pro balení a nasazení hostujícího spustitelného souboru](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázka dvě hosta spustitelných souborů (C# a Node.js) komunikaci přes službu pojmenování pomocí rozhraní REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Ručně balíček více aplikace spustitelná hostem
Případně můžete ručně zabalit spustitelný soubor typu Host. Pro ruční balení, tento článek používá nástroj balíčku Service Fabric, který je k dispozici na [ http://aka.ms/servicefabricpacktool ](https://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Balení aplikace Node.js
Tento článek předpokládá, že Node.js není nainstalována na uzlech v clusteru Service Fabric. V důsledku toho budete muset přidat Node.exe do kořenového adresáře aplikace uzlu před balení. Struktura adresářů aplikace Node.js (pomocí webovou architekturu Express a modul šablonovacím) by měla vypadat podobně jako následující:

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

V dalším kroku vytvoříte balíček aplikace pro aplikaci Node.js. Následující kód vytvoří balíček aplikace Service Fabric, která obsahuje aplikaci Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Níže je uveden popis parametrů, které jsou používány:

* **/ source** odkazuje na adresář aplikace, která se má zabalit.
* **/ target** definuje adresář, ve kterém by měl být balíček vytvořen. Tento adresář musí být odlišný od zdrojového adresáře.
* **příkazy/appname** definuje název aplikace existující aplikace. Je důležité pochopit, že to se přeloží na název v manifestu služby a nikoliv k názvu aplikace Service Fabric.
* **/exe** definuje spustitelného souboru, který Pokud chcete spustit, v tomto případě by měl Service Fabric `node.exe`.
* **/Ma** definuje argument, který se používá ke spuštění spustitelného souboru. Jak Node.js není nainstalovaná, Service Fabric potřebuje spustit webový server Node.js pomocí provádí `node.exe bin/www`.  `/ma:'bin/www'` říká nástroje balení pro použití `bin/www` jako argument pro node.exe.
* **/ Typ aplikace** definuje název typu aplikace Service Fabric.

Pokud přejdete do adresáře, který byl zadán parametr/target, uvidíte, že nástroj vytvořila plně funkční balíčku Service Fabric, jak je znázorněno níže:

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
Vygenerovaný ServiceManifest.xml má teď oddíl, který popisuje, jak spustit webový server Node.js, jak je znázorněno v následujícím fragmentu kódu:

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
V této ukázce webový server Node.js naslouchá port 3000, takže je potřeba aktualizovat informace o koncovém bodu v souboru ServiceManifest.xml, jak je znázorněno níže.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Balení aplikace MongoDB
Teď, když máte zabalené aplikace Node.js, vám pokračujte a balíček MongoDB. Jak už bylo uvedeno dříve, teď projít kroky nejsou specifická pro Node.js a MongoDB. Ve skutečnosti se vztahují na všechny aplikace, které mají být oprav jako jedna aplikace Service Fabric.  

Chcete-li zabalit MongoDB, ujistěte se, že balíček Mongod.exe a Mongo.exe. Obě binární soubory jsou umístěny v `bin` adresáře z adresáře instalace MongoDB. Vypadá podobně jako následující adresářovou strukturu.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric musí začínat MongoDB podobný příkaz níže, takže budete muset použít `/ma` parametr při vytváření balíčku MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Data se nezachová v případě selhání uzlu když vložíte do adresáře dat MongoDB na místní adresáře uzlu. By měl použít odolných úložišť nebo implementovat prevence ztráty dat sady replik MongoDB.  
>
>

V Powershellu nebo příkazového řádku můžeme spustit nástroj balení s následujícími parametry:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Pokud chcete přidat MongoDB balíčku aplikace Service Fabric, budete muset Ujistěte se, že odkazuje parametr/target do stejného adresáře, který již obsahuje aplikaci manifestu spolu s aplikace Node.js. Také je třeba Ujistěte se, že používáte stejný název ApplicationType.

Teď přejděte do adresáře a zkontrolujte, co nástroj vytvořil.

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
Jak je vidět, nástroj Přidat novou složku, MongoDB, k adresáři, který obsahuje binární soubory MongoDB. Pokud otevřete `ApplicationManifest.xml` souboru, uvidíte, že balíček nyní obsahuje jak aplikací v Node.js a MongoDB. Následující kód zobrazí obsah manifestu aplikace.

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
Posledním krokem je publikujte aplikaci do místního clusteru Service Fabric s využitím následujících skriptů prostředí PowerShell:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Po publikování aplikace do místního clusteru je úspěšně, můžete přístup k aplikaci Node.js na portu, který jsme zadali v manifestu služby aplikace Node.js – například http://localhost:3000.

V tomto kurzu jste viděli, jak jednoduše zabalit dvě stávajících aplikací jako jedna aplikace Service Fabric. Můžete také se naučili, jak ji nasadit do Service Fabric tak, aby ho využívat některé funkce Service Fabric, jako je vysoká dostupnost a stavu systému integrace.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Přidání další spustitelné soubory typu Host do existující aplikace v Linuxu pomocí Yeomana

Pokud chcete přidat další službu do aplikace již vytvořené pomocí `yo`, proveďte následující kroky: 
1. Změňte adresář na kořenovou složku stávající aplikace.  Například `cd ~/YeomanSamples/MyApplication`, pokud `MyApplication` je aplikace vytvořená pomocí Yeomanu.
2. Spustit `yo azuresfguest:AddService` a zadejte potřebné podrobnosti.

## <a name="next-steps"></a>Další postup
* Další informace o nasazování kontejnerů s [přehled Service Fabric a kontejnery](service-fabric-containers-overview.md)
* [Ukázka pro balení a nasazení hostujícího spustitelného souboru](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázka dvě hosta spustitelných souborů (C# a Node.js) komunikaci přes službu pojmenování pomocí rozhraní REST](https://github.com/Azure-Samples/service-fabric-containers)
