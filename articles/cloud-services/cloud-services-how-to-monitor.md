---
title: Monitorování cloudové služby Azure | Dokumenty společnosti Microsoft
description: Popisuje, co monitorování cloudové služby Azure zahrnuje a jaké jsou některé z vašich možností.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 01/29/2018
ms.author: tagore
ms.openlocfilehash: 61c794ba03934ae1828ba310f3f776bfb61b652b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273095"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Úvod do monitorování cloudových služeb

Můžete sledovat metriky výkonu klíčů pro všechny cloudové služby. Každá role cloudové služby shromažďuje minimální data: využití procesoru, využití sítě a využití disku. Pokud cloudová služba `Microsoft.Azure.Diagnostics` má rozšíření použít na roli, tato role můžete shromažďovat další body dat. Tento článek obsahuje úvod do Diagnostika Azure pro cloudové služby.

Při základním monitorování jsou vzorkována data čítače výkonu z instancí rolí a shromažďována v 3minutových intervalech. Tato základní data monitorování nejsou uložena ve vašem účtu úložiště a není s ním spojeny žádné další náklady.

S pokročilým monitorováním jsou vzorkovány další metriky a shromažďovány v intervalech 5 minut, 1 hodina a 12 hodin. Agregovaná data jsou uložena v účtu úložiště, v tabulkách a po 10 dnech se vymažou. Použitý účet úložiště je nakonfigurován podle role; můžete použít různé účty úložiště pro různé role. Tato možnost je konfigurována pomocí připojovacího řetězce v souborech [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) a [.cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg)


## <a name="basic-monitoring"></a>Základní monitorování

Jak je uvedeno v úvodu, cloudová služba automaticky shromažďuje základní data monitorování z hostitelského virtuálního počítače. Tato data zahrnují procento procesoru, vstup/vycházku sítě a čtení i zápis na disku. Shromážděná data monitorování se automaticky zobrazí na stránkách přehledu a metrik y cloudové služby na webu Azure Portal. 

Základní monitorování nevyžaduje účet úložiště. 

![základní dlaždice monitorování cloudových služeb](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Pokročilé sledování

Pokročilé monitorování zahrnuje použití rozšíření **Diagnostika Azure** (a volitelně Application Insights SDK) na roli, kterou chcete sledovat. Rozšíření diagnostiky používá konfigurační soubor (na roli) s názvem **diagnostics.wadcfgx** ke konfiguraci monitorovaných metrik diagnostiky. Rozšíření Azure Diagnostic shromažďuje a ukládá data v účtu Azure Storage. Tato nastavení jsou konfigurována v souborech **WADCFGX**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)a [.cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg) To znamená, že s pokročilým monitorováním jsou spojeny dodatečné náklady.

Při vytváření každé role do ní Visual Studio přidá rozšíření Diagnostika Azure. Toto rozšíření diagnostiky může shromažďovat následující typy informací:

* Čítače vlastního výkonu
* Protokoly aplikací
* Protokoly událostí Windows
* Zdroj události rozhraní .NET
* Protokoly IIS
* Trasování událostí pro Windows na základě manifestu
* Výpisy stavu systému
* Protokoly chyb zákazníka

> [!IMPORTANT]
> Zatímco všechna tato data jsou agregována do účtu úložiště, portál **neposkytuje** nativní způsob, jak data zmapovat. Důrazně doporučujeme integrovat do aplikace jinou službu, jako je Application Insights.

## <a name="setup-diagnostics-extension"></a>Rozšíření diagnostiky instalace

Za prvé, pokud nemáte **klasický** účet úložiště, [vytvořte si ho](../storage/common/storage-account-create.md). Ujistěte se, že účet úložiště je vytvořen s **klasickým modelem nasazení** zadané.

Dále přejděte na **prostředek účtu úložiště (klasické).** Vyberte **Nastavení** > **Přístupových kláves** a zkopírujte hodnotu **Primárního připojovacího řetězce.** Tuto hodnotu potřebujete pro cloudovou službu. 

Pro povolení rozšířené diagnostiky je nutné změnit dva konfigurační soubory, **servicedefinition.csdef** a **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

V souboru **ServiceDefinition.csdef** přidejte `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` nové nastavení pojmenované pro každou roli, která používá pokročilou diagnostiku. Visual Studio přidá tuto hodnotu do souboru při vytváření nového projektu. V případě, že chybí, můžete jej nyní přidat. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Definuje nové nastavení, které musí být přidáno do každého souboru **ServiceConfiguration.cscfg.** 

S největší pravděpodobností máte dva **soubory .cscfg,** jeden s názvem **ServiceConfiguration.cloud.cscfg** pro nasazení do Azure a jeden s názvem **ServiceConfiguration.local.cscfg,** který se používá pro místní nasazení v emulovaném prostředí. Otevřete a změňte každý soubor **.cscfg.** Přidejte nastavení `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`s názvem . Nastavte hodnotu primárního **připojovacího řetězce** klasického účtu úložiště. Pokud chcete použít místní úložiště ve vývojovém `UseDevelopmentStorage=true`počítači, použijte .

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

## <a name="use-application-insights"></a>Použití přehledů aplikací

Při publikování cloudové služby z Visual Studia máte možnost odeslat diagnostická data do Application Insights. V té době můžete vytvořit prostředek Azure application insights nebo odeslat data do existujícího prostředku Azure. Vaše cloudová služba může být monitorována application insights pro dostupnost, výkon, selhání a využití. Vlastní grafy lze přidat do Application Insights, takže můžete vidět data, která jsou nejdůležitější. Data instance role lze shromažďovat pomocí sady Application Insights SDK v projektu cloudové služby. Další informace o tom, jak integrovat Application Insights, najdete v [tématu Application Insights s cloudovými službami](../azure-monitor/app/cloudservices.md).

Všimněte si, že zatímco pomocí application insights můžete zobrazit čítače výkonu (a další nastavení), které jste zadali prostřednictvím rozšíření Diagnostika Windows Azure, získáte pouze bohatší prostředí integrací sady Application Insights SDK do sady Application Insights do sady Application Insights pracovníka a webové role.


## <a name="next-steps"></a>Další kroky

- [Další informace o přehledech aplikací pomocí cloudových služeb](../azure-monitor/app/cloudservices.md)
- [Nastavení čítačů výkonu](diagnostics-performance-counters.md)




