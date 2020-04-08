---
title: Správa plánu služby App Service
description: Přečtěte si, jak provádět různé úkoly při správě plánu služby App Service, jako je vytváření, přesouvání, škálování a odstraňování.
keywords: app service, azure app service, škálování, plán služby app service, změna, vytváření, správa, správa
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d40f5db65ce9ca90ae978bac4491bdebccc2a328
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811711"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Správa plánu služby App Service v Azure

Plán [služby Azure App Service](overview-hosting-plans.md) poskytuje prostředky, které aplikace služby App Service potřebuje ke spuštění. Tato příručka ukazuje, jak spravovat plán služby App Service.

## <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

> [!TIP]
> Pokud máte prostředí služby App Service, přečtěte si informace [o vytvoření plánu služby App Service v prostředí služby App Service](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Můžete vytvořit prázdný plán služby App Service nebo můžete vytvořit plán jako součást vytváření aplikací.

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Vytvořit prostředek**.

   ![Vytvořte prostředek na webu Azure Portal.][createResource] 

1. Vyberte **Nová** > **webová aplikace** nebo jiný druh aplikace Služby App Service.

   ![Vytvořte aplikaci na webu Azure Portal.][createWebApp] 

2. Před konfigurací plánu služby App Service nakonfigurujte část **Podrobnosti instance.** Nastavení, jako je **publikování** a **operační systémy** můžete změnit dostupné cenové úrovně pro váš plán služby App Service. **Oblast** určuje, kde se vytvoří váš plán služby App Service. 
   
3. V části **Plán servisu aplikace** vyberte existující plán nebo vytvořte plán výběrem **možnosti Vytvořit nový**.

   ![Vytvořte plán služby App Service.][createASP] 

4. Při vytváření plánu můžete vybrat cenovou úroveň nového plánu. Ve **sku a velikosti**vyberte **Změnit velikost,** chcete-li změnit cenovou úroveň. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Přesunutí aplikace do jiného plánu služby App Service

Aplikaci můžete přesunout do jiného plánu služby App Service, pokud zdrojový plán a cílový plán jsou ve _stejné skupině prostředků a zeměpisné oblasti_.

> [!NOTE]
> Azure nasazuje každý nový plán služby App Service do jednotky nasazení, interně nazývané webový prostor. Každá oblast může mít mnoho webových prostorů, ale vaše aplikace se může pohybovat pouze mezi plány, které jsou vytvořeny ve stejném webovém prostoru. Prostředí služby App Service je izolovaný webový prostor, takže aplikace lze přesouvat mezi plány ve stejném prostředí služby App Service, ale ne mezi plány v různých prostředích služby App Service.
>
> Při vytváření plánu nelze určit požadovaný webový prostor, ale je možné zajistit, aby byl plán vytvořen ve stejném webovém prostoru jako existující plán. Stručně řečeno, všechny plány vytvořené se stejnou kombinací skupiny prostředků a oblastí jsou nasazeny do stejného webového prostoru. Pokud jste například vytvořili plán ve skupině prostředků A a oblasti B, bude do stejného webového prostoru nasazen jakýkoli plán, který následně vytvoříte ve skupině prostředků A a oblasti B. Všimněte si, že plány nelze přesunout webové prostory po jejich vytvoření, takže nelze přesunout plán do "stejného webového prostoru" jako jiný plán přesunutím do jiné skupiny prostředků.
> 

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte **služby aplikací** a vyberte aplikaci, kterou chcete přesunout.

2. V levé nabídce vyberte **Změnit plán služby App Service**.

3. V rozevíracím plánu **Služby App Service** vyberte existující plán, do který chcete aplikaci přesunout. Rozevírací seznam zobrazuje pouze plány, které jsou ve stejné skupině prostředků a zeměpisné oblasti jako aktuální plán služby App Service. Pokud žádný takový plán neexistuje, umožňuje vytvořit plán ve výchozím nastavení. Nový plán můžete vytvořit také ručně výběrem **možnosti Vytvořit nový**.

4. Pokud vytvoříte plán, můžete vybrat cenovou úroveň nového plánu. V **cenové úrovni**vyberte existující úroveň, kterou chcete změnit. 
   
   > [!IMPORTANT]
   > Pokud přesouváte aplikaci z plánu s vyšší vrstvou na plán s nižšívrstvou, například z **D1** na **F1**, může aplikace ztratit určité možnosti v cílovém plánu. Pokud například vaše aplikace používá certifikáty TLS/SSL, může se zobrazit tato chybová zpráva:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. Po dokončení vyberte **OK**.
   
   ![Výběr plánu služby App Service.][change] 

## <a name="move-an-app-to-a-different-region"></a>Přesunutí aplikace do jiné oblasti

Oblast, ve které vaše aplikace běží, je oblast plánu služby App Service, ve kterém se nachází. Oblast plánu služby App Service však nelze změnit. Pokud chcete aplikaci spustit v jiné oblasti, jednou z alternativ je klonování aplikací. Klonování vytvoří kopii aplikace v novém nebo existujícím plánu služby App Service v libovolné oblasti.

**Aplikaci Clone** najdete v části **Vývojové nástroje** v nabídce.

> [!IMPORTANT]
> Klonování má určitá omezení. Můžete si o nich přečíst v [klonování aplikací služby Azure App](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Škálování plánu služby App Service

Pokud chcete navýšit úroveň oceňování plánu služby App Service, [přečtěte si tématu Vertika navýšit kapacitu aplikace v Azure](manage-scale-up.md).

Chcete-li škálovat počet instancí aplikace, přečtěte si témat [ruční nebo automatické měřítko počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Odstranění plánu služby App Service

Chcete-li se vyhnout neočekávaným poplatkům, když odstraníte poslední aplikaci v plánu služby App Service, služba App Service také odstraní plán ve výchozím nastavení. Pokud se místo toho rozhodnete plán zachovat, měli byste plán změnit na **úroveň Free,** abyste vám neúčtovali poplatky.

> [!IMPORTANT]
> Plány služby App Service, které nemají žádné aplikace s nimi spojené, stále účtují poplatky, protože si nadále rezervují nakonfigurované instance virtuálních počítače.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vertikální navýšení kapacity aplikace v Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png
