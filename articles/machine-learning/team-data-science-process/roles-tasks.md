---
title: Tým datové vědy proces role a úkoly – Azure | Microsoft Docs
description: Přehled klíčové komponenty, role pracovníky a přidružené úlohy pro datové vědy týmového projektu.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: deguhath
ms.openlocfilehash: 00d8761a7afe4b727c04626c0ba7c032b2277f48
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="team-data-science-process-roles-and-tasks"></a>Tým proces vědecké účely dat rolí a úloh

Proces vědecké účely Team dat je architektura vyvinuté společností Microsoft, který obsahuje strukturovaných metodiku efektivně vytvářet řešení prediktivní analýzy a inteligentní aplikace. Tento článek popisuje role klíče pracovníky a jejich přidružených úloh, které jsou zpracovávány vědecké zpracování dat team standardizace tohoto postupu. 

Tento úvod odkazy na návodů, které poskytují pokyny o tom, jak nastavit prostředí TDSP pro celého datového vědecké účely skupin, týmů vědecké účely dat a projektů. Společnost Microsoft poskytuje podrobné pokyny pomocí Visual Studio Team Services (VSTS) v kurzů k jako naše hostování kód platformy a nástroje pro agilní plánování team úlohy správy, řízení přístupu a spravovat úložiště. 

Můžete také moci tyto informace slouží k implementaci TDSP na vlastní kód hostování a založených na agilním plánování nástroj. 

## <a name="structures-of-data-science-groups-and-teams"></a>Struktury skupin vědecké účely dat a týmy
Funkce vědecké účely dat v podnicích může být často uspořádány v následující hierarchie:

1. ***Vědecké účely data skupiny/s***

2. ***Data vědecké účely team/s v rámci skupiny/s***

V této struktury nebudou jsou skupiny a tým zájemce. Obvykle se provádí projektu vědecké účely data týmem vědecké účely data, která se může skládat z projektu zájemce (pro úlohy správy a zásady správného řízení projektu) a datových vědců nebo techniky (jednotlivé přispěvatele / technické pracovníky), kteří budou spuštěny vědecké zpracování dat data technici částí projektu a. Před provádění nastavení a zásady správného řízení je potřeba zájemců skupiny, tým nebo projektu.

## <a name="definition-of-four-tdsp-roles"></a>Definice čtyři TDSP rolí
Za předpokladu výše jsme určili čtyři odlišné role pro naši pracovníci týmu:

1. ***Správce skupiny***. Správce skupiny je správce celé datové vědy jednotky v podniku. Datové vědy jednotky může mít několik týmů, z nichž každý pracuje na více projektů pocházejícími jedinečných obchodních dat vědecké účely. Správce skupiny může delegovat na náhradní jejich úloh, ale úkoly spojené s rolí se nemění.

2. ***Tým realizace***. Vedoucí týmu spravuje tým v jednotce vědecké účely dat organizace. Tým se skládá z několika datových vědců. Pro datové vědy jednotku s pouze malý počet datových vědců může být správce skupiny a vést Team stejná osoba.

3. ***Projekt realizace***. Projekt realizace spravuje denní aktivity jednotlivých datových vědců na konkrétní datové vědy projektu.

4. ***Projekt jednotlivých Přispěvatel***. Vědecký pracovník dat, obchodní analytik, analýzu dat, architekti atd. Jednotlivé Přispěvatel projektu provede projektu vědecké účely data. 


**[AZURE.NOTE]**: V závislosti na strukturu v podniku jedna osoba může hrát více než jedné role nebo může být víc než jedna osoba funguje v roli. Často to může být velká písmena v malé podniky a podniky s malým počtem pracovníky ve své organizaci vědecké účely data.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Potřeba provést čtyři pracovníky

Následující obrázek znázorňuje úkoly nejvyšší úrovně pro pracovníky role při přijetí a provádění procesu Team dat vědecké účely jako conceptualized společností Microsoft. 

![Přehled rolí a úloh](./media/roles-tasks/overview-tdsp-top-level.png)

Toto schéma a následující, podrobnější přehled úloh, které jsou přiřazeny ke každé roli v TDSP by měly pomoci zvolit odpovídající kurz založený na vaše odpovědnosti v organizaci.

>[AZURE.NOTE] V následujících pokynech ukážeme postup, jak nastavit prostředí TDSP a dokončení úlohy vědecké účely dalších dat v aplikaci Visual Studio Team Services (služby VSTS). Určíme jak provést tyto úlohy s služby VSTS, protože se jedná, co se používá k implementaci TDSP ve společnosti Microsoft. Služby VSTS umožňuje spolupráci díky integraci správy pracovních položek, které sledují úlohy a kód hostitelské služby používána ke sdílení nástrojů, uspořádání verze a na základě rolí zabezpečení. Budete moci vybrat jiné platformy, pokud dáváte přednost, implementovat úlohy ohraničená TDSP. Ale v závislosti na platformě, některé funkce, které jsme využít ze služby VSTS nemusí být k dispozici. 
>
>Používáme [datové vědy virtuálního počítače (DSVM)](http://aka.ms/dsvm) na Azure cloud jako analytics plochy s několik nástrojů vědecké účely oblíbených data předem nakonfigurované a integraci s různými software společnosti Microsoft a službám Azure. DSVM nebo jiné vývojové prostředí můžete použít k implementaci TDSP. 


## <a name="group-manager-tasks"></a>Správce skupiny úlohy

Správce skupiny, nebo určený správce systému TDSP přijmout TDSP jsou prováděny následující úlohy:

- Vytvoření **skupinový účet** v kód hostování platformy (například Githubu, Git, služby VSTS nebo jiné)
- Vytvoření **úložiště šablon projektu** na účet skupiny a z úložiště šablon projektu vyvinutými týmem Microsoft TDSP počáteční hodnoty. Poskytuje úložiště šablon projektu TDSP od společnosti Microsoft **standardizované adresářovou strukturu** včetně adresáře pro data, kód a dokumenty a poskytuje sadu **standardizované šablony dokumentů** který povede procesu vědecké účely efektivní data. 
- Vytvoření **nástroj úložiště**a jeho počáteční hodnoty z úložiště nástroj vyvinutými týmem Microsoft TDSP. Úložiště nástroj TDSP od společnosti Microsoft poskytuje sadu užitečné nástroje zefektivnit práci při vědecký pracovník data, včetně nástrojů pro interaktivní zkoumání dat, analýzu a vytváření sestav a pro směrný plán, modelování a vytváření sestav.
- Nastavit **zásad řízení zabezpečení** tyto dvě úložišť na vašem účtu skupiny.  

Podrobné pokyny, najdete v části [úlohy správce skupiny pro tým datové vědy](group-manager-tasks.md). 


## <a name="team-lead-tasks"></a>Tým realizace úlohy

Vést Team (nebo správce a určené týmového projektu) přijmout TDSP jsou prováděny následující úlohy:

- Pokud je vybrán služby VSTS být hostující platforma kódu pro verze a spolupráci, vytvořte **týmového projektu** na serveru služby VSTS skupiny. Tato úloha, jinak můžete přeskočena.
- Vytvořte **týmového projektu šablony úložiště** pod týmového projektu a počáteční hodnoty z úložiště šablon projektu skupiny nastavit tak, že vaše skupina správce nebo delegovaného správce. 
- Vytvořte **úložiště pro nástroj team**a přidejte nástroje team konkrétní do úložiště. 
- (Volitelné) Vytvoření **[úložiště Azure file](https://azure.microsoft.com/services/storage/files/)** použije k uložení datové prostředky, které mohou být užitečné pro celý tým. Ostatní členové týmu lze připojit tento soubor úložiště sdíleného cloudu na jejich analýzy počítačů.
- (Volitelné) Připojte úložiště Azure file **datové vědy virtuální počítač** (DSVM) týmu vést a přidejte datové prostředky na něm.
- Nastavit **řízení zabezpečení** nástrojem přidávání členů týmu a nakonfigurujte svá oprávnění. 

Podrobné pokyny, najdete v části [Team vést úlohy pro tým datové vědy](team-lead-tasks.md).  


## <a name="project-lead-tasks"></a>Úkoly realizace projektu

Pomocí projektu vést přijmout TDSP jsou prováděny následující úlohy:

- Vytvoření **projektu úložiště** pod týmového projektu a počáteční hodnoty z týmu projektu šablony úložiště. 
- (Volitelné) Vytvoření **úložiště Azure file** který se má použít k ukládání datových prostředků projektu. 
- (Volitelné) Připojte úložiště Azure file **datové vědy virtuální počítač** (DSVM) projektu vést a přidejte projekt datové prostředky na něm.
- Nastavit **řízení zabezpečení** nástrojem přidávání členů projektu a nakonfigurujte svá oprávnění. 

Podrobné pokyny, najdete v části [projektu vést úlohy pro tým datové vědy](project-lead-tasks.md). 

## <a name="project-individual-contributor-tasks"></a>Jednotlivé Přispěvatel úkoly projektu

Podle jednotlivých Přispěvatel projektu (obvykle Data vědecký pracovník) umožňuje provádět projekt vědecké účely dat pomocí TDSP jsou prováděny následující úlohy:

- Klon **projektu úložiště** nastavit tak, že realizace projektu. 
- (Volitelné) Připojit sdílený **úložiště Azure file** týmu a projekt na jejich **datové vědy virtuální počítač** (DSVM).
- Spusťte projekt. 

 
Podrobné pokyny pro do projektu Startovní, najdete v části [projektu jednotlivé přispěvatele pro tým datové vědy](project-ic-tasks.md). 


## <a name="data-science-project-execution"></a>Spuštění projektu dat vědecké účely
 
Pomocí následujících příslušné sady pokynů, můžete vytvořit datových vědců realizace projektu a týmů pracovních položek pro sledování všechny úlohy a fází, které potřebuje projektu na jeho začátku na konec. Pomocí git také podporuje spolupráci mezi datových vědců a zajišťuje, aby artefakty vygenerována během provádění projektu verze řízené a sdílet všichni členové projektu.

Pokynů pro spuštění projektu byly vyvinuty podle předpokladu, že pracovní položky a git, které jsou úložiště na služby VSTS projektu. Pomocí služby VSTS pro obě umožňuje propojit váš pracovní položky pomocí Git větve úložišť vašeho projektu. Tímto způsobem můžete snadno sledovat, co bylo provedeno pro pracovní položku. 

Následující obrázek znázorňuje tento pracovní postup pro použití TDSP spuštění projektu.

![Spuštění projektu typické dat vědecké účely](./media/roles-tasks/overview-project-execute.png)

Pracovní postup obsahuje kroky, které je možné rozdělit na tři aktivity:

- Plánování (projektu vést) sprintu
- Vývoj artefakty na větvích git adres pracovních položek (vědecký pracovník dat)
- Revize kódu a slučování větví s hlavní větve (vést projektu nebo ostatní členové týmu)

Podrobné pokyny v pracovním postupu spuštění projektu, najdete v části [provádění datové vědy projekty](project-execution.md).

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na podrobnější popis jednotlivých rolí a úloh, které jsou definované procesu Team dat vědecké účely:

- [Úlohy správce skupiny pro data tým vědecké účely](group-manager-tasks.md)
- [Tým realizace úlohy pro data tým vědecké účely](team-lead-tasks.md)
- [Úlohy realizace projektu pro data tým vědecké účely](project-lead-tasks.md)
- [Jednotlivé přispěvatele projektu pro data tým vědecké účely](project-ic-tasks.md)