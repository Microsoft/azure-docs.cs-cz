---
title: Správa aplikací pro víc prostředí
description: Aplikace Azure Service Fabric lze spouštět v clusterech, které jsou v rozsahu od jednoho počítače po tisíce počítačů. V některých případech budete chtít nakonfigurovat aplikaci odlišně pro tato různá prostředí. Tento článek popisuje, jak definovat různé parametry aplikace pro každé prostředí.
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: 51bc77abca8ce2b3878df1c1859203618d3e2e27
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574441"
---
# <a name="manage-applications-for-multiple-environments"></a>Správa aplikací pro víc prostředí

Clustery Azure Service Fabric umožňují vytvářet clustery s využitím kteréhokoli z několika tisíc počítačů. Ve většině případů je potřeba, abyste nasadili aplikaci v rámci více konfigurací clusteru: místní vývojový cluster, sdílený vývojový cluster a váš provozní cluster. Všechny tyto clustery se považují za různá prostředí, ve kterých je váš kód spuštěný. Binární soubory aplikace mohou běžet bez úprav v rámci tohoto spektra, ale často chcete aplikaci nakonfigurovat odlišně.

Vezměte v úvahu dva jednoduché příklady:
  - vaše služba naslouchá na definovaném portu, ale je potřeba, aby se tento port lišil v různých prostředích.
  - pro databázi v různých prostředích je potřeba zadat jiné přihlašovací údaje pro vazbu.

## <a name="specifying-configuration"></a>Určení konfigurace

Konfiguraci, kterou zadáte, můžete rozdělit do dvou kategorií:

- Konfigurace, která se vztahuje na spouštění služeb
  - Například číslo portu pro koncový bod nebo počet instancí služby
  - Tato konfigurace je určena v souboru manifestu aplikace nebo služby.
- Konfigurace, která se vztahuje na kód vaší aplikace
  - Například informace o vazbě pro databázi
  - Tato konfigurace se dá zadat buď pomocí konfiguračních souborů, nebo proměnných prostředí.

> [!NOTE]
> Ne všechny atributy v souboru manifestu aplikace a služby podporují parametry.
> V těchto případech se musíte spoléhat na nahrazování řetězců jako součást pracovního postupu nasazení. V Azure DevOps můžete použít rozšíření jako nahradit tokeny: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens nebo v Jenkinse můžete spustit úlohu skriptu a nahradit hodnoty.
>

## <a name="specifying-parameters-during-application-creation"></a>Určení parametrů během vytváření aplikace

Při vytváření pojmenovaných instancí aplikace v Service Fabric máte možnost předávat parametry. Způsob, jakým to provedete, závisí na tom, jak vytváříte instanci aplikace.

  - Rutina v prostředí PowerShell [`New-ServiceFabricApplication`](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) přebírá parametry aplikace jako zatřiďovací tabulku.
  - Pomocí sfctl [`sfctl application create`](./service-fabric-sfctl-application.md#sfctl-application-create) příkaz přebírá parametry jako řetězec JSON. Skript install.sh používá sfctl.
  - Sada Visual Studio poskytuje sadu souborů parametrů ve složce Parameters v projektu aplikace. Tyto soubory parametrů jsou používány při publikování ze sady Visual Studio pomocí Azure DevOps Services nebo Azure DevOps Server. V aplikaci Visual Studio jsou soubory parametrů předávány do skriptu Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Další kroky
Následující články ukazují, jak používat některé z konceptů, které jsou zde popsané:

- [Určení proměnných prostředí pro služby v Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Jak zadat číslo portu služby pomocí parametrů v Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Jak parametrizovat konfigurační soubory](service-fabric-how-to-parameterize-configuration-files.md)

- [Referenční informace o proměnných prostředí](service-fabric-environment-variables-reference.md)
