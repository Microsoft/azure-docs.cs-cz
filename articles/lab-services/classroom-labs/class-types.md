---
title: Příklady typů tříd ve službě Azure Lab Services | Dokumenty společnosti Microsoft
description: Obsahuje některé typy tříd, pro které můžete nastavit testovací prostředí pomocí služby Azure Lab Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79296723"
---
# <a name="class-types-overview---azure-lab-services"></a>Přehled typů tříd – Azure Lab Services

Azure Lab Services umožňuje rychle nastavit laboratorní prostředí učebny v cloudu. Články v této části obsahují pokyny, jak nastavit několik typů testovacích prostředí pro učebny pomocí služby Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Hluboké učení při zpracování přirozeného jazyka

Pomocí služby Azure Lab Services můžete nastavit testovací prostředí zaměřené na hloubkové učení v oblasti zpracování přirozeného jazyka (NLP). Zpracování přirozeného jazyka (NLP) je forma umělé inteligence (AI), která umožňuje počítačům s překladem, rozpoznáváním řeči a dalšími možnostmi porozumění jazykům. Studenti, kteří používají třídu NLP, získají virtuální počítač (VM) linuxového, aby se naučili, jak používat algoritmy neuronových sítí k vývoji modelů hlubokého učení, které se používají pro analýzu psaného lidského jazyka.

Podrobné informace o tom, jak nastavit tento typ testovacího prostředí, najdete [v tématu Nastavení testovacího prostředí zaměřeného na hloubkové učení ve zpracování přirozeného jazyka pomocí služby Azure Lab Services](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Skriptování Shellu v Linuxu

Můžete nastavit laboratoř učit shell skriptování na Linuxu. Skriptování je užitečnou součástí správy systému, která správcům umožňuje vyhnout se opakovaným úlohám. V tomto ukázkovém scénáři třída zahrnuje tradiční bash skripty a rozšířené skripty. Rozšířené skripty jsou skripty, které kombinují bash příkazy a Ruby. Tento přístup umožňuje Ruby předávat data a bash příkazy pro interakci s shell.

Studenti, kteří tyto skriptovací třídy, získají virtuální stroj Linux, aby se naučili základy Linuxu, a také se seznámili s skriptováním bash shellu. Virtuální počítač S Linuxem je dodáván s povoleným přístupem ke vzdálené ploše a s nainstalovanými textovými editory [gedit](https://help.gnome.org/users/gedit/stable/) a [Visual Studio Code.](https://code.visualstudio.com/)

Podrobné informace o tom, jak nastavit tento typ testovacího prostředí, naleznete v [tématu Shell skriptování na Linuxu](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Etický hacking

Můžete nastavit laboratoř pro třídu, která se zaměřuje na forenzní stránku etického hackingu. Penetrační testování, což je praxe používaná komunitou etických hackerů, nastává, když se někdo pokusí získat přístup k systému nebo síti, aby prokázal chyby zabezpečení, které může útočník se zlými úmysly zneužít.

V etické hacking třídy, studenti se mohou naučit moderní techniky pro obranu proti zranitelnosti. Každý student dostane virtuální počítač hostitele Windows Serveru, který má dva vnořené virtuální počítače – jeden virtuální stroj s obrazem [Metasploitable3](https://github.com/rapid7/metasploitable3) a další počítač s obrazem [Kali Linux.](https://www.kali.org/) Metasploitable virtuální počítač se používá pro využití účely.  Virtuální stroj Kali Linux poskytuje přístup k nástrojům potřebným k provádění forenzních úloh.

Podrobné informace o tom, jak nastavit tento typ laboratoře, najdete v [tématu Nastavení laboratoře pro výuku etické hacking třídy](class-type-ethical-hacking.md).

## <a name="database-management"></a>Správa databází
Databáze pojmy jsou jedním z úvodních kurzů vyučovaných ve většině oddělení informatiky na vysoké škole. Můžete nastavit testovací prostředí pro třídu správy základních databází ve službě Azure Lab Services. Například můžete nastavit šablonu virtuálního počítače v testovacím prostředí s databázovým serverem [MySQL](https://www.mysql.com/) nebo serverem [SQL Server 2019.](https://www.microsoft.com/sql-server/sql-server-2019)

Podrobné informace o tom, jak nastavit tento typ testovacího prostředí, naleznete v [tématu Nastavení testovacího prostředí pro výuku správy databází pro relační databáze](class-type-database-management.md).

## <a name="python-and-jupyter-notebooks"></a>Notebooky Python a Jupyter
Ve službách Azure Lab Services můžete nastavit šablonový počítač s nástroji potřebnými k tomu, abyste studenty naučili používat [poznámkové bloky Jupyter](http://jupyter-notebook.readthedocs.io). Jupyter Notebooks je open source projekt, který vám umožní snadno kombinovat bohatý text a spustitelný zdrojový kód [Pythonu](https://www.python.org/) na jednom plátně nazývaném notebook. Spuštění poznámkového bloku má za následek lineární záznam vstupů a výstupů.  Tyto výstupy mohou zahrnovat text, tabulky informací, bodové obrázky a další.

Podrobné informace o tom, jak nastavit tento typ testovacího prostředí, najdete v [tématu Nastavení testovacího prostředí pro výuku datové vědy pomocí Pythonu a Poznámkových bloků Jupyter](class-type-jupyter-notebook.md).

## <a name="mobile-app-development-with-android-studio"></a>Vývoj mobilních aplikací s využitím sady Android Studio
Můžete nastavit testovací prostředí ve službě Azure Lab Services učit úvodní třídy vývoje mobilních aplikací. Tato třída se zaměřuje na mobilní aplikace pro Android, které mohou být publikovány v [Obchodě Google Play](https://play.google.com/store/apps).  Studenti se učí, jak používat [Android Studio](https://developer.android.com/studio) k vytváření aplikací.  [Visual Studio Emulátor pro Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) se používá k testování aplikace místně.

Podrobné informace o tom, jak nastavit tento typ testovacího prostředí, najdete v [tématu Nastavení testovacího prostředí pro výuku vývoje mobilních aplikací pomocí sady Android Studio](class-type-mobile-dev-android-studio.md).


## <a name="next-steps"></a>Další kroky

Viz následující články:

- [Nastavení testovacího prostředí zaměřeného na hloubkové učení při zpracování přirozeného jazyka pomocí služby Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Skriptování Shellu v Linuxu](class-type-shell-scripting-linux.md)
- [Etický hacking](class-type-ethical-hacking.md)