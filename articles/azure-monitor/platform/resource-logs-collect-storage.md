---
title: Archivujte protokoly prostředků Azure do účtu úložiště | Dokumenty společnosti Microsoft
description: Zjistěte, jak archivovat protokoly prostředků Azure pro dlouhodobé uchovávání v účtu úložiště.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 787640ef08ee91220f42065af155c372247f0136
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804601"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Archivace protokolů prostředků Azure do účtu úložiště
[Protokoly platformy](platform-logs-overview.md) v Azure, včetně protokolu aktivit Azure a protokolů prostředků, poskytují podrobné diagnostické a auditující informace pro prostředky Azure a platformu Azure, na které závisí.  Tento článek popisuje shromažďování protokolů platformy do účtu úložiště Azure pro uchování dat pro archivaci.

## <a name="prerequisites"></a>Požadavky
Účet úložiště Azure musíte [vytvořit,](../../storage/common/storage-account-create.md) pokud ho ještě nemáte. Účet úložiště nemusí být ve stejném předplatném jako protokoly odesílání prostředků, pokud uživatel, který konfiguruje nastavení má odpovídající přístup RBAC k oběma předplatným.

> [!IMPORTANT]
> Chcete-li odeslat data do neměnného úložiště, nastavte neměnné zásady pro účet úložiště, jak je popsáno v [sadou a spravovat zásady neměnnosti pro úložiště objektů Blob](../../storage/blobs/storage-blob-immutability-policies-manage.md). Je nutné postupovat všechny kroky v tomto článku, včetně povolení chráněných objektů BLOB zápisy.

> [!IMPORTANT]
> Účty Azure Data Lake Storage Gen2 nejsou aktuálně podporované jako cíl pro nastavení diagnostiky, i když mohou být uvedeny jako platná možnost na webu Azure Portal.


Neměli byste používat existující účet úložiště, který má jiné, non-monitoring data uložená v něm, takže můžete lépe řídit přístup k datům. Pokud archivujete protokol aktivit a protokoly prostředků společně, můžete použít stejný účet úložiště, abyste uchovávají všechna data monitorování v centrálním umístění.

## <a name="create-a-diagnostic-setting"></a>Vytvoření diagnostického nastavení
Odešlete protokoly platformy do úložiště a dalších cílů vytvořením diagnostického nastavení pro prostředek Azure. Podrobnosti najdete [v tématu Vytvoření diagnostického nastavení pro shromažďování protokolů a metrik v Azure.](diagnostic-settings.md)


## <a name="collect-data-from-compute-resources"></a>Shromažďování dat z výpočetních prostředků
Diagnostická nastavení budou shromažďovat protokoly prostředků pro výpočetní prostředky Azure, jako je jakýkoli jiný prostředek, ale ne jejich hostovaný operační systém nebo úlohy. Chcete-li tato data shromažďovat, nainstalujte [agenta diagnostiky Windows Azure](diagnostics-extension-overview.md). 


## <a name="schema-of-platform-logs-in-storage-account"></a>Schéma protokolů platformy v účtu úložiště

Po vytvoření nastavení diagnostiky je v účtu úložiště vytvořen kontejner úložiště, jakmile dojde k události v jedné z povolených kategorií protokolu. Objekty BLOB v kontejneru používají následující konvence pojmenování:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Objekt blob pro skupinu zabezpečení sítě může mít například název podobný následujícímu:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Každý objekt blob PT1H.json obsahuje objekt blob ve formátu JSON událostí, ke kterým došlo během hodiny zadané v adrese URL objektu blob (například h=12). Během aktuální hodiny se události připojují do souboru PT1H.json, když k nim dojde. Hodnota minuty (m = 00) je vždy 00, protože události protokolu prostředků jsou rozděleny do jednotlivých objektů BLOB za hodinu.

V souboru PT1H.json je každá událost uložena v následujícím formátu. To bude používat společné schéma nejvyšší úrovně, ale bude jedinečné pro každou službu Azure, jak je popsáno v [schématu protokolů prostředků](diagnostic-logs-schema.md) a schéma [protokolu aktivit](activity-log-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Protokoly platformy jsou zapsány do úložiště objektů blob pomocí [řádků JSON](http://jsonlines.org/), kde každá událost je řádek a znak nového řádku označuje novou událost. Tento formát byl implementován v listopadu 2018. Před tímto datem byly protokoly zapsány do úložiště objektů blob jako json pole záznamů, jak je popsáno v [části Příprava na změnu formátu protokolů platformy Azure Monitor archivované ho do účtu úložiště](resource-logs-blob-format.md).

## <a name="next-steps"></a>Další kroky

* [Další informace o protokolech prostředků](platform-logs-overview.md).
* [Vytvořte diagnostické nastavení pro shromažďování protokolů a metrik v Azure](diagnostic-settings.md).
* [Stáhnout objekty BLOB pro analýzu](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Archivujte protokoly služby Azure Active Directory pomocí nástroje Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
