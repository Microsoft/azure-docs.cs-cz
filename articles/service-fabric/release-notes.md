---
title: Verze služby Azure Service Fabric
description: Zpráva k vydání verze týkající se nejnovějších funkcí a vylepšení v Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 78fb96cae3d3d128da608183f37771b2ecf66dcf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165362"
---
# <a name="service-fabric-releases"></a>Service Fabric verze

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Průvodce odstraňováním potíží</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">sledování problémů</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">možnosti podpory</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">podporované verze</a>  
|  <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Ukázky kódu</a>

Tento článek poskytuje další informace o nejnovější vydané verze a aktualizace modulu runtime Service Fabric a sady SDK.

## <a name="whats-new-in-service-fabric"></a>**Co je nového v Service Fabric**

### <a name="service-fabric-65"></a>Service Fabric verze 6.5

Nejnovější verze Service Fabric obsahuje možnosti podpory, spolehlivost a vylepšení výkonu, nové funkce, opravy chyb a vylepšení pro usnadnění správy životního cyklu aplikací a clusteru.

> [!IMPORTANT]
> Service Fabric 6.5 je ve finální verzi s využitím Service Fabric tools podporují v sadě Visual Studio 2015. Zákazníci by měli přesunout do [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) do budoucna.

Zde je, co je nového v Service Fabric 6.5:

- Obsahuje Service Fabric Explorer [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) kontroly aplikací, když jste odeslali do úložiště imagí.

- [Aplikace orchestraci oprav (POA)](service-fabric-patch-orchestration-application.md) verze [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) zahrnuje mnoho vylepšení vlastní diagnostiku dokážou. Zákazníci POA se doporučuje, přejděte na tuto verzi.

- [Ve výchozím nastavení je povolena služba Eventstoru](service-fabric-visualizing-your-cluster.md#event-store) u clusterů Service Fabric 6.5, pokud jste se rozhodli navýšení kapacity.

- Přidání [události životního cyklu repliky](service-fabric-diagnostics-event-generation-operational.md#replica-events) pro stavové služby.

- [Lepší viditelnost počáteční stav uzlu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), včetně úrovně clusteru upozornění, pokud je počátečním uzlem není v pořádku (*dolů*, *odebráno* nebo *neznámý*).

- [Nástroj pro obnovení po havárii Service Fabric aplikace](https://github.com/Microsoft/Service-Fabric-AppDRTool) umožňuje stavové služby Service Fabric provést rychlé obnovení při primární clusteru dojde k havárii. Data z primární clusteru se synchronizuje neustále na sekundární pohotovostní aplikace pomocí pravidelné zálohování a obnovení.

- Visual Studio – podpora pro [publikování aplikace .NET Core na linuxových clusterech](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) se budou instalovat automaticky pro Service Fabric 6.5 (a novějších verzí) při upgradu nebo vytvoření nového clusteru s Linuxem v Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) se instaluje standardně na clusterech OneBox MacOS nebo Linux.

Další podrobnosti najdete v tématu [zpráva k vydání verze pro Service Fabric verze 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

## <a name="previous-versions"></a>Předchozí verze

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 vydané verze

| Datum vydání | Vydat | Další informace |
|---|---|---|
| 30. listopadu 2018 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12. prosince 2018 | [Clustery Azure Service Fabric 6.4 aktualizovat verzi pro Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4\. února 2019 | [Azure Service Fabric 6.4 aktualizace verze](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4\. březen 2019 | [Azure Service Fabric 6.4 aktualizace verze](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8\. dubna 2019 | [Azure Service Fabric 6.4 aktualizace verze](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2\. května 2019 | [Azure Service Fabric 6.4 aktualizace verze](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28. května 2019 | [Azure Service Fabric 6.4 aktualizace verze](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
