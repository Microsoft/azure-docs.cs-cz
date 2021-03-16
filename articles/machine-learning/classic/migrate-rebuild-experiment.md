---
title: 'ML Studio (Classic): migrace na Azure Machine Learning – experiment pro opětovné sestavení'
description: Znovu sestavte experimenty Studio (Classic) v Návrháři Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bb944cb034fdd7cc51648314154a654bc1265533
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564966"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>Opětovné sestavení experimentu studia (Classic) v Azure Machine Learning

V tomto článku se naučíte, jak znovu sestavit experiment v rámci studia (Classic) v Azure Machine Learning. Další informace o migraci z studia (Classic) najdete v [článku Přehled migrace](migrate-overview.md).

**Experimenty** studia (Classic) jsou podobné **kanálům** v Azure Machine Learning. V Azure Machine Learningch kanálech je ale postavená na stejné back-endové sadě SDK. To znamená, že máte dvě možnosti pro vývoj ve službě Machine Learning: Návrhář přetahování nebo sady SDK pro první kód.

Další informace o vytváření kanálů pomocí sady SDK najdete v tématu [co jsou Azure Machine Learning kanály](../concept-ml-pipelines.md#building-pipelines-with-the-python-sdk).


## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Pracovní prostor služby Azure Machine Learning. [Vytvořte pracovní prostor Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Experiment studia (Classic), který se má migrovat.
- [Nahrajte datovou sadu](migrate-register-dataset.md) do Azure Machine Learning.

## <a name="rebuild-the-pipeline"></a>Opětovné sestavení kanálu

Jakmile [datovou sadu migrujete do Azure Machine Learning](migrate-register-dataset.md), jste připraveni znovu vytvořit experiment.

V Azure Machine Learning se vizuální graf nazývá **koncept kanálu**. V této části znovu vytvoříte klasický experiment jako koncept kanálu.

1. Přejít na Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com))
1. V levém navigačním podokně vyberte v **Návrháři** > **snadno připravené moduly –** ![ snímek obrazovky s informacemi o tom, jak vytvořit novou koncept kanálu.](../media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Manuálně znovu sestavte experiment s moduly návrháře.
    
    V [tabulce mapování modulů](migrate-overview.md#studio-classic-and-designer-module-mapping) vyhledejte náhradní moduly. Mnohé z nejoblíbenějších modulů studia (Classic) mají v Návrháři stejné verze.

    > [!Important]
    > Pokud váš experiment používá modul spuštění skriptu jazyka R, je nutné provést další kroky k migraci experimentu. Další informace najdete v tématu [migrace modulů skriptu jazyka R](migrate-execute-r-script.md).

1. Upravit parametry.
    
    Vyberte jednotlivé moduly a upravte parametry na panelu nastavení modulu vpravo. Pomocí parametrů znovu vytvořte funkce experimentu studia (Classic). Další informace o jednotlivých modulech najdete v [Referenční příručce k modulům](../algorithm-module-reference/module-reference.md).

## <a name="submit-a-run-and-check-results"></a>Odeslat výsledky spuštění a ověřit

Po opětovném vytvoření experimentu studia (Classic) je čas odeslat **spuštění kanálu**.

Spuštění kanálu se provede na **výpočetním cíli** připojeném k vašemu pracovnímu prostoru. Můžete nastavit výchozí cíl výpočtů pro celý kanál, nebo můžete určit výpočetní cíle pro jednotlivé moduly.

Jakmile odešlete běh z konceptu kanálu, změní se na **běh kanálu**. Každé spuštění kanálu se zaznamenává a přihlašuje Azure Machine Learning.

Chcete-li nastavit výchozí cíl výpočtů pro celý kanál:
1.  ![ V Návrháři vedle názvu kanálu vyberte ikonu ozubeného kolečka ](../media/tutorial-designer-automobile-price-train-score/gear-icon.png) .
1. Vyberte **vybrat cíl výpočtů**.
1. Podle pokynů na obrazovce vyberte existující výpočetní výkon nebo vytvořte nový výpočet.

Teď, když je váš cíl služby COMPUTE nastavený, můžete odeslat spuštění kanálu:

1. V horní části plátna vyberte **Odeslat**.
1. Vyberte **vytvořit nový** a vytvořte nový experiment.
    
    Experimenty organizují podobné kanály společně. Pokud kanál spouštíte několikrát, můžete vybrat stejný experiment pro po sobě jdoucí běhy. To je užitečné pro protokolování a sledování.
1. Zadejte název experimentu. Pak vyberte **Odeslat**.

    První spuštění může trvat až 20 minut. Vzhledem k tomu, že výchozí výpočetní nastavení má minimální velikost uzlu 0, Návrhář musí po nečinnosti přidělit prostředky. Po sobě jdoucí běhy trvat kratší dobu, protože uzly jsou už přidělené. Chcete-li zrychlit dobu běhu, můžete vytvořit výpočetní prostředky s minimální velikostí uzlu 1 nebo vyšší.

Po dokončení spuštění můžete kontrolovat výsledky každého modulu:

1. Klikněte pravým tlačítkem na modul, jehož výstup chcete zobrazit.
1. Vyberte možnost **vizualizovat**, **Zobrazit výstup** nebo **Zobrazit protokol**.

    - **Vizualizace**: Náhled datové sady výsledků.
    - **Zobrazit výstup**: otevře odkaz na umístění výstupního úložiště. Toto použijte k prozkoumání nebo stažení výstupu. 
    - **Zobrazit protokol**: zobrazí ovladače a systémové protokoly. Pomocí **70_driver_log** můžete zobrazit informace související s vaším uživatelem odeslaným skriptem, jako jsou chyby a výjimky.

> [!IMPORTANT]
> Moduly návrháře používají Open Source balíčky Pythonu ve srovnání s balíčky C# v aplikaci Studio (Classic). V důsledku toho se může výstup modulu mírně lišit mezi návrhářem a Studio (Classic). 


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak v Azure Machine Learning znovu sestavit experiment studia (Classic). Dalším krokem je [opětovné sestavení webových služeb v Azure Machine Learning](migrate-rebuild-web-service.md).


Podívejte se na další články v řadě migrace pro Studio (Classic):

1. [Přehled migrace](migrate-overview.md)
1. [Migrujte datovou sadu](migrate-register-dataset.md).
1. **Znovu sestavte školicí kanál studia (Classic)**.
1. [Opětovné sestavení webové služby studia (Classic)](migrate-rebuild-web-service.md).
1. [Integrujte webovou službu Azure Machine Learning s klientskými aplikacemi](migrate-rebuild-integrate-with-client-app.md).
1. [Migrace spouštěného skriptu R](migrate-execute-r-script.md)
