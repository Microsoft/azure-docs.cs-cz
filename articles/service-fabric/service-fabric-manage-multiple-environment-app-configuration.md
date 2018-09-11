---
title: Správa aplikací pro více prostředí v Azure Service Fabric | Dokumentace Microsoftu
description: Aplikace Azure Service Fabric může běžet na clusterech tohoto rozsahu velikosti z jednoho počítače na tisíce počítačů. V některých případech můžete nakonfigurovat svoji aplikaci pro těchto různých prostředích. Tento článek popisuje, jak definovat parametry jiné aplikace pro každé prostředí.
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
ms.openlocfilehash: dac96ef6fce38a0557444e181fa6eccb649cfb9a
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298331"
---
# <a name="manage-applications-for-multiple-environments"></a>Správa aplikací pro víc prostředí

Clustery Azure Service Fabric umožňují vytvářet clustery pomocí odkudkoli z jedné až několika tisíc počítačů. Ve většině případů si uvědomíte, museli nasazovat aplikace napříč konfiguracemi s více clusteru: místního vývojového clusteru, sdílené vývojového clusteru a clusteru pro produkční prostředí. Všechny tyto clustery jsou považovány za různých prostředích, které má váš kód pro spuštění v. Binární soubory aplikace spuštěny bez úprav přes tento širokého spektra, ale často chcete provést konfiguraci aplikace jiným způsobem.

Vezměte v úvahu dva jednoduché příklady:
  - vaše služba naslouchá na portu definovaná, ale je nutné tento port je jiná napříč prostředí
  - je potřeba zadat přihlašovací údaje jinou vazbou pro databáze v prostředí

## <a name="specifying-configuration"></a>Určení konfigurace

Konfigurace, které zadáte, je možné rozdělit do dvou kategorií:

- Konfigurace, které platí pro spouštění vašich služeb
  - Například číslo portu pro koncový bod nebo počet instancí služby
  - Tato konfigurace je zadán v adresáři aplikace nebo souboru manifestu služby
- Konfigurace, která se vztahuje na kód vaší aplikace
  - Například informace o vazbě pro databázi
  - Tato konfigurace je možné poskytnout prostřednictvím konfiguračních souborů nebo proměnné prostředí

> [!NOTE]
> Ne všechny atributy v aplikaci a service manifest podporu parametrů souborů.
> V takových případech budete muset spoléhat na nahrazování řetězců jako součást pracovního postupu nasazení. V Azure DevOps můžete použít rozšíření například nahradit tokeny: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens nebo jenkins můžete spustit skript úkolu k nahrazení hodnoty.
>

## <a name="specifying-parameters-during-application-creation"></a>Zadání parametrů během vytváření aplikace

Při vytváření instance s názvem aplikace v Service Fabric, máte možnost k předání v parametrech. Způsob, jak to provedete závisí na tom, jak vytvořit instanci aplikace.

  - V Powershellu [ `New-ServiceFabricApplication` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) rutiny přijímá parametry aplikace jako zatřiďovací tabulku.
  - Pomocí sfctl, [ `sfctl application create` ](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) příkaz přijímá parametry jako řetězec formátu JSON. Tento skript install.sh využívá sfctl.
  - Visual Studio vám nabízí sadu parametrů soubory ve složce parametry v projektu aplikace. Tyto soubory parametr se používá při publikování ze sady Visual Studio, pomocí služby Azure DevOps nebo Team Foundation Server. V sadě Visual Studio soubory parametrů jsou neputuje do skriptu Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Další postup
Následující články ukazují, jak používat některé koncepty popsané tady:

- [Jak zadat proměnné prostředí pro služby v Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Jak zadat číslo portu služby v Service Fabric pomocí parametrů](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Jak parametrizovat konfiguračních souborů](service-fabric-how-to-parameterize-configuration-files.md)

- [Odkaz na proměnnou prostředí](service-fabric-environment-variables-reference.md)
