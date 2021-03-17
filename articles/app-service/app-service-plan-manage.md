---
title: Správa plánu App Service
description: Naučte se, jak provádět různé úlohy pro správu plánu App Service, jako je vytváření, přesouvání, škálování a odstraňování.
keywords: App Service, Azure App Service, škálování, plán služby App Service, změna, vytváření, Správa, Správa
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: 58ddf60a842a9583eae25a875f55227cee772a8a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582529"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Správa plánu App Service v Azure

[Plán Azure App Service](overview-hosting-plans.md) poskytuje prostředky, které aplikace App Service potřebuje ke spuštění. V této příručce se dozvíte, jak spravovat plán App Service.

## <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

> [!TIP]
> Pokud máte App Service Environment, přečtěte si téma [Vytvoření plánu App Service v App Service Environment](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Můžete vytvořit prázdný plán App Service, nebo můžete vytvořit plán jako součást vytváření aplikací.

1. V [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**.

   ![Vytvořte prostředek v Azure Portal.][createResource] 

1. Vyberte možnost **Nová**  >  **Webová aplikace** nebo jiný druh aplikace služby App Service.

   ![Vytvořte aplikaci v Azure Portal.][createWebApp] 

2. Před konfigurací plánu App Service nakonfigurujte oddíl **podrobností instance** . Nastavení, jako je **publikování** a **operační systém** , může změnit dostupné cenové úrovně plánu App Service. **Oblast** určuje, kde se vytvoří plán App Service. 
   
3. V části **plán App Service** vyberte existující plán nebo vytvořte plán výběrem možnosti **vytvořit nový**.

   ![Vytvořte plán App Service.][createASP] 

4. Při vytváření plánu můžete vybrat cenovou úroveň nového plánu. V části **SKU a velikost** vyberte **změnit velikost** , aby se změnila cenová úroveň. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Přesunutí aplikace do jiného plánu App Service

Aplikaci můžete přesunout do jiného plánu App Service, pokud se zdrojový a cílový plán nacházejí ve _stejné skupině prostředků a zeměpisné oblasti_.

> [!NOTE]
> Azure nasadí každý nový App Service plán do jednotky nasazení interně označované jako webspace. Každá oblast může mít mnoho webspaces, ale aplikaci lze přesouvat pouze mezi plány, které jsou vytvořeny ve stejném webovém prostoru. App Service Environment je izolovaný webový prostor, takže aplikace se dají přesouvat mezi plány ve stejné App Service Environment, ale ne mezi plány v různých App Service prostředích.
>
> Při vytváření plánu nemůžete zadat webový prostor, ale je možné zajistit, aby byl plán vytvořen ve stejném webovém prostoru jako stávající plán. V krátkém případě jsou všechny plány vytvořené se stejnou kombinací skupiny prostředků a oblastí nasazeny do stejného webspace. Pokud jste například vytvořili plán ve skupině prostředků a a v oblasti B, pak bude libovolný plán, který následně vytvoříte ve skupině prostředků a a oblast B, nasazen do stejného webspace. Všimněte si, že plány nemůžou po vytvoření přesunout webspaces, takže nemůžete přesunout plán do stejného webspaceu jako jiný plán, a to tak, že ho přesunete do jiné skupiny prostředků.
> 

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **App Services** a vyberte aplikaci, kterou chcete přesunout.

2. V nabídce vlevo vyberte **změnit App Service plán**.

3. V rozevíracím seznamu **plán App Service** vyberte existující plán, do kterého chcete aplikaci přesunout. Rozevírací seznam zobrazuje jenom plány, které jsou ve stejné skupině prostředků a zeměpisné oblasti jako aktuální plán App Service. Pokud žádný takový plán neexistuje, můžete ve výchozím nastavení vytvořit plán. Nový plán můžete také vytvořit ručně výběrem možnosti **vytvořit nový**.

4. Pokud vytvoříte plán, můžete vybrat cenovou úroveň nového plánu. V části **cenová úroveň** vyberte existující úroveň, kterou chcete změnit. 
   
   > [!IMPORTANT]
   > Pokud přesouváte aplikaci z plánu s vyšší vrstvou na plán nižší úrovně, například z **D1** na **F1**, aplikace může přijít o určité možnosti v cílovém plánu. Pokud vaše aplikace například používá certifikáty TLS/SSL, může se zobrazit tato chybová zpráva:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. Po dokončení vyberte **OK**.
   
   ![Selektor App Serviceho plánu][change] 

## <a name="move-an-app-to-a-different-region"></a>Přesunutí aplikace do jiné oblasti

Oblast, ve které vaše aplikace běží, je oblast plánu App Service. Nemůžete však změnit oblast plánu App Service. Pokud chcete aplikaci spustit v jiné oblasti, bude jedna alternativa klonování aplikací. Klonování vytvoří kopii vaší aplikace v novém nebo existujícím plánu App Service v jakékoli oblasti.

**Klonování aplikace** můžete najít v části **vývojové nástroje** v nabídce.

> [!IMPORTANT]
> Klonování má určitá omezení. Můžete si je přečíst v tématu [Azure App Service klonování aplikací](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Škálování plánu App Service

Postup horizontálního navýšení kapacity plánu App Service najdete v tématu věnovaném [horizontálnímu navýšení kapacity aplikace v Azure](manage-scale-up.md).

Pokud chcete škálovat počet instancí aplikace, přečtěte si téma [Ruční nebo automatické škálování počtu instancí](../azure-monitor/autoscale/autoscale-get-started.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Odstranit plán App Service

Aby nedošlo k neočekávaným poplatkům, při odstranění poslední aplikace v plánu App Service App Service také ve výchozím nastavení odstraní plán. Pokud se rozhodnete plán zachovat, změňte plán na úroveň **Free** , abyste se vám neúčtovali.

> [!IMPORTANT]
> App Service plánů, ke kterým nejsou přidružené žádné aplikace, se budou účtovat poplatky, protože nadále vyhradí nakonfigurované instance virtuálních počítačů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vertikální navýšení kapacity aplikace v Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png