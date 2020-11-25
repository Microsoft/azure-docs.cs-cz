---
title: Vytvoření webové aplikace v pomocném mechanismu v1
description: Naučte se vytvářet webové aplikace v App Service Environment v1. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d0ac8e0cf187d1a6a329f2c7332c9ec37018d4cf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005227"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Vytvoření webové aplikace ve App Service Environment v1

> [!NOTE]
> Tento článek se týká App Service Environment v1.  Existuje novější verze App Service Environment, kterou je snazší použít a která je spuštěná na výkonnější infrastruktuře. Další informace o nové verzi začíná [úvodem do App Service Environment](intro.md).
> 

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak vytvořit plány Web Apps a App Service v [App Service Environment v1](app-service-app-service-environment-intro.md) (pomocného programu). 

> [!NOTE]
> Pokud se chcete dozvědět, jak vytvořit webovou aplikaci, ale nemusíte ji provádět v App Service Environment, přečtěte si téma [Vytvoření webové aplikace .NET](../quickstart-dotnetcore.md) nebo některého z souvisejících kurzů pro jiné jazyky a rozhraní.
> 
> 

## <a name="prerequisites"></a>Požadavky
V tomto kurzu se předpokládá, že jste vytvořili App Service Environment. Pokud jste to ještě neudělali, přečtěte si téma [vytvoření App Service Environment](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Vytvoření webové aplikace
1. Na webu [Azure Portal](https://portal.azure.com/)klikněte na **vytvořit prostředek > web a mobilní zařízení > webovou aplikaci**. 
   
    ![Snímek obrazovky, který ukazuje, kde vytvořit webovou aplikaci v Azure Portal.][1]
2. Vyberte své předplatné.  
   
    Pokud máte více předplatných, abyste si mohli ve svém App Service Environment vytvořit aplikaci, musíte použít stejné předplatné, které jste použili při vytváření prostředí. 
3. Vyberte nebo vytvořte skupinu prostředků.
   
    *Skupiny prostředků* umožňují spravovat související prostředky Azure jako jednotku a jsou užitečné při vytváření pravidel *řízení přístupu na základě role Azure (Azure RBAC)* pro vaše aplikace. Další informace najdete v článku [Přehled Azure Resource Manageru][ResourceGroups]. 
4. Vyberte nebo vytvořte plán služby App Service.
   
    *Plány App Service* jsou spravované sady webových aplikací.  Po výběru ceny se za normálních podmínek místo individuálních aplikací použije cena za App Service plán. V rámci služby řízení přihlašování platíte za výpočetní instance přidělené k pomocnému programu místo toho, co jste vystavili pomocí ASP.  Pro horizontální navýšení kapacity webové aplikace můžete škálovat instance App Serviceho plánu a ovlivňují všechny webové aplikace v tomto plánu.  Některé funkce, jako jsou například sloty webu nebo Integrace virtuální sítě, mají také omezení množství v rámci plánu.  Další informace najdete v tématu [Přehled plánů Azure App Service](../overview-hosting-plans.md) .
   
    Plány App Service v rámci služby přihlašování můžete identifikovat v umístění, které je uvedeno v názvu plánu.  
   
    ![Snímek obrazovky, který ukazuje, jak zobrazit plány App Service ve vašem pomocném mechanismu.][5]
   
    Pokud chcete použít plán App Service, který již v App Service Environment existuje, vyberte tento plán. Pokud chcete vytvořit nový plán App Service, přečtěte si následující část tohoto kurzu a [vytvořte App Service plán v App Service Environment](#createplan).
5. Zadejte název vaší webové aplikace a pak klikněte na **vytvořit**. 
   
    Pokud vaše pomocné služby používá externí VIP, adresa URL aplikace v pomocném mechanismu je: [*název_webu*]. [*název App Service Environment*]. p.azurewebsites.NET místo [*název_webu*]. azurewebsites.NET
   
    Pokud vaše pomocné služby používá interní virtuální IP adresu, adresa URL aplikace v tomto pomocném programu je: [*název_webu*]. [*subdoména zadaná během vytváření POmocného mechanismu* řízení]   
    Po výběru stránky ASP během vytváření pomocného mechanismu se zobrazí aktualizace subdomény pod **názvem** .

## <a name="create-an-app-service-plan"></a><a name="createplan"></a> Vytvoření plánu App Service
Při vytváření plánu App Service v App Service Environment se volby pracovních procesů liší, protože v pomocném mechanismu pro řízení neexistují žádné sdílené pracovní procesy.  Pracovní procesy, které se mají použít, jsou ty, které správce přidělil správci.  To znamená, že pokud chcete vytvořit nový plán, musíte mít více pracovníků přidělených vašemu fondu pracovních procesů pomocného programu, než celkový počet instancí ve všech vašich plánech, které už jsou v daném fondu pracovních procesů.  Pokud ve fondu pracovních procesů pomocného mechanismu přihlašování nemáte dostatečná pracovní procesy k vytvoření vašeho plánu, budete je muset přičíst pomocí Správce pomocného programu.

Dalším rozdílem v App Servicech plánech hostovaných App Service Environment je nedostatek cenového výběru.  Když máte App Service Environment platíte za výpočetní prostředky používané systémem a nepřidali jste poplatky za plány v tomto prostředí.  Normálně při vytváření plánu App Service vyberete Cenový tarif, který určuje vaši fakturaci.  App Service Environment je v podstatě soukromým umístěním, kde můžete vytvářet obsah.  Platíte za prostředí a nebudete hostovat svůj obsah.

Následující pokyny ukazují, jak vytvořit plán App Service, když vytváříte webovou aplikaci, jak je vysvětleno v předchozí části tohoto kurzu.

1. V uživatelském rozhraní pro výběr plánu klikněte na **vytvořit nový** a zadejte název pro svůj plán stejným způsobem, jako byste normálně nemuseli být mimo správce.
2. V ovládacím prvku pro výběr umístění vyberte pomocné okno, které chcete použít.
   
    Vzhledem k tomu, že App Service Environment je v podstatě soukromým umístěním nasazení, zobrazuje se v části umístění. 
   
    ![Snímek obrazovky, který zobrazuje výběr umístění pro výběr požadovaného pomocného panelu.][2]
   
    Po výběru pomocného mechanismu pro výběr umístění se aktualizují uživatelské rozhraní pro vytváření App Serviceho plánu.  Umístění nyní zobrazuje název systému pomocného programu a oblast, ve které se nachází, a výběr cenového plánu je nahrazen nástrojem pro výběr fondu pracovních procesů.  
   
    ![Snímek obrazovky, který po výběru pomocného mechanismu pro výběr umístění zobrazuje podrobnosti o systému pomocného mechanismu řízení.][3]

### <a name="selecting-a-worker-pool"></a>Výběr fondu pracovních procesů
Normálně v Azure App Service a mimo App Service Environment existují 3 výpočetní velikosti, které jsou k dispozici s výběrem vyhrazeného cenového plánu.  Podobně můžete u pomocného mechanismu služeb definovat až 3 fondy pracovníků a zadat výpočetní velikost, která se používá pro tento fond pracovních procesů.  To znamená, že pro klienty pomocného mechanismu služeb je místo výběru cenového plánu s výpočetní velikostí pro plán App Service vybrat, co se nazývá *fond pracovních procesů*.  

Uživatelské rozhraní pro výběr fondu pracovních procesů zobrazuje výpočetní velikost, která se používá pro tento fond pracovních procesů pod názvem.  Dostupné množství znamená, kolik výpočetních instancí je k dispozici pro použití v daném fondu.  Celkový fond může mít ve skutečnosti víc instancí, než je toto číslo, ale tato hodnota odkazuje na to, kolik se nepoužívá.  Pokud potřebujete upravit App Service Environment pro přidání dalších výpočetních prostředků, přečtěte si téma [konfigurace App Service Environment](app-service-web-configure-an-app-service-environment.md).

![Snímek obrazovky, který zobrazuje podokno fond pracovních procesů, kde můžete vybrat fondy pracovních procesů pro pomocné okno.][4]

V tomto příkladu vidíte, že jsou k dispozici pouze dva fondy pracovních procesů. Důvodem je, že správce pomocného mechanismu přiděluje pouze hostitele do těchto dvou fondů pracovních procesů.  Třetí se zobrazí, když jsou do něj přidělené virtuální počítače.  

## <a name="after-web-app-creation"></a>Po vytvoření webové aplikace
Pro spouštění webových aplikací a správu plánů App Service v pomocném mechanismu služby je potřeba vzít v úvahu několik důležitých informací.  

Jak bylo uvedeno dříve, vlastník pomocného mechanismu řízení zodpovídá za velikost systému a v důsledku toho je také zodpovědný za to, že existuje dostatečná kapacita pro hostování požadovaných App Servicech plánů. Pokud nejsou k dispozici žádní pracovní procesy, nebudete moci vytvořit plán App Service.  To platí také pro škálování webové aplikace.  Pokud potřebujete víc instancí, budete muset App Service Environment správce, abyste mohli přidat další pracovní procesy.

Po vytvoření webové aplikace a plánu App Service je vhodné ji škálovat.  V pomocném mechanismu služby budete vždycky muset mít aspoň 2 instance App Serviceho plánu, aby se zajistila odolnost vašich aplikací.  Škálování plánu App Service v pomocném mechanismu služby je stejné jako normální prostřednictvím uživatelského rozhraní App Service plánu.  Další informace o škálování, [Jak škálovat webovou aplikaci v App Service Environment](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: /powershell/azure/?view=azps-3.8.0