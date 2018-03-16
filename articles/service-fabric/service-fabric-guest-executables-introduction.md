---
title: "Nasadit existující spustitelný soubor do Azure Service Fabric | Microsoft Docs"
description: "Další informace o balení stávající aplikace jako Host spustitelného souboru, aby ji můžete nasadit na cluster Service Fabric."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: mfussell;mikhegn
ms.openlocfilehash: 328c00697a3c81f5af8488d4303feb7618d81301
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Nasadit existující spustitelný soubor do Service Fabric
Jakýkoli typ kódu, třeba Node.js, Java nebo C++ v Azure Service Fabric můžete spustit jako služby. Service Fabric odkazuje na těchto typů služeb jako hosta spustitelné soubory.

Spustitelné soubory hosta budou vyhodnocené jako bezstavové služby Service Fabric. V důsledku toho jsou umístěny na uzlech v clusteru, na základě dostupnosti a další metriky. Tento článek popisuje, jak pro zabalení a nasazení hosta spustitelný soubor do clusteru Service Fabric pomocí Visual Studio nebo nástroj příkazového řádku.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Výhody spuštění spustitelného souboru v Service Fabric Host
Existuje několik výhod pro spuštění spustitelného souboru v clusteru Service Fabric Host:

* Vysoká dostupnost Aplikace, které běží v Service Fabric jsou vysoce dostupné. Service Fabric zajistí, že instance aplikace běží.
* Sledování stavu. Monitorování stavu Service Fabric zjišťuje, zda aplikace běží a poskytuje diagnostické informace, pokud dojde k selhání.   
* Správa životního cyklu aplikací. Kromě toho poskytuje upgrady bez výpadků, Service Fabric nabízí automatického vrácení zpět na předchozí verzi, pokud existuje událost stavu chybný hlášené během upgradu.    
* Hustotou. Více aplikací můžete spustit v clusteru, není potřeba pro každou aplikaci, aby běžela na svůj vlastní hardware.
* Možnosti rozpoznání: Pomocí REST můžete volat službu Service Fabric Naming, kterou chcete najít další služby v clusteru. 

## <a name="samples"></a>Ukázky
* [Ukázka pro balení a nasazení spustitelný soubor hosta](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázka dvěma hosta spustitelné soubory (C# a nodejs) komunikaci přes službu Naming pomocí REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Přehled aplikace a soubory manifestu služby
Jako součást nasazení spustitelný soubor hosta, je užitečné Service Fabric balení a nasazení modelu pochopit, jak je popsáno v [aplikačního modelu](service-fabric-application-model.md). Balení modelu Service Fabric spoléhá na dva soubory XML: manifestů aplikace a služby. Definice schématu pro ApplicationManifest.xml a ServiceManifest.xml soubory se instaluje s Service Fabric SDK do *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifest aplikace** manifest aplikace se používá k popisu aplikace. Zobrazí seznam služeb, které ji tvoří a musí být nasazené dalších parametrů, které slouží k určení jak jednu nebo více služeb, jako je počet instancí.

  Aplikace v Service Fabric je jednotka nasazení a upgrade. Aplikace lze upgradovat jako na jednu jednotku, kde se spravují potenciální chyby a potenciální odvolání. Service Fabric zaručuje, že proces upgradu je buď úspěšné, nebo, pokud se upgrade nezdaří, nenechává aplikace v neznámý nebo nestabilním stavu.
* **Manifest služby** service manifest popisuje součásti služby. Obsahuje data, jako třeba název a typ služby a jeho kódu a konfigurace. Manifest služby zahrnuje také některé další parametry, které lze použít ke konfiguraci služby po jejím nasazení.

## <a name="application-package-file-structure"></a>Struktura souboru balíčku aplikace
Pokud chcete nasadit aplikace do Service Fabric, by mělo vycházet aplikace předdefinované adresářovou strukturu. Následuje příklad této struktury.

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

ApplicationPackageRoot obsahuje ApplicationManifest.xml soubor, který definuje aplikace. Tak, aby obsahovala všechny artefakty, které služba vyžaduje, aby se používá podadresáři pro každou službu obsažené v aplikaci. Tyto podadresáře jsou ServiceManifest.xml a obvykle následující:

* *Kód*. Tento adresář obsahuje kód služby.
* *Konfigurace*. Tento adresář obsahuje soubor souborech Settings.xml (a další soubory v případě potřeby), že služba přístup za běhu k načtení specifické nastavení.
* *Data*. To je další adresář k uložení další místní data, která může být nutné službu. Data slouží k ukládání pouze dočasných dat. Service Fabric kopírování nebo replikovat změny do adresáře dat, pokud služba musí být přemístění (například během převzetí služeb při selhání).

> [!NOTE]
> Není nutné vytvářet `config` a `data` adresářů, pokud je nepotřebujete.
>
>

## <a name="next-steps"></a>Další kroky
Najdete v následujících článcích související informace a úlohy.
* [Nasazení hostujícího spustitelného souboru](service-fabric-deploy-existing-app.md)
* [Nasazení několika hostujících spustitelných souborů](service-fabric-deploy-multiple-apps.md)
* [Vytvoření první aplikace spustitelné hosta pomocí sady Visual Studio](quickstart-guest-app.md)
* [Ukázka pro balení a nasazení spustitelný soubor hosta](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), včetně odkaz na zkušební verzi nástroje balení
* [Ukázka dvěma hosta spustitelné soubory (C# a nodejs) komunikaci přes službu Naming pomocí REST](https://github.com/Azure-Samples/service-fabric-containers)

