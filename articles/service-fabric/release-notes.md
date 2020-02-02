---
title: Verze Azure Service Fabric
description: Poznámky k verzi pro Azure Service Fabric. Obsahuje informace o nejnovějších funkcích a vylepšeních v Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 73624df3cb7b04c3c442a6654f1388017482b2ba
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935439"
---
# <a name="service-fabric-releases"></a>Verze Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Poradci při potížích s průvodci</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">
sledování problémů</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Možnosti</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">propodporované verze</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">ukázky kódu</a>

V tomto článku najdete další informace o nejnovějších vydáních a aktualizacích Service Fabric runtime a sadách SDK.

## <a name="whats-new-in-service-fabric"></a>Co je nového v Service Fabric

### <a name="service-fabric-70"></a>Service Fabric 7,0

Azure Service Fabric 7,0 je teď k dispozici! Pomocí Azure Portal nebo prostřednictvím Azure Resource Manager nasazení budete moct aktualizovat na 7,0. V důsledku zpětné vazby od zákazníků k vydaným obdobím po dobu svátků nezačneme automaticky aktualizovat clustery nastavené na příjem automatických upgradů do ledna.
V lednu budeme pokračovat standardním postupem a clustery s povolenými automatickými upgrady, začnou se aktualizace 7,0 automaticky dostávat. Před začátkem zavedení budeme poskytovat další oznámení.
Také aktualizujeme naše datum plánované verze, abychom zjistili, že tyto zásady máme v potaz. V budoucích [plánech](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)vydaných verzí najdete informace o aktualizacích.
 
Toto je nejnovější vydaná verze Service Fabric a je načtená s klíčovými funkcemi a vylepšeními.

### <a name="key-announcements"></a>Oznámení klíčů
 - [**Podpora KeyVaultReference pro aplikační tajné klíče (Preview)** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Service Fabric aplikace, které mají povolené [spravované identity](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) , teď můžou přímo odkazovat na adresu URL Key Vault tajných klíčů jako na proměnnou prostředí, parametr aplikace nebo přihlašovací údaje úložiště kontejnerů. Service Fabric bude tajný klíč automaticky přeložit pomocí spravované identity aplikace. 
     
- **Vylepšená bezpečnost upgradu pro bezstavové služby**: Pokud chcete zaručit dostupnost během upgradu aplikace, zavedli jsme nové konfigurace, abychom definovali [minimální počet instancí bezstavových služeb](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) , které se budou považovat za dostupné. Dřív byla tato hodnota 1 pro všechny služby a nedá se změnit. Díky této nové kontrole bezpečnosti pro jednotlivé služby můžete zajistit, aby vaše služby během upgradování aplikace, upgrady clusterů a jiné údržby zachovaly minimální počet instancí, a další údržbu, která spoléhá na kontrolu stavu a bezpečnosti Service Fabric.
  
- [**Omezení prostředků pro uživatelské služby**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services): uživatelé můžou nastavit omezení prostředků pro uživatelské služby na uzlu, aby se předešlo scénářům, jako je vyčerpání prostředků Service Fabric systémových služeb. 
  
- [**Velmi vysoké náklady na přesun služeb**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) pro typ repliky. Repliky s velmi vysokými náklady na přesun budou přesunuty pouze v případě porušení omezení v clusteru, které nelze opravit jiným způsobem. Další informace o tom, kdy je využití "velmi vysoké" nákladů na přesunutí přijatelné a další okolnosti, najdete v dokumentaci.
  
-  **Další bezpečnostní kontroly clusteru**: v této verzi jsme představili konfigurovatelnou kontrolu bezpečnosti kvora uzlů pro počáteční uzly. To vám umožní přizpůsobit, kolik počátečních uzlů musí být k dispozici během životního cyklu clusteru a scénářů správy. Operace, které by mohly převzít cluster pod konfigurovanou hodnotou, jsou zablokované. Dnes výchozí hodnota je vždy kvorum počátečních uzlů, například pokud máte 7 počátečních uzlů, operace, která by poznamenala méně než 5 počátečních uzlů, bude ve výchozím nastavení blokována. V důsledku této změny byste mohli nastavit minimální bezpečnou hodnotu 6, která by v jednom okamžiku umožňovala snížit pouze jeden uzel počáteční hodnoty.
   
- Přidání podpory pro [**správu služby zálohování a obnovení v Service Fabric Explorer**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Díky tomu jsou možné následující aktivity přímo z SFX: zjišťování služby zálohování a obnovení, vytváření zásad zálohování, povolení automatického zálohování, provádění záloh ad hoc, spouštění operací obnovení a procházení stávajících záloh.

- Oznamujeme dostupnost [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): Tento nástroj pomáhá ověřit, že typy používané ve spolehlivých kolekcích jsou během upgradu postupného použití předávány a zpětně kompatibilní. To pomáhá zabránit selháním upgradu nebo ztrátě dat a poškození dat z důvodu chybějících nebo nekompatibilních typů.

- [**Povolit stabilní čtení u sekundárních replik**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1): stabilní čtení omezí sekundární repliky tak, aby vracely hodnoty, které byly kvorum-potvrzeno.

Kromě toho tato verze obsahuje další nové funkce, opravy chyb a vylepšení podpory, spolehlivosti a výkonu. Úplný seznam změn najdete v [poznámkách k verzi](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Verze Service Fabric 7,0

| Datum vydání | Vydání | Další informace |
|---|---|---|
| 18. listopadu 2019 | [Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Poznámky k verzi](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30. ledna 2020 | [Vydání aktualizace pro Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Poznámky k verzi](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|


### <a name="service-fabric-65"></a>Service Fabric 6,5

Tato verze zahrnuje podporu, spolehlivost a vylepšení výkonu, nové funkce, opravy chyb a vylepšení, které usnadňují správu životního cyklu clusteru a aplikací.

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

Další podrobnosti najdete v [poznámkách k verzi pro Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Verze Service Fabric 6,5

| Datum vydání | Vydání | Další informace |
|---|---|---|
| 11. června 2019 | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Poznámky k verzi](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2\. července 2019 | [Vydání aktualizace pro Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Poznámky k verzi](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29. července 2019 | [Vydání aktualizace pro Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Poznámky k verzi](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Srpna 23, 2019 | [Vydání aktualizace pro Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Poznámky k verzi](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14. října 2019 | [Vydání aktualizace pro Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Poznámky k verzi](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Předchozí verze

### <a name="service-fabric-64-releases"></a>Verze Service Fabric 6,4

| Datum vydání | Vydání | Další informace |
|---|---|---|
| 30. listopadu 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12. prosince 2018 | [Azure Service Fabric 6,4 aktualizace pro clustery Windows ve verzi](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4\. února 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4\. března 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8\. dubna 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2\. května 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28. května 2019 | [Vydání aktualizace pro Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Poznámky k verzi](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
