---
title: Změny v clusteru přístup k konfigurace – Azure HDInsight
description: Další informace o změnách přístup k citlivým clusteru konfigurační pole.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 85a6737ee111975d4c7ecf078673280127bfd0fd
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610197"
---
# <a name="changes-to-cluster-configuration-access"></a>Změny v přístupu ke konfiguraci clusteru

Nejnovější vydaná verze sady SDK Azure HDInsight přináší některé důležité změny pro podporu více velice přesně kontrolovat přístup na základě rolí k získání citlivé informace. Jako část z nich změní některé **akce mohou být požadovány** Pokud použijete jednu z metod ovlivněné.

## <a name="sdk-for-net-500"></a>Sada SDK pro .NET 5.0.0

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) bude **už nevracel citlivé parametry** , jako je úložiště klíčů (základní web) nebo přihlašovací údaje protokolu HTTP (brány).
    - Pokud chcete načíst všechny konfigurace, včetně citlivé parametrů, použijte `ConfigurationOperationsExtensions.List` do budoucna.  Všimněte si, že uživatelé s rolí 'Čtečky' nebude možné použít tuto metodu. Díky tomu umožňuje zajistit detailní kontrolu nad tím, které mohou uživatelé citlivých informací pro cluster. 
    - Chcete-li načíst jenom přihlašovací údaje brány protokolu HTTP, použijte `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) je nyní zastaralá a nahradila ji `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) a [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) jsou nyní zastaralé. Je povolený protokol HTTP teď vždy, takže tyto metody jsou už je nepotřebujete.