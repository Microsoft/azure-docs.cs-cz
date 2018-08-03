---
title: Správa plánu služby App Service v Azure | Dokumentace Microsoftu
description: Zjistěte, jak k provádění různých úloh ke správě plánu služby App Service.
keywords: služby App service, služby azure app service, škálování, plán služby app service, změnit, vytvoření, správa, Správa
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
ms.openlocfilehash: 2c08522df598bd5c6313c3f026efe48e1c4a2c56
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449355"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Správa plánu služby App Service v Azure

[Plán služby App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) poskytuje prostředky, které je potřeba spustit aplikaci služby App Service. Tato příručka ukazuje, jak spravovat plán služby App Service.

## <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

> [!TIP]
> Pokud máte službu App Service Environment, přečtěte si [vytvořte plán služby App Service ve službě App Service Environment](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Můžete vytvořit prázdnou plán služby App Service, nebo jako součást vytváření aplikací můžete vytvořit plán.

1. V [webu Azure portal](https://portal.azure.com)vyberte **nový** > **Web + mobilní zařízení**a pak vyberte **webovou aplikaci** nebo jiný typ aplikace služby App Service.

1. Vyberte existující plán služby App Service nebo vytvořit plán pro novou aplikaci.

   ![Vytvoření aplikace na webu Azure Portal.][createWebApp]

   Vytvoření plánu:

   a. Vyberte **[+] vytváření nových**.

      ![Vytvoření plánu služby App Service.][createASP] 

   b. Pro **plán služby App Service**, zadejte název plánu.

   c. Pro **umístění**, vyberte odpovídající umístění.

   d. Pro **cenová úroveň**, vyberte odpovídající cenové úrovně služby. Vyberte **zobrazit všechny** do zobrazení další cenové možnosti, jako například **Free** a **Shared**. Po výběru cenové úrovně, klikněte na tlačítko **vyberte** tlačítko.

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Přesun aplikace do jiného plánu služby App Service

Aplikaci můžete přesunout na jiný plán služby App Service, plán zdrojového a cílového plánu jsou v _stejnou skupinu prostředků a zeměpisné oblasti_.

1. V [webu Azure portal](https://portal.azure.com), přejděte do aplikace, kterou chcete přesunout.

1. V nabídce, vyhledejte **plán služby App Service** oddílu.

1. Vyberte **plán služby App Service pro změnu** otevřít **plán služby App Service** selektor.

   ![Selektor plán služby App Service.][change] 

1. V **plán služby App Service** selektor, vyberte existující v úmyslu přesunout do této aplikace.   

> [!IMPORTANT]
> **Plán App Service vyberte** stránka se vyfiltruje podle následujících kritérií: 
> - Ve stejné skupině prostředků existuje 
> - Existuje ve stejné zeměpisné oblasti 
> - Existuje ve stejném webový prostor:  
> 
> A _webový prostor_ je logická konstrukce ve službě App Service, která definuje seskupení prostředků serveru. Zeměpisné oblasti (například USA – západ) obsahuje mnoho webových prostorech, aby bylo možné přidělit zákazníci, kteří používají služby App Service. V současné době nelze přesunout prostředky App Service mezi webové prostory. 
> 

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Každý plán má vlastní cenovou úroveň. Například přesun na web z **Free** vrstvy k **standardní** umožňuje úroveň všechny aplikace přiřazené k němu chcete používat funkce a prostředky **standardní** úroveň. Ale přesun aplikace z plánu vyšší vrstvenou do plánu vrstvené malá znamená, že už máte přístup k některým funkcím. Pokud vaše aplikace používá funkce, která není k dispozici v rámci cílového plánu, dojde k chybě, která zobrazuje funkce, které se používá, který není k dispozici. 

Například pokud některé z aplikací používá certifikáty SSL, může se zobrazit tato chybová zpráva:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

V takovém případě před aplikace lze přesunout do cílového plánu, musíte buď:
- Vertikálně navýšit kapacitu cenovou úroveň plánu cíl, který se má **základní** nebo vyšší.
- Odeberte všechna připojení SSL k vaší aplikaci.

## <a name="move-an-app-to-a-different-region"></a>Přesun aplikace do jiné oblasti

Oblast, ve kterém běží vaše aplikace je plánu služby App Service, který je v oblasti. Nelze však změnit oblast plán služby App Service. Pokud chcete ke spouštění vaší aplikace v jiné oblasti, jeden alternativou je aplikace klonování. Klonování vytvoří kopii aplikace do nové nebo existující plán služby App Service v libovolné oblasti.

Můžete najít **klonování aplikací** v **nástroje pro vývoj** části nabídky.

> [!IMPORTANT]
> Klonování má určitá omezení. Si můžete přečíst o nich v [klonování aplikace služby Azure App Service](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Škálování plánu služby App Service

Škálování služby App Service je plán cenová úroveň, přečtěte si téma [vertikální navýšení kapacity aplikace v Azure](web-sites-scale.md).

Horizontální navýšení kapacity počtu instancí aplikace, najdete v článku [ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Odstranit plán služby App Service

Chcete-li se vyhnout neočekávaným poplatkům, když odstraníte poslední aplikace v plánu služby App Service, služby App Service se odstraní také plánu ve výchozím nastavení. Pokud se rozhodnete zajistit plánem, měli byste změnit plán **Free** vrstvy, takže se vám neúčtují poplatky.

> [!IMPORTANT]
> Plány služby App Service, které jste se k nim má přiřazené žádné aplikace pořád nabíhat poplatky, protože jsou nadále rezervovat nakonfigurovaných instancí virtuálních počítačů.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Škálování aplikace v Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
