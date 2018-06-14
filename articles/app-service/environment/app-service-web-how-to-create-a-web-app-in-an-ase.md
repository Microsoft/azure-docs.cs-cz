---
title: Vytvořit webovou aplikaci App Service Environment v1
description: Naučte se vytvářet webové aplikace a aplikace plány služeb v v1 App Service Environment
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/11/2017
ms.author: ccompy
ms.openlocfilehash: 1e8540409c6174ad02bd2d9d57c53e0279f49871
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386907"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Vytvořit webovou aplikaci App Service Environment v1

> [!NOTE]
> Tento článek je o v1 App Service Environment.  Existuje novější verze App Service Environment, který je jednodušší použít a běží na výkonnější infrastruktury. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
> 

## <a name="overview"></a>Přehled
Tento kurz ukazuje, jak vytvářet webové aplikace a plánů služby App Service v [App Service Environment v1](app-service-app-service-environment-intro.md) (App Service Environment). 

> [!NOTE]
> Pokud chcete zjistěte, jak vytvořit webovou aplikaci, ale není nutné provést ve službě App Service Environment, najdete v článku [vytvoření webové aplikace .NET](../app-service-web-get-started-dotnet.md) nebo jeden z související kurzy pro jiných jazyků a rozhraní.
> 
> 

## <a name="prerequisites"></a>Požadavky
Tento kurz předpokládá, že jste vytvořili služby App Service Environment. Pokud jste to neudělali, ještě, přečtěte si téma [vytvoření služby App Service Environment](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Vytvoření webové aplikace
1. V [portálu Azure](https://portal.azure.com/), klikněte na tlačítko **vytvořit prostředek > Web + mobilní > Webová aplikace**. 
   
    ![][1]
2. Vyberte své předplatné.  
   
    Pokud máte více předplatných, uvědomte si, že pokud chcete vytvořit aplikaci ve službě App Service Environment, budete muset používat stejné předplatné, které jste použili při vytváření prostředí. 
3. Vyberte nebo vytvořte skupinu prostředků.
   
    *Skupiny prostředků* umožňují správu souvisejících prostředků Azure jako jednotku a jsou užitečné při vytváření *řízení přístupu na základě role* (RBAC) pravidla pro vaše aplikace. Další informace najdete v tématu [přehled Azure Resource Manageru][ResourceGroups]. 
4. Vyberte nebo vytvořte plán služby App Service.
   
    *Plánů služby App Service* jsou spravované sady webové aplikace.  Obvykle vyberete cenovou ceny požadované je použita na plán služby App Service a nikoli pro jednotlivé aplikace. V App Service Environment platíte za výpočetní instance přidělené App Service Environment místo mít uvedené s vaší ASP.  Škálování počtu instancí webové aplikace, které můžete škálovat instance služby App Service plán a ovlivní všechny webové aplikace v tomto plánu.  Některé funkce, jako je například sloty lokality nebo integrace virtuální sítě také mít omezení množství v rámci plánu.  Další informace najdete v tématu [přehled plánů služby Azure App Service](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
   
    Plány služby App Service ve vašem App Service Environment poznáte podle umístění, které je uvedeno v části název plánu.  
   
    ![][5]
   
    Pokud chcete použít plán služby App Service, která již existuje ve službě App Service Environment, vyberte jej. Pokud chcete vytvořit nový plán služby App Service, najdete v následující části tohoto kurzu [vytvořit plán služby App Service ve službě App Service Environment](#createplan).
5. Zadejte název pro vaši webovou aplikaci a pak klikněte na tlačítko **vytvořit**. 
   
    Pokud vaše App Service Environment používá externí VIP adresa URL aplikace v App Service Environment je: [*sitename*]. [ *název služby App Service Environment*]. p.azurewebsites.net místo [*sitename*]. azurewebsites.net
   
    Pokud vaše App Service Environment používá interní VIP adresu URL aplikace, jsou App Service Environment: [*sitename*]. [ *subdomény zadané během vytváření App Service Environment*]   
    Po výběru vaší ASP během vytváření App Service Environment uvidíte subdoméně aktualizovat níže **název**

## <a name="createplan"></a> Vytvořit plán služby App Service
Když vytvoříte plán služby App Service ve službě App Service Environment, vaše volby pracovního procesu se liší, protože neexistují žádné sdílené pracovních procesů v App Service Environment.  Pracovní procesy, které budete muset použít jsou ty, které byly přiděleny App Service Environment správce.  To znamená, že pokud chcete vytvořit nový plán, musíte mít další pracovních procesů přidělených pro váš fond pracovních procesů App Service Environment než celkový počet instancí napříč všemi plánu již v tomto fondu pracovních procesů.  Pokud nemáte dostatek pracovních procesů ve fondu pracovních App Service Environment k vytvoření plánu, budete muset spolupráci s vaším správcem App Service Environment získat přidat.

Další rozdíl s plány služby App Service hostované služby App Service Environment je nedostatek ceny výběr.  Pokud máte služby App Service Environment jsou platícího za výpočetní prostředky, které používá systém a nemají přidané poplatky pro plány v tomto prostředí.  Když vytvoříte plán služby App Service je obvykle vybrat cenový plán, určující vaši fakturaci.  Služby App Service Environment je v podstatě do privátní umístění, kde můžete vytvořit obsah.  Platí pro prostředí a není pro hostování vašeho obsahu.

Následující pokyny ukazují, jak vytvořit plán služby App Service při vytváření webové aplikace, jak je popsáno v předchozí části kurzu.

1. Klikněte na tlačítko **vytvořit nový** v uživatelském rozhraní možnost plánu a zadejte název pro váš plán jako za normálních okolností byste mimo App Service Environment.
2. Vyberte App Service Environment, který chcete použít z vašeho výběru umístění.
   
    Protože služby App Service Environment je v podstatě umístění privátní nasazení, zobrazuje se v umístění. 
   
    ![][2]
   
    Po výběru App Service Environment ve výběru umístění vytvoření plánu služby App Service aktualizací uživatelského rozhraní.  Umístění se teď zobrazuje název systému App Service Environment a oblast je v a nástroje pro výběr cenové plán bude nahrazen pomocí pracovního procesu výběru fondu.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Výběr fondu pracovních procesů
Za normálních okolností ve službě Azure App Service a mimo služby App Service Environment, existují 3 výpočetní velikostí, které jsou k dispozici s výběrem plánu vyhrazené ceny.  Podobným způsobem pro App Service Environment můžete definovat až 3 fondy pracovních procesů a zadejte velikost výpočetních, který se používá pro tento fond pracovních procesů.  Co to znamená pro klienty App Service Environment je místo výběru cenový plán s velikostí výpočetní pro plán služby App Service, můžete vybrat, co se nazývá *fondu pracovních procesů*.  

Výběr fondu pracovní uživatelského rozhraní zobrazuje velikost výpočetních používaných pro tento fond pracovních procesů pod názvem.  K dispozici odkazuje na tom, kolik výpočetní instance jsou k dispozici pro použití v tomto fondu.  Celkový počet fondu, může mít více instancí než tento počet, ale tato hodnota se odkazuje jednoduše kolik se nepoužívají.  Pokud potřebujete upravit najdete v části App Service Environment přidat další výpočetní prostředky [konfigurace služby App Service Environment](app-service-web-configure-an-app-service-environment.md).

![][4]

V tomto příkladu najdete v části k dispozici pouze dva fondy pracovních procesů. Je to způsobeno správce App Service Environment pouze přidělené hostitele do těchto fondů dvě pracovního procesu.  Třetí by objeví, když nejsou virtuální počítače přidělené do ní.  

## <a name="after-web-app-creation"></a>Po vytvoření webové aplikace
Existuje několik důležité informace týkající se spouštění webových aplikací a správu plány služby App Service v App Service Environment, které je nutné vzít v úvahu.  

Jak již bylo uvedeno dříve, vlastník App Service Environment je zodpovědný za velikost systému a proto jsou také zajistí, že je dostatečnou kapacitu k hostování požadované plány služby App Service. Pokud neexistují žádné pracovníky k dispozici, nebudete moct vytvořit plán služby App Service.  Toto je také hodnotu PRAVDA, aby vertikálním navýšení kapacity vaší webové aplikace.  Pokud potřebujete další instance je třeba získat správce služby App Service Environment přidat další pracovní procesy.

Po vytvoření webové aplikace a plán služby App Service je vhodné vertikálně navýšit kapacitu.  V App Service Environment vždy musíte mít minimálně 2 instance vašeho plánu služby App Service zajistit odolnost proti chybám pro vaše aplikace.  Změna velikosti plán aplikační služby App Service Environment je stejný jako normální prostřednictvím uživatelského rozhraní plán služby App Service.  Další informace o škálování [postup škálování webové aplikace ve službě App Service Environment](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/resource-group-overview.md
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
