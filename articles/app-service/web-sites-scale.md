---
title: Škálování aplikace v Azure | Microsoft Docs
description: Zjistěte, jak škálování aplikace v Azure App Service k přidání kapacity a funkcí.
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
ms.openlocfilehash: f8478a0aef52f8e573fa300a0b1328ea9ce5129f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030994"
---
# <a name="scale-up-an-app-in-azure"></a>Škálování aplikace v Azure

> [!NOTE]
> Nové **PremiumV2** vrstvy získáte rychlejších procesorů, úložiště SSD a zdvojnásobí paměti jádra poměr existující cenové úrovně. S výhodou, výkon může ušetřit peníze při spuštění aplikace na méně instancí. Škálování až **PremiumV2** vrstvy, najdete v části [konfigurace PremiumV2 úroveň služby App Service](app-service-configure-premium-tier.md).
>

Tento článek ukazuje, jak škálování aplikace v Azure App Service. Existují dva pracovní postupy pro škálování, škálování nahoru a škálování a tento článek vysvětluje rozšiřování škálování využívajících pracovního postupu.

* [Vertikální navýšení kapacity](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): získat další procesoru, paměti, místa na disku a speciálních funkcí, jako vyhrazené virtuální počítače (VM), vlastní domény a certifikáty, přípravné sloty, automatické škálování a další. Škálovat změnou cenové úrovně plánu služby App Service, který je součástí vaší aplikace.
* [Horizontální navýšení kapacity](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): zvyšte počet instancí virtuálního počítače, které spuštění aplikace.
  Můžete škálovat na až 20 instance, v závislosti na cenovou úroveň. [Prostředí App Service](environment/intro.md) v **izolovaná** další vrstvy zvyšuje spočítat Škálováním na více systémů na 100 instance. Další informace o škálování najdete v tématu [ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md). Zde můžete zjistit, jak používat automatické škálování, což je pro škálování počtu instancí automaticky na základě předdefinovaných pravidel a plány.

Nastavení škálování trvat jenom sekund použít a vliv na všechny aplikace ve vaší [plán služby App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
Budou měnit kód nebo znovu nasadit aplikace nevyžadují.

Informace o cenách a funkce jednotlivých plány služby App Service najdete v tématu [podrobnosti o cenách na aplikaci služby](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Předtím, než přepnete z plán služby App Service **volné** úroveň, je nutné nejprve odebrat [limitů útraty](https://azure.microsoft.com/pricing/spending-limits/) nastavené pro vaše předplatné Azure. Zobrazit nebo změnit možnosti pro vaše předplatné Microsoft Azure App Service naleznete v tématu [předplatná Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Škálování cenové úrovně
1. V prohlížeči otevřete [portál Azure][portal].
2. Na stránce aplikace služby App Service, klikněte na tlačítko **všechna nastavení**a potom klikněte na **vertikálně navýšit kapacitu**.
   
    ![Přejděte do škálovat aplikaci Azure.][ChooseWHP]
3. Zvolte úroveň a pak klikněte na **použít**.
   
    **Oznámení** karta bude flash zelená **úspěch** po dokončení operace.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Škálování související informační zdroje
Pokud vaše aplikace závisí na jiné služby, například Azure SQL Database nebo úložiště Azure, můžete postupně škálovat tyto prostředky samostatně. Tyto prostředky nejsou spravovány nástrojem plán služby App Service.

1. V **Essentials**, klikněte **skupiny prostředků** odkaz.
   
    ![Škálování souvisejících prostředků Azure aplikace](./media/web-sites-scale/RGEssentialsLink.png)
2. V **Souhrn** součástí **skupiny prostředků** klikněte na prostředek, který chcete škálovat. Následující snímek obrazovky ukazuje prostředků databáze SQL a prostředek služby Azure Storage.
   
    ![Přejděte na stránku skupiny prostředků škálování aplikace Azure](./media/web-sites-scale/ResourceGroup.png)
3. Pro databáze SQL prostředek, klikněte na tlačítko **nastavení** > **cenová úroveň** škálování cenové úrovně.
   
    ![Škálování SQL databáze back-end pro vaše aplikace Azure](./media/web-sites-scale/ScaleDatabase.png)
   
    Můžete také zapnout [geografická replikace](../sql-database/sql-database-geo-replication-overview.md) pro instanci databáze SQL.
   
    Prostředek Azure Storage, klikněte na tlačítko **nastavení** > **konfigurace** škálování možnosti úložiště.
   
    ![Škálování účtu úložiště Azure používat aplikaci Azure](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Porovnání cenové úrovně
Podrobné informace, jako je například velikosti virtuálních počítačů u jednotlivých cenových úrovní najdete v části [podrobnosti o cenách na aplikaci služby](https://azure.microsoft.com/pricing/details/web-sites/).
Tabulku omezení služby, kvóty a omezení a podporovaných funkcích v jednotlivých vrstvách, najdete v části [omezení služby App Service](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="next-steps"></a>Další postup
* Informace o cenách, podpory a smlouvy o úrovni služeb získáte pomocí následujících odkazů:
  
    [Podrobnosti o cenách přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Plánům podpory Azure Microsoft](https://azure.microsoft.com/support/plans/)
  
    [smlouvám o úrovni služeb](https://azure.microsoft.com/support/legal/sla/)
  
    [Podrobnosti o cenách na SQL databázi](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Virtuální počítač a velikost cloudových služeb pro Microsoft Azure][vmsizes]
  
* Informace o službě Azure App Service osvědčených postupů, včetně vytváření škálovatelná a flexibilní architektuře, najdete v části [osvědčené postupy: Azure App Service Web Apps](https://azure.microsoft.com/blog/best-practices-windows-azure-websites-waws/).
* Videa o škálování služby App Service aplikace najdete v následujících zdrojích informací:
  
  * [Kdy škálovat weby Azure - s Stefan Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Automatické škálování webů Azure, využití procesoru nebo naplánované – s Stefan Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Jak Azure měřítko weby - s Stefan Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
