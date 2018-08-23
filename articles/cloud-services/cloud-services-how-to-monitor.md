---
title: Monitorování cloudové služby Azure | Dokumentace Microsoftu
description: Popisuje, co monitorování cloudové služby Azure zahrnuje a jaké některé možnosti jsou.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: jeconnoc
ms.openlocfilehash: e9fbe59c40fe55218429a3b479ddbbec7220c66a
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056643"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Úvod do cloudové služby monitorování

Můžete monitorovat klíčové metriky výkonu pro kteroukoli cloudovou službu. Shromažďuje minimální údaje o každé role cloudové služby: využití CPU, využití sítě a využití disku. Pokud má cloudové služby `Microsoft.Azure.Diagnostics` rozšíření u role, tuto roli můžete shromažďovat další datové body. Tento článek obsahuje úvod do Azure Diagnostics pro cloudové služby.

Základní monitorování data čítače výkonu z instancí rolí je Vzorkovaná a shromážděných v intervalech 3minutové. Tato základní monitorování data nejsou uložená v účtu úložiště a k němu má přidružené žádné další poplatky.

Další metriky pomocí rozšířené monitorování se odebírají a shromažďují v intervalech 5 minut, 1 hodinou a 12 hodin. Agregovaná data uložená v účtu úložiště, v tabulkách a se vyprazdňují po 10 dnů. Role; je nakonfigurovaný účet úložiště používané můžete použít jiný účet úložiště pro jiné role. To se nastavuje pomocí připojovacího řetězce v [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) a [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) soubory.


## <a name="basic-monitoring"></a>Základní monitorování

Jak jsme uvedli v úvodu, Cloudová služba automaticky shromažďovat základní data monitorování z hostitele virtuálního počítače. Tato data zahrnují procento procesoru, vstup/výstup sítě a čtení disku/zápis. Shromážděná data monitorování se automaticky zobrazí na stránce Přehled a metriky cloudové služby na webu Azure Portal. 

Základní monitorování nevyžaduje, aby účet úložiště. 

![dlaždice monitorování základní cloudové služby](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Pokročilé sledování

Rozšířené monitorování zahrnuje použití **Azure Diagnostics** rozšíření (a volitelně Application Insights SDK) na roli, kterou chcete monitorovat. Diagnostické rozšíření používá konfigurační soubor (na roli) s názvem **diagnostics.wadcfgx** konfigurace diagnostických metrik monitorovat. Rozšíření Azure Diagnostic shromažďuje a ukládá data v účtu služby Azure Storage. Tato nastavení jsou konfigurována **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef), a [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) soubory. To znamená, že je speciální náklady spojené s rozšířené monitorování.

Jak se vytvoří každou roli, Visual Studio přidá rozšíření Azure Diagnostics. Toto rozšíření diagnostiky můžete shromažďovat následující typy informací:

* Vlastní čítače výkonu
* Protokoly aplikací
* Protokoly událostí Windows
* Zdroj události .NET
* Protokoly IIS
* Trasování událostí pro Windows na základě manifestu
* Výpisy stavu systému
* Protokoly chyb zákazníka

> [!IMPORTANT]
> Zatímco tato data se agregují do účtu úložiště, na portálu nepodporuje **není** nativní způsob, jak data grafu. Důrazně doporučujeme integraci jiné služby, jako jsou Application Insights do vaší aplikace.

## <a name="setup-diagnostics-extension"></a>Instalace diagnostického rozšíření

První, pokud nemáte k dispozici **classic** účet úložiště, [vytvořit](../storage/common/storage-quickstart-create-account.md). Ujistěte se, že účet úložiště se vytvoří s **model nasazení Classic** zadané.

Dále přejděte **účet úložiště (klasický)** prostředků. Vyberte **nastavení** > **přístupové klíče** a zkopírujte **primární připojovací řetězec** hodnotu. Tuto hodnotu budete potřebovat pro cloudovou službu. 

Existují dva konfigurační soubory pro pokročilou diagnostiku, aby byla povolena, musíte změnit **ServiceDefinition.csdef** a **souboru ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

V **ServiceDefinition.csdef** přidejte nové nastavení s názvem `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` pro každou roli, která používá pokročilou diagnostiku. Když vytvoříte nový projekt sady Visual Studio přidá tuto hodnotu do souboru. V případě, že chybí, můžete ji nyní přidat. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Definuje nové nastavení, která je nutné přidat ke každému **souboru ServiceConfiguration.cscfg** souboru. 

Pravděpodobně máte dva **.cscfg** soubory, jednu s názvem **ServiceConfiguration.cloud.cscfg** pro nasazení do Azure a cluster s názvem **ServiceConfiguration.local.cscfg** který se používá pro místní nasazení v prostředí emulované. Otevřete a změňte každou **.cscfg** souboru. Přidejte nastavení s názvem `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Nastavte hodnotu **primární připojovací řetězec** účtu klasického úložiště. Pokud chcete použít místní úložiště na vývojovém počítači, použijte `UseDevelopmentStorage=true`.

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

## <a name="use-application-insights"></a>Pomocí služby Application Insights

Když publikujete Cloudovou službu v sadě Visual Studio, budete mít možnost posílat diagnostická data do Application Insights. Můžete vytvoří prostředek služby Application Insights Azure v daném čase nebo odesílat data do stávající prostředky Azure. Cloudové služby můžete monitorovat pomocí Application Insights pro dostupnost, výkon, chyby a využití. Vlastní grafy můžete přidat do služby Application Insights, kde můžete zobrazit data, která nejvíc záleží. Pomocí Application Insights SDK do projektu cloudové služby se můžou shromažďovat role instance data. Další informace o tom, jak integrovat Application Insights najdete v tématu [Application Insights s cloudovými službami](../application-insights/app-insights-cloudservices.md).

Všimněte si, že zatímco Application Insights můžete použít k zobrazení čítačů výkonu (a další nastavení) zadáte prostřednictvím rozšíření diagnostiky Windows Azure, můžete pouze získat pohodlnější a pestřejší prostředí díky integraci služby Application Insights SDK do vaší role pracovního procesu a web.


## <a name="next-steps"></a>Další postup

- [Další informace o Application Insights s cloudovými službami](../application-insights/app-insights-cloudservices.md)
- [Nastavení čítače výkonu](diagnostics-performance-counters.md)

