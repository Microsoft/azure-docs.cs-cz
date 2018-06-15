---
title: Postup škálování aplikace ve službě App Service Environment
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
ms.openlocfilehash: d04a5fce920dae25507cdf2f64832574e24c51dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836550"
---
# <a name="scaling-apps-in-an-app-service-environment"></a>Škálování aplikací ve službě App Service Environment
Ve službě Azure App Service jsou obvykle tři věci, které je možné škálovat:

* ceny plán
* velikost pracovního procesu 
* počet instancí.

V App Service Environment není potřeba vyberte nebo změňte cenový plán.  Z hlediska funkce je již v cenová úroveň schopnosti Premium.  

Vzhledem k velikosti pracovního procesu můžete přiřadit správce App Service Environment velikost nachází výpočetní prostředek, který se má použít pro každý fond pracovních procesů.  To znamená pracovní skupina 1 může mít s P4 výpočetní prostředky a pracovní fond 2 s P1 výpočetní prostředky, v případě potřeby.  Nemají být v pořadí velikost.  Podrobnosti ohledně velikosti a jejich cenovou najdete v tématu dokumentu zde [Azure App Service – ceny][AppServicePricing].  Zůstane škálování možnosti pro webové aplikace a plány služby App Service ve službě App Service Environment jako:

* Výběr fondu pracovního procesu
* počet instancí

Změna buď položky se provádí prostřednictvím uživatelského rozhraní odpovídající zobrazená pro vaše App Service Environment hostované plány služby App Service.  

![][1]

Nelze škálovat vaše ASP překračuje počet dostupných výpočetních prostředků ve fondu pracovních procesů, který vaše ASP je v.  Pokud třeba výpočetní prostředky v tomto fondu pracovních procesů, které je potřeba získat správce App Service Environment je přidat.  Pro informace ohledně opětovná konfigurace vaší App Service Environment, přečtěte si informace v tomto poli: [postup konfigurace služby App Service environment][HowtoConfigureASE].  Můžete také využít výhod funkce škálování App Service Environment, které chcete přidat kapacitu podle plánu nebo metriky.  Chcete-li získat další informace o konfiguraci najdete v části Automatické škálování pro prostředí App Service Environment, samotné [postup konfigurace automatického škálování App Service Environment][ASEAutoscale].

Můžete vytvořit více aplikaci pomocí výpočetní prostředky z fondů jiný pracovní plány služby, nebo můžete použít stejný fond pracovních procesů.  Například pokud máte (10) k dispozici výpočetní prostředky ve fondu 1 pracovního procesu, můžete vytvořit jeden plán služby app service pomocí (6) výpočetní prostředky a plánování druhý služby app service používající (4) výpočetní prostředky.

### <a name="scaling-the-number-of-instances"></a>Škálování počtu instancí
Při prvním vytvoření webové aplikace ve službě App Service Environment začíná 1 instance.  Pak si můžete škálovat pro další instance zajistit další výpočetní prostředky pro vaši aplikaci.   

Pokud vaše App Service Environment má dostatečnou kapacitu, a to je poměrně jednoduché.  Přejdete do vašeho plánu služby App Service, kde webů, které chcete vertikálně navýšit kapacitu a vyberte škálování.  Otevře se uživatelské rozhraní, kde můžete ručně nastavit stupnici pro vaše prostředí ASP nebo konfigurace pravidel automatického škálování pro vaši ASP.  Chcete-li ručně škálování aplikace jednoduše nastavte ***škálovat podle*** k ***počet instancí, který nastavím ručně***.  Odsud přesuňte jezdec požadované množství nebo zadejte do pole vedle jezdce.  

![][2] 

Pravidla automatického škálování pro webovou stránku ASP.NET v App Service Environment fungovat stejně jako obvykle.  Můžete vybrat ***procento využití procesoru*** pod ***škálovat podle*** a vytvořte pravidla automatického škálování pro vaši ASP na základě procento využití procesoru nebo je můžete vytvořit složitější pravidla pomocí ***pravidla plánu a výkonu*** .  Podrobnější informace o konfiguraci zobrazíte škálování použijte příručku zde [škálování aplikace v Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Výběr fondu pracovního procesu
Jak již bylo uvedeno dříve, výběr fondu pracovního procesu se získají z uživatelského rozhraní ASP.  Otevře se okno pro ASP, který chcete škálovat a vyberte fond pracovních procesů.  Zobrazí se všechny fondy pracovních procesů, které jste nakonfigurovali ve službě App Service Environment.  Pokud máte jenom jeden pracovní proces fondu pak uvidíte pouze jeden fond uvedené.  Chcete-li změnit co fond pracovních procesů je vaše prostředí ASP v, jednoduše vybrat fond pracovních procesů, který chcete, aby váš plán služby App Service pro přesun do.  

![][3]

Než přejdete vaší ASP z jeden pracovní proces fondu je důležité se ujistit, že máte dostatečnou kapacitu pro vaše prostředí ASP.  V seznamu fondy pracovních procesů nejen je uveden název fondu pracovního procesu, ale můžete také zjistit, kolik pracovníci jsou k dispozici v tomto fondu pracovních procesů.  Ujistěte se, že nejsou k dispozici dostatek instancí, které jsou k dispozici tak, aby obsahovala plán aplikační služby.  Pokud třeba více výpočetní prostředky ve fondu pracovních procesů, které chcete přesunout do, pak získáte správce App Service Environment je přidat.  

> [!NOTE]
> Přesun Klientova že studený způsobí, že ASP z fondu jeden pracovní proces se spustí aplikace v tomto prostředí ASP.  To může způsobit požadavky na spuštění pomalu, jako je vaše aplikace cold zahájeno nové výpočetní prostředky.  Studený start se vyhnout pomocí [aplikace horké až schopností] [ AppWarmup] ve službě Azure App Service.  Inicializace aplikace modul popsané v článku funguje i pro studenou spustí vzhledem k tomu, že proces inicializace je také volána, když aplikace jsou cold zahájeno nové výpočetní prostředky. 
> 
> 

## <a name="getting-started"></a>Začínáme
Chcete-li začít pracovat s prostředí App Service, přečtěte si téma [jak k vytvoření služby App Service Environment][HowtoCreateASE]

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
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../web-sites-scale.md
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
