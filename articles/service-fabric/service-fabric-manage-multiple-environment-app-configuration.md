---
title: Správa aplikací pro více prostředí
description: Aplikace Azure Service Fabric lze spouštět v clusterech, které se pohybují ve velikosti od jednoho počítače k tisícům počítačů. V některých případech budete chtít nakonfigurovat aplikaci odlišně pro tato různá prostředí. Tento článek popisuje, jak definovat různé parametry aplikace pro prostředí.
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 0bca690fd585b288f15cbab21c1c951474390318
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196975"
---
# <a name="manage-applications-for-multiple-environments"></a>Správa aplikací pro víc prostředí

Clustery Azure Service Fabric umožňují vytvářet clustery pomocí libovolného místa od jednoho po tisíce počítačů. Ve většině případů se ocitnete museli nasadit aplikaci ve více konfiguracích clusteru: místní vývoj clusteru, sdíleného vývojového clusteru a produkčního clusteru. Všechny tyto clustery jsou považovány za různá prostředí, ve kterých má váš kód spuštěn. Binární soubory aplikace lze spustit bez úprav v tomto širokém spektru, ale často chcete aplikaci nakonfigurovat odlišně.

Vezměme si dva jednoduché příklady:
  - vaše služba naslouchá na definovaném portu, ale potřebujete, aby se tento port lišil v různých prostředích
  - je třeba zadat různá pověření pro vazbu pro databázi v různých prostředích

## <a name="specifying-configuration"></a>Určení konfigurace

Konfiguraci, kterou zadáte, lze rozdělit do dvou kategorií:

- Konfigurace, která se vztahuje na způsob spuštění služeb
  - Například číslo portu pro koncový bod nebo počet instancí služby
  - Tato konfigurace je určena v souboru manifestu aplikace nebo služby.
- Konfigurace, která se vztahuje na kód aplikace
  - Například informace o vazbě pro databázi
  - Tuto konfiguraci lze poskytnout buď prostřednictvím konfiguračních souborů nebo proměnných prostředí

> [!NOTE]
> Ne všechny atributy v souboru manifestu aplikace a služby podporují parametry podpory.
> V těchto případech musíte spoléhat na nahrazení řetězců jako součást pracovního postupu nasazení. V Azure DevOps můžete použít rozšíření jako https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens nahradit tokeny: nebo v Jenkins můžete spustit úlohu skriptu nahradit hodnoty.
>

## <a name="specifying-parameters-during-application-creation"></a>Určení parametrů při vytváření aplikace

Při vytváření instance pojmenované aplikace v Service Fabric, máte možnost předat parametry. Způsob, jakým to uděláte, závisí na způsobu vytvoření instance aplikace.

  - V prostředí PowerShell [`New-ServiceFabricApplication`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) rutina přebírá parametry aplikace jako hodnotitelnou hodnotu hash.
  - Pomocí sfctl [`sfctl application create`](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) příkaz přebírá parametry jako řetězec JSON. Skript install.sh používá sfctl.
  - Visual Studio poskytuje sadu souborů parametrů ve složce Parametry v projektu aplikace. Tyto soubory parametrů se používají při publikování z Visual Studia pomocí Služby Azure DevOps Services nebo Azure DevOps Server. V sadě Visual Studio jsou soubory parametrů předávány do skriptu Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Další kroky
Následující články ukazují, jak používat některé z konceptů popsaných zde:

- [Jak zadat proměnné prostředí pro služby v Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Jak zadat číslo portu služby pomocí parametrů v service fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Jak parametrizovat konfigurační soubory](service-fabric-how-to-parameterize-configuration-files.md)

- [Odkaz na proměnnou prostředí](service-fabric-environment-variables-reference.md)
