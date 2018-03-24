---
title: Visual vytváření obsahu v Azure Data Factory | Microsoft Docs
description: Další informace o použití visual vytváření obsahu v Azure Data Factory
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/9/2018
ms.author: shlo
ms.openlocfilehash: bce74c80f53eda654bb0289a1ff8a6cb88fd13f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="visual-authoring-in-azure-data-factory"></a>Visual vytváření obsahu v Azure Data Factory
Azure Data Factory uživatelské rozhraní prostředí (UX) umožňuje vizuálně vytvořit a nasadit prostředky pro vytváření dat bez nutnosti psaní jakéhokoli kódu. Můžete přetáhněte na plátno kanálu aktivity, proveďte test spustí, interaktivně, ladění a nasadit a monitorovat běží vaše kanálu. Pro použití uživatelského k provedení visual vytváření dvěma způsoby:

- Vytvořit přímo ve službě Data Factory.
- Autor integrace Gitu Visual Studio Team Services (VSTS) pro spolupráci, Správa zdrojového kódu a správa verzí.

## <a name="author-directly-with-the-data-factory-service"></a>Vytvořit přímo ve službě Data Factory
Visual vytváření obsahu pomocí služby Data Factory se liší od visual vytváření obsahu pomocí služby VSTS dvěma způsoby:

- Služba Data Factory neobsahuje úložiště pro ukládání entity JSON pro změny.
- Služba Data Factory není optimalizována pro spolupráci nebo Správa verzí.

![Konfigurovat službu Data Factory ](media/author-visually/configure-data-factory.png)

Při použití uživatelského **vytváření plátno** k vytváření přímo se službou Data Factory, jenom **publikovat** režim je k dispozici. Veškeré změny, které provedete jsou publikovány přímo ke službě Data Factory.

![Režim publikování](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Autor s integrací služby VSTS Git
Vytváření Visual s integrací služby VSTS Git podporuje zdrojového kódu a spolupráce for work v objektu pro vytváření datových kanálů. Objekt pro vytváření dat můžete přidružit účet úložiště služby VSTS Git pro řízení zdrojů, spolupráce, Správa verzí a tak dále. Jeden účet služby VSTS Git může mít několik úložiště, ale úložiště Git služby VSTS může být přidružen pouze jeden datový objekt pro vytváření. Pokud nemáte účet služby VSTS nebo úložiště, postupujte podle [tyto pokyny](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) k vytvoření vašich prostředků.

> [!NOTE]
> Soubory skriptu a data můžete ukládat v úložišti služby VSTS GIT. Ale budete muset ručně odeslat soubory do úložiště Azure. Objekt pro vytváření dat kanál nebyl automaticky odeslán skriptu nebo datové soubory uložené v úložišti GIT služby VSTS do služby Azure Storage.

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>Konfigurace úložiště Git služby VSTS s Azure Data Factory
Úložiště GIT služby VSTS můžete nakonfigurovat pomocí služby data factory pomocí dvou metod.

<a name="method1"></a>
#### <a name="configuration-method-1-lets-get-started-page"></a>Metoda konfigurace 1: Pojďme stránku Začínáme
V Azure Data Factory, přejděte do **můžeme začít** stránky. Vyberte **konfigurace úložiště kódu**:

![Konfigurace úložiště služby VSTS kódu](media/author-visually/configure-repo.png)

**Nastavení úložiště** se zobrazí v podokně Konfigurace:

![Konfigurovat nastavení úložiště kódu](media/author-visually/repo-settings.png)

V podokně se zobrazují následující kód služby VSTS nastavení úložiště:

| Nastavení | Popis | Hodnota |
|:--- |:--- |:--- |
| **Typ úložiště** | Typ úložiště služby VSTS kódu.<br/>**Poznámka:**: Githubu se aktuálně nepodporuje. | Visual Studio Team Services Git |
| **Visual Studio Team Services Account** | Název účtu služby VSTS. Můžete vyhledat název účtu služby VSTS v `https://{account name}.visualstudio.com`. Můžete [přihlásit ke svému účtu služby VSTS](https://www.visualstudio.com/team-services/git/) přístup váš profil Visual Studio a zobrazit projekty a úložiště. | \<název účtu > |
| **ProjectName** | Služby VSTS název projektu. Můžete vyhledat název projektu služby VSTS v `https://{account name}.visualstudio.com/{project name}`. | \<služby VSTS název projektu > |
| **RepositoryName** | Název vaší služby VSTS kód úložiště. Služby VSTS projekty obsahují úložiště Git ke správě zdrojového kódu s růstem projektu. Můžete vytvořit nové úložiště, nebo použití existující úložiště, který už v projektu. | \<název služby VSTS kód úložiště > |
| **Import existujících prostředků pro vytváření dat do úložiště** | Určuje, zda chcete importovat existující objekt pro vytváření zdroje dat z uživatelského **vytváření plátno** do úložiště Git služby VSTS. Vyberte pole pro import vašich prostředků objekt pro vytváření dat do úložiště Git přidruženého ve formátu JSON. Tato akce exportuje každého prostředku jednotlivě (to znamená, propojené služby a datové sady exportují do samostatné JSONs). Pokud není toto políčko zaškrtnuto, nebudou importovány existující prostředky. | Vybrané (výchozí) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metoda konfigurace 2: UX vytváření plátno
V Azure Data Factory UX **vytváření plátno**, vyhledejte datovou továrnu. Vyberte **Data Factory** rozevírací nabídce a potom vyberte **konfigurace úložiště kódu**.

Zobrazí se podokno konfigurace. Podrobnosti o nastavení konfigurace najdete v tématu popisů v <a href="#method1">metoda konfigurace 1</a>.

![Konfigurovat nastavení úložiště kódu UX pro vytváření obsahu](media/author-visually/configure-repo-2.png)

### <a name="use-version-control"></a>Použití správy verzí
Systémy kontroly verze (také označované jako _ovládací prvek zdroje_) mohli spolupracovat na kód a sledovat změny, které jsou vytvářeny pomocí kódu základní vývojáři. Správa zdrojového kódu je základní nástroj pro projekty více vývojáři.

Každý úložiště Git služby VSTS, který je spojen s objekt pro vytváření dat má hlavní větve. Když máte přístup do úložiště Git služby VSTS, můžete změnit kód tak, že zvolíte **synchronizace** nebo **publikovat**:

![Změnit kód tak, že synchronizace nebo publikování](media/author-visually/sync-publish.png)

#### <a name="sync-code-changes"></a>Synchronizace změn kódu
Po výběru **synchronizace**, můžete vyžádat změny z hlavní větve do svojí větve místní nebo nabízené změní z vaší místní větve do hlavní větve.

![Synchronizace změn kódu](media/author-visually/sync-change.png)

#### <a name="publish-code-changes"></a>Publikování změn kódu
Vyberte **publikovat** Ruční publikování změn kódu v hlavní větvi ke službě Data Factory.

> [!IMPORTANT]
> Hlavní větve se nemusí shodovat s co je nasazen ve službě Data Factory. Hlavní větve *musí* ke službě Data Factory publikovat ručně.

## <a name="use-the-expression-language"></a>Použití výrazu jazyka
Výrazy pro hodnoty vlastností můžete zadat pomocí výrazu jazyka, která je podporována službou Azure Data Factory. Informace o podporovaných výrazech naleznete v tématu [výrazy a funkce v Azure Data Factory](control-flow-expression-language-functions.md).

Zadejte výrazy pro hodnoty vlastností pomocí uživatelského **vytváření plátno**:

![Použití výrazu jazyka](media/author-visually/expression-language.png)

## <a name="specify-parameters"></a>Zadejte parametry
Můžete zadat parametry pro kanálů a datové sady v Azure Data Factory **parametry** kartě. Můžete snadno využít parametry ve vlastnostech výběrem **přidat dynamického obsahu**:

![Přidejte dynamický obsah](media/author-visually/dynamic-content.png)

Můžete použít existující parametry nebo zadat nové parametry pro vaše hodnot vlastností:

![Zadejte parametry pro hodnoty vlastností](media/author-visually/parameters.png)

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
Vyberte **zpětné vazby** komentář o funkcí nebo oznámení o problémech s nástrojem Microsoft:

![Váš názor](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Další postup
Další informace o monitorování a Správa kanálů najdete v tématu [monitorování a Správa kanálů prostřednictvím kódu programu](monitor-programmatically.md).
