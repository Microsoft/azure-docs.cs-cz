---
title: Monitorování cloudové služby Azure (Classic) | Microsoft Docs
description: Popisuje, co zahrnuje monitorování cloudové služby Azure a jaké jsou některé z vašich možností.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 60f320f86860cca482cdf25c7d93f84dae8c4e5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743368"
---
# <a name="introduction-to-cloud-service-classic-monitoring"></a>Seznámení s monitorováním cloudové služby (Classic)

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Můžete monitorovat klíčové metriky výkonu pro libovolnou cloudovou službu. Každá role cloudové služby shromažďuje minimální data: využití CPU, využití sítě a využití disku. Pokud má cloudová služba `Microsoft.Azure.Diagnostics` u role použité rozšíření, může tato role shromažďovat další body dat. Tento článek poskytuje Úvod do Azure Diagnostics Cloud Services.

Díky základnímu monitorování jsou data čítače výkonu z instancí rolí ve vzorku a shromažďována v intervalech po dobu 3 minut. Tato základní data monitorování se ve vašem účtu úložiště neukládají a nevztahují se na ně žádné další náklady.

S pokročilým monitorováním jsou vzorky a shromážděny další metriky v intervalech 5 minut, 1 hodina a 12 hodin. Agregovaná data jsou uložená v účtu úložiště v tabulkách a vyprázdní se po 10 dnech. Použitý účet úložiště je nakonfigurovaný rolí. pro různé role můžete použít jiné účty úložiště. Tato konfigurace je nakonfigurována pomocí připojovacího řetězce v souborech [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) a [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .


## <a name="basic-monitoring"></a>Základní monitorování

Jak je uvedeno v úvodu, cloudová služba automaticky shromažďuje základní data monitorování z hostitelského virtuálního počítače. Tato data zahrnují procento využití procesoru, vstupně-výstupní sítě a čtení a zápis na disk. Shromážděná data monitorování se automaticky zobrazí na stránkách přehled a metrika cloudové služby v Azure Portal. 

Základní monitorování nevyžaduje účet úložiště. 

![dlaždice monitorování základní cloudové služby](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Pokročilé sledování

Rozšířené monitorování zahrnuje použití rozšíření **Azure Diagnostics** (a volitelně sady Application Insights SDK) na roli, kterou chcete monitorovat. Diagnostické rozšíření pomocí konfiguračního souboru (na roli) s názvem **Diagnostics. wadcfgx** konfiguruje monitorované metriky diagnostiky. Diagnostické rozšíření Azure shromažďuje a ukládá data v Azure Storagem účtu. Tato nastavení jsou konfigurována v souborech **. wadcfgx**, [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)a [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) . To znamená, že se k pokročilému monitorování účtují dodatečné náklady.

Při vytvoření každé role Visual Studio přidá k němu rozšíření Azure Diagnostics. Toto diagnostické rozšíření může shromažďovat následující typy informací:

* Vlastní čítače výkonu
* Protokoly aplikací
* Protokoly událostí Windows
* Zdroj události .NET
* Protokoly IIS
* Trasování událostí pro Windows na základě manifestu
* Výpisy stavu systému
* Protokoly chyb zákazníků

> [!IMPORTANT]
> I když jsou všechna tato data agregována do účtu úložiště, portál **neposkytuje nativní** způsob, jak data graficky vytvořit. Důrazně doporučujeme integrovat do své aplikace další službu, například Application Insights.

## <a name="setup-diagnostics-extension"></a>Rozšíření Instalace diagnostiky

Za prvé, pokud nemáte účet **klasického** úložiště, [vytvořte ho](../storage/common/storage-account-create.md). Ujistěte se, že je účet úložiště vytvořený s uvedeným **modelem nasazení Classic** .

Potom přejděte do prostředku **účtu úložiště (Classic)** . Vyberte **Nastavení**  >  **přístupové klíče** a zkopírujte hodnotu **primárního připojovacího řetězce** . Tuto hodnotu budete potřebovat pro cloudovou službu. 

Existují dva konfigurační soubory, které je třeba změnit, aby bylo možné povolit pokročilou diagnostiku, **ServiceDefinition. csdef** a **ServiceConfiguration. cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition. csdef

V souboru **ServiceDefinition. csdef** přidejte nové nastavení s názvem `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` pro každou roli, která používá pokročilou diagnostiku. Visual Studio přidá tuto hodnotu do souboru při vytváření nového projektu. V případě, že chybí, můžete ho nyní přidat. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Tím se definuje nové nastavení, které se musí přidat ke každému **ServiceConfiguration souboru. cscfg** . 

Pravděpodobně máte dva soubory **. cscfg** , jednu s názvem **ServiceConfiguration. Cloud. cscfg** pro nasazení do Azure a jednu s názvem **ServiceConfiguration. Local. cscfg** , která se používá pro místní nasazení v emulovaném prostředí. Otevřete a změňte každý soubor **. cscfg** . Přidejte nastavení s názvem `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` . Nastavte hodnotu na **primární připojovací řetězec** klasického účtu úložiště. Pokud chcete použít místní úložiště ve vývojovém počítači, použijte `UseDevelopmentStorage=true` .

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

## <a name="use-application-insights"></a>Použít Application Insights

Když publikujete cloudovou službu ze sady Visual Studio, budete mít možnost Odeslat diagnostická data do Application Insights. V tuto chvíli můžete vytvořit prostředek služby Application Insights Azure nebo odeslat data do existujícího prostředku Azure. Cloudovou službu je možné monitorovat pomocí Application Insights dostupnosti, výkonu, selhání a využití. Vlastní grafy lze přidat do Application Insights tak, abyste viděli data, která jsou nejvíc nejdůležitější. Data instance role se dají shromažďovat pomocí Application Insights SDK v projektu cloudové služby. Další informace o tom, jak integrovat Application Insights, najdete v článku [Application Insights s Cloud Services](../azure-monitor/app/cloudservices.md).

Všimněte si, že když můžete použít Application Insights k zobrazení čítačů výkonu (a dalších nastavení), která jste zadali prostřednictvím rozšíření Windows Azure Diagnostics, získáte bohatou činnost integrující sadu SDK Application Insights do rolí pracovního procesu a webu.


## <a name="next-steps"></a>Další kroky

- [Další informace o Application Insights s Cloud Services](../azure-monitor/app/cloudservices.md)
- [Nastavení čítačů výkonu](diagnostics-performance-counters.md)




