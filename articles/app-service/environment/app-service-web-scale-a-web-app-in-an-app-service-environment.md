---
title: Jak škálovat aplikaci v App Service Environment – Azure
description: Škálování aplikace v App Service Environment
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: jimbe
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 452c3b9aac58e1eca25b6417eb891fed7180ada7
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636635"
---
# <a name="scaling-apps-in-an-app-service-environment"></a>Škálování aplikací ve službě App Service Environment
V Azure App Service existují obvykle tři věci, které můžete škálovat:

* Cenový tarif
* velikost pracovního procesu 
* počet instancí.

V případě pomocného mechanismu není nutné vybrat nebo změnit Cenový tarif.  V souvislosti s funkcemi už je na úrovni cenové kapacity Premium.  

S ohledem na velikosti pracovních procesů může správce pomocného programu přiřadit velikost výpočetního prostředku, který se má použít pro každý fond pracovních procesů.  To znamená, že můžete mít fond pracovních procesů 1 – P4 výpočetní prostředky a fond pracovních procesů 2 s výpočetními prostředky P1, pokud je to potřeba.  Nemusí být v pořadí velikostí.  Podrobnosti o velikostech a jejich cenách najdete tady v dokumentu [Azure App Service ceny][AppServicePricing].  Tím se zachová možnost škálování pro webové aplikace a App Service plány v App Service Environment:

* Výběr fondu pracovních procesů
* počet instancí

Změna jedné položky se provádí prostřednictvím příslušného uživatelského rozhraní zobrazeného pro plány hostované v App Service.  

![][1]

Nemůžete škálovat prostředí ASP nad rámec počtu dostupných výpočetních prostředků ve fondu pracovních procesů, ve kterém je vaše prostředí ASP.  Pokud v tomto fondu pracovních procesů potřebujete výpočetní prostředky, je potřeba, abyste správce pomocného mechanismu mohli přidat.  Informace o opětovné konfiguraci služby pomocného mechanismu pro čtení najdete tady: [Jak nakonfigurovat App Service prostředí][HowtoConfigureASE].  Můžete také využít výhod funkcí automatického škálování pomocného mechanismu, abyste mohli přidat kapacitu na základě plánu nebo metrik.  Další informace o konfiguraci automatického škálování pro samotný prostředí přihlašování k systému najdete v tématu [Konfigurace automatického škálování pro App Service Environment][ASEAutoscale].

Můžete vytvořit několik plánů služby App Service s využitím výpočetních prostředků z různých fondů pracovních procesů nebo můžete použít stejný fond pracovních procesů.  Například pokud máte (10) dostupných výpočetních prostředků ve fondu pracovních procesů 1, můžete vytvořit jeden plán služby App Service s využitím (6) výpočetních prostředků a druhý plán služby App Service, který využívá (4) výpočetní prostředky.

### <a name="scaling-the-number-of-instances"></a>Škálování počtu instancí
Při prvním vytvoření webové aplikace v App Service Environment začíná s 1 instancí.  Pak můžete horizontální navýšení kapacity na další instance a poskytnout tak pro svou aplikaci další výpočetní prostředky.   

Pokud má vaše pomocného mechanismu dostatek kapacity, je to poměrně jednoduché.  Přejdete na plán App Service, který obsahuje weby, které chcete škálovat, a vyberte škálovat.  Tím se otevře uživatelské rozhraní, kde můžete ručně nastavit škálování pro ASP nebo nakonfigurovat pravidla automatického škálování pro ASP.  K ručnímu škálování aplikace stačí nastavit ***škálování*** na ***počet instancí, který zadáte ručně***.  Z tohoto místa buď přetáhněte posuvník na požadované množství, nebo ho zadejte do pole vedle posuvníku.  

![][2] 

Pravidla automatického škálování pro ASP v pomocném mechanismu služby fungují stejně jako normálně.  Můžete vybrat ***procento CPU*** v části ***škálovat podle*** a vytvořit pravidla automatického škálování pro ASP na základě PROCENTUÁLNÍho podílu procesoru, nebo můžete vytvořit složitější pravidla pomocí ***pravidel plánování a výkonu***.  Pokud chcete zobrazit podrobnější informace o konfiguraci automatického škálování, postupujte podle pokynů [v tématu škálování aplikace v Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Výběr fondu pracovních procesů
Jak bylo uvedeno dříve, k výběru fondu pracovních procesů se dostanete z uživatelského rozhraní ASP.  Otevřete okno pro ASP, u kterého chcete škálovat a vyberte fond pracovních procesů.  Zobrazí se všechny fondy pracovních procesů, které jste nakonfigurovali v App Service Environment.  Pokud máte jenom jeden fond pracovních procesů, zobrazí se jenom jeden z nich.  Pokud chcete změnit, na jaký fond pracovních procesů je ASP, stačí vybrat fond pracovních procesů, do kterého chcete App Service plán přesunout.  

![][3]

Před přesunutím ASP z jednoho pracovního fondu na jiný je důležité mít jistotu, že budete mít dostatečnou kapacitu pro ASP.  V seznamu fondů pracovních procesů není pouze uvedený název fondu pracovních procesů, ale můžete také zjistit, kolik pracovních procesů je k dispozici v daném fondu pracovních procesů.  Ujistěte se, že je k dispozici dostatek instancí pro zahrnutí vašeho plánu App Service.  Pokud ve fondu pracovních procesů potřebujete víc výpočetních prostředků, na které chcete přejít, potom správce pomocného mechanismu přidejte.  

> [!NOTE]
> Přesunutí ASP z jednoho pracovního fondu způsobí, že se aplikace v tomto prostředí ASP studeným startem.  To může způsobit zpomalení požadavků, protože vaše aplikace je v nových výpočetních prostředcích spuštěná.  Studeným startem se můžete vyhnout použitím [schopnosti zahřívání aplikace][AppWarmup] v Azure App Service.  Modul inicializace aplikace popsaný v článku také funguje pro studený start, protože proces inicializace je vyvolán také v případě, že byly aplikace studené na nových výpočetních prostředcích zahájeny. 
> 
> 

## <a name="getting-started"></a>Začínáme
Pokud chcete začít pracovat s App Service prostředími, přečtěte si téma [vytvoření App Service Environment][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../manage-scale-up.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../manage-scale-up.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
