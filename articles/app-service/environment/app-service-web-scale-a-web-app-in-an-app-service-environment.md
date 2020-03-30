---
title: Škálování aplikace ve skutužce ase v1
description: Škálování aplikace v prostředí služby App Service. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší verze v1 ASE.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 43849ca7084f2237c37ad537c50f4e94ac4ea7c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688680"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>Škálování aplikací v prostředí služby App Service v1
Ve službě Azure App Service jsou obvykle tři věci, které můžete škálovat:

* cenový plán
* velikost pracovníka 
* počet instancí.

Ve skuse se stavu služeb a řízení není nutné vybírat nebo měnit cenový plán.  Pokud jde o možnosti, je již na úrovni cenových schopností Premium.  

S ohledem na velikosti pracovního procesu správce služby ASE můžete přiřadit velikost výpočetního prostředku, který se má použít pro každý fond pracovních prostředků.  To znamená, že můžete mít worker pool 1 s výpočetními prostředky P4 a fond pracovních procesů 2 s výpočetními prostředky P1, pokud je to žádoucí.  Nemusí být v pořadí velikostí.  Podrobnosti o velikostech a jejich cenách najdete v dokumentu tady [Azure App Service Pricing][AppServicePricing].  Možnosti škálování pro webové aplikace a plány služby App Service v prostředí služby App Service tak budou:

* výběr fondu pracovníků
* počet instancí

Změna obou položek se provádí prostřednictvím příslušného ui zobrazené pro vaše plány hostované služby App Service služby Služby služby ASE.  

![][1]

Asp nelze vertikálně navýšit kapacitu nad rámec počtu dostupných výpočetních prostředků ve fondu pracovních prostředků, ve které se váš asp nachází.  Pokud potřebujete výpočetní prostředky v tomto fondu pracovních prostředků, musíte získat správce služby ASE, který je přidá.  Informace o opětovné konfiguraci služby ASE naleznete v tomto textu: [Jak nakonfigurovat prostředí služby App Service][HowtoConfigureASE].  Můžete také využít funkce automatického škálování služby ASE k přidání kapacity na základě plánu nebo metriky.  Další podrobnosti o konfiguraci automatického škálování pro samotné prostředí služby ASE naleznete v tématu [Konfigurace automatického škálování pro prostředí služby App Service][ASEAutoscale].

Můžete vytvořit více plánů služby App Service pomocí výpočetních prostředků z různých fondů pracovních procesů, nebo můžete použít stejný fond pracovních procesů.  Například pokud máte (10) k dispozici výpočetní prostředky ve fondu pracovních prostředků 1, můžete vytvořit jeden plán služby aplikace pomocí (6) výpočetní prostředky a druhý plán služby aplikace, která používá (4) výpočetní prostředky.

### <a name="scaling-the-number-of-instances"></a>Změna velikosti počtu instancí
Při prvním vytvoření webové aplikace v prostředí služby App Service začíná 1 instance.  Potom můžete horizontální navýšení kapacity na další instance a poskytnout další výpočetní prostředky pro vaši aplikaci.   

Pokud vaše ASE má dostatečnou kapacitu, pak je to docela jednoduché.  Přejdete do plánu služby App Service, který obsahuje weby, které chcete vertikálně navýšit kapacitu, a vyberte škálovat.  Tím se otevře rozhraní, ve kterém můžete ručně nastavit měřítko pro asp nebo nakonfigurovat pravidla automatického škálování pro asp.  Chcete-li ručně škálovat aplikaci jednoduše nastavit ***měřítko*** podle ***počtu instancí, které jsem zadat ručně***.  Zde buď přetáhněte jezdec na požadované množství, nebo jej zadejte do pole vedle jezdce.  

![][2] 

Pravidla automatického škálování pro asp v ase fungovat stejně jako obvykle.  Můžete vybrat ***procento procesoru*** v části ***Měřítko podle*** a vytvořit pravidla automatického škálování pro prostředí ASP na základě procenta procesoru nebo můžete vytvořit složitější pravidla pomocí pravidel plánu a ***výkonu***.  Chcete-li zobrazit podrobnější informace o konfiguraci automatického škálování, použijte průvodce zde [Scale a app in Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Výběr fondu pracovních pracovníků
Jak již bylo uvedeno dříve, výběr fondu pracovních pracovníků je přístupný z uzp.  Otevřete okno pro asp, který chcete škálovat a vyberte fond pracovních procesů.  Zobrazí se všechny fondy pracovních procesů, které jste nakonfigurovali v prostředí služby App Service.  Pokud máte pouze jeden fond pracovních pracovníků, zobrazí se pouze jeden fond uvedený.  Chcete-li změnit, jaký fond pracovních míst se nachází, jednoduše vyberte fond pracovních procesů, do kterého se má plán služby App Service přesunout.  

![][3]

Před přesunutím technologie ASP z jednoho fondu pracovních pracovníků do jiného je důležité zajistit dostatečnou kapacitu pro asp.  V seznamu fondů pracovních procesů je uveden nejen název fondu pracovních procesů, ale můžete také zjistit, kolik pracovníků je v tomto fondu pracovníků k dispozici.  Ujistěte se, že je k dispozici dostatek instancí, které obsahují plán služby App Service.  Pokud potřebujete další výpočetní prostředky ve fondu pracovních prostředků, do kterýchcete přesunout, pak přimějte správce služby ASE, který je přidá.  

> [!NOTE]
> Přesunutí technologie ASP z jednoho fondu pracovních procesů způsobí studené spuštění aplikací v tomto asp.  To může způsobit, že požadavky běží pomalu, protože vaše aplikace je studená spuštěná na nových výpočetních prostředcích.  Studené spuštění lze vyhnout pomocí [funkce zahřívání aplikace][AppWarmup] ve službě Azure App Service.  Modul inicializace aplikace popsaný v článku funguje také pro studené starty, protože proces inicializace je také vyvolán, když jsou aplikace studené spuštěny na nových výpočetních prostředcích. 
> 
> 

## <a name="getting-started"></a>Začínáme
Pokud chcete začít s prostředím služby App Service, přečtěte si informace [o tom, jak vytvořit prostředí služby App Service.][HowtoCreateASE]

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
