---
title: Vytvoření webové aplikace v App Service Environment v1
description: Zjistěte, jak vytvořit webové aplikace a aplikace plány služeb v App Service Environment v1
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
ms.openlocfilehash: 78a1f3bb1d49a8a2e8a44d665abd211c32711d7c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961411"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Vytvoření webové aplikace v App Service Environment v1

> [!NOTE]
> Tento článek je o App Service Environment v1.  Existuje novější verze služby App Service Environment, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
> 

## <a name="overview"></a>Přehled
Tento kurz ukazuje postupy při vytváření webových aplikací a plánů služby App Service v [služby App Service Environment v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Pokud chcete se dozvědět, jak vytvořit webovou aplikaci, ale není potřeba provádět ve službě App Service Environment, naleznete v tématu [vytvoření webové aplikace .NET](../app-service-web-get-started-dotnet.md) nebo související kurzech pro ostatní jazyky a architektury.
> 
> 

## <a name="prerequisites"></a>Požadavky
Tento kurz předpokládá, že jste vytvořili službu App Service Environment. Pokud jste tak ještě neučinili, přečtěte si téma [vytvoření služby App Service Environment](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Vytvoření webové aplikace
1. V [webu Azure Portal](https://portal.azure.com/), klikněte na tlačítko **vytvořit prostředek > Web + mobilní zařízení > Webová aplikace**. 
   
    ![][1]
2. Vyberte své předplatné.  
   
    Pokud máte více předplatných, mějte na paměti, že pokud chcete vytvořit aplikaci v App Service Environment, budete muset použít stejné předplatné, které jste použili při vytváření prostředí. 
3. Vyberte nebo vytvořte skupinu prostředků.
   
    *Skupiny prostředků* vám umožní spravovat související prostředky Azure jako celku a jsou užitečné při navazování *řízení přístupu na základě rolí* pravidla (RBAC) pro vaše aplikace. Další informace najdete v tématu [přehled Azure Resource Manageru][ResourceGroups]. 
4. Vyberte nebo vytvořte plán služby App Service.
   
    *Plány služby App Service* jsou spravované sady webových aplikací.  Obvykle při výběru ceny účtované ceny se použije k plánu služby App Service, nikoli pro jednotlivé aplikace. Ve službě ASE platíte za výpočetní instance přidělené služby ASE místo mít uvedené s vaší ASP.  Vertikální navýšení kapacity počtu instancí webové aplikace, které můžete vertikálně navýšit kapacitu instance vaší služby App Service a plán ovlivní všechny webové aplikace v tomto plánu.  Některé funkce, jako je například sloty webu nebo integrace virtuální sítě také mít omezení množství v rámci plánu.  Další informace najdete v tématu [přehled plánů služby Azure App Service](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
   
    Plány služby App Service můžete identifikovat ve vaší službě ASE pohledem na umístění, které je uvedeno v části název plánu.  
   
    ![][5]
   
    Pokud chcete použít plán služby App Service, který již existuje ve službě App Service Environment, vyberte tento plán. Pokud chcete vytvořit nový plán služby App Service, najdete v následující části tohoto kurzu, [vytvořte plán služby App Service ve službě App Service Environment](#createplan).
5. Zadejte název pro vaši webovou aplikaci a pak klikněte na tlačítko **vytvořit**. 
   
    Pokud vaše App Service Environment používá externí VIP adresa URL aplikace v App Service Environment je: [*sitename*]. [ *název služby App Service Environment*]. p.azurewebsites.net místo [*sitename*]. azurewebsites.net
   
    Pokud vaše App Service Environment používá interní VIP adresu URL aplikace, jsou App Service Environment: [*sitename*]. [ *subdomény zadané během vytváření App Service Environment*]   
    Po výběru vaší ASP při vytvoření služby ASE se zobrazí subdomény, aktualizujte níže **název**

## <a name="createplan"></a> Vytvoření plánu služby App Service
Když vytvoříte plán služby App Service ve službě App Service Environment, vaše volby pracovního procesu se liší, protože neexistují žádné sdílené pracovní procesy ve službě ASE.  Pracovní procesy, které je nutné použít jsou ty, které byly přiděleny služby ASE správce.  To znamená, že pokud chcete vytvořit nový plán, musíte mít další pracovních procesů přidělených pro váš fond pracovních procesů služby ASE než celkový počet instancí ve všech vašich plánů už v tomto fondu pracovních procesů.  Pokud nemáte dostatek pracovních procesů ve fondu pracovních procesů vaší služby ASE k vytvoření vašeho plánu, musíte pracovat s vaším správcem služby ASE je přidána.

Další rozdíl s plány služby App Service, které jsou hostované ve službě App Service Environment je chybějící výběr cenové.  Pokud máte službu App Service Environment se platit za výpočetní prostředky použité v systému a nemají přidání poplatky pro plány v tomto prostředí.  Obvykle při vytváření plánu služby App Service je vybrat cenový plán, který určuje fakturace.  Služby App Service Environment je v podstatě privátní umístění, kde můžete vytvářet obsah.  Platíte jenom pro prostředí a není pro hostování vašeho obsahu.

Následující pokyny ukazují, jak vytvořit plán služby App Service při vytváření webové aplikace, jak je popsáno v předchozí části tohoto kurzu.

1. Klikněte na tlačítko **vytvořit nový** v naplánování výběru uživatelského rozhraní a zadejte název plánu, stejně jako obvykle mimo prostředí ASE.
2. Vyberte službu ASE, kterou chcete použít z vašeho výběru umístění.
   
    Protože je v podstatě umístění privátní nasazení služby App Service Environment se zobrazí v části umístění. 
   
    ![][2]
   
    Po výběru prostředí ase. při výběru umístění vytvoření plánu služby App Service aktualizací uživatelského rozhraní.  Umístění nyní zobrazuje název systémem ASE a oblasti je v a nástroje pro výběr cenový plán se nahradí ovládacího prvku pro výběr fondu pracovních procesů.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Vyberte fond pracovních procesů
Normálně ve službě Azure App Service a mimo službu App Service Environment, existují 3 velikosti výpočetní prostředky, které jsou k dispozici s výběrem vyhrazené cenový plán.  Podobným způsobem službu ase můžete definovat až 3 fondy pracovních procesů a určete velikost výpočetních, který se používá pro tento fond pracovních procesů.  To znamená pro tenanty služby ase se místo výběru cenového plánu s velikostí výpočetní prostředky pro váš plán služby App Service, je vybrat, co se volá *fond pracovních procesů*.  

Výběr fondu pracovních procesů uživatelského rozhraní zobrazuje velikost výpočetní prostředky použité pro tento fond pracovních procesů pod názvem.  K dispozici množství odkazuje na tom, kolik výpočetní instance jsou k dispozici pro použití v tomto fondu.  Celkový počet fondu může mít více instancí než tento počet, ale tato hodnota odkazuje na jednoduše kolik nejsou používány.  Pokud je potřeba upravit, přečtěte si služby App Service Environment, chcete-li přidat další výpočetní prostředky [konfigurace služby App Service Environment](app-service-web-configure-an-app-service-environment.md).

![][4]

V tomto příkladu najdete v článku k dispozici pouze dva fondy pracovních procesů. Důvodem je, Správce služby ASE jen přidělených hostitele do těchto dvou pracovních procesů fondů.  Třetí se zobrazují, když nejsou virtuální počítače přidělené do něj.  

## <a name="after-web-app-creation"></a>Po vytvoření webové aplikace
Existuje několik důležitých informací pro spuštění webových aplikací a správě plánů služby App Service ve službě ASE, které je nutné vzít v úvahu.  

Jak bylo uvedeno dříve, vlastník služby ASE zodpovídá za velikost systému a díky tomu jsou také za zajištění, že je dostatečnou kapacitu k hostování požadované plány služby App Service. Pokud neexistují žádné dostupné pracovní procesy, nebudete moct vytvořit plán služby App Service.  Toto je také true pro škálování webové aplikace.  Pokud potřebujete více instancí je třeba získat správce služby App Service Environment, aby přidal další pracovní procesy.

Po vytvoření webové aplikace a plán služby App Service je vhodné ji vertikálně navýšit kapacitu.  Ve službě ASE vždy musíte mít aspoň 2 instancí plánu služby App Service poskytují odolnost proti chybám pro vaše aplikace.  Škálování plánu služby App Service ve službě ASE je stejná jako za normálních okolností prostřednictvím uživatelského rozhraní plán služby App Service.  Další informace o škálování [jak škálovat webovou aplikaci ve službě App Service Environment](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

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
[AzurePowershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
