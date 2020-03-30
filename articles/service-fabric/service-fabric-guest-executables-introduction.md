---
title: Balíček existujícího spustitelného souboru pro Azure Service Fabric
description: Přečtěte si o balení existující aplikace jako spustitelný soubor hosta, takže ji lze nasadit do clusteru Service Fabric.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 3d7aab28a32effa2caf7b04b830d72e5e3dfda56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457834"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Nasazení existujícího spustitelného souboru do service fabric
Můžete spustit libovolný typ kódu, jako je například Node.js, Java nebo C++ v Azure Service Fabric jako služba. Service Fabric označuje tyto typy služeb jako spustitelné soubory hosta.

Spustitelné soubory hosta jsou zpracovány service fabric jako bezstavové služby. V důsledku toho jsou umístěny na uzlech v clusteru, na základě dostupnosti a další metriky. Tento článek popisuje, jak zabalit a nasadit spustitelný soubor hosta do clusteru Service Fabric pomocí sady Visual Studio nebo nástroje příkazového řádku.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Výhody spuštění spustitelného souboru hosta v service fabric
Spuštění spustitelného souboru hosta v clusteru Service Fabric má několik výhod:

* Vysoká dostupnost Aplikace, které běží v Service Fabric jsou vysoce dostupné. Service Fabric zajišťuje, že instance aplikace jsou spuštěny.
* Sledování zdravotního stavu. Monitorování stavu service fabric zjistí, zda je spuštěna aplikace a poskytuje diagnostické informace, pokud dojde k selhání.   
* Správa životního cyklu aplikací. Kromě poskytování upgradů bez prostojů poskytuje Service Fabric automatické vrácení zpět na předchozí verzi, pokud je během upgradu hlášena špatná zdravotní událost.    
* Hustota. Můžete spustit více aplikací v clusteru, což eliminuje potřebu pro každou aplikaci spustit na vlastní hardware.
* Zjistitelnost: Pomocí služby REST můžete volat službu Service Fabric Naming service a vyhledat další služby v clusteru. 

## <a name="samples"></a>ukázky
* [Ukázka pro balení a nasazení spustitelného souboru hosta](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázka dvou spustitelných souborů hosta (C# a nodejs), které komunikují prostřednictvím služby Pojmenování pomocí rest](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Přehled souborů manifestu aplikací a služeb
V rámci nasazení spustitelného souboru hosta je užitečné porozumět modelu balení service fabric a modelu nasazení, jak je popsáno v [modelu aplikace](service-fabric-application-model.md). Model balení Service Fabric závisí na dvou souborech XML: manifesty aplikace a služby. Definice schématu pro soubory ApplicationManifest.xml a ServiceManifest.xml je nainstalována sadou Service Fabric SDK do *sady C:\Program Files\Microsoft SDKs\Service Fabric\Schemas\ServiceFabricServiceModel.xsd*.

* **Manifest aplikace** Manifest aplikace se používá k popisu aplikace. Uvádí služby, které jej tvoří, a další parametry, které se používají k definování, jak by měla být nasazena jedna nebo více služeb, například počet instancí.

  V Service Fabric je aplikace jednotkou nasazení a upgradu. Aplikace může být upgradována jako jedna jednotka, kde jsou spravovány potenciální selhání a potenciální vrácení zpět. Service Fabric zaručuje, že proces upgradu je úspěšný, nebo pokud upgrade selže, nenechá aplikaci v neznámém nebo nestabilním stavu.
* **Manifest služby** Manifest služby popisuje součásti služby. Obsahuje data, jako je například název a typ služby a její kód a konfigurace. Manifest služby také obsahuje některé další parametry, které lze použít ke konfiguraci služby po nasazení.

## <a name="application-package-file-structure"></a>Struktura souborů balíčku aplikace
Chcete-li nasadit aplikaci service fabric, aplikace by měla následovat předdefinovanou strukturu adresáře. Následuje příklad této struktury.

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

Název ApplicationPackageRoot obsahuje soubor ApplicationManifest.xml, který definuje aplikaci. Podadresář pro každou službu zahrnutou v aplikaci se používá k obsahovat všechny artefakty, které služba vyžaduje. Tyto podadresáře jsou ServiceManifest.xml a obvykle následující:

* *kód*. Tento adresář obsahuje kód služby.
* *Konfigurace*. Tento adresář obsahuje soubor Settings.xml (a další soubory, které má služba přístup za běhu a načíst konkrétní nastavení konfigurace.
* *Data*. Toto je další adresář pro uložení dalších místních dat, které může služba potřebovat. Data by měla být použita k ukládání pouze dočasných dat. Service Fabric nekopíruje ani replikovat změny do adresáře dat, pokud služba musí být přemístěna (například během převzetí služeb při selhání).

> [!NOTE]
> Nemusíte vytvářet `config` a `data` adresáře, pokud je nepotřebujete.
>
>

## <a name="next-steps"></a>Další kroky
Související informace a úkoly naleznete v následujících článcích.
* [Nasazení hostujícího spustitelného souboru](service-fabric-deploy-existing-app.md)
* [Nasazení několika hostujících spustitelných souborů](service-fabric-deploy-multiple-apps.md)
* [Vytvoření první spustitelné aplikace hosta pomocí sady Visual Studio](quickstart-guest-app.md)
* [Ukázka pro balení a nasazení spustitelného souboru hosta](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), včetně odkazu na předběžnou verzi balicího nástroje
* [Ukázka dvou spustitelných souborů hosta (C# a nodejs), které komunikují prostřednictvím služby Pojmenování pomocí rest](https://github.com/Azure-Samples/service-fabric-containers)

