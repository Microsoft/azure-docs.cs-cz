---
title: Na virtuálním počítači Azure pomocí sady Visual Studio | Microsoft Docs
description: Pomocí sady Visual Studio na virtuální počítač Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: sacalla
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.prod: vs-devops-alm
ms.date: 03/02/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 3e37edbf8d29441541e77ed0660dc9a71618fdee
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894237"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio bitové kopie v Azure
V předkonfigurovaných Azure virtuálního počítače (VM) pomocí sady Visual Studio je rychlý a snadný způsob, jak přechod od nic k prostředí vývoj nahoru a spuštěna. Bitové kopie systému s různými konfiguracemi sady Visual Studio jsou k dispozici v [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=%22visual%20studio%202017%22&page=1).

Jste nováčky v prostředí Azure? [Vytvořte si bezplatný účet Azure](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>Jaké konfigurace a verzí jsou k dispozici?
Bitové kopie pro nejnovější hlavní verze, Visual Studio 2017 a Visual Studio 2015, najdete v Azure Marketplace. Pro každou hlavní verzi uvidíte, původně vydaná verze (RTW) a nejnovější aktualizovaných verzí. Každý z těchto verzí nabízí Visual Studio Enterprise a edice Visual Studio Community. Tyto bitové kopie se aktualizovaly nejméně každý měsíc, aby obsahovaly nejnovější aktualizace Visual Studio a systému Windows. Při názvy imagí zůstávají stejné, obsahuje popis každé bitové kopie verze nainstalovaného produktu a datum "od" image.

| Prodejní verze                                              | Edice                     |     Verze produktu     |
|:------------------------------------------------------------:|:----------------------------:|:-----------------------:|
| Visual Studio 2017: Nejnovější (verze 15.7)                    |    Enterprise, komunity     |      Verze 15.7.0     |
| Visual Studio 2017: Nejnovější verze Preview (verze 15.8 Preview 1) |    Enterprise, komunity     |      Verze 15.8.1     |
|         Visual Studio 2017: RTW                              |    Enterprise, komunity     |      Verze 15.0.13    |
|   Visual Studio 2015: Nejnovější (Update 3)                      |    Enterprise, komunity     |  Verze 14.0.25431.01  |
|         Visual Studio 2015: RTW                              |             Žádný             | (Jeho platnost pro obsluhu) |

> [!NOTE]
> V souladu s Microsoft obsluhy zásad původně vydaná verze (RTW) sady Visual Studio 2015 vypršela pro obsluhu. Visual Studio 2015 Update 3 je jediný zbývající verze nabízí pro produktovou řadu sady Visual Studio 2015.

Další informace najdete v tématu [zásad údržby Visual Studio](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Jaké funkce jsou nainstalovány?
Každý image obsahuje funkci doporučené nastavení pro tento edicí sady Visual Studio. Obecně platí že instalační program obsahuje:

* Všechny dostupné úlohy, včetně každé zatížení doporučená volitelné součásti
* .NET 4.6.2 a .NET 4.7 sady SDK, cílení balíčky a nástroje pro vývojáře
* Visual F #
* GitHub rozšíření pro Visual Studio
* Technologie LINQ to SQL nástroje

Příkazovým řádkem použitým k instalaci sady Visual Studio, při vytváření bitové kopie je následující:

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

Pokud obrázky neobsahují funkce sady Visual Studio, které budete potřebovat, poskytnout zpětnou vazbu prostřednictvím nástroje zpětné vazby v pravém horním rohu stránky.

## <a name="what-size-vm-should-i-choose"></a>Zvolte jakou velikost virtuálního počítače
Azure nabízí celou řadu velikostí virtuálních počítačů. Protože Visual Studio je výkonný vícevláknové aplikace, budete chtít velikost virtuálního počítače, který obsahuje alespoň dva procesory a 7 GB paměti. Doporučujeme následující velikosti virtuálních počítačů pro bitové kopie sady Visual Studio:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Další informace o nejnovější velikosti počítačů najdete v tématu [velikosti pro systém Windows virtuálních počítačů v Azure](/azure/virtual-machines/windows/sizes).

S Azure můžete znovu vyvážit počáteční volbu změnou velikosti virtuálního počítače. Můžete zřídit nový virtuální počítač s více odpovídající velikost, nebo změnit velikost vašeho stávajícího virtuálního počítače na jiný základní hardware. Další informace najdete v tématu [změnit velikost virtuálního počítače Windows](/azure/virtual-machines/windows/resize-vm).

## <a name="after-the-vm-is-running-whats-next"></a>Jakmile je virtuální počítač spuštěný, co je další?
Visual Studio odpovídá modelu "přineste vlastní licence" v Azure. Stejně jako u instalace na speciální hardware, jeden z první kroků je licencování instalace Visual Studia. Odemknout Visual Studio, buď:
- Přihlaste se pomocí účtu Microsoft, který je spojen s předplatným Visual Studio 
- Odemknout Visual Studio s kódem product key, které byly dodány s počátečním nákupu

Další informace najdete v tématu [Přihlaste se k sadě Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) a [jak odemknout Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Jak můžu uložit pro budoucí vývoj virtuálních počítačů nebo tým používat?

Spektrum vývojových prostředí je velký a skutečné náklady spojené s vytváření složitějších prostředí. Bez ohledu na vašem prostředí configuration můžete uložit nebo zachytit, virtuální počítač nakonfigurovaný jako "Základní bitová kopie" pro budoucí použití nebo pro ostatní členové týmu. Potom při dalším spuštění nového virtuálního počítače, můžete zřídit ze základní bitovou kopii, nikoli bitovou kopii Azure Marketplace.

Rychlý přehled: použití nástroje pro přípravu systému (Sysprep) a vypnout spuštění virtuálního počítače a pak zachytíte *(obrázek 1)* virtuální počítač jako bitovou kopii prostřednictvím uživatelského rozhraní na portálu Azure. Azure uloží `.vhd` soubor, který obsahuje bitovou kopii v účtu úložiště dle vlastního výběru. Nová bitová kopie se pak objeví jako prostředek obrázku v seznamu prostředků vaše předplatné.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Obrázek 1) Zaznamenejte bitovou kopii prostřednictvím portálu Azure uživatelského rozhraní.*</center>

Další informace najdete v tématu [vytvořte bitovou kopii spravované zobecněný virtuálního počítače v Azure](/azure/virtual-machines/windows/capture-image-resource).

> [!IMPORTANT]
> Nezapomeňte použít nástroj Sysprep k přípravě virtuálního počítače. Pokud tento krok přeskočíte, Azure nemůže zřídit virtuální počítač z bitové kopie.

> [!NOTE]
> Stále platit náklady pro úložiště bitových kopií, ale že přírůstkové náklady mohou být zanedbatelný ve srovnání s nákladů pro každý člen týmu, který potřebuje jeden opětovné vytvoření virtuálního počítače od začátku. Například stojí několik odběru k vytvoření a uložení 127 GB image dobu jednoho měsíce, který je opakovaně použitelné vaší celý tým. Tyto náklady jsou však zanedbatelný ve srovnání s hodiny, kdy každý zaměstnanec investují vytváří a ověřit správnou konfiguraci dev pole pro vlastní použití.

Kromě toho vývojářských úloh nebo technologie potřebovat další škálování, jako jsou typy konfigurací vývoj a konfigurací s více počítači. Azure DevTest Labs můžete použít k vytvoření _recepty_ který automatizovat vytváření vašeho "zlaté Image." DevTest Labs můžete také použít ke správě zásad pro virtuální počítače běžící vašeho týmu. [Pomocí Azure DevTest Labs pro vývojáře](/azure/devtest-lab/devtest-lab-developer-lab) je nejlepší zdroj pro další informace o DevTest Labs.

## <a name="next-steps"></a>Další postup
Teď, když víte o předkonfigurovaných bitové kopie sady Visual Studio, dalším krokem je vytvoření nového virtuálního počítače:

* [Vytvoření virtuálního počítače prostřednictvím portálu Azure](quick-create-portal.md)
* [Virtuální počítače s Windows – přehled](overview.md)
