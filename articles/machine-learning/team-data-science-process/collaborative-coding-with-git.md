---
title: Kolaborativní kódování s Gitem – Proces vědecké ho spoje pro týmová data
description: Jak udělat vývoj kolaborativního kódu pro projekty datových věd pomocí Gitu s agilním plánováním.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0708e395eff90ff5b889c05f0fd5e7a98205c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721893"
---
# <a name="collaborative-coding-with-git"></a>Kódování založené na spolupráci s využitím Gitu

Tento článek popisuje, jak používat Git jako rámec pro vývoj kolaborativního kódu pro projekty datových věd. Článek popisuje, jak propojit kód v Azure Repos na [agilní vývoj](agile-development.md) pracovních položek v Azure Boards, jak provést revize kódu a jak vytvořit a sloučit žádosti o přijetí změn.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Propojení pracovní položky s větev Azure Repos 

Azure DevOps poskytuje pohodlný způsob, jak připojit Azure Boards User Story nebo task pracovní položku s azure úložiště úložiště úložiště Úložiště Azure. Příběh uživatele nebo úkol můžete propojit přímo s kódem, který je k němu přidružen. 

Chcete-li připojit pracovní položku k nové větvi, vyberte **položku Akce** se třemi tečkami (**...**) vedle pracovní položky a v místní nabídce přejděte na novou **větev**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

V **dialogovém okně Vytvořit větev** zadejte nový název větve a základní úložiště a větev Azure Repos Git. Základní úložiště musí být ve stejném projektu Azure DevOps jako pracovní položka. Základní větev může být hlavní větev nebo jiná existující větev. Vyberte **Vytvořit větev**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Můžete také vytvořit novou větev pomocí následujícího příkazu Git bash ve Windows nebo Linuxu:

```bash
git checkout -b <new branch name> <base branch name>

```
Pokud nezadáte název \<základní větve>, bude nová `master`větev založena na . 

Chcete-li přepnout do pracovní větve, spusťte následující příkaz: 

```bash
git checkout <working branch name>
```

Po přepnutí do pracovní větve můžete začít vyvíjet artefakty kódu nebo dokumentace k dokončení pracovní položky. Spuštění `git checkout master` vás přepne zpět do větve. `master`

Je vhodné vytvořit větev Git pro každou pracovní položku příběhuživatele. Potom pro každou pracovní položku úkolu můžete vytvořit větev založenou na větvi Příběh uživatele. Uspořádejte větve v hierarchii, která odpovídá relaci Příběh u uživatelů a úkol, pokud více lidí pracuje na různých uživatelských scénářích pro stejný projekt nebo na různých úkolech pro stejný uživatelský scénář. Konflikty můžete minimalizovat tím, že každý člen týmu pracuje na jiné větvi nebo na jiném kódu nebo jiných artefaktech při sdílení větve. 

Následující diagram znázorňuje doporučenou strategii větvení pro TDSP. Nemusíte potřebovat tolik větví, kolik je zde uvedeno, zejména pokud na projektu pracuje pouze jeden nebo dva lidé nebo pouze jedna osoba pracuje na všech úkolech příběhu uživatele. Ale oddělení vývojové větve od hlavní větve je vždy dobrým postupem a může pomoci zabránit přerušení větve uvolnění vývojovými aktivitami. Úplný popis modelu větve Git najdete [v tématu Úspěšný model větvení Gitu](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Můžete také propojit pracovní položku s existující větev. Na stránce **Podrobnosti** pracovní položky vyberte **Přidat odkaz**. Pak vyberte existující větev, ke které chcete propojit pracovní položku, a vyberte **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>Práce na větvi a potvrzení změn 

Po provedení změny pro pracovní položku, jako je například přidání `script` souboru skriptu R do větve místního počítače, můžete potvrdit změnu z místní větve na nadřazenou pracovní větev pomocí následujících příkazů Git bash:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Vytvoření žádosti o přijetí změn

Po jednom nebo více potvrzení a odešle, když jste připraveni sloučit aktuální pracovní větev do své základní větve, můžete vytvořit a odeslat *žádost o přijetí vyžádat* v Azure Repos. 

Na hlavní stránce projektu Azure DevOps přejděte na**požadavky na vyžádat si o přijetí vyžádat** **v** > levém navigačním panelu. Pak vyberte některou z nových tlačítek **žádosti o přijetí vzbuzování** nebo odkaz **Vytvořit žádost o přijetí vzbuzování.**

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Na obrazovce **Nový požadavek na přijetí změn** přejděte v případě potřeby do úložiště Git a větve, do které chcete sloučit změny. Přidejte nebo změňte jakékoli další informace, které chcete. V části **Recenzenti**přidejte jména recenzentů a vyberte **Vytvořit**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>Kontrola a sloučení

Po vytvoření žádosti o přijetí vyžádat si recenzenti obdrží e-mailové oznámení, aby žádost o přijetí informací zkontrolovali. Recenzenti otestují, zda změny fungují, a pokud možno změny zkontrolujte s žadatelem. Recenzenti mohou na základě svého posouzení provádět komentáře, požadovat změny a schválit nebo odmítnout žádost o přijetí změn. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Poté, co recenzenti změny schválí, můžete vy nebo někdo jiný s oprávněními ke sloučení sloučit pracovní větev do její základní větve. Vyberte **Dokončit**a pak v dialogovém okně **Dokončit žádost o přijetí dál** vyberte Dokončit **sloučení.** Pracovní větev můžete po sloučení odstranit. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Zkontrolujte, zda je požadavek označen jako **DOKONČENÝ**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Když se v levé navigaci vrátíte do **reposu,** uvidíte, že jste `script` od odstranění větve přešli do hlavní větve.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Můžete také použít následující příkazy Git `script` bash sloučit pracovní větev do její základní větve a odstranit pracovní větev po sloučení:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Další kroky

[Provádění úloh datové vědy](execute-data-science-tasks.md) ukazuje, jak pomocí nástrojů dokončit několik běžných úloh datové vědy, jako je například interaktivní zkoumání dat, analýza dat, vytváření sestav a vytváření modelů.

[Příklad návody](walkthroughs.md) uvádí návody konkrétní scénáře s odkazy a popisy miniatur. Propojené scénáře ilustrují, jak kombinovat cloudové a místní nástroje a služby do pracovních postupů nebo kanálů a vytvářet inteligentní aplikace. 

