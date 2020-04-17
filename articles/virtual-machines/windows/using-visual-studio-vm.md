---
title: Použití Visual Studia na virtuálním počítači Azure
description: Použití Visual Studia na virtuálním počítači Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cathysull
manager: cathys
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.date: 12/04/2019
ms.author: cathys
keywords: visualstudio
ms.openlocfilehash: 76c8ec8f3d691a897ec924e06b76beec746ad14a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451580"
---
# <a name="visual-studio-images-on-azure"></a>Image Visual Studia v Azure
Použití Visual Studia v předkonfigurovaném virtuálním počítači Azure (VM) je rychlý a snadný způsob, jak přejít z ničeho do běžícího vývojového prostředí. Bitové kopie systému s různými konfiguracemi sady Visual Studio jsou dostupné na [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure).

Jste nováčky v prostředí Azure? [Vytvořte si bezplatný účet Azure](https://azure.microsoft.com/free).

> [!NOTE]
> Ne všechna předplatná jsou způsobilá k nasazení bitových kopií Windows 10. Další informace najdete [v tématu Použití klienta Windows v Azure pro scénáře pro vývoj a testování](https://docs.microsoft.com/azure/virtual-machines/windows/client-images)

## <a name="what-configurations-and-versions-are-available"></a>Jaké konfigurace a verze jsou k dispozici?
Obrázky pro nejnovější hlavní verze, Visual Studio 2019, Visual Studio 2017 a Visual Studio 2015, najdete na Azure Marketplace.  Pro každou vydanou hlavní verzi se zobrazí původně verze RTW (released to web" (RTW) a nejnovější aktualizované verze.  Každá z těchto verzí nabízí visual studio enterprise a visual studio community edice.  Tyto obrázky jsou aktualizovány alespoň každý měsíc, aby zahrnovaly nejnovější aktualizace sady Visual Studio a systémwindows.  Zatímco názvy obrázků zůstávají stejné, popis každého obrázku obsahuje nainstalovanou verzi produktu a datum "k datu".

| Verze vydání                                                                                                                                                | Edice              | Verze produktu   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: Nejnovější (verze 16.4)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Podnik, Komunita | Verze 16.4.0    |
| [Visual Studio 2019: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019?tab=Overview)                         | Podnik, Komunita | Verze 16.0.9    |
| [Visual Studio 2017: Nejnovější (verze 15.9)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)           | Podnik, Komunita | Verze 15.9.17   |
| [Visual Studio 2017: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)                             | Podnik, Komunita | Verze 15.0.27  |
| [Visual Studio 2015: Nejnovější (aktualizace 3)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)               | Podnik, Komunita | Verze 14.0.25431.01 |

> [!NOTE]
> V souladu se zásadami údržby společnosti Microsoft vypršela platnost původně vydané (RTW) verze sady Visual Studio 2015 pro údržbu. Visual Studio 2015 Update 3 je jediná zbývající verze nabízená pro produktovou řadu Visual Studio 2015.

Další informace naleznete v zásadách [údržby sady Visual Studio](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Jaké funkce jsou nainstalovány?
Každá bitová kopie obsahuje doporučenou sadu funkcí pro tuto edici sady Visual Studio. Obecně platí, že instalace zahrnuje:

* Všechny dostupné úlohy, včetně doporučených volitelných součástí každého pracovního vytížení
* Sady SDK .NET 4.6.2 a .NET 4.7, sady Cílovací balíčky a Vývojářské nástroje
* Visual F#
* GitHub Extension for Visual Studio
* Nástroje LINQ až SQL

Příkazový řádek používaný k instalaci sady Visual Studio při vytváření bitových kopií je následující:

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Pokud obrázky neobsahují funkci sady Visual Studio, kterou požadujete, zadejte zpětnou vazbu prostřednictvím nástroje pro zpětnou vazbu v pravém horním rohu stránky.

## <a name="what-size-vm-should-i-choose"></a>Jakou velikost virtuálního počítače si mám vybrat?
Azure nabízí celou řadu velikostí virtuálních strojů. Vzhledem k tomu, že Visual Studio je výkonná aplikace s více vlákny, chcete velikost virtuálního počítače, která obsahuje alespoň dva procesory a 7 GB paměti. Pro image Visual Studia doporučujeme následující velikosti virtuálních zařízení:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Další informace o nejnovějších velikostech počítačů najdete v [tématu Velikosti virtuálních počítačů s Windows v Azure](/azure/virtual-machines/windows/sizes).

S Azure můžete znovu vyvážit svou počáteční volbu tím, že převažujete virtuální počítač. Můžete buď zřídit nový virtuální počítač s vhodnější velikostí, nebo změnit velikost stávajícího virtuálního počítače na jiný základní hardware. Další informace naleznete [v tématu Změna velikosti virtuálního počítače se systémem Windows](/azure/virtual-machines/windows/resize-vm).

## <a name="after-the-vm-is-running-whats-next"></a>Co bude dál po spuštění virtuálního virtuálního virtuálního provozu?
Visual Studio se řídí modelem "přineste si vlastní licenci" v Azure. Stejně jako u instalace na proprietární hardware, jedním z prvních kroků je licencování instalace sady Visual Studio. Chcete-li aplikaci Visual Studio odemknout,
- Přihlášení pomocí účtu Microsoft, který je přidružený k předplatnému Visual Studia 
- Odemknutí sady Visual Studio pomocí kódu Product Key dodané s počátečním nákupem

Další informace najdete [v tématu Přihlášení k Sadě Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) a Jak [odemknout Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Jak uložím vývojový virtuální hod pro budoucí nebo týmové použití?

Spektrum vývojových prostředí je obrovské a s budováním složitějších prostředí jsou spojeny skutečné náklady. Bez ohledu na konfiguraci prostředí můžete uložit nebo zachytit nakonfigurovaný virtuální počítač jako "základní bitovou kopii" pro budoucí použití nebo pro ostatní členy týmu. Potom při zavádění nového virtuálního počítače, zřídíte jej ze základní image, nikoli image Azure Marketplace.

Shrnutí: Použijte nástroj příprava systému (Sysprep) a vypnout spuštěný virtuální počítač a pak zachytit *(obrázek 1)* virtuálního počítače jako image prostřednictvím ui na portálu Azure. Azure uloží `.vhd` soubor, který obsahuje image v účtu úložiště podle vašeho výběru. Nový obrázek se pak zobrazí jako prostředek obrázku v seznamu prostředků vašeho předplatného.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Obrázek 1) Zachyťte bitovou kopii prostřednictvím uzuliny portálu Azure.*</center>

Další informace najdete [v tématu Vytvoření spravované bitové kopie generalizovaného virtuálního počítače v Azure](/azure/virtual-machines/windows/capture-image-resource).

> [!IMPORTANT]
> Nezapomeňte použít Sysprep k přípravě virtuálního počítače. Pokud tento krok zmeškáte, Azure nemůže zřídit virtuální počítač z image.

> [!NOTE]
> Stále vznikají některé náklady na ukládání bitových kopií, ale že přírůstkové náklady mohou být nevýznamné ve srovnání s režijnínáklady na znovu sestavit virtuální ho virtuálního zařízení od začátku pro každého člena týmu, který potřebuje jeden. Například vytvoření a uložení obrázku 127 GB na měsíc, který je opakovaně použitelný celým týmem, stojí několik dolarů. Tyto náklady jsou však nevýznamné ve srovnání s hodinami, které každý zaměstnanec investuje do sestavení a ověření správně nakonfigurovaného pole pro dev pro jejich individuální použití.

Kromě toho vaše vývojové úkoly nebo technologie může potřebovat větší škálování, jako jsou různé konfigurace vývoje a více konfigurací počítače. Azure DevTest Labs můžete použít k vytvoření _receptů,_ které automatizují konstrukci vaší "zlaté image". DevTest Labs můžete taky použít ke správě zásad pro spuštěné virtuální počítače vašeho týmu. [Použití Azure DevTest Labs pro vývojáře](/azure/devtest-lab/devtest-lab-developer-lab) je nejlepším zdrojem dalších informací o DevTest Labs.

## <a name="next-steps"></a>Další kroky
Teď, když víte o předkonfigurovaných iimages Sady Visual Studio, dalším krokem je vytvoření nového virtuálního počítače:

* [Vytvoření virtuálního počítače prostřednictvím portálu Azure](quick-create-portal.md)
* [Windows Virtuální počítače – přehled](overview.md)
