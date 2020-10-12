---
title: Zabalení stávajícího spustitelného souboru do Azure Service Fabric
description: Přečtěte si o sbalení existující aplikace jako spustitelného souboru hosta, aby ji bylo možné nasadit do clusteru Service Fabric.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 8b808d092001196a4d2150e44d508e031db95554
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247383"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Nasaďte existující spustitelný soubor pro Service Fabric
Jako službu můžete v Azure Service Fabric spustit libovolný typ kódu, jako je například Node.js, Java nebo C++. Service Fabric odkazuje na tyto typy služeb jako spustitelné soubory typu Host.

Spustitelné soubory hosta jsou ošetřeny Service Fabric jako bezstavové služby. V důsledku toho jsou umístěny na uzlech v clusteru, a to na základě dostupnosti a dalších metrik. Tento článek popisuje, jak zabalit a nasadit spustitelný soubor hosta do clusteru Service Fabric, pomocí sady Visual Studio nebo nástroje příkazového řádku.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Výhody spuštění hostovaného spustitelného souboru v Service Fabric
Pro spuštění spustitelného souboru hosta v Service Fabric clusteru je k dispozici několik výhod:

* Vysoká dostupnost Aplikace, které běží v Service Fabric, jsou vytvořeny jako vysoce dostupné. Service Fabric zajistí, aby byly spuštěné instance aplikace.
* Sledování stavu. Service Fabric monitoring stavu zjistí, jestli je aplikace spuštěná, a když dojde k selhání, poskytne diagnostické informace.   
* Správa životního cyklu aplikací. Kromě poskytování upgradů bez výpadků nabízí Service Fabric automatické vrácení zpět předchozí verzi, pokud dojde k chybné události stavu hlášené během upgradu.    
* Výsadb. V clusteru můžete spustit několik aplikací, což eliminuje nutnost spuštění každé aplikace na vlastním hardwaru.
* Zjistitelnost: použití REST můžete zavolat službu pojmenování Service Fabric a najít další služby v clusteru. 

## <a name="samples"></a>ukázky
* [Ukázka balení a nasazení hostovaného spustitelného souboru](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázka dvou spustitelných souborů hosta (C# a NodeJS), které komunikují přes službu pojmenování pomocí REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Přehled souborů manifestu aplikace a služby
V rámci nasazení hostovaného spustitelného souboru je užitečné pochopit Service Fabric balíčky a model nasazení, jak je popsáno v [modelu aplikace](service-fabric-application-model.md). Model Service Fabric balení spoléhá na dva soubory XML: manifesty aplikace a služby. Definice schématu pro ApplicationManifest.xml a soubory ServiceManifest.xml se nainstalují se sadou SDK Service Fabric do složky *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifest aplikace** Manifest aplikace se používá k popisu aplikace. Uvádí služby, které ji tvoří, a další parametry, které se používají k definování toho, jak má být nasazena jedna nebo více služeb, například počet instancí.

  V Service Fabric je aplikace jednotka nasazení a upgrade. Aplikaci je možné upgradovat jako jednu jednotku, kde se spravují potenciální chyby a potenciální vrácení zpět. Service Fabric garantuje, že proces upgradu je buď úspěšný, nebo, pokud se upgrade nepovede, aplikace nezůstane v neznámém nebo nestabilním stavu.
* **Manifest služby** Manifest služby popisuje součásti služby. Obsahuje data, jako jsou název a typ služby a její kód a konfigurace. Manifest služby obsahuje také některé další parametry, které lze použít ke konfiguraci služby po jejím nasazení.

## <a name="application-package-file-structure"></a>Struktura souborů balíčku aplikace
Chcete-li nasadit aplikaci pro Service Fabric, aplikace by měla dodržovat předdefinovanou adresářovou strukturu. Následuje příklad této struktury.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot obsahuje soubor ApplicationManifest.xml definující aplikaci. Podadresář pro každou službu, která je součástí aplikace, se používá k zahrnutí všech artefaktů, které služba vyžaduje. Tyto podadresáře jsou ServiceManifest.xml a obvykle následující:

* *Kód*. Tento adresář obsahuje kód služby.
* *Konfigurace*. Tento adresář obsahuje Settings.xml soubor (a v případě potřeby i další soubory), ke kterým může služba přistupovat za běhu a načíst konkrétní nastavení konfigurace.
* *Data*. Toto je další adresář pro ukládání dalších místních dat, která může služba potřebovat. Data by se měla používat k ukládání pouze dočasných dat. Service Fabric nekopíruje ani nereplikuje změny v adresáři dat, pokud je potřeba přemístění služby (například během převzetí služeb při selhání).

> [!NOTE]
> `config`Pokud je nepotřebujete, nemusíte vytvářet `data` adresáře a.
>
>

## <a name="next-steps"></a>Další kroky
Související informace a úlohy najdete v následujících článcích.
* [Nasazení hostujícího spustitelného souboru](service-fabric-deploy-existing-app.md)
* [Nasazení několika hostujících spustitelných souborů](./service-fabric-deploy-existing-app.md)
* [Vytvoření první spustitelné aplikace hosta pomocí sady Visual Studio](quickstart-guest-app.md)
* [Ukázka balení a nasazení spustitelného souboru hosta](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), včetně odkazu na předběžnou verzi nástroje pro balení
* [Ukázka dvou spustitelných souborů hosta (C# a NodeJS), které komunikují přes službu pojmenování pomocí REST](https://github.com/Azure-Samples/service-fabric-containers)
