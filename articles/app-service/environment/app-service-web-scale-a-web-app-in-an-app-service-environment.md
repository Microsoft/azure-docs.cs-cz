---
title: Jak škálovat aplikaci ve službě App Service Environment – Azure
description: Škálování aplikace ve službě App Service Environment
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
ms.openlocfilehash: c94e7f761dcbc7e05965e359d7f9a864335c9c6b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269341"
---
# <a name="scaling-apps-in-an-app-service-environment"></a>Škálování aplikací ve službě App Service Environment
Ve službě Azure App Service jsou obvykle tři věci, které je možné škálovat:

* Cenový plán
* velikost pracovního procesu 
* počet instancí.

Ve službě ASE není nutné vybrat nebo změna cenového plánu.  Co se týče možnosti je již v cenové úrovni funkce úrovně Premium.  

S ohledem na velikosti pracovního procesu můžete přiřadit správce služby ASE velikost výpočetních prostředků pro každý fond pracovních procesů.  To znamená, může mít 1 fondu pracovních procesů s P4 výpočetní prostředky a 2 fondu pracovních procesů s P1 výpočetní prostředky, v případě potřeby.  Nemají být podle velikosti.  Podrobnosti o velikostech a cenách najdete v dokumentu zde [Azure App Service – ceny][AppServicePricing].  Kvůli tomu škálování možností pro webové aplikace a plány služby App Service ve službě App Service Environment bude:

* Výběr fondu pracovních procesů
* počet instancí

Změna buď položku se provádí prostřednictvím odpovídající uživatelské rozhraní zobrazí pro hostované služby ASE plány služby App Service.  

![][1]

Nejde škálovat vaše ASP nad rámec počet dostupných výpočetních prostředků ve fondu pracovních procesů, které je vaše prostředí ASP v.  Pokud třeba výpočetní prostředky v tomto fondu pracovních procesů, budete muset požádat správce vaší služby ASE je přidat.  Opětovná Konfigurace informací o vaší služby ASE najdete tyto informace tady: [Postup konfigurace služby App Service environment][HowtoConfigureASE].  Můžete také využít výhod funkce automatického škálování služby ASE, které chcete přidat kapacitu podle plánu nebo metriky.  Další podrobnosti o konfiguraci najdete v článku automatického škálování pro prostředí ASE samotné [postup konfigurace automatického škálování služby App Service Environment][ASEAutoscale].

Více aplikací můžete vytvořit pomocí výpočetních prostředků z fondy pracovních procesů různé plány služby, nebo můžete použít stejný fond pracovních procesů.  Například pokud máte v 1 fondu pracovních procesů (10) k dispozici výpočetní prostředky, můžete také vytvořit jeden plán služby app service pomocí (6) výpočetní prostředky a plánu služby app service druhý, který používá (4) výpočetní prostředky.

### <a name="scaling-the-number-of-instances"></a>Škálování počtu instancí
Při prvním vytvoření webové aplikace ve službě App Service Environment začíná 1 instance.  Horizontální navýšení kapacity můžete klikněte na další instance k poskytnutí dalších výpočetních prostředků pro vaši aplikaci.   

Pokud vaše služba ASE má dostatečnou kapacitu, je to poměrně jednoduché.  Přejít na plán služby App Service, která obsahuje servery, které chcete vertikálně navýšit kapacitu a vyberte horizontální snížení kapacity.  Otevře se uživatelské rozhraní, kde můžete ručně nastavit stupnici pro vaše prostředí ASP nebo nakonfigurovat pravidla automatického škálování pro vaše prostředí ASP.  Chcete-li ručně škálovat aplikaci jednoduše nastavte ***škálovat*** k ***počet instancí, který nastavím ručně***.  Odsud posuňte posuvník na požadované množství nebo zadejte do pole vedle posuvníku.  

![][2] 

Pravidla automatického škálování pro ASP ve službě ASE fungují stejně jako obvykle.  Můžete vybrat ***procento využití procesoru*** pod ***škálovat*** a vytvoření pravidla automatického škálování pro vaši ASP na základě procento využití procesoru nebo je můžete vytvořit složitější pravidla pomocí ***pravidla výkonu a plánování*** .  Zobrazíte kompletní podrobnosti o konfiguraci automatického škálování použít Průvodce zde [škálování aplikace ve službě Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Výběr fondu pracovních procesů
Jak bylo uvedeno dříve, výběru fondu pracovních procesů se přistupuje z uživatelského rozhraní ASP.  Otevře se okno pro ASP, kterou chcete škálovat a vyberte fond pracovních procesů.  Zobrazí se všechny fondy pracovních procesů, které jste nakonfigurovali ve službě App Service Environment.  Pokud máte jen jeden pracovní proces fondu pak uvidíte pouze jeden fond uvedený.  Chcete-li změnit co fond pracovních procesů, je vaše prostředí ASP v stačí vybrat fond pracovních procesů, který chcete, aby váš plán služby App Service pro přesun do.  

![][3]

Před přechodem vaší ASP z jeden pracovní proces fondu je důležité se ujistit, že máte dostatečnou kapacitu pro vaše prostředí ASP.  V seznamu fondy pracovních procesů nejen je uveden název fondu pracovních procesů, ale můžete také zjistit, kolik pracovní procesy jsou dostupné v tomto fondu pracovních procesů.  Ujistěte se, že jsou tak, aby obsahovala plán služby App Service k dispozici dostatek instancí.  Pokud potřebujete víc výpočetních prostředků ve fondu pracovních procesů, který chcete přesunout, potom získá správce služby ASE je přidat.  

> [!NOTE]
> Přesun že ASP z jeden pracovní proces fondu způsobí, že studeného spustí aplikaci v prostředí ASP.  To může způsobit, zařazování žádostí běží pomalu, jak vaše aplikace je úplné spuštění na nové výpočetní prostředky.  Studený start můžete vyhnout použitím [zahřívání schopnost aplikace] [ AppWarmup] ve službě Azure App Service.  Inicializace aplikace modulu je popsáno v článku souvisejícím s úplným spuštěním také vyhovuje, protože proces inicializace je také voláno, když aplikace jsou úplné spuštění na nové výpočetní prostředky. 
> 
> 

## <a name="getting-started"></a>Začínáme
Začínáme s App Service Environment najdete v článku [jak k vytvoření služby App Service Environment][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../web-sites-scale.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../web-sites-scale.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
