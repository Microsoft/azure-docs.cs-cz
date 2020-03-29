---
title: Porovnání vlastních obrázků a vzorců v laboratořích DevTest | Dokumenty společnosti Microsoft
description: Seznamte se s rozdíly mezi vlastními obrázky a vzorci jako základy virtuálních zařízení, abyste se mohli rozhodnout, který z nich nejlépe vyhovuje vašemu prostředí.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a3cb259a-7d80-40ec-8ee8-45105704d589
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: ae7556eda817b9eb7be84f9d4a23ea91d3d5440d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "64680306"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Porovnání vlastních obrázků a vzorců v devTest Labs
[Vlastní image](devtest-lab-create-template.md) a [vzorce](devtest-lab-manage-formulas.md) lze použít jako základ pro vytvořené nové [virtuální aplikace](devtest-lab-add-vm.md). Klíčovým rozdílem mezi vlastními obrázky a vzorci je však to, že vlastní bitová kopie je jednoduše bitová kopie založená na virtuálním pevném disku, zatímco vzorec je obraz založený na virtuálním pevném disku *kromě* předkonfigurovaných nastavení – například Velikost virtuálního počítače, virtuální síť, podsíť a artefakty. Tato předkonfigurovaná nastavení jsou nastavena s výchozími hodnotami, které mohou být přepsány v době vytvoření virtuálního počítače. Tento článek vysvětluje některé výhody (výhody) a nevýhody (nevýhody) k použití vlastních obrázků versus použití vzorců.

## <a name="custom-image-pros-and-cons"></a>Vlastní obrazové výhody a nevýhody
Vlastní image poskytují statický, neměnný způsob, jak vytvořit virtuální chod z požadovaného prostředí. 

**Výhody**

* Zřizování virtuálních služeb z vlastní image je rychlé, protože se po změně virtuálního virtuálního virtuálního mísy z bitové kopie nic nezmění. Jinými slovy, neexistují žádná nastavení, která by se použila, protože vlastní obrázek je pouze obrázek bez nastavení. 
* Virtuální virtuální ho disponála vytvořená z jedné vlastní image jsou identické.

**Nevýhody**

* Pokud potřebujete aktualizovat některé aspekty vlastní bitové kopie, musí být obrázek znovu vytvořen.  

## <a name="formula-pros-and-cons"></a>Pro a zápory formule
Vzorce poskytují dynamický způsob vytváření virtuálních počítačů z požadované konfigurace nebo nastavení.

**Výhody**

* Změny v životním prostředí mohou být zachyceny za běhu prostřednictvím artefaktů. Například pokud chcete, aby byl virtuální počítač nainstalovaný s nejnovějšími bity z kanálu vydání nebo zařadit nejnovější kód z úložiště, můžete jednoduše zadat artefakt, který nasazuje nejnovější bity, nebo zařazuje nejnovější kód ve vzorci společně s cílovou základnou Obrázek. Vždy, když tento vzorec se používá k vytvoření virtuálních stránek, nejnovější bity/kód jsou nasazeny nebo zapsány do virtuálního soudu. 
* Vzorce můžou definovat výchozí nastavení, která vlastní image nemohou poskytnout – například velikosti virtuálních zařízení a nastavení virtuální sítě. 
* Nastavení uložená ve vzorci se zobrazí jako výchozí hodnoty, ale při vytvoření virtuálního počítače lze upravit. 

**Nevýhody**

* Vytvoření virtuálního virtuálního virtuálního pracovního místa ze vzorce může trvat déle než vytvoření virtuálního virtuálního virtuálního virtuálního mísy z vlastní image.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související příspěvky blogu
* [Vlastní obrázky nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Další kroky
- [Nejčastější dotazy k devTest Labs](devtest-lab-faq.md)