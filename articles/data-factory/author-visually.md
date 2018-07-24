---
title: Vizuální vytváření obsahu v Azure Data Factory | Dokumentace Microsoftu
description: Další informace o použití vizuálního vytváření ve službě Azure Data Factory
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: shlo
ms.openlocfilehash: 655a6ab2960047cde50bec2953015283ca8577f0
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214846"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Vizuální vytváření obsahu v Azure Data Factory
Azure Data Factory uživatelské rozhraní rozhraní (UX) umožňuje vizuálně vytvoříte a nasadíte prostředky pro službu data factory bez nutnosti psát jakýkoli kód. Můžete přetáhnout aktivity na plátno kanálu, provádějte testovací běhy, využívejte iterativní ladění a nasadit a monitorovat spuštění kanálů. Existují dvě metody pro provádění vizuálního vytváření pomocí uživatelského rozhraní:

- Autor přímo ve službě Data Factory.
- Autor integrace Visual Studio Team Services (VSTS) Git pro spolupráci, správy zdrojového kódu nebo Správa verzí.

## <a name="author-directly-with-the-data-factory-service"></a>Autor přímo ve službě Data Factory
Vizuální vytváření obsahu pomocí služby Data Factory se liší od vizuálním vytváření s VSTS dvěma způsoby:

- Služba Data Factory neobsahuje úložiště pro ukládání entity JSON pro vaše změny.
- Služba Data Factory není optimalizovaná pro spolupráci a správu verzí.

![Konfigurace služby Data Factory ](media/author-visually/configure-data-factory.png)

Při použití uživatelského rozhraní **plátno pro vytváření obsahu** vytvořit přímo ve službě Data Factory, pouze **Publikovat vše** režim je k dispozici. Všechny změny, které provedete, se publikují přímo do služby Data Factory.

![Režim publikování](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Vytváření s využitím integrace VSTS Git
Pro práci na vaše kanály data factory vizuálním vytváření s využitím integrace VSTS Git podporuje správu zdrojového kódu a spolupráci. Datové továrny můžete přidružit účet úložiště VSTS Git pro správy zdrojového kódu, spolupráci, správu verzí a tak dále. Jeden účet úložiště VSTS Git můžete mít více úložišť, ale může být přidružený pouze jeden datový objekt pro vytváření úložiště VSTS Git. Pokud nemáte účet VSTS nebo úložišti, postupujte podle [tyto pokyny](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) k vytvoření vašich prostředků.

> [!NOTE]
> Skript a datových souborů můžete uložit v úložišti GIT služby VSTS. Ale budete muset ručně nahrání souborů do služby Azure Storage. Kanál služby Data Factory není automaticky odeslat soubory skriptu nebo data uložená v úložišti GIT služby VSTS do služby Azure Storage.

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>Konfigurace úložiště VSTS Git s Azure Data Factory
Úložiště VSTS GIT můžete nakonfigurovat pomocí služby data factory pomocí dvou metod.

#### <a name="method1"></a> Metoda konfigurace 1: Stránka Začínáme

Ve službě Azure Data Factory, přejděte **pusťme se do práce** stránky. Vyberte **konfigurace úložiště kódu**:

![Konfigurace úložiště kódu VSTS](media/author-visually/configure-repo.png)

**Nastavení úložiště** otevře se podokno konfigurace:

![Konfigurovat nastavení úložiště kódu](media/author-visually/repo-settings.png)

V podokně se zobrazí nastavení úložiště VSTS následovně:

| Nastavení | Popis | Hodnota |
|:--- |:--- |:--- |
| **Typ úložiště** | Typ úložištěm kódu VSTS.<br/>**Poznámka:**: GitHub v tuto chvíli nepodporuje. | Visual Studio Team Services Git |
| **Azure Active Directory** | Název tenanta Azure AD. | <your tenant name> |
| **Účet služby Visual Studio Team Services** | Název účtu VSTS. Můžete najít název účtu VSTS na `https://{account name}.visualstudio.com`. Je možné [přihlásit ke svému účtu VSTS](https://www.visualstudio.com/team-services/git/) pro přístup k profilu Visual Studio a zobrazit projekty a úložiště. | <your account name> |
| **ProjectName** | Název projektu VSTS. Můžete najít na název projektu VSTS `https://{account name}.visualstudio.com/{project name}`. | <your VSTS project name> |
| **RepositoryName** | Název úložiště kódu VSTS. Úložiště Git pro správu zdrojového kódu, jak se projekt rozrůstá obsahují projekty VSTS. Můžete vytvořit nové úložiště nebo použít existující úložiště, který je již ve vašem projektu. | <your VSTS code repository name> |
| **Spolupráce větve** | Větvi VSTS spolupráci, které se použije pro publikování. Ve výchozím nastavení je to `master`. Toto nastavení změňte v případě, že chcete publikovat prostředky z jiné větve. | <your collaboration branch name> |
| **Kořenová složka** | Kořenové složky ve vaší větvi spolupráci VSTS. | <your root folder name> |
| **Importovat do úložiště stávající prostředky Data Factory** | Určuje, jestli se má naimportovat stávající prostředky data factory z uživatelského rozhraní **plátno pro vytváření obsahu** do úložiště VSTS Git. Vyberte pole pro import prostředky data factory do přidružené úložiště Git ve formátu JSON. Tato akce exportuje každého prostředku zvlášť (to znamená, propojené služby a datové sady se exportují do samostatných JSONs). Když toto políčko není zaškrtnuto, nenaimportují se existující prostředky. | Vybrané (výchozí) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metoda konfigurace 2: UX plátno pro vytváření obsahu
V uživatelském prostředí Azure Data Factory **plátno pro vytváření obsahu**, vyhledejte svou datovou továrnu. Vyberte **služby Data Factory** rozevírací nabídky a pak vyberte **konfigurace úložiště kódu**.

Otevře se podokno konfigurace. Podrobnosti o nastavení konfigurace najdete v popisech v <a href="#method1">metody konfigurace 1</a>.

![Konfigurovat nastavení úložiště kódu pro vytváření uživatelského rozhraní](media/author-visually/configure-repo-2.png)

#### <a name="switch-to-a-different-git-repo"></a>Přepnout na jiné úložiště Git

Přepnout na jiné úložiště Git, vyhledejte ikonu v pravém horním rohu na stránce Přehled služby Data Factory, jak je znázorněno na následujícím snímku obrazovky. Pokud nevidíte ikonu, vymažte mezipaměť místní prohlížeče. Vyberte ikonu a odebere přidružení aktuální úložiště.

Po odebrání přidružení k aktuální úložiště, můžete nakonfigurovat nastavení Git použít jiné úložiště. Pak můžete importovat stávající prostředky Data Factory do nového úložiště.

![Odebere přidružení aktuálního úložiště Git.](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>Správa verzí
Systémy správy verzí (označované také jako _správy zdrojového kódu_) umožňují vývojářům spolupráce na kódu a sledování změn provedených na kód základní. Správy zdrojového kódu je to důležitý nástroj pro vývojáře více projekty.

Každé úložiště VSTS Git, který je spojen s data factory má větev spolupráci. (`master` je výchozím nastavení spolupráci větev). Uživatelé mohou také vytvářet větve funkcí kliknutím **+ novou větev** a vývoj v větve funkcí.

![Změnit kód synchronizace nebo publikování](media/author-visually/sync-publish.png)

Jakmile budete připraveni s vývojem pro funkce ve vaší větvi funkce, můžete kliknout na **vytvořit žádost o přijetí změn**. Tím přejdete na VSTS GIT, kde můžete zvýšit o přijetí změn požadavků, revizemi kódu, a sloučit změny do větve spolupráci. (`master` je výchozí nastavení). Jsou povoleny pouze pro publikování do služby Data Factory ze své větve spolupráci. 

![Vytvořte novou žádost o přijetí změn](media/author-visually/create-pull-request.png)

#### <a name="publish-code-changes"></a>Publikování změn kódu
Poté, co mají sloučit změny do větve spolupráce (`master` je výchozí nastavení), vyberte **publikovat** ručního publikování změn kódu v hlavní větvi do služby Data Factory.

![Publikujte změny do služby Data Factory](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> Hlavní větev se nemusí shodovat s co je nasazená ve službě Data Factory. V hlavní větvi *musí* ručně publikovat do služby Data Factory.

## <a name="use-the-expression-language"></a>Použijte jazyk výrazů
Výrazy pro hodnoty vlastnosti můžete určit pomocí výrazů jazyka, který je podporovaný službou Azure Data Factory. 

Zadejte výrazy pro hodnoty vlastnosti tak, že vyberete **Přidání dynamického obsahu**:

![Použijte jazyk výrazů](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>Pomocí služby functions a parametry

Můžete použít funkce nebo zadejte parametry pro kanálů a datových sad v datové továrně **Tvůrce výrazů**:

Informace o podporovaných výrazů naleznete v tématu [výrazům a funkcím ve službě Azure Data Factory](control-flow-expression-language-functions.md).

![Přidat dynamický obsah](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
Vyberte **zpětnou vazbu** komentovat funkcí nebo oznámení o problémech s nástrojem Microsoft:

![Váš názor](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Další postup
Další informace o monitorování a Správa kanálů najdete v tématu [monitorování a Správa kanálů prostřednictvím kódu programu](monitor-programmatically.md).
