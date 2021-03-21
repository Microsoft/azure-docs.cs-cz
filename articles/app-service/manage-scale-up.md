---
title: Horizontální navýšení kapacity funkcí a kapacit
description: Naučte se navýšení kapacity aplikace v Azure App Service. Získejte větší nároky na procesor, paměť, místo na disku a další funkce.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: eb0ce5cfadbab247299cd3c22bf6ee618bf19731
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100582475"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Horizontální navýšení kapacity aplikace v Azure App Service

V tomto článku se dozvíte, jak škálovat aplikaci v Azure App Service. Existují dva pracovní postupy pro škálování, horizontální navýšení kapacity a horizontální navýšení kapacity a tento článek vysvětluje pracovní postup horizontálního navýšení kapacity.

* [Horizontální navýšení kapacity](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Získejte více procesorů, paměti, místa na disku a navíc funkce, jako jsou vyhrazené virtuální počítače (VM), vlastní domény a certifikáty, pracovní sloty, automatické škálování a další. Horizontální navýšení kapacity můžete škálovat změnou cenové úrovně App Service plánu, ke kterému vaše aplikace patří.
* [Horizontální](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)navýšení kapacity: zvyšte počet instancí virtuálních počítačů, ve kterých běží vaše aplikace.
  V závislosti na cenové úrovni můžete škálovat na až 30 instancí. [App Service prostředí](environment/intro.md) v **izolované** úrovni dále zvyšují počet škálování na více instancí 100. Další informace o horizontálním navýšení kapacity najdete v tématu [Ruční nebo automatické škálování počtu instancí](../azure-monitor/autoscale/autoscale-get-started.md). Zde zjistíte, jak používat automatické škálování, což znamená automatické škálování počtu instancí na základě předdefinovaných pravidel a plánů.

Nastavení škálování trvá jenom pár sekund, které se mají použít, a ovlivňuje všechny aplikace v [plánu App Service](../app-service/overview-hosting-plans.md).
Nevyžadují změnu kódu nebo opětovného nasazení aplikace.

Informace o cenách a funkcích jednotlivých plánů App Service najdete v článku podrobnosti o [cenách App Service](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Než z **bezplatné** úrovně přepnete plán App Service, musíte nejdřív odebrat [Limity útraty](https://azure.microsoft.com/pricing/spending-limits/) pro vaše předplatné Azure. Pokud chcete zobrazit nebo změnit možnosti pro předplatné Microsoft Azure App Service, přečtěte si téma [Microsoft Azure předplatná][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Horizontální škálování cenové úrovně

> [!NOTE]
> Postup při horizontálním navýšení kapacity na **PremiumV3** úrovně najdete v tématu [Konfigurace vrstvy PremiumV3 pro App Service](app-service-configure-premium-tier.md).
>

1. Otevřete v prohlížeči portál [Azure Portal][portal].

1. Na stránce aplikace App Service v nabídce vlevo vyberte **škálovat (App Service plán)**.
   
3. Zvolte úroveň a pak vyberte **použít**. Vyberte různé kategorie (například **produkci**) a také **Zobrazte další možnosti** a zobrazte další úrovně.
   
    ![Přejděte k horizontálnímu navýšení kapacity aplikace Azure.][ChooseWHP]

    Po dokončení operace se zobrazí automaticky otevírané okno s oznámením, které má zelenou značku kontroly úspěšnosti.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Prostředky související s horizontálním škálováním
Pokud vaše aplikace závisí na dalších službách, jako je Azure SQL Database nebo Azure Storage, můžete tyto prostředky škálovat samostatně. Tyto prostředky nejsou spravované plánem App Service.

1. Na stránce **Přehled** vaší aplikace vyberte odkaz **Skupina prostředků** .
   
    ![Horizontální navýšení kapacity souvisejících prostředků aplikace Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. V části **Souhrn** na stránce **Skupina prostředků** vyberte prostředek, který chcete škálovat. Následující snímek obrazovky ukazuje SQL Database prostředek.
   
    ![Přechod na stránku skupiny prostředků pro horizontální navýšení kapacity aplikace Azure](./media/web-sites-scale/ResourceGroup.png)

    Postup horizontálního navýšení kapacity souvisejícího prostředku najdete v dokumentaci pro konkrétní typ prostředku. Například pro horizontální navýšení kapacity jednoho SQL Database najdete informace v tématu věnovaném [škálování prostředků jedné databáze v Azure SQL Database](../azure-sql/database/single-database-scale.md). Postup horizontálního navýšení kapacity Azure Database for MySQL prostředků najdete v tématu věnovaném [škálování prostředků MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Porovnat cenové úrovně

Podrobné informace, jako jsou velikosti virtuálních počítačů pro každou cenovou úroveň, najdete v článku [o cenách App Service](https://azure.microsoft.com/pricing/details/app-service).

Tabulka omezení, kvót a omezení služby a podporovaných funkcí v jednotlivých úrovních najdete v tématu [omezení App Service](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Další zdroje informací

[Ruční nebo automatické škálování počtu instancí](../azure-monitor/autoscale/autoscale-get-started.md)  
[Konfigurace PremiumV3 úrovně pro App Service](app-service-configure-premium-tier.md)

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