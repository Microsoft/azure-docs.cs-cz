---
title: Verze Azure Service Fabric
description: Poznámky k verzi pro nejnovější funkce a vylepšení v Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 636518688aec1f514b551e34219acfc1c9e81aec
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965386"
---
# <a name="service-fabric-releases"></a>Verze Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Poradci při potížích s průvodci</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank"></a> 
| –<a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Možnosti</a> 
| podpory –<a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">ukázky kódu</a> pro<a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">podporované verze</a> 
| 

V tomto článku najdete další informace o nejnovějších vydáních a aktualizacích Service Fabric runtime a sadách SDK.

## <a name="whats-new-in-service-fabric"></a>Co je nového v Service Fabric

### <a name="service-fabric-65"></a>Service Fabric 6,5

Nejnovější verze Service Fabric zahrnují podporu, spolehlivost a vylepšení výkonu, nové funkce, opravy chyb a vylepšení, které usnadňují správu životního cyklu clusteru a aplikací.

> [!IMPORTANT]
> Service Fabric 6,5 je konečná verze s podporou nástrojů Service Fabric Tools v aplikaci Visual Studio 2015. Zákazníkům se doporučuje přesunout se do sady [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) až dál.

V Service Fabric 6,5 je nový co je:

- Service Fabric Explorer obsahuje [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) pro kontrolu aplikací, které jste nahráli do úložiště imagí.

- [Aplikace orchestrace opravy (Poa)](service-fabric-patch-orchestration-application.md) verze [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) obsahuje mnoho vylepšení s automatickým diagnostikou. Zákazníkům rodu POA se doporučuje přejít na tuto verzi.

- [Služba eventstoru je ve výchozím nastavení povolená](service-fabric-visualizing-your-cluster.md#event-store) pro clustery Service Fabric 6,5, pokud jste se nerozhodli.

- Byly přidány [události životního cyklu repliky](service-fabric-diagnostics-event-generation-operational.md#replica-events) pro stavové služby.

- [Lepší viditelnost stavu počátečního uzlu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), včetně upozornění na úrovni clusteru, pokud je počáteční uzel v pořádku (*dolů*, *odebrán* nebo *Neznámý*).

- [Service Fabric Nástroj pro zotavení po havárii](https://github.com/Microsoft/Service-Fabric-AppDRTool) umožňuje Service Fabric stavové služby rychle obnovit v případě, že dojde k havárii primárního clusteru. Data z primárního clusteru se průběžně synchronizují v sekundárním úsporném režimu pomocí pravidelného zálohování a obnovování.

- Podpora sady Visual Studio pro [publikování aplikací .NET Core do clusterů se systémem Linux](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) se nainstaluje automaticky pro Service Fabric 6,5 (a novější verze) při upgradu nebo vytvoření nového clusteru se systémem Linux v Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) se ve výchozím nastavení instaluje v clusterech OneBox MacOS/Linux.

Další podrobnosti najdete v poznámkách k [verzi pro Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Verze Service Fabric 6,5

| Datum vydání | Vydat | Další informace |
|---|---|---|
| 11. června 2019 | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Poznámky k verzi](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2\. července 2019 | [Vydání aktualizace pro Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Poznámky k verzi](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29. července 2019 | [Vydání aktualizace pro Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Poznámky k verzi](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |


## <a name="previous-versions"></a>Předchozí verze

### <a name="service-fabric-64-releases"></a>Verze Service Fabric 6,4

| Datum vydání | Vydat | Další informace |
|---|---|---|
| 30. listopadu 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12. prosince 2018 | [Azure Service Fabric 6,4 aktualizace pro clustery Windows ve verzi](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4\. února 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4\. března 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8\. dubna 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2\. května 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28. května 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
