---
title: Vytvoření a správa hybridních připojení | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit hybridní připojení, správě připojení a instalaci správce hybridního připojení. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
ms.assetid: aac0546b-3bae-41e0-b874-583491a395ea
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 1c05a50f82f5c235c76ff234efe183172e0863bf
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232976"
---
# <a name="create-and-manage-hybrid-connections"></a>Vytvoření a správa hybridních připojení

> [!IMPORTANT]
> Hybridní připojení BizTalk jsou vyřazena z provozu a nahrazena hybridními připojeními App Service. Další informace, včetně informací o tom, jak spravovat existující hybridní připojení BizTalk, najdete v tématu [Hybridní připojení Azure App Service](../app-service/app-service-hybrid-connections.md).

>[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="overview-of-the-steps"></a>Přehled kroků
1. Vytvoření hybridního připojení tak, že zadáte **název hostitele** nebo **plně kvalifikovaný název domény** místní prostředku v privátní síti.
2. Propojte s Azure web apps nebo Azure mobile apps hybridní připojení.
3. Instalace správce hybridního připojení na místním prostředkům a připojení ke konkrétní hybridní připojení. Na webu Azure portal poskytuje možnosti jedním kliknutím k instalaci a připojení.
4. Spravujte hybridní připojení a jejich klíče připojení.

Toto téma obsahuje seznam těchto kroků. 

> [!IMPORTANT]
> Je možné nastavit koncovým bodem hybridního připojení k IP adrese. Pokud používáte IP adresu, může nebo nemusí dosáhnout místnímu prostředku, v závislosti na klientu. Hybridní připojení, závisí na straně klienta provádění vyhledávání DNS. Ve většině případů **klienta** je kód vaší aplikace. Pokud klient nebude provádět vyhledávání DNS (se nepokusí se přeložit IP adresu, jako by šlo názvu domény (x.x.x.x)), pak nejsou odesílány přenosů přes hybridní připojení.
> 
> Například (pseudokódu), můžete definovat **10.4.5.6** jako místního hostitele:
> 
> **Následujícím scénáři funguje:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **Následující scénář nefunguje:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>Vytvoření hybridního připojení
Hybridní připojení se dá vytvořit v [Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) **nebo** pomocí [rozhraní REST API BizTalk Services](https://msdn.microsoft.com/library/azure/dn232347.aspx). 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

#### <a name="additional"></a>Další
* Je možné vytvořit více hybridní připojení. Zobrazit [BizTalk Services: Tabulka edic](biztalk-editions-feature-chart.md) počet připojení povolených. 
* Každé hybridní připojení je vytvořené pomocí páru připojovacích řetězců: aplikace klíče této SEND a On-premises klíče, které NASLOUCHAT. Každý pár má primární a sekundární klíč. 

## <a name="LinkWebSite"></a>Propojení služby Azure App Service webovou aplikaci nebo mobilní aplikace
Odkaz na webovou aplikaci nebo mobilní aplikace ve službě Azure App Service na existující hybridní připojení, vyberte **použít existující hybridní připojení** v okně hybridní připojení. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>Instalace správce hybridního připojení on-premises
Po vytvoření hybridního připojení nainstalujte Správce hybridního připojení místních prostředků. Můžete ho stáhnout z webových aplikací Azure nebo z vaší služby BizTalk. 

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]
 
[Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) je také dobré zdroje.

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Další
* Správce hybridního připojení lze nainstalovat v následujících operačních systémech:
  
  * Windows Server 2008 R2 (.NET Framework 4.5 + a Windows Management Framework 4.0 + je nutná)
  * Windows Server 2012 (Windows Management Framework 4.0 + je nutná)
  * Windows Server 2012 R2
* Po instalaci správce hybridního připojení se stane toto: 
  
  * Hybridní připojení, který je hostovaný v Azure se automaticky konfigurují pro použití primární připojovací řetězec aplikace. 
  * On-Premises prostředku se automaticky konfigurují pro použití primární připojovací řetězec místní.
* Správce hybridního připojení, musíte použít připojovací řetězec platným místním pro autorizaci. Azure Web Apps nebo Mobile Apps, musí pro autorizaci použít připojovací řetězec platnou aplikaci.
* Hybridní připojení je možné škálovat nainstalováním jiná instance správce hybridního připojení na jiný server. Konfigurace naslouchacího procesu lokálně stejnou adresu použít jako první naslouchací proces s místními. V takovém případě provoz probíhá mezi naslouchacích procesů aktivní místní náhodně distribuovaných (round robin). 

## <a name="ManageHybridConnection"></a>Správa hybridních připojení

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) je také dobré zdroje.

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Kopírování/regenerate hybridní připojovacích řetězců

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) je také dobré zdroje.

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Pomocí zásad skupiny řídit místní prostředky, které používají hybridní připojení
1. Stáhněte si [šablony pro správu Správce hybridního připojení](https://www.microsoft.com/download/details.aspx?id=42963).
2. Extrahujte soubory.
3. Na počítači, který upravuje zásad skupiny postupujte takto:  
   
   * Kopírování. Soubory ADMX *%WINROOT%\PolicyDefinitions* složky.
   * Kopírování. ADML soubory *%WINROOT%\PolicyDefinitions\en-us* složky.

Po zkopírování můžete změnit zásady Editor zásad skupiny.

## <a name="next"></a>Další
[Přehled hybridních připojení](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>Viz také
[Rozhraní REST API pro správu služby BizTalk Services v Microsoft Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk Services: Tabulka edic](biztalk-editions-feature-chart.md)  
[Vytvoření služby BizTalk](biztalk-provision-services.md)  
[BizTalk Services: Karty Řídicí panel, Sledování a Škálování](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
