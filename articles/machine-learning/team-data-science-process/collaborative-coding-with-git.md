---
title: Kódování pomocí Git - vědecké zpracování týmových dat založené na spolupráci
description: Postup vývoje spolupráci kódu pro projekty datových věd používat Git na agilní plánování.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 95285bf82fbf1582abcb5d95424b472d392a7a59
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134484"
---
# <a name="collaborative-coding-with-git"></a>Kódování založené na spolupráci s využitím Gitu

V tomto článku zjistíte, jak provádět vývoj spolupráci kódu pro projekty datových věd jako vývojářská platforma sdílený kód pomocí Gitu. Vysvětluje postup propojíte kódování aktivity a pracovní plánované v [Agilního vývoje](agile-development.md) a jak revizemi kódu.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Propojit pracovní položku s větví Git 

Služby Azure DevOps poskytuje pohodlný způsob, jak připojit pracovní položky (scénáře nebo úkol) s větví Git. To umožňuje scénáře nebo úloha přímý odkaz na kód s ním spojená. 

Pro připojení pracovní položky na novou větev, dvakrát klikněte na pracovní položku a v místním okně klikněte na tlačítko **vytvořit novou větev** pod **+ přidat odkaz**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Zadejte informace pro tuto novou větev, jako je například název větve, základní úložiště Git a pobočky. Úložiště Git, zvolili musí být úložišti v rámci stejného projektu patřící do pracovní položky. Základní větví může být hlavní větev nebo některé existující větev.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Dobrým postupem je vytvořit větev Git pro každé pracovní položky scénář. Pak pro každou pracovní položku úkolu, vytvoříte větev na základě větve scénáře. Uspořádání větví v hierarchické díky tomu, který odpovídá pro scénář úkolů vztahů je užitečný, pokud máte více lidem pracovat na různých scénářů stejného projektu nebo mít více lidem pracovat na různých úloh stejné scénáře. Konflikty můžete minimalizovat, když každý člen týmu pracuje na jinou větev a každý člen funguje na různých kódů nebo jiných artefaktů při sdílení větev. 

Následující obrázek znázorňuje doporučené strategii větvení pro TDSP. Nemusí potřebovat jako řada větví, jako jsou zde uvedeny, zvláště když máte jen jeden nebo dva lidé začali pracovat na stejném projektu, nebo pouze jeden člověk funguje v jednotlivých úlohách tohoto scénáře. Ale oddělení vývoje větev z hlavní větve je vždy vhodné. To může pomoct zabránit větve vydané verze se přerušila vývojových aktivit. Podrobnější popis modelu větev Git najdete v [A úspěšné Git větvení modelu](http://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Pokud chcete přepnout na větev, kterou chcete pracovat, spusťte následující příkaz v příkazovém prostředí (Windows nebo Linuxem). 

    git checkout <branch name>

Změna *< název větve\>*  k **hlavní** přepne zpět do **hlavní** větve. Po přepnutí do pracovní větve, můžete začít pracovat na pracovní položky, vývoj kódu nebo v dokumentaci artefakty potřebné k dokončení položky. 

Můžete také propojit pracovní položky do existující větev. V **podrobností** stránky pracovní položky, místo kliknutí na **vytvořit novou větev**, kliknete na **+ přidat odkaz**. Vyberte větev, který chcete propojit pracovní položku. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Můžete také vytvořit novou větev v Git Bashi příkazy. Pokud < název základní větve\> chybí, < nový název větve\> vychází _hlavní_ větve. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Práce na větev a potvrdit změny 

Nyní předpokládejme, že provedete některé změny *data\_ingestování* větev pro potřeby pracovní položky, jako je například přidávání souboru R na větev v místním počítači. Potvrdit R soubory přidané do větve pro tuto pracovní položku, pokud jste v této větvi v prostředí Git pomocí následujících příkazů Git:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Vytvořit žádost o přijetí změn na služby Azure DevOps 

Až budete připravení po pár potvrzení a nasdílení změn, ke sloučení do jeho základní větev aktuální větve můžete odeslat **žádosti o přijetí změn** na služby Azure DevOps. 

Přejděte na hlavní stránku vašeho projektu a klikněte na tlačítko **kód**. Vyberte větev, která se má sloučit a název úložiště Git, které chcete sloučit do větve. Klikněte na **žádosti o přijetí změn**, klikněte na tlačítko **nové žádosti o přijetí změn** vytvořit přezkoumání žádosti o přijetí změn před k její základní větve je sloučen práce na větvi.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Vyplňte některé popis této žádosti o přijetí změn, přidání revidujících a odeslat ji.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Revize a sloučení 

Při vytvoření žádosti o přijetí změn vaši recenzenti získat e-mailové oznámení ke kontrole žádosti o přijetí změn. Revidující se muset zkontrolovat, zda změny pracují, či nikoli a pokud je to možné otestovat změny s žadateli. Na základě jejich posouzení, revidující mohli schválit nebo odmítnout žádosti o přijetí změn. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

Po dokončení kontroly pracovní větve je sloučen do jeho základní větve kliknutím **Complete** tlačítko. Můžete odstranit pracovní větev, až se sloučila. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Potvrďte v levém horním rohu, který požadavek je označen jako **DOKONČENO**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Když přejdete zpátky do úložiště v rámci **kód**, jsou řekli, že byl jste přešli do hlavní větve.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Následující příkazy Gitu můžete také provést sloučení vaší pracovní větve na její základní větev a odstranit pracovní větev po sloučení:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>Další postup

[Spustit z úloh datových věd](execute-data-science-tasks.md) ukazuje způsob použití nástroje dokončit několik běžných úloh vědeckého zpracování jako je například interaktivní zkoumání dat, analýzy dat, vykazování a vytvoření modelu.

Postupy, které popisují všechny kroky v procesu pro **konkrétních scénářů** jsou také k dispozici. Jsou uvedena v seznamu a propojené s Miniatura popisy v [příklad návody](walkthroughs.md) článku. Jejich ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

