---
title: Nasazení existujícího spustitelného souboru do Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o balení existující aplikaci jako Host spustitelného souboru, takže je možné nasadit do clusteru Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: aljo
ms.openlocfilehash: b7efeb1b4d83f6a6b372f73a7c0a5ca9bffdc052
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670589"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Nasazení existujícího spustitelného souboru do Service Fabric
Jakýkoli typ kódu, jako je Node.js, Java nebo C++ v Azure Service Fabric můžete spustit jako službu. Service Fabric odkazuje na tyto typy služeb jako hostující spustitelné soubory.

Spustitelné soubory hosta nakládá jako bezstavové služby Service Fabric. V důsledku toho jsou umístěny na uzlech v clusteru, na základě dostupnosti a další metriky. Tento článek popisuje, jak zabalit a nasadit spustitelný soubor typu Host do clusteru Service Fabric pomocí sady Visual Studio nebo nástroj příkazového řádku.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Výhody spuštění spustitelného souboru v Service Fabric Host
Existuje několik výhod ke spuštění spustitelného souboru v clusteru Service Fabric Host:

* Vysoká dostupnost Aplikace spuštěné v Service Fabric jsou vysoce dostupné. Service Fabric zajišťuje, že instance aplikace běží.
* Monitorování stavu. Monitorování stavu Service Fabric zjistí, zda aplikace běží a poskytuje diagnostické informace, pokud dojde k selhání.   
* Správa životního cyklu aplikací. Kromě zadání upgrady bez výpadků, Service Fabric nabízí automatického vrácení zpět na předchozí verzi, pokud existuje událost stavu chybný hlášená během upgradu.    
* Hustota. Můžete spustit několik aplikací v clusteru, není potřeba pro každou aplikaci spouštět na vlastní hardware.
* Zjistitelnost: Pomocí rozhraní REST může volat službě pojmenování service Fabric k nalezení dalších služeb v clusteru. 

## <a name="samples"></a>Ukázky
* [Ukázka pro balení a nasazení hostujícího spustitelného souboru](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázka dvě hosta spustitelných souborů (C# a Node.js) komunikaci přes službu pojmenování pomocí rozhraní REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Přehled aplikací a souborů manifestu služby
Jako součást nasazení hostujícího spustitelného souboru, je užitečné k pochopení modelu balení a nasazení služby Service Fabric, jak je popsáno v [aplikační model](service-fabric-application-model.md). Balení modelu Service Fabric závisí na dvou souborech XML: manifesty aplikace a služby. Definice schématu pro soubor ApplicationManifest.xml a ServiceManifest.xml se instaluje se sadou Service Fabric SDK do *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifest aplikace** manifestu aplikace se používá k popisu aplikace. Uvádí služby, které tvoří ji a musí být nasazené další parametry, které se používají k definování jak jeden nebo více služeb, jako je počet instancí.

  Aplikace v Service Fabric je jednotka nasazení a upgrade. Aplikace je možné upgradovat jako jednu jednotku, ve kterém se spravují potenciální chyby a potenciální vrácení zpět. Service Fabric zaručuje, že proces upgradu je buď úspěšné, nebo pokud upgrade selže, nenechává aplikace v neznámý nebo nestabilním stavu.
* **Manifest služby** service manifest popisuje součástí služby. Obsahuje data, jako je například název a typ služby a její kód a konfigurace. Manifest služby zahrnuje také některé další parametry, které lze použít ke konfiguraci po nasazení služby.

## <a name="application-package-file-structure"></a>Struktura souboru balíčku aplikace
K nasazení aplikace Service Fabric, by měla aplikace postupujte podle předdefinovaného adresářovou strukturu. Následuje příklad této struktury.

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

ApplicationPackageRoot obsahuje soubor ApplicationManifest.xml, který definuje aplikaci. Obsahuje všechny artefakty, které služba vyžaduje, aby společně podadresář pro každou službu obsažené v aplikaci. Tyto podsložky jsou ServiceManifest.xml a obvykle následující:

* *Kód*. Tento adresář obsahuje kódu služby.
* *Konfigurace*. Tento adresář obsahuje souboru Settings.xml (a další soubory v případě potřeby), kterým služba přístup za běhu načíst nakonfigurovat specifické nastavení.
* *Data*. Toto je další adresář k uložení další místní data, která může být nutné službu. Data by měla sloužit k ukládání jenom dočasné data. Service Fabric při kopírování nebo replikovat změny dat adresáře, pokud služba potřebuje přemístění (například během převzetí služeb při selhání).

> [!NOTE]
> Není nutné vytvořit `config` a `data` adresářů, pokud už je nepotřebujete.
>
>

## <a name="next-steps"></a>Další postup
Naleznete v následujících článcích pro související informace a úkoly.
* [Nasazení hostujícího spustitelného souboru](service-fabric-deploy-existing-app.md)
* [Nasazení několika hostujících spustitelných souborů](service-fabric-deploy-multiple-apps.md)
* [Vytvoření vaší první aplikace spustitelná hostem pomocí sady Visual Studio](quickstart-guest-app.md)
* [Ukázka pro balení a nasazení hostujícího spustitelného souboru](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), včetně odkazu na předběžnou verzi nástroje pro balení
* [Ukázka dvě hosta spustitelných souborů (C# a Node.js) komunikaci přes službu pojmenování pomocí rozhraní REST](https://github.com/Azure-Samples/service-fabric-containers)

