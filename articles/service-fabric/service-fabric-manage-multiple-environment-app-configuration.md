---
title: Správa aplikací pro prostředí s více v Azure Service Fabric | Microsoft Docs
description: Aplikace Azure Service Fabric lze spustit na clusterech rozsahu velikost z jednoho počítače tisíce počítačů. V některých případech můžete ke konfiguraci vaší aplikace pro tato rozmanitých prostředí. Tento článek vysvětluje postup definujte parametry jinou aplikaci na prostředí.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 15ad606578970290cef440ec4efdd967ca0c0b32
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="manage-applications-for-multiple-environments"></a>Správa aplikací pro víc prostředí

Azure Service Fabric clustery umožňují vytvářet clustery pomocí kdekoli z jednoho až několika tisíc počítačů. Ve většině případů se přistihnete nutnosti nasazení vaší aplikace v rámci více konfigurace clusteru: místního vývojového clusteru a clusteru s podporou sdíleného vývoj provozní cluster. Všechny tyto clustery jsou považovány za různých prostředích, který má být spuštěný kódu. Binární soubory aplikace můžete spustit bez úprav přes tento široké spektrum, ale chcete často jinak konfigurace aplikace.

Vezměte v úvahu dvě jednoduché příklady:
  - vaše služba naslouchá na definovaný port, ale je nutné tento port k lišit prostředích
  - je třeba zadat jinou vazbou přihlašovací údaje pro databázi prostředích

## <a name="specifying-configuration"></a>Určení konfigurace

Konfiguraci, kterou zadáte, je možné rozdělit do dvou kategorií:

- Konfigurace, která se vztahuje na tom, jak vaše služby jsou spouštěny
  - Například číslo portu pro koncový bod nebo počet instancí služby
  - Tato konfigurace je zadán v aplikaci nebo soubor manifestu služby
- Konfigurace, která se vztahuje na kódu aplikace
  - Například informace o vazbě pro databázi
  - Tato konfigurace se dá zajistit buď pomocí konfiguračních souborů nebo proměnné prostředí

> [!NOTE]
> Všechny atributy v aplikaci a service manifest souboru podporu parametry.
> V takových případech budete muset spoléhat na nahrazování řetězců v rámci pracovního postupu nasazení. Ve Visual Studio Team Services můžete použít rozšíření jako nahradit tokeny: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens nebo ve volaných může spustit úlohu skript k nahrazení hodnoty.
>

## <a name="specifying-parameters-during-application-creation"></a>Zadání parametrů během vytváření aplikace

Při vytváření instancí s názvem aplikace v Service Fabric, máte možnost předat parametry. Způsob, jakým se závisí na tom, jak vytvořit instanci aplikace.

  - V prostředí PowerShell [ `New-ServiceFabricApplication` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) rutiny používá parametry aplikace jako zatřiďovací tabulku.
  - Pomocí sfctl, [ `sfctl application create` ](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) příkaz přebírá parametry jako řetězec formátu JSON. Skript install.sh používá sfctl.
  - Visual Studio poskytuje sadu parametr soubory ve složce parametry v projektu aplikace. Tyto soubory parametr se používá při publikování ze sady Visual Studio, pomocí sady Visual Studio Team Service nebo Team Foundation Server. V sadě Visual Studio soubory parametrů jsou předávány FabricApplication.ps1 nasazení skriptu.

## <a name="next-steps"></a>Další postup
V následujících článcích ukazují, jak používat některé z konceptů popsaných v tomto poli:

- [Určení proměnných prostředí pro služby v Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Tom, jak zadat číslo portu služby pomocí parametrů v Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Postupy: Parametrizace konfigurační soubory](service-fabric-how-to-parameterize-configuration-files.md)

- [Reference proměnné prostředí](service-fabric-environment-variables-reference.md)
