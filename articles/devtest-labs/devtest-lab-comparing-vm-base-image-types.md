---
title: Porovnání vlastních imagí a vzorců v DevTest Labs | Microsoft Docs
description: Přečtěte si o rozdílech mezi vlastními imagemi a vzorci jako se základy virtuálních počítačů, abyste se mohli rozhodnout, který z nich nejlépe vyhovuje vašemu prostředí.
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
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84896323"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Porovnání vlastních imagí a vzorců v DevTest Labs
[Vlastní image](devtest-lab-create-template.md) i [vzorce](devtest-lab-manage-formulas.md) lze použít jako základ pro [vytvoření nových virtuálních počítačů](devtest-lab-add-vm.md). Klíčovým rozdílem mezi vlastními imagemi a vzorci je ale to, že vlastní image je jednoduše image založená na virtuálním pevném disku (VHD), zatímco vzorec je image založená na virtuálním pevném disku ( *kromě* předem nakonfigurovaných nastavení), jako je třeba velikost virtuálního počítače, virtuální síť, podsíť a artefakty. Tato předem nakonfigurovaná nastavení se nastavují s výchozími hodnotami, které se dají přepsat v době vytváření virtuálního počítače. Tento článek popisuje některé výhody (specialisty) a nevýhody (nevýhody) používání vlastních imagí a používání vzorců.

## <a name="custom-image-pros-and-cons"></a>Vlastní specialisty a nevýhody vlastních imagí
Vlastní image poskytují statický a neměnný způsob vytváření virtuálních počítačů z požadovaného prostředí. 

**Výhody**

* Zřizování virtuálních počítačů z vlastní image je rychlé, protože po vyvýšení virtuálního počítače z image se nic nemění. Jinými slovy, neexistují žádná nastavení, která by se měla použít, protože vlastní image je jenom obrázek bez nastavení. 
* Virtuální počítače vytvořené z jedné vlastní image jsou identické.

**Cons**

* Pokud potřebujete aktualizovat některý aspekt vlastní image, je nutné znovu vytvořit bitovou kopii.  

## <a name="formula-pros-and-cons"></a>Specialisty na vzorce a nevýhody
Vzorce poskytují dynamický způsob vytváření virtuálních počítačů z požadovaných konfigurací nebo nastavení.

**Výhody**

* Změny v prostředí se můžou zachytit průběžně prostřednictvím artefaktů. Například pokud chcete, aby byl virtuální počítač nainstalovaný s nejnovějšími bity z vašeho kanálu vydaných verzí nebo aby si zavedlo nejnovější kód z úložiště, můžete jednoduše zadat artefakt, který nasadí nejnovější bity nebo zařadí nejnovější kód do vzorce spolu s cílovou základní imagí. Pokaždé, když se tento vzorec použije k vytvoření virtuálních počítačů, na virtuální počítač se nasadí nebo zařadí nejnovější bity/kód. 
* Vzorce můžou definovat výchozí nastavení, která vlastní image nemůžou poskytovat – například velikosti virtuálních počítačů a nastavení virtuální sítě. 
* Nastavení uložené ve vzorci se zobrazují jako výchozí hodnoty, ale po vytvoření virtuálního počítače se dají změnit. 

**Cons**

* Vytvoření virtuálního počítače ze vzorce může trvat delší dobu než vytvoření virtuálního počítače z vlastní image.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související blogové příspěvky
* [Vlastní obrázky nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Další kroky
- [Nejčastější dotazy k DevTest Labs](devtest-lab-faq.md)