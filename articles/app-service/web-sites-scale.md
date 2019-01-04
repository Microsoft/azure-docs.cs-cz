---
title: Vertikálně navýšit kapacitu funkce a kapacity - službě Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak pro vertikální navýšení kapacity aplikace v Azure App Service a přidávání kapacity a funkcí.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 98d3d1f6fc0f2f30196f360811808579dfbab312
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727475"
---
# <a name="scale-up-an-app-in-azure"></a>Vertikální navýšení kapacity aplikace v Azure

> [!NOTE]
> Nové **PremiumV2** úroveň poskytuje rychlejší procesory, SSD úložištěm a Double poměr paměti k jádrům stávající cenové úrovně. S výhodou, výkon můžete ušetřit peníze při spuštění aplikace na méně instancí. Chcete-li škálovat až **PremiumV2** vrstvy, přečtěte si téma [úrovně PremiumV2 konfigurace pro službu App Service](app-service-configure-premium-tier.md).
>

Tento článek ukazuje, jak škálovat aplikaci ve službě Azure App Service. Existují dva pracovní postupy pro škálování, škálování nahoru a horizontální navýšení kapacity a tento článek vysvětluje vertikálního navýšení pracovního postupu.

* [Vertikálně navýšit kapacitu](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Získejte další procesoru, paměti, místa na disku a další funkce, jako je vyhrazené virtuální počítače (VM), vlastních domén a certifikáty, přípravné sloty, automatické škálování a další. Vertikálně navýšit kapacitu změnou cenové úrovně plánu služby App Service, které vaše aplikace patří.
* [Horizontální navýšení kapacity](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Zvýšíte počet instancí virtuálních počítačů, na kterých běží vaše aplikace.
  Můžete horizontálně navýšit až 20 instancí, v závislosti na cenovou úroveň. [App Service Environment](environment/intro.md) v **izolované** další úrovně zvyšuje počet vašich horizontální navýšení kapacity až na 100 instancí. Další informace o horizontální navýšení kapacity najdete v tématu [ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md). Existuje zjistíte, jak pomocí automatického škálování, což je škálování počtu instancí automaticky na základě předdefinovaných pravidel a plány.

Nastavení škálování trvat jen několik sekund pro použití a ovlivňují všechny aplikace ve vaší [plán služby App Service](../app-service/overview-hosting-plans.md).
Můžete změnit váš kód nebo znovu nasadit aplikaci nevyžadují.

Informace o cenách a funkcích jednotlivé plány služby App Service najdete v tématu [podrobnosti o cenách na App Service](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Předtím, než přepnete z plánu služby App Service **Free** vrstvy, musíte nejdřív odebrat [limitech útraty](https://azure.microsoft.com/pricing/spending-limits/) nastavené pro vaše předplatné Azure. Chcete-li zobrazit nebo změnit možnosti pro vaše předplatné Microsoft Azure App Service, najdete v článku [předplatná Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Vertikálně navýšit kapacitu svou cenovou úroveň
1. V prohlížeči se otevře [webu Azure portal][portal].
2. Na stránce aplikace služby App Service, klikněte na tlačítko **všechna nastavení**a potom klikněte na tlačítko **vertikálně navýšit kapacitu**.
   
    ![Přejděte na vertikální navýšení kapacity aplikace Azure.][ChooseWHP]
3. Volba úrovně a poté klikněte na tlačítko **použít**.
   
    **Oznámení** karta bude flash zelený **úspěch** po dokončení operace.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Škálování související prostředky
Pokud vaše aplikace závisí na jiné služby, jako je Azure SQL Database nebo Azure Storage, můžete vertikálně navýšit kapacitu tyto prostředky samostatně. Tyto prostředky nejsou spravovány nástrojem plán služby App Service.

1. V **Essentials**, klikněte na tlačítko **skupiny prostředků** odkaz.
   
    ![Vertikální navýšení kapacity aplikace Azure související prostředky](./media/web-sites-scale/RGEssentialsLink.png)
2. V **Souhrn** součástí **skupiny prostředků** klikněte na prostředek, který chcete škálovat. Následující snímek obrazovky ukazuje prostředek databáze SQL a prostředek služby Azure Storage.
   
    ![Přejděte na stránce skupiny prostředků vertikální navýšení kapacity aplikace Azure](./media/web-sites-scale/ResourceGroup.png)
3. Pro prostředek databáze SQL klikněte na tlačítko **nastavení** > **cenová úroveň** škálovat cenovou úroveň.
   
    ![Vertikálně navýšit kapacitu back-end SQL Database pro vaši aplikaci Azure](./media/web-sites-scale/ScaleDatabase.png)
   
    Můžete také zapnout [geografickou replikaci](../sql-database/sql-database-geo-replication-overview.md) pro vaši instanci SQL Database.
   
    Pro prostředek služby Azure Storage, klikněte na tlačítko **nastavení** > **konfigurace** vertikálně navýšit kapacitu možností úložiště.
   
    ![Vertikální navýšení kapacity účtu úložiště Azure používat aplikaci Azure](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Porovnejte cenové úrovně
Podrobné informace, jako je například velikosti virtuálních počítačů u jednotlivých cenových úrovní najdete v části [podrobnosti o cenách na App Service](https://azure.microsoft.com/pricing/details/web-sites/).
Tabulka obsahující limity, kvóty a omezení a podporovaných funkcích v jednotlivých úrovních, naleznete v tématu [limity služby App Service](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="next-steps"></a>Další postup
* Informace o cenách, podpora a SLA najdete na následujících odkazech:
  
    [Podrobnosti o cenách za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Microsoft má v plánu podpory Azure](https://azure.microsoft.com/support/plans/)
  
    [smlouvám o úrovni služeb](https://azure.microsoft.com/support/legal/sla/)
  
    [Podrobnosti o cenách na SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Virtuální počítač a velikost cloudových služeb pro Microsoft Azure][vmsizes]
  
* Informace o službě Azure App Service najdete v části osvědčených postupů, včetně vytváření škálovatelné a odolné architektury, [osvědčených postupů: Azure App Service Web Apps](https://azure.microsoft.com/blog/best-practices-windows-azure-websites-waws/).
* Videa o škálování aplikací služby App Service naleznete v následujících zdrojích:
  
  * [Kdy škálovat weby Azure – s Stefan Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Automatické škálování Azure Websites, využití procesoru nebo naplánované – s Stefan Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Azure Websites škálování – s Stefan Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
