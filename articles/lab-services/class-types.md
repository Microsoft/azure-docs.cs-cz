---
title: Příklady typů tříd v Azure Lab Services | Microsoft Docs
description: Poskytuje některé typy tříd, pro které můžete nastavit Labs pomocí Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7ebc89c040be732c230f0c3f54f68fb91466eb68
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436398"
---
# <a name="class-types-overview---azure-lab-services"></a>Přehled typů tříd – Azure Lab Services

Azure Lab Services vám umožní rychle nastavit prostředí pro laboratorní prostředí učebny v cloudu. Články v této části poskytují pokyny, jak nastavit několik typů cvičení pomocí Azure Lab Services.

## <a name="big-data-analytics"></a>Analýzy velkých objemů dat
Můžete nastavit testovací prostředí GPU pro výuku třídy analýzy velkých objemů dat. U tohoto typu třídy studenti zjistí, jak zpracovávat velké objemy dat, a použít algoritmy strojového a statistického učení k odvození přehledů dat. Klíčový cíl pro studenty je Naučte se používat nástroje pro analýzu dat, jako je open source softwarový balíček Apache Hadoop, který poskytuje nástroje pro ukládání, správu a zpracování velkých objemů dat. 

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [Nastavení testovacího prostředí pro analýzu velkých objemů dat pomocí nasazení Docker pro datovou platformu Hortonworks](class-type-big-data-analytics.md).

## <a name="database-management"></a>Správa databází
Koncepty databází jsou jedním z úvodních kurzů, které jsou v rámci vysokoškolského ministerstva v oblasti počítačové vědy. V Azure Lab Services můžete nastavit testovací prostředí pro třídu správy základní databáze. V testovacím prostředí můžete například nastavit šablonu virtuálního počítače s databázovým serverem [MySQL](https://www.mysql.com/) nebo serverem [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) .

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [Nastavení testovacího prostředí pro učení správy databází pro relační databáze](class-type-database-management.md).

## <a name="deep-learning-in-natural-language-processing"></a>Hluboké učení ve zpracování přirozeného jazyka
Můžete nastavit testovací prostředí zaměřené na obsáhlý Learning při zpracování přirozeného jazyka (NLP) pomocí Azure Lab Services. Zpracování přirozeného jazyka (NLP) je forma umělé Intelligence (AI), která umožňuje počítačům s překlady, rozpoznáváním řeči a dalšími možnostmi porozumění jazyku. Studenti, kteří přebírají třídu NLP, získají virtuální počítač se systémem Linux, aby se dozvěděli, jak použít síťové algoritmy neuronové pro vývoj modelů hloubkového učení, které se používají k analýze napsaného lidského jazyka.

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [Nastavení testovacího prostředí zaměřeného na obsáhlý Learning při zpracování v přirozeném jazyce pomocí Azure Lab Services](class-type-deep-learning-natural-language-processing.md).

## <a name="ethical-hacking"></a>Etický hacking
Můžete nastavit testovací prostředí pro třídu, která se zaměřuje na forenzníou stranu etických útoků. Testování průniku, praxe používané etickým komunitou pro hackery, nastává, když se někdo pokusí získat přístup k systému nebo síti a Ukázat tak ohrožení zabezpečení, která by škodlivý útočník mohl zneužít.

V etických třídách útoku Students se můžou seznámit s moderními technikami, které chrání před ohrožením zabezpečení. Každý Student získá virtuální počítač hostitele Windows serveru, který má dva vnořené virtuální počítače – jeden virtuální počítač s imagí [Metasploitable3](https://github.com/rapid7/metasploitable3) a další počítač s imagí [Kali Linux](https://www.kali.org/) . Virtuální počítač Metasploitable se používá pro zneužití účely.  Virtuální počítač se systémem Kali Linux poskytuje přístup k nástrojům potřebným ke spouštění úloh forenzní.

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [Nastavení testovacího prostředí pro výuku etické třídy útoků](class-type-ethical-hacking.md).

## <a name="matlab"></a>MATLAB
Program [MATLAB](https://www.mathworks.com/products/matlab.html), který představuje matricovou laboratoř, je programovací platforma z [MathWorks](https://www.mathworks.com/).  Kombinuje výpočetní výkon a vizualizaci díky oblíbeným nástrojům v oblastech matematických, inženýrských, fyzika a chemie.

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [Nastavení testovacího prostředí pro výuku programu MATLAB](class-type-matlab.md).

## <a name="project-lead-the-way-pltw"></a>Způsob vedoucího projektu (PLTW)
[Vedoucí projektu (PLTW)](https://www.pltw.org/) je nezisková organizace, která poskytuje podrobné přehledy PreK-12 v rámci USA v počítačové vědy, inženýrské a biolékařské vědě.  V každé třídě PLTW studenti využívají různé softwarové aplikace jako součást praktických výukových zkušeností.

Podrobné informace o tom, jak nastavit tyto typy testovacích prostředí, najdete v tématu Nastavení testovacích prostředí [pro projekt vedoucí způsob, jakým jsou třídy](class-type-pltw.md).

## <a name="python-and-jupyter-notebooks"></a>Poznámkové bloky Python a Jupyter
Můžete nastavit počítač šablony v Azure Lab Services s nástroji potřebnými k učení studentů, jak používat [poznámkové bloky Jupyter](http://jupyter-notebook.readthedocs.io). Jupyter Poznámkový blok je open source projekt, který umožňuje snadno kombinovat text a spustitelný zdrojový kód [Pythonu](https://www.python.org/) na jednom plátně s názvem Poznámkový blok. Spuštění poznámkového bloku má za následek lineární záznam vstupů a výstupů.  Tyto výstupy můžou zahrnovat text, tabulky informací, bodové a další.

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [Nastavení testovacího prostředí pro učení datových věd pomocí poznámkových bloků Python a Jupyter](class-type-jupyter-notebook.md).

## <a name="shell-scripting-on-linux"></a>Skriptování Shellu v Linuxu
Můžete nastavit laboratorní prostředí pro učení skriptování prostředí v systému Linux. Skriptování je užitečnou součástí správy systému, která správcům umožňuje vyhnout se opakovaným úlohám. V tomto ukázkovém scénáři třída pokrývá tradiční skripty bash a rozšířené skripty. Rozšířené skripty jsou skripty, které kombinují příkazy bash a Ruby. Tento přístup umožňuje Ruby předávat data kolem a bash příkazů pro interakci s prostředím.

Studenti, kteří přebírají tyto třídy skriptování, získají virtuální počítač se systémem Linux, aby se seznámili se základy systému Linux, a také se seznámili s skriptováním prostředí bash. Virtuální počítač se systémem Linux přichází s povoleným přístupem ke vzdálené ploše a s nainstalovanými [gedit](https://help.gnome.org/users/gedit/stable/) a [Visual Studio Codemi](https://code.visualstudio.com/) textovými editory.

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [skriptování prostředí v systému Linux](class-type-shell-scripting-linux.md).

## <a name="solidworks-computer-aided-design-cad"></a>SolidWorks – návrh s podporou počítačů (CAD)
Můžete nastavit testovací prostředí GPU, které poskytuje technickým studentům přístup k [SolidWorks](https://www.solidworks.com/).  SolidWorks poskytuje prostředí 3D CAD pro modelování objektů Solid.  Pomocí SolidWorks můžou technici snadno vytvářet, vizualizovat, simulovat a zdokumentovat jejich návrhy.

Podrobné informace o tom, jak tento typ testovacího prostředí nastavit, najdete v tématu [Nastavení testovacího prostředí pro inženýrské třídy pomocí SolidWorks](class-type-solidworks.md) .

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Nastavení testovacího prostředí zaměřeného na obsáhlý Learning při zpracování v přirozeném jazyce pomocí Azure Lab Services](class-type-deep-learning-natural-language-processing.md)
- [Skriptování Shellu v Linuxu](class-type-shell-scripting-linux.md)
- [Etický hacking](class-type-ethical-hacking.md)