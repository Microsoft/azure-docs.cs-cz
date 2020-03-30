---
title: Vytvoření webové aplikace ve službě ASE v1
description: Přečtěte si, jak vytvářet webové aplikace v prostředí služby App Service v1. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší verze v1 ASE.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5c947617f0c27708e72f9bff92e2b0041473cd92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266192"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Vytvoření webové aplikace v prostředí služby App Service v1

> [!NOTE]
> Tento článek je o prostředí služby App Service v1.  K dispozici je novější verze prostředí služby App Service, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi začněte [s úvodem do prostředí služby App Service](intro.md).
> 

## <a name="overview"></a>Přehled
Tento kurz ukazuje, jak vytvořit webové aplikace a plány služby App Service v [prostředí služby App Service v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Pokud se chcete dozvědět, jak vytvořit webovou aplikaci, ale nemusíte to dělat v prostředí služby App Service, přečtěte si část [Vytvoření webové aplikace .NET](../app-service-web-get-started-dotnet.md) nebo některého ze souvisejících kurzů pro jiné jazyky a architektury.
> 
> 

## <a name="prerequisites"></a>Požadavky
Tento kurz předpokládá, že jste vytvořili prostředí služby App Service. Pokud jste to ještě neudělali, přečtěte [si informace o vytvoření prostředí služby App Service](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Vytvoření webové aplikace
1. Na [webu Azure Portal](https://portal.azure.com/)klikněte na **Create a resource > Web + Mobile > Web App**. 
   
    ![][1]
2. Vyberte své předplatné.  
   
    Pokud máte více předplatných, uvědomte si, že k vytvoření aplikace v prostředí služby App Service, musíte použít stejné předplatné, které jste použili při vytváření prostředí. 
3. Vyberte nebo vytvořte skupinu prostředků.
   
    *Skupiny prostředků* umožňují spravovat související prostředky Azure jako jednotku a jsou užitečné při vytváření pravidel *řízení přístupu (RBAC) na základě rolí* pro vaše aplikace. Další informace naleznete v tématu [Přehled Azure Resource Manager][ResourceGroups]. 
4. Vyberte nebo vytvořte plán služby App Service.
   
    *Plány služby App Service* jsou spravované sady webových aplikací.  Obvykle, když vyberete ceny, cena účtovaná se použije na plán služby App Service, nikoli na jednotlivé aplikace. Ve službě ASE platíte za výpočetní instance přidělené služby ASE, nikoli za to, co jste uvedli s prostředím ASP.  Chcete-li vertikálně navýšit počet instancí webové aplikace, můžete vertikálně navýšit kapacitu instancí plánu služby App Service a ovlivní všechny webové aplikace v tomto plánu.  Některé funkce, jako jsou sloty webu nebo integrace virtuální sítě, mají také omezení množství v rámci plánu.  Další informace najdete v tématu [Přehled plánů služby Azure App Service.](../overview-hosting-plans.md)
   
    Plány služby App Service můžete ve službě ASE identifikovat tak, že se podíváte na umístění, které je uvedeno pod názvem plánu.  
   
    ![][5]
   
    Pokud chcete použít plán služby App Service, který už v prostředí služby App Service existuje, vyberte tento plán. Pokud chcete vytvořit nový plán služby App Service, podívejte se na následující část tohoto [kurzu, vytvořit plán služby App Service v prostředí služby App Service](#createplan).
5. Zadejte název webové aplikace a klikněte na **Vytvořit**. 
   
    Pokud vaše ase používá externí VIP, adresa URL aplikace ve službě ASE je: [*název webu*]. [*název prostředí služby App Service*(p.azurewebsites.net namísto *[sitename*_azurewebsites.net
   
    Pokud vaše služba ASE používá interní virtuální ip adresu, adresa URL aplikace v této službě ASE je: [*název_webu*]. [*subdoména zadaná při vytváření služby ASE*]   
    Po výběru asp během vytváření ase se zobrazí aktualizace subdomény pod **názvem**

## <a name="create-an-app-service-plan"></a><a name="createplan"></a>Vytvoření plánu služby App Service
Když vytvoříte plán služby App Service v prostředí služby App Service, vaše možnosti pracovního procesu se liší, protože ve službě ASE nejsou žádní sdílené pracovníky.  Pracovníci, které musíte použít, jsou ty, které byly přiděleny služby ASE správcem.  To znamená, že k vytvoření nového plánu je potřeba mít více pracovníků přidělených do fondu pracovních sil služby ASE než celkový počet instancí ve všech vašich plánech, které jsou již v tomto fondu pracovníků.  Pokud nemáte dostatek pracovníků ve fondu pracovních sil služby ASE k vytvoření plánu, musíte spolupracovat s správcem služby ASE, abyste je přidali.

Dalším rozdílem s plány služby App Service hostovanými prostředím služby App Service je nedostatek výběru cen.  Pokud máte prostředí služby App Service, které platíte za výpočetní prostředky používané systémem a nemáte přidané poplatky za plány v tomto prostředí.  Obvykle při vytváření plánu služby App Service vyberete cenový plán, který určuje vaši fakturaci.  Prostředí služby App Service je v podstatě soukromé umístění, kde můžete vytvářet obsah.  Platíte za životní prostředí a ne za hostování obsahu.

Následující pokyny ukazují, jak vytvořit plán služby App Service při vytváření webové aplikace, jak je vysvětleno v předchozí části kurzu.

1. Klikněte na **Vytvořit nový** v uživatelském nastavení výběru plánu a zadejte název plánu stejně jako obvykle mimo ase.
2. Vyberte službu ASE, kterou chcete použít, z výběru polohy.
   
    Vzhledem k tomu, že prostředí služby App Service je v podstatě umístění soukromého nasazení, zobrazí se v části Umístění. 
   
    ![][2]
   
    Po výběru služby ASE ve výběru umístění se aktualizuje vytvoření ui plánu služby App Service.  Umístění nyní zobrazuje název systému služby ASE a oblast, ve které se nachází, a výběr cenového plánu je nahrazen výběrem fondu pracovních pracovníků.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Výběr fondu pracovníků
Normálně ve službě Azure App Service a mimo prostředí služby App Service jsou k dispozici 3 výpočetní velikosti, které jsou k dispozici s výběrem vyhrazeného cenového plánu.  Podobným způsobem pro ase můžete definovat až 3 fondy pracovníků a určit výpočetní velikost, která se používá pro tento fond pracovních procesů.  Co to znamená pro klienty služby ASE je, že místo výběru cenového plánu s velikostí výpočetních prostředků pro váš plán služby App Service vyberete, co se nazývá *fond pracovních míst*.  

Uživatelské prostředí pro výběr fondu pracovních pracovníků zobrazuje velikost výpočetních prostředků použitou pro tento fond pracovních prostředků pod názvem.  Dostupné množství odkazuje na počet výpočetních instancí, které jsou k dispozici pro použití v tomto fondu.  Celkový fond může mít ve skutečnosti více instancí než toto číslo, ale tato hodnota odkazuje jednoduše na to, kolik se nepoužívá.  Pokud potřebujete upravit prostředí služby App Service a přidat další výpočetní prostředky, přečtěte si informace [o konfiguraci prostředí služby App Service](app-service-web-configure-an-app-service-environment.md).

![][4]

V tomto příkladu se zobrazí pouze dva fondy pracovních procesů k dispozici. Důvodem je, že správce služby ASE pouze přiděleny hostitelé do těchto dvou fondů pracovních procesů.  Třetí by se zobrazí, když jsou do něj přiděleny virtuální chody.  

## <a name="after-web-app-creation"></a>Po vytvoření webové aplikace
Existuje několik aspektů pro spouštění webových aplikací a správu plánů služby App Service v ase, které je třeba vzít v úvahu.  

Jak již bylo uvedeno dříve, vlastník služby ASE je zodpovědný za velikost systému a v důsledku toho jsou také zodpovědní za zajištění dostatečné kapacity pro hostování požadovaných plánů služby App Service. Pokud nejsou k dispozici žádní pracovníci, nebudete moct vytvořit plán služby App Service.  To platí také pro škálování webové aplikace.  Pokud potřebujete více instancí, budete muset získat správce prostředí služby App Service, abyste přidali další pracovníky.

Po vytvoření webové aplikace a plánu služby App Service je vhodné ji škálovat.  Ve službě ASE musíte mít vždy alespoň 2 instance vašeho plánu služby App Service, abyste mohli poskytovat odolnost proti chybám pro vaše aplikace.  Škálování plán služby App Service ve službě ASE je stejný jako normální prostřednictvím ui plánu služby Aplikace.  Další informace o škálování, [Jak škálovat webové aplikace v prostředí služby App Service](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

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
[AzurePowershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
