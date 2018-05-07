---
title: Spolupráce kódování s Gitem - Azure Machine Learning | Microsoft Docs
description: Tom, jak provést vývoj spolupráce kódu pro projekty vědecké účely dat pomocí Git agilního plánování.
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
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: 55b47362f334d7366b6cc537a96dc7d046c23733
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="collaborative-coding-with-git"></a>Kódování založené na spolupráci s využitím Gitu

V tomto článku jsme popisují postup vývoj spolupráce kódu pro projekty vědecké účely dat pomocí Git jako sdílený kód rozhraní pro vývoj. Vysvětluje postup propojení tyto aktivity k práci plánováno v kódování [Agile vývoj](agile-development.md) a postup code recenze.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Propojit pracovní položky s větev Git 

Služby VSTS poskytuje pohodlný způsob, jak připojit pracovní položky (článek nebo úloh) Git větev. To vám umožní propojit článek nebo úloha přímo na kód s ním spojená. 

Pro připojení pracovní položku k nové větve, dvakrát klikněte na pracovní položky a v místním okně, klikněte na tlačítko **vytvoření nové větve** pod **+ přidat odkaz**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Zadejte informace pro toto nové větve, jako je například název větve, základní úložiště Git a větev. Zvolené úložiště Git musí být v rámci stejné týmový projekt, který pracovní položka patří do úložiště. Základní větev může být hlavní větve nebo některé stávající firemní pobočky.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Dobrým postupem je vytvoření větve Git pro každý scénář pracovní položku. Potom pro každou položku pracovních úloh, vytvoříte větev podle scénáře firemní pobočky. Uspořádání větve tento hierarchický takovým způsobem, který odpovídá vztahy scénáře úloh je užitečné, pokud máte více lidí pracujících na různých scénářů stejného projektu, nebo máte několik lidí pracujících na různých úloh pro stejný článek. Konflikty můžete minimalizovat, když každý člen týmu funguje na různých větve a každý člen funguje na různých kódů nebo jiných artefakty při sdílení větev. 

Následující obrázek znázorňuje doporučené větvení strategie pro TDSP. Nemusí potřebovat mnoho větví jako zde se zobrazují, zejména pokud máte pouze jednu nebo dvě lidí pracujících na stejném projektu, nebo na všechny úlohy scénáře funguje jenom jedna osoba. Ale vždy oddělení vývoj větev z hlavní větve je vhodné. To může pomoct zabránit přerušení aktivitami vývoj verze větev. Podrobnější popis modelu větve Git naleznete v [A úspěšné Git vytvoření větve modelu](http://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Chcete-li přepnout do větve, kterou chcete pracovat na, spusťte následující příkaz v prostředí příkazu (Windows nebo Linux). 

    git checkout <branch name>

Změna *< názvu větve\>*  k **hlavní** přepínače zpět do **hlavní** firemní pobočky. Po přepnutí na pracovní větev, můžete spustit funguje na pracovní položky, vývoj kódu nebo v dokumentaci artefakty potřebné k dokončení položky. 

Můžete také propojit pracovní položky do existující větve. V **podrobností** stránku pracovní položky, místo kliknutí na **vytvoření nové větve**, kliknutí na tlačítko **+ přidat odkaz**. Pak vyberte větve, který chcete propojit pracovních položek. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Můžete také vytvořit nové větve v Git Bash příkazy. Pokud < název základní pobočky\> chybí, < nový název větve\> je založena na _hlavní_ firemní pobočky. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Práce na větev a provést změny 

Nyní předpokládejme, že provedete některé změny *data\_přijímání* větve pro pracovní položky, jako je například přidávání soubor R na větev v místním počítači. Soubory R přidané do větve pro tuto pracovní položku můžete potvrdit, zadaný jste uvedené pobočky v prostředí vaší Git, pomocí následujících příkazů Git:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Vytvořit žádost o přijetí změn na služby VSTS 

Až budete připravení po několik potvrzení a oznámení, sloučit aktuální větev do jeho základní větve, můžete odeslat **žádosti o přijetí změn** na serveru služby VSTS. 

Přejděte na hlavní stránce týmového projektu a klikněte na tlačítko **kód**. Vyberte větev slučované a název úložiště Git, který chcete sloučit větev do. Pak klikněte na tlačítko **žádosti o přijetí změn**, klikněte na tlačítko **nové žádosti o přijetí změn** vytvořit kontrolu žádost o přijetí změn, před prací na větev sloučena do jeho základní větve.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Vyplňte některé popis o tuto žádost o přijetí změn, přidání revidujících a poslat ho.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Přečtěte si a sloučení 

Při vytváření žádosti o přijetí změn recenzenti získat e-mail s oznámením ke kontrole žádosti o přijetí změn. Kontroloři muset zkontrolujte, jestli změny pracují nebo Ne a pokud je to možné otestovat změn pomocí žadatel. Na základě jejich posouzení, můžete kontroloři schválit nebo odmítnout žádosti o přijetí změn. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

Po dokončení kontrola je pracovní větev sloučena do jeho základní větve kliknutím **Complete** tlačítko. Můžete se rozhodnout odstranit pracovní větev po se sloučil. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Potvrďte v levém horním rohu, který žádost je označen jako **DOKONČENO**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Při návratu zpět do úložiště v rámci **kód**, výzva, aby vám byla přepnuta do hlavní větve.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Můžete taky následující příkazy Gitu sloučení pracovní větev do jeho základní větve a po slučování odstraňte pracovní větev:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>Další postup

[Spuštění úloh vědecké účely data](execute-data-science-tasks.md) ukazuje způsob použití nástroje dokončit několik běžné úkoly vědecké účely dat, jako je například interaktivní zkoumání dat, analýzy dat, vytváření sestav a vytváření modelu.

Návody, které ukazují všechny kroky v procesu pro **konkrétních scénářů** jsou také uvedeny. Jsou uvedena v seznamu a propojené s miniatur popisy v [příklad návody](walkthroughs.md) článku. Se ukazují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu vytvoření inteligentního aplikace. 

