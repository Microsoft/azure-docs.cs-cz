---
title: Archivace protokolů prostředků Azure do účtu úložiště | Microsoft Docs
description: Naučte se archivovat protokoly prostředků Azure pro dlouhodobé uchovávání v účtu úložiště.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 843c179826b2064a1be24d3cee84b398987b4aed
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658967"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Archivace protokolů prostředků Azure do účtu úložiště
[Protokoly platforem](platform-logs-overview.md) v Azure, včetně protokolů aktivit Azure a protokolů prostředků, poskytují podrobné informace o diagnostice a auditování pro prostředky Azure a platformu Azure, na které jsou závislé.  Tento článek popisuje, jak shromažďovat protokoly platforem pro účet služby Azure Storage a uchovávat data pro archivaci.

## <a name="prerequisites"></a>Požadavky
Pokud ještě nemáte [účet úložiště Azure](../../storage/common/storage-account-create.md) , musíte ho vytvořit. Účet úložiště nemusí být ve stejném předplatném jako prostředek odesílající protokoly, pokud uživatel, který konfiguruje nastavení, má odpovídající přístup RBAC k oběma předplatným.


> [!IMPORTANT]
> Účty Azure Data Lake Storage Gen2 nejsou v současné době podporovány jako cíl pro nastavení diagnostiky, i když mohou být v Azure Portal uvedeny jako platná možnost.


Neměli byste používat existující účet úložiště, který obsahuje jiná, nemonitorovaná data, která jsou v něm uložená, abyste mohli lépe řídit přístup k datům. Pokud společně archivujte protokoly aktivit a protokoly prostředků, můžete použít stejný účet úložiště, abyste zachovali všechna data monitorování v centrálním umístění.

## <a name="create-a-diagnostic-setting"></a>Vytvoření nastavení diagnostiky
Odeslání protokolů platformy do úložiště a dalších cílů vytvořením nastavení diagnostiky pro prostředek Azure. Podrobnosti najdete [v tématu Vytvoření nastavení diagnostiky pro shromáždění protokolů a metrik v Azure](diagnostic-settings.md) .


## <a name="collect-data-from-compute-resources"></a>Shromažďování dat z výpočetních prostředků
Nastavení diagnostiky bude shromažďovat protokoly prostředků pro výpočetní prostředky Azure, jako jsou všechny ostatní prostředky, ale ne jejich hostovaný operační systém nebo úlohy. Chcete-li shromáždit tato data, nainstalujte [agenta pro Windows Azure Diagnostics](diagnostics-extension-overview.md). 


## <a name="schema-of-platform-logs-in-storage-account"></a>Schéma protokolů platformy v účtu úložiště

Po vytvoření nastavení diagnostiky se kontejner úložiště vytvoří v účtu úložiště, jakmile dojde k události v jedné z kategorií povolených protokolů. Objekty BLOB v kontejneru používají následující konvence vytváření názvů:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Například objekt BLOB pro skupinu zabezpečení sítě může mít název podobný následujícímu:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Každý objekt blob PT1H.json obsahuje objekt blob ve formátu JSON událostí, ke kterým došlo během hodiny zadané v adrese URL objektu blob (například h=12). Během aktuální hodiny se události připojují do souboru PT1H.json, když k nim dojde. Hodnota minut (m = 00) je vždy 00, protože události protokolu prostředku jsou v jednotlivých objektech blob za hodinu rozděleny.

V souboru PT1H. JSON jsou jednotlivé události uložené v následujícím formátu. Bude se používat běžné schéma nejvyšší úrovně, ale jedinečné pro každou službu Azure, jak je popsáno v tématu schéma [prostředků protokoly](diagnostic-logs-schema.md) a [schéma protokolu aktivit](activity-log-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Protokoly platforem se zapisují do úložiště objektů BLOB pomocí [řádků JSON](http://jsonlines.org/), kde každá událost je řádek a znak nového řádku indikuje novou událost. Tento formát byl implementován v listopadu 2018. Před tímto datem byly protokoly zapsány do úložiště objektů BLOB jako pole JSON záznamů, jak je popsáno v tématu [Příprava změny formátu na Azure monitor protokoly platformy archivované na účet úložiště](resource-logs-blob-format.md).

## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace o protokolech prostředků](platform-logs-overview.md).
* [Vytvořte nastavení diagnostiky pro shromažďování protokolů a metrik v Azure](diagnostic-settings.md).
* [Stáhněte si objekty blob pro analýzu](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Archivace Azure Active Directory protokolů pomocí Azure monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
