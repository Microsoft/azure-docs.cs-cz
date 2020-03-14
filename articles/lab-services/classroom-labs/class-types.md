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
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 27619a69a1f7fbded8ce6430afc2b8e9a8b4a00c
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79296723"
---
# <a name="class-types-overview---azure-lab-services"></a>Přehled typů tříd – Azure Lab Services

Azure Lab Services vám umožní rychle nastavit prostředí pro laboratorní prostředí učebny v cloudu. Články v této části poskytují pokyny k nastavení několika typů učebn Labs pomocí Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Obsáhlý Learning při zpracování v přirozeném jazyce

Můžete nastavit testovací prostředí zaměřené na obsáhlý Learning při zpracování přirozeného jazyka (NLP) pomocí Azure Lab Services. Zpracování přirozeného jazyka (NLP) je forma umělé Intelligence (AI), která umožňuje počítačům s překlady, rozpoznáváním řeči a dalšími možnostmi porozumění jazyku. Studenti, kteří přebírají třídu NLP, získají virtuální počítač se systémem Linux, aby se dozvěděli, jak použít síťové algoritmy neuronové pro vývoj modelů hloubkového učení, které se používají k analýze napsaného lidského jazyka.

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [Nastavení testovacího prostředí zaměřeného na obsáhlý Learning při zpracování v přirozeném jazyce pomocí Azure Lab Services](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Skriptování Shellu v Linuxu

Můžete nastavit laboratorní prostředí pro učení skriptování prostředí v systému Linux. Skriptování je užitečnou součástí správy systému, která správcům umožňuje vyhnout se opakovaným úlohám. V tomto ukázkovém scénáři třída pokrývá tradiční skripty bash a rozšířené skripty. Rozšířené skripty jsou skripty, které kombinují příkazy bash a Ruby. Tento přístup umožňuje Ruby předávat data kolem a bash příkazů pro interakci s prostředím.

Studenti, kteří přebírají tyto třídy skriptování, získají virtuální počítač se systémem Linux, aby se seznámili se základy systému Linux, a také se seznámili s skriptováním prostředí bash. Virtuální počítač se systémem Linux přichází s povoleným přístupem ke vzdálené ploše a s nainstalovanými [gedit](https://help.gnome.org/users/gedit/stable/) a [Visual Studio Codemi](https://code.visualstudio.com/) textovými editory.

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [skriptování prostředí v systému Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Etický hacking

Můžete nastavit testovací prostředí pro třídu, která se zaměřuje na forenzníou stranu etických útoků. Testování průniku, praxe používané etickým komunitou pro hackery, nastává, když se někdo pokusí získat přístup k systému nebo síti a Ukázat tak ohrožení zabezpečení, která by škodlivý útočník mohl zneužít.

V etických třídách útoku Students se můžou seznámit s moderními technikami, které chrání před ohrožením zabezpečení. Každý Student získá virtuální počítač hostitele Windows serveru, který má dva vnořené virtuální počítače – jeden virtuální počítač s imagí [Metasploitable3](https://github.com/rapid7/metasploitable3) a další počítač s imagí [Kali Linux](https://www.kali.org/) . Virtuální počítač Metasploitable se používá pro zneužití účely.  Virtuální počítač se systémem Kali Linux poskytuje přístup k nástrojům potřebným ke spouštění úloh forenzní.

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [Nastavení testovacího prostředí pro výuku etické třídy útoků](class-type-ethical-hacking.md).

## <a name="database-management"></a>Správa databází
Koncepty databází jsou jedním z úvodních kurzů, které jsou v rámci vysokoškolského ministerstva v oblasti počítačové vědy. V Azure Lab Services můžete nastavit testovací prostředí pro třídu správy základní databáze. V testovacím prostředí můžete například nastavit šablonu virtuálního počítače s databázovým serverem [MySQL](https://www.mysql.com/) nebo serverem [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) .

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [Nastavení testovacího prostředí pro učení správy databází pro relační databáze](class-type-database-management.md).

## <a name="python-and-jupyter-notebooks"></a>Poznámkové bloky Python a Jupyter
Můžete nastavit počítač šablony v Azure Lab Services s nástroji potřebnými k učení studentů, jak používat [poznámkové bloky Jupyter](http://jupyter-notebook.readthedocs.io). Jupyter Poznámkový blok je open source projekt, který umožňuje snadno kombinovat text a spustitelný zdrojový kód [Pythonu](https://www.python.org/) na jednom plátně s názvem Poznámkový blok. Spuštění poznámkového bloku má za následek lineární záznam vstupů a výstupů.  Tyto výstupy můžou zahrnovat text, tabulky informací, bodové a další.

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [Nastavení testovacího prostředí pro učení datových věd pomocí poznámkových bloků Python a Jupyter](class-type-jupyter-notebook.md).

## <a name="mobile-app-development-with-android-studio"></a>Vývoj mobilních aplikací s využitím sady Android Studio
V Azure Lab Services můžete nastavit testovací prostředí, abyste mohli naučit úvodní třídu pro vývoj mobilních aplikací. Tato třída se zaměřuje na mobilní aplikace pro Android, které mohou být publikovány na [obchod Google Play](https://play.google.com/store/apps).  Studenti se dozvíte, jak používat [Android Studio](https://developer.android.com/studio) k sestavování aplikací.  [Emulátor sady Visual Studio pro Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) slouží k místnímu testování aplikace.

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [Nastavení testovacího prostředí pro učení vývoje mobilních aplikací pomocí Android Studio](class-type-mobile-dev-android-studio.md).


## <a name="next-steps"></a>Další kroky

Viz následující články:

- [Nastavení testovacího prostředí zaměřeného na obsáhlý Learning při zpracování v přirozeném jazyce pomocí Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Skriptování prostředí v systému Linux](class-type-shell-scripting-linux.md)
- [Etické útoky](class-type-ethical-hacking.md)