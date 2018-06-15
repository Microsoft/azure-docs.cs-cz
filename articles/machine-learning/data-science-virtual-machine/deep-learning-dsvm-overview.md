---
title: Úvod do hloubkového učení virtuálního počítače – Azure | Microsoft Docs
description: Scénáře klíče analýzu a součásti pro přímý Learning virtuální počítače.
keywords: přímý učení, AI, vědecké účely nástrojů data, data vědecké účely virtuálního počítače, nástroje pro vědecké zpracování dat, vědecké zpracování dat linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 35370d3d143fdc154728aa8b31b9874227e46168
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31406887"
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Úvod do hloubkového učení virtuálního počítače

## <a name="why-deep-learning-virtual-machine"></a>Proč hloubkové učení virtuálního počítače? 

Stále, hluboké učení algoritmy / hluboké neuronové sítě se stávají jeden z oblíbených metody používané v mnoha machine learning problémy. Jsou obzvláště dobrý na počítač léčby úkoly, jako je obrázek, text, zvuku a videa pochopení často blíží lidského kognitivní úrovně v některé konkrétní domény pomocí pokročilé architektury hluboké neuronové sítě a získání přístupu k velké sady data pro učení modely. Hloubkové learning vyžaduje velké nároky na výpočetní výkon ke cvičení modelů pomocí těchto rozsáhlých datových sad. Cloud a dostupnost grafických procesorů (GPU) se stává stále možné vytvářet sofistikované hluboké neuronové architektury a cvičení je na velké datové sady na výkonné počítačové infrastruktury v cloudu.  [Datové vědy virtuálního počítače](overview.md) poskytl bohatou sadu nástrojů a ukázky pro přípravu dat, machine learning a hloubkové učení. Ale jedním z problémů, uživatelé, jimž je ke zjištění nástroje a ukázky pro konkrétní scénáře, jako je hloubkové learning snadno a taky snadno zřídit instancí virtuálních počítačů na bázi GPU. Tento přímý Learning virtuálního počítače (DLVM) řeší tyto problémy. 

## <a name="what-is-deep-learning-virtual-machine"></a>Co je hloubkové Virtual Machine Learning? 
Virtuální počítač hloubkového učení je speciálně konfigurované varianta [datové vědy virtuálního počítače](overview.md) (DSVM), aby bylo snazší pomocí virtuálních počítačů na bázi GPU instance pro cvičení hloubkové learning modely. Je podporován v systému Windows 2016 a Ubuntu Data vědecké účely virtuálním počítači.  Sdílí stejné Image virtuálních počítačů jádra (a tím i bohaté sady nástrojů) jako DSVM ale je nastavena k usnadnění hloubkové learning. Pro bitové kopie a pochopení textu, který široce platí pro mnoho scénářů AI reálného života taky poskytujeme ukázky začátku do konce. Virtuální počítač hloubkového learning také pokusí navázat bohatou sadu nástrojů a ukázek na DSVM snadno zjistitelný podle zpřístupnění katalog Nástroje a ukázky na virtuálním počítači. Z hlediska nástrojů, hloubkové Virtual Machine Learning poskytuje několik oblíbených hloubkové learning rozhraní, nástroje získat a předběžně zpracovat bitovou kopii, textová data. Úplný seznam nástrojů, můžete se podívat do [stránka s přehledem dat vědecké účely virtuální počítač](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Další kroky

Začínáme s hloubkovým Learning virtuálního počítače pomocí následujících kroků:

* [Zřídit hluboká učení virtuálního počítače](provision-deep-learning-dsvm.md)
* [Použijte přímý učení virtuálního počítače](use-deep-learning-dsvm.md)
* [Referenční dokumentace nástroje](dsvm-deep-learning-ai-frameworks.md)
* [Ukázky](dsvm-samples-and-walkthroughs.md)
