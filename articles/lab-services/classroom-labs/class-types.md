---
title: Příklady typů tříd v Azure Lab Services | Microsoft Docs
description: Poskytuje některé typy tříd, pro které můžete nastavit Labs pomocí Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 0e8b203240b4b2d6b67534ab52a7cd4ccf5df571
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976588"
---
# <a name="class-types-overview---azure-lab-services"></a>Přehled typů tříd – Azure Lab Services
Azure Lab Services vám umožní rychle nastavit prostředí pro laboratorní prostředí učebny v cloudu. Články v této části poskytují pokyny k nastavení několika typů učebn Labs pomocí Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Obsáhlý Learning při zpracování v přirozeném jazyce
Můžete nastavit testovací prostředí zaměřené na obsáhlý Learning při zpracování přirozeného jazyka (NLP) pomocí Azure Lab Services. Zpracování přirozeného jazyka (NLP) je forma umělé Intelligence (AI), která umožňuje počítačům s překlady, rozpoznáváním řeči a dalšími možnostmi porozumění jazyku. Studenti, kteří přebírají třídu NLP, získají virtuální počítač se systémem Linux, aby se dozvěděli, jak použít síťové algoritmy neuronové pro vývoj modelů hloubkového učení, které se používají k analýze napsaného lidského jazyka. 

Podrobné informace o tom, jak nastavit tento typ testovacího prostředí, najdete v tématu [Nastavení testovacího prostředí zaměřeného na obsáhlý Learning při zpracování v přirozeném jazyce pomocí Azure Lab Services](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Skriptování prostředí v systému Linux
Můžete nastavit laboratorní prostředí pro učení skriptování prostředí v systému Linux. Skriptování je užitečnou součástí správy systému, která správcům umožňuje vyhnout se opakovaným úlohám. V tomto ukázkovém scénáři třída pokrývá tradiční skripty bash a rozšířené skripty. Rozšířené skripty jsou skripty, které kombinují příkazy bash a Ruby. Tento přístup umožňuje Ruby předávat data kolem a bash příkazů pro interakci s prostředím. 

Studenti, kteří přebírají tyto třídy skriptování, získají virtuální počítač se systémem Linux, aby se seznámili se základy systému Linux, a také se seznámili s skriptováním prostředí bash. Virtuální počítač se systémem Linux přichází s povoleným přístupem ke vzdálené ploše a s nainstalovanými [gedit](https://help.gnome.org/users/gedit/stable/) a [Visual Studio Codemi](https://code.visualstudio.com/) textovými editory.

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [skriptování prostředí v systému Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Etické útoky 
Můžete nastavit testovací prostředí pro třídu, která se zaměřuje na forenzníou stranu etických útoků. Testování průniku, praxe používané etickým komunitou pro hackery, nastává, když se někdo pokusí získat přístup k systému nebo síti a Ukázat tak ohrožení zabezpečení, která by škodlivý útočník mohl zneužít. 

V etických třídách útoku Students se můžou seznámit s moderními technikami, které chrání před ohrožením zabezpečení. Každý Student získá virtuální počítač hostitele Windows serveru, který má dva vnořené virtuální počítače – jeden virtuální počítač s imagí **Metaspoiltable** a další počítač s imagí [Kali Linux](https://www.kali.org/) . Virtuální počítač Metasploitable se používá pro zneužití účely a virtuální počítač Kali poskytuje přístup k nástrojům potřebným ke spouštění úloh forenzní.

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [Nastavení testovacího prostředí pro výuku etické třídy hackerů](class-type-ethical-hacking.md).

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články: 

- [Nastavení testovacího prostředí zaměřeného na obsáhlý Learning při zpracování v přirozeném jazyce pomocí Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Skriptování prostředí v systému Linux](class-type-shell-scripting-linux.md)