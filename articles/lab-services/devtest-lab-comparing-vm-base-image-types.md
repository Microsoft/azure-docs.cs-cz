---
title: Porovnání vlastních imagí a vzorců ve službě DevTest Labs | Dokumentace Microsoftu
description: Získejte informace o rozdílech mezi vlastní imagí a vzorců jako bází virtuálních počítačů, abyste se mohli rozhodnout, která z nich nejlépe vyhovuje prostředí.
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
ms.openlocfilehash: 37288fd4a9c7558d05728b8ce03df505117e0232
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611266"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Porovnání vlastních imagí a vzorců ve službě DevTest Labs
Obě [vlastních imagí](devtest-lab-create-template.md) a [vzorce](devtest-lab-manage-formulas.md) slouží jako základ pro [vytvořit nové virtuální počítače](devtest-lab-add-vm.md). Klíče rozdílu mezi vlastní imagí a vzorců je však, že vlastní image je jednoduše image založenou na virtuální pevný disk, když vzorec je image založenou na virtuální pevný disk *kromě* předkonfigurovaném nastavení – třeba velikost virtuálního počítače, virtuální sítě podsíť a artefakty. Tato předem nakonfigurovaná nastavení se nastavují s výchozími hodnotami, které se dá přepsat v době vytvoření virtuálního počítače. Tento článek vysvětluje některé (profesionály) výhody a nevýhody (nevýhody) pomocí vlastních imagí a vzorců.

## <a name="custom-image-pros-and-cons"></a>Vlastní image výhody a nevýhody
Vlastní Image poskytují statické, neměnné způsob, jak vytvořit virtuální počítače z požadované prostředí. 

**V oblasti IT**

* Zřizování virtuálních počítačů z vlastní image je rychlý, protože po virtuálního počítače je spuštěné z image se nic nemění. Jinými slovy nejsou žádná nastavení použít vlastní image jsou jen image bez nastavení. 
* Virtuální počítače vytvořené z jedné vlastní image jsou identické.

**Nevýhody**

* Pokud je potřeba aktualizovat některé aspekty vlastní image, musí se znovu vytvořit bitovou kopii.  

## <a name="formula-pros-and-cons"></a>Vzorec výhody a nevýhody
Vzorce poskytují dynamické způsob, jak vytvořit virtuální počítače z požadované konfigurace a nastavení.

**V oblasti IT**

* Změny v prostředí se dají zachytit v reálném čase pomocí artefaktů. Například pokud chcete virtuální počítač nainstalovat s nejnovější součásti z kanálu pro vydávání verzí, nebo zařazení nejnovější kód ze svého úložiště, můžete jednoduše zadat artefakt, který nasadí nejnovější součásti nebo využívá nejnovější kód ve vzorci spolu s základní cíl obrázek. Pokaždé, když se tento vzorec se používá k vytvoření virtuálních počítačů, nejnovější bitů/kódu jsou nasazené/zařazen do virtuálního počítače. 
* Vzorce můžete definovat výchozí nastavení, která vlastní Image neposkytuje – například velikosti virtuálních počítačů a nastavení virtuální sítě. 
* Nastavení uložené ve vzorci se zobrazují jako výchozí hodnoty, ale je možné upravit, když se vytvoří virtuální počítač. 

**Nevýhody**

* Vytvoření virtuálního počítače ze vzorce, může trvat déle než vytvoření virtuálního počítače z vlastní image.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související blogové příspěvky
* [Vlastní Image nebo vzorce?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Další postup
- [Nejčastější dotazy k DevTest Labs](devtest-lab-faq.md)