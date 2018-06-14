---
title: Spravovat plán služby App Service v Azure | Microsoft Docs
description: Zjistěte, jak při provádění různých úloh pro správu plán služby App Service.
keywords: služby App service, služby azure app service, škálování, plán služby app service, změnit, vytvořit, spravovat, správy
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 1dfe8a903e19ff524a1c4a0228e6aefcbe9ff183
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29117676"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Spravovat plán služby App Service v Azure

[Plán služby Azure App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) poskytuje prostředky, které aplikace služby App Service je potřeba spustit. Tato příručka ukazuje, jak spravovat plán služby App Service.

## <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

> [!TIP]
> Pokud máte služby App Service Environment, najdete v části [vytvořit plán služby App Service ve službě App Service Environment](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Můžete vytvořit plán služby App Service prázdný, nebo můžete vytvořit plán při vytváření aplikace.

1. V [portál Azure](https://portal.azure.com), vyberte **nový** > **Web + mobilní**a potom vyberte **webové aplikace** nebo jiný druh aplikaci služby App Service.

2. Vyberte existující plán aplikační služby nebo vytvořte plán pro novou aplikaci.

   ![Vytvoření aplikace v portálu Azure.][createWebApp]

   Vytvoření plánu:

   a. Vyberte **[+] vytvořit nový**.

      ![Vytvořte plán služby App Service.][createASP] 

   b. Pro **plán služby App Service**, zadejte název plánu.

   c. Pro **umístění**, vyberte požadované místo.

   d. Pro **cenová úroveň**, vyberte příslušnou cenovou úroveň pro službu. Vyberte **zobrazit všechny** do zobrazení více ceny možnosti, jako například **volné** a **sdílené**. Až vyberete cenovou úroveň, klikněte na **vyberte** tlačítko.

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Přesuňte aplikace na jiný plán aplikační služby

Aplikace můžete přesunout na jiný plán aplikační služby, pokud jsou plán zdroj a cíl plán v _stejnou skupinu prostředků a geografické oblasti_.

1. V [portál Azure](https://portal.azure.com), přejděte do aplikace, který chcete přesunout.

2. V nabídce, vyhledejte **plán služby App Service** části.

3. Vyberte **plán služby App Service změnu** otevřete **plán služby App Service** selektor.

   ![Selektor plán služby App Service.][change] 

4. V **plán služby App Service** selektor, vyberte existující v úmyslu přesunout do této aplikace.   

> [!IMPORTANT]
> **Plán aplikační služby vyberte** stránky se filtrují podle následujících kritérií: 
> - Existuje ve stejné skupině prostředků 
> - Existuje ve stejné zeměpisné oblasti 
> - Existuje ve stejné webový prostor  
> 
> A _webový prostor_ je logická konstrukce ve službě App Service, která definuje seskupení prostředků serveru. Geografické oblasti (například západní USA) obsahuje mnoho webspaces, aby bylo možné přidělit zákazníkům, kteří používají služby App Service. V současné době nelze přesunout prostředky služby App Service mezi webspaces. 
> 

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Každý plán má svou vlastní cenová úroveň. Například přesun do lokality ze **volné** vrstvy na **standardní** vrstvy umožňuje všechny aplikace, které jsou přiřazené použití funkcí a prostředky **standardní** vrstvy. Přesunutí aplikace z plánu vyšší vrstvené na plán vrstvené nižší však znamená, už nebude mít přístup k určité funkce. Pokud vaše aplikace používá funkce, která není k dispozici v cílové plánu, dojde k chybě, která zobrazuje funkce, které se používá, který není k dispozici. 

Pokud některé z aplikací používá certifikáty protokolu SSL, například může zobrazit tato chybová zpráva:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

V takovém případě před aplikace můžete přesunout do cílového plán, musíte buď:
- Škálování cenové úrovně plánu cíl na **základní** nebo vyšší.
- Odebírání všech připojení SSL k vaší aplikace.

## <a name="move-an-app-to-a-different-region"></a>Přesunutí aplikace v jiné oblasti

Oblast, ve kterém běží vaše aplikace je oblasti, které je v plánu služby App Service. Nelze však změnit plán služby App Service oblast. Pokud chcete ke spouštění vaší aplikace v jiné oblasti, jeden alternativou je aplikace klonování. Klonování zhotoví kopii aplikaci v nové nebo existující plán služby App Service v libovolné oblasti.

Můžete najít **klonování aplikace** v **nástroje pro vývoj** části nabídky.

> [!IMPORTANT]
> Klonování má určitá omezení. Další informace o nich [klonování aplikace Azure App Service](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Škálovat plán služby App Service

Škálování App Service je plán cenová úroveň najdete v tématu [škálování aplikace v Azure](web-sites-scale.md).

Chcete-li škálovat počet instancí aplikace, přečtěte si téma [ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Odstranit plán služby App Service

Abyste předešli neočekávané poplatky, když odstraníte poslední aplikace v rámci plánu služby App Service, služby App Service také odstraní plán ve výchozím nastavení. Pokud si zvolíte, aby plán, měli byste změnit plán **volné** vrstvy, aby se vám neúčtují poplatky.

> [!IMPORTANT]
> Plány služby App Service, které mají žádné aplikace, které s nimi spojených stále platit poplatky, protože budou i nadále rezervovat nakonfigurované instance virtuálních počítačů.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Škálování aplikace v Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
