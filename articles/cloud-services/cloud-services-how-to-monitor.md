---
title: Monitorovat cloudové služby Azure | Microsoft Docs
description: Popisuje, co monitorování Azure Cloud Service zahrnuje a jaké některé možnosti jsou.
services: cloud-services
documentationcenter: ''
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: adegeo
ms.openlocfilehash: f3a3a1beb8540ee8ab0502379396c06ea505fb44
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2018
ms.locfileid: "29149902"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Úvod do monitorování cloudové služby

Můžete monitorovat klíčové metriky výkonu pro všechny cloudové služby. Každé cloudové služby role shromažďuje údaje o minimální: využití procesoru, využití sítě a využití disku. Pokud má cloudové služby `Microsoft.Azure.Diagnostics` rozšíření u role, tato role může shromažďovat další datové body. Tento článek obsahuje úvod do Azure Diagnostics pro cloudové služby.

S základní monitorování, se data čítače výkonu z instancí role vzorků a shromažďují v intervalech 3 minut. Tato základní monitorování data nejsou uložená v účtu úložiště a k němu přidruženy bez dalších nákladů.

Další metriky s pokročilé monitorování se odebírají a shromážděné v intervalech 5 minut, 1 hodina a 12 hodin. Agregovaná data je uložený v účtu úložiště, v tabulkách a vyprázdní se po 10 dní. Účet úložiště používané nastavena role; jiným účtům úložiště můžete použít pro různé role. To je nakonfigurované připojovací řetězec [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) a [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) soubory.


## <a name="basic-monitoring"></a>Základní monitorování

Jak jsme uvedli v úvodu, Cloudová služba automaticky shromažďovat základní data monitorování z hostitele virtuálního počítače. Tato data zahrnují procento procesoru, sítě a konec a disku pro čtení a zápis. Shromážděná data monitorování se automaticky zobrazí na stránce Přehled a metriky cloudové služby na portálu Azure. 

Základní monitorování nevyžaduje žádný účet úložiště. 

![monitorování dlaždice základní cloudové služby](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Pokročilé sledování

Pokročilé monitorování, která využívá **Azure Diagnostics** rozšíření (a volitelně Application Insights SDK) na roli, kterou chcete sledovat. Rozšíření diagnostiky používá soubor konfigurace (podle role) s názvem **diagnostics.wadcfgx** ke konfiguraci diagnostiky metriky monitorovány. Rozšíření Azure diagnostiky shromažďuje a ukládá data do účtu Azure Storage. Tato nastavení jsou konfigurována na **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef), a [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) soubory. To znamená, že je navíc náklady spojené s pokročilé monitorování.

Při vytváření každou roli, Visual Studio přidá rozšíření Azure Diagnostics. Toto rozšíření diagnostiky může shromažďovat následující typy informací:

* Vlastní čítače výkonu
* Protokoly aplikací
* Protokoly událostí systému Windows
* Zdroj události rozhraní .NET
* Protokoly IIS
* Manifest na základě trasování událostí pro Windows
* Výpisy stavu systému
* Protokoly chyb zákazníka

> [!IMPORTANT]
> Zatímco tato data je agregován do účtu úložiště, nemá portálu **není** umožňují nativní grafu data. Důrazně doporučujeme integraci jiné služby, jako je Application Insights do své aplikace.

## <a name="setup-diagnostics-extension"></a>Instalace rozšíření diagnostiky

První, pokud nemáte **classic** účet úložiště, [vytvořit](../storage/common/storage-create-storage-account.md#create-a-storage-account). Ujistěte se, že je vytvořen účet úložiště s **model nasazení Classic** zadaný.

Potom přejděte na **účet úložiště (klasické)** prostředků. Vyberte **nastavení** > **přístupové klíče** a zkopírujte **primární připojovací řetězec** hodnotu. Tato hodnota je nutné pro cloudovou službu. 

Existují dvě konfigurační soubory, které je nutné změnit pokročilé Diagnostics povolení, **ServiceDefinition.csdef** a **souboru ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

V **ServiceDefinition.csdef** soubor, přidejte nové nastavení s názvem `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` pro každou roli, která používá pokročilé diagnostiky. Visual Studio tato hodnota přidá do souboru při vytvoření nového projektu. V případě, že není nalezena, přidejte ji nyní. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Definuje nové nastavení, která musí být přidán do každé **souboru ServiceConfiguration.cscfg** souboru. 

S největší pravděpodobností máte dva **.cscfg** souborům, jednu s názvem **ServiceConfiguration.cloud.cscfg** pro nasazení do Azure a jednu s názvem **ServiceConfiguration.local.cscfg** která se používá pro místní nasazení v prostředí emulované. Otevřete a změňte každou **.cscfg** souboru. Přidejte nastavení s názvem `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Nastavte hodnotu na **primární připojovací řetězec** účtu úložiště classic. Pokud chcete použít místní úložiště na vývojovém počítači, použijte `UseDevelopmentStorage=true`.

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Pomocí Application Insights

Když publikujete službu cloudu v sadě Visual Studio, budete mít možnost posílat diagnostická data do služby Application Insights. Můžete vytvořit prostředek Application Insights Azure v daném čase nebo odeslat data do existující prostředek Azure. Cloudové služby můžete sledovat pomocí Application Insights pro dostupnosti, výkonu, chyb a využití. Vlastní grafy mohou být přidány do Application Insights, aby mohli zobrazit data, nejvíc záleží. Pomocí Application Insights SDK v projekt cloudové služby se můžou shromažďovat role instance data. Další informace o tom, jak integrovat Application Insights najdete v tématu [Application Insights s cloudovými službami](../application-insights/app-insights-cloudservices.md).

Všimněte si, zda při Application Insights můžete použít k zobrazení čítače výkonu (a ostatních nastavení) jste zadali prostřednictvím rozšíření Windows Azure Diagnostics, můžete pouze získat bohatší možnosti integrací Application Insights SDK do vašeho Worker a webové role.


## <a name="next-steps"></a>Další postup

- [Další informace o Application Insights s cloudovými službami](../application-insights/app-insights-cloudservices.md)
- [Nastavit čítače výkonu](diagnostics-performance-counters.md)

