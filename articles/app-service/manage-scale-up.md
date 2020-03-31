---
title: Škálování funkcí a kapacit
description: Přečtěte si, jak vertikálně navýšit kapacitu aplikace ve službě Azure App Service. Získejte více procesoru, paměti, místa na disku a další funkce.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: dfd9297e80836978b8a185df3fc4659676383802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659895"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Škálování aplikace ve službě Azure App Service

V tomto článku se ukazuje, jak škálovat aplikaci ve službě Azure App Service. Existují dva pracovní postupy pro škálování, škálování navýšit kapacitu a horizontální navýšení kapacity a tento článek vysvětluje vertikálně navýšit pracovní postup.

* [Škálování nahoru:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Získejte další procesor, paměť, místo na disku a další funkce, jako jsou vyhrazené virtuální počítače (VM), vlastní domény a certifikáty, pracovní sloty, automatické škálování a další. Můžete vertikálně navýšit tím, že změníte cenovou úroveň plánu služby App Service, ke kterému vaše aplikace patří.
* [Horizontální navýšení kapacity:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Zvyšte počet instancí virtuálních aplikací, které spouštějí vaši aplikaci.
  V závislosti na cenové úrovni můžete škálovat až na 30 instancí. [Prostředí služby App Service](environment/intro.md) v **izolované** vrstvě dále zvyšuje počet horizontálních navýšení kapacity na 100 instancí. Další informace o horizontálním navýšení kapacity naleznete [v tématu Škálování počtu instancí ručně nebo automaticky](../monitoring-and-diagnostics/insights-how-to-scale.md). Zde zjistíte, jak používat automatické škálování, což je automaticky škálovat počet instancí na základě předdefinovaných pravidel a plánů.

Nastavení škálování trvá pouze několik sekund, než se použijí a ovlivní všechny aplikace v [plánu služby App Service](../app-service/overview-hosting-plans.md).
Nevyžadují, abyste změnili kód nebo znovu nasadili aplikaci.

Informace o cenách a funkcích jednotlivých plánů služby App Service najdete [v tématu Podrobnosti o cenách služby App Service](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Než přepnete plán služby App Service z úrovně **Free,** musíte nejprve odebrat [limity útraty](https://azure.microsoft.com/pricing/spending-limits/) pro vaše předplatné Azure. Informace o zobrazení nebo změně možností předplatného služby Microsoft Azure App Service najdete v [tématu Předplatná Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Škálování cenové úrovně

> [!NOTE]
> Pokud chcete vertikálně navýšit kapacitu na úroveň **PremiumV2,** přečtěte si téma [Konfigurace úrovně PremiumV2 pro službu App Service](app-service-configure-premium-tier.md).
>

1. Otevřete v prohlížeči portál [Azure Portal][portal].

1. Na stránce aplikace App Service v yberte v levé nabídce **Možnost Vertika navýšit kapacitu (plán služby App Service).**
   
3. Vyberte svou úroveň a pak vyberte **Použít**. Vyberte různé kategorie (například **Výroba)** a také **viz další možnosti** pro zobrazení více úrovní.
   
    ![Přejděte na vertikálně navýšit kapacitu aplikace Azure.][ChooseWHP]

    Po dokončení operace se zobrazí automaticky otevírané okno s zeleným zaškrtnutím úspěchu.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Škálovat související zdroje
Pokud vaše aplikace závisí na jiných službách, jako je Azure SQL Database nebo Azure Storage, můžete škálovat tyto prostředky samostatně. Tyto prostředky nejsou spravovány plánem služby App Service.

1. Na stránce **Přehled** aplikace vyberte odkaz **Skupina prostředků.**
   
    ![Škálování navýšit související prostředky aplikace Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. V části **Souhrn** **na** stránce Skupina prostředků vyberte prostředek, který chcete škálovat. Následující snímek obrazovky ukazuje prostředek databáze SQL.
   
    ![Přechod na stránku skupiny prostředků pro navýšení kapacity aplikace Azure](./media/web-sites-scale/ResourceGroup.png)

    Chcete-li navýšit kapacitu souvisejícího prostředku, přečtěte si dokumentaci pro konkrétní typ prostředku. Chcete-li například vertikálně navýšit kapacitu jedné databáze SQL, [přečtěte si například škálování prostředků jedné databáze v azure sql database](../sql-database/sql-database-single-database-scale.md). Pokud chcete vertikálně navýšit kapacitu azure databáze pro prostředek MySQL, najdete [v tématu škálování prostředků MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Porovnání cenových úrovní

Podrobné informace, jako jsou velikosti virtuálních připojení pro každou cenovou úroveň, najdete v [tématu Podrobnosti o cenách služby App Service](https://azure.microsoft.com/pricing/details/app-service).

Tabulka omezení služeb, kvót a omezení a podporovaných funkcí v každé vrstvě najdete v [tématu Omezení služby Aplikace](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Další zdroje informací

[Ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[Konfigurace úrovně PremiumV2 pro službu App Service](app-service-configure-premium-tier.md)

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
