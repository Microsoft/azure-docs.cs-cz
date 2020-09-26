---
title: Nejčastější dotazy k šabloně ARM
description: Nejčastější dotazy týkající se Azure Resource Manager šablon.
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 47ac8d0e3172645ec168d5cfe7a002d84765b864
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333116"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>Nejčastější dotazy týkající se šablon ARM

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se šablon Azure Resource Manager (ARM).

## <a name="getting-started"></a>Začínáme

* **Co jsou šablony ARM a proč je mám použít?**

  Šablony ARM jsou soubory JSON, kde definujete, co chcete nasadit do Azure. Šablony vám pomůžou implementovat řešení infrastruktury jako kódu pro Azure. Vaše organizace může opakovaně a spolehlivě nasadit požadovanou infrastrukturu do různých prostředí.
  
  Další informace o tom, jak šablony ARM vám pomůžou spravovat infrastrukturu Azure, najdete v tématu [co jsou šablony ARM?](overview.md)

* **Návody začít se šablonami?**

  Abyste zjednodušili vytváření šablon ARM, potřebujete správné nástroje. Doporučujeme nainstalovat [Visual Studio Code](https://code.visualstudio.com/) a [rozšíření Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Rychlý úvod k těmto nástrojům najdete v tématu [rychlý Start: vytvoření Azure Resource Manager šablon pomocí Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

  Až budete připraveni získat informace o vytváření šablon ARM, začněte v [šablonách ARM na začátečníkích řadu kurzů pro začátečníky](template-tutorial-create-first-template.md). Tyto kurzy vás provedou procesem vytvoření šablony ARM krok za krokem. Dozvíte se o různých oddílech šablony a o tom, jak spolu pracují. Tento obsah je také k dispozici jako [modul Microsoft Learn](/learn/modules/authoring-arm-templates/).

* **Mám k nasazení do Azure použít šablony ARM nebo Terraformu?**

  Použijte možnost, kterou si přejete nejlépe. Obě služby vám pomůžou s automatizací nasazení do Azure.
  
  Věříme, že existují výhody používání šablon ARM pro jiné služby infrastruktury jako kódu. Další informace o těchto výhodách najdete v tématu [Proč zvolit šablony ARM?](overview.md#why-choose-arm-templates)

## <a name="build-2020"></a>Sestavení 2020

* **Ve společnosti Microsoft Build 2020 jsem prezentace vynechala. Je prezentace k dispozici pro zobrazení?**

  Ano, [Sledujte ho prosím kdykoli](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions).

* **Kde mohu získat další informace o nových funkcích, které jste oznámili při sestavování?**

  Obecné informace o funkcích, které pracujeme, najdete v naší [skupině nasazení služby Azure Advisory Yammer](https://aka.ms/ARMMeet).

  Pokud se chcete dozvědět o novém jazyce šablony, [Zaregistrujte si oznámení](https://aka.ms/armLangUpdates).

  Další informace o specifikacích šablon naleznete v tématu [Azure Resource Manager specifikace šablon (Preview)](template-specs.md).

## <a name="creating-and-testing-templates"></a>Vytváření a testování šablon

* **Kde můžu získat informace o osvědčených postupech pro šablony ARM?**

  Doporučení k implementaci šablon najdete v tématu [osvědčené postupy pro šablonu ARM](template-best-practices.md). Po vytvoření šablony spusťte [sadu nástrojů ARM test Toolkit](https://github.com/azure/arm-ttk). Kontroluje, zda vaše šablona odpovídá doporučeným postupům.

* **Nastavil (a) jsem prostředí prostřednictvím portálu. Existuje nějaký způsob, jak získat šablonu z existující skupiny prostředků?**

  Ano, šablonu můžete [exportovat](export-template-portal.md) ze skupiny prostředků. Vyexportovaná šablona je dobrým výchozím bodem pro získání informací o šablonách, ale budete ji pravděpodobně chtít před použitím v produkčním prostředí revidovat.
  
  Při exportování šablony můžete vybrat prostředky, které chcete zahrnout do šablony.

* **Můžu v šabloně ARM vytvořit skupinu prostředků a nasadit do ní prostředky?**

  Ano, v šabloně můžete vytvořit skupinu prostředků, když nasadíte šablonu na úrovni předplatného Azure. Příklad vytvoření skupiny prostředků a nasazení prostředků najdete v tématu [Skupina prostředků a prostředky](deploy-to-subscription.md#resource-groups).

* **Můžu vytvořit předplatné v šabloně ARM?**

  Ještě ne, ale pracujeme na tom.

* **Jak můžu otestovat šablonu před nasazením?**

  Před nasazením doporučujeme, abyste spustili [sadu nástrojů ARM test Toolkit](https://github.com/azure/arm-ttk) a [operaci citlivostní zpracování](template-deploy-what-if.md) na vašich šablonách. Sada testů kontroluje, zda vaše šablona používá osvědčené postupy. Poskytuje upozornění, když identifikuje změny, které by mohly zlepšit způsob implementace šablony.

  Operace citlivosti zobrazuje změny, které vaše šablona provede pro vaše prostředí. Před nasazením uvidíte nezamýšlené změny. Co když také vrátí všechny chyby, které může detekovat při ověřování před výstupem. Například pokud vaše šablona obsahuje syntaktickou chybu, vrátí tuto chybu. Vrátí také všechny chyby, které může zjistit konečný stav nasazených prostředků. Například pokud vaše šablona nasadí účet úložiště s názvem, který se už používá, co-if vrátí tuto chybu.

* **Kde najdu informace o vlastnostech, které jsou k dispozici pro každý typ prostředku?**

  VS Code poskytuje IntelliSense pro práci s vlastnostmi prostředku. Můžete si také prohlédnout [odkaz na šablonu](/azure/templates/) pro vlastnosti a popisy.

* **Potřebuji vytvořit více instancí typu prostředku. Návody vytvořit iterátor v mé šabloně?**

  Pomocí elementu Copy určete více než jednu instanci. Můžete použít kopírování na [prostředky](copy-resources.md), [vlastnosti](copy-properties.md), [proměnné](copy-variables.md)a [výstupy](copy-outputs.md).

## <a name="template-language"></a>Jazyk šablony

* **Slyšeli jsme, že pracujete na novém jazyku šablony. Kde můžu získat další informace?**

  Pokud chcete zobrazit náhled nového jazyka, přečtěte si téma [úložiště bicep projektu](https://github.com/Azure/bicep). Pokud chcete mít přehled o novém jazyku, [Zaregistrujte si oznámení](https://aka.ms/armLangUpdates).

* **Existuje plán na podporu vytváření šablon v YAML?**

  V současné době není k dispozici žádný plán pro podporu YAML. Věříme, že nový jazyk šablony nabízí řešení, které je snazší použít než YAML nebo JSON.

* **Můžu I nadále psát šablony ve formátu JSON po vydání nového jazyka šablony?**

  Ano, můžete pokračovat v používání šablon JSON.

* **Budete nabízet Nástroj pro převod šablon JSON na nový jazyk šablony?**

  Ano.

## <a name="template-specs"></a>Specifikace šablon

* **Jak se můžu zapojit do verze Preview specifikací šablon?**

  [Připojte se k seznamu čekání](https://aka.ms/templateSpecsWaitlist) pro specifikace šablon.

* **Jak se týkají specifikace šablon a plány Azure?**

  Azure modrotisky budou ve své implementaci používat specifikace šablon nahrazením `blueprint definition` prostředku objektem `template spec` . Poskytneme cestu migrace k převedení definice podrobného plánu na specifikaci šablony, ale rozhraní API definice podrobného plánu bude i nadále podporováno. V prostředku nejsou žádné změny `blueprint assignment` . Plány budou mít za následek uživatelské prostředí pro vytvoření prostředí v Azure, které se řídí.

* **Nahradí se v nich specifikace šablony propojené šablony?**

  Ne, ale specifikace šablony jsou navržené tak, aby dobře fungovaly s propojenými šablonami. Před nasazením nadřazené šablony není nutné propojenou šablonu přesunout do veřejně přístupného koncového bodu. Místo toho zabalíte nadřazenou šablonu a její artefakty společně při vytváření specifikace šablony.

* **Můžou se specifikace šablon sdílet mezi předplatnými?**

  Ano, je možné je používat v rámci předplatných, pokud má uživatel oprávnění ke čtení pro specifikaci šablony. V klientech nelze používat specifikace šablon.

## <a name="scripts-in-templates"></a>Skripty v šablonách

* **Můžu do šablony přidat skript, aby se úkoly, které nejsou v šabloně možné?**

  Ano, použít [skripty pro nasazení](deployment-script-template.md). Do šablon můžete zahrnout Azure PowerShell nebo skripty rozhraní příkazového řádku Azure CLI. Tato funkce je ve verzi Preview.

* **Můžu pořád používat rozšíření vlastních skriptů a konfiguraci požadovaného stavu (DSC)?**

  Tyto možnosti jsou stále k dispozici a nebyly změněny. Skripty nasazení jsou navržené tak, aby prováděly akce, které nesouvisí s hostem virtuálního počítače. Pokud potřebujete spustit skript v hostitelském operačním systému virtuálního počítače, bude lepší volbou rozšíření vlastních skriptů nebo DSC. Skripty nasazení ale mají výhody, jako je například nastavení trvání časového limitu.

* **Jsou v Azure Government podporovány skripty nasazení?**

  Ano, můžete použít skripty nasazení v US Gov – Arizona a US Gov – Virginie.

## <a name="preview-changes-before-deployment"></a>Náhled změn před nasazením

* **Můžu zobrazit náhled změn, ke kterým dojde před nasazením šablony?**

  Ano, použijte [funkci citlivostní informace](template-deploy-what-if.md). Vyhodnotí aktuální stav vašeho prostředí a porovná ho se stavem, který bude existovat po nasazení. Můžete zkontrolovat souhrnné změny, abyste se ujistili, že šablona nemá žádné neočekávané výsledky.

* **Můžu použít co-if s režimem přírůstkového i kompletního?**

  Ano, oba [režimy nasazení](deployment-modes.md) jsou podporovány. Příklad použití přírůstkového režimu najdete v tématu [spuštění operace citlivosti](template-deploy-what-if.md#run-what-if-operation). Příklad použití úplného režimu najdete v tématu [potvrzení odstranění](template-deploy-what-if.md#confirm-deletion).

* **Funguje s propojenými šablonami?**

  Ano, co když vyhodnocuje stav nadřazené šablony a jejích propojených šablon.

* **Můžu v kanálu Azure použít co dělat?**

  Ano, pokud chcete ověřit, že kanál by měl pokračovat, můžete použít citlivost.

* **Když používám, co když se zobrazuje změny ve vlastnostech, které nejsou v mé šabloně Očekával se tento "hluk"?**

  Co když je ve verzi Preview. Pracujeme na snížení hluku. Pomůžete nám vylepšit odesláním problémů v našem úložišti GitHub: https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>Vizualizér šablon

* **Existuje způsob, jak mi vizualizovat šablonu ARM a její prostředky?**

  Máme [vs Code rozšíření od komunity](https://aka.ms/ARMVisualizer) , které nabízí skvělou úlohu vizualizace vaší šablony ARM. Zobrazuje prostředky, které nasazujete, a vztahy mezi nimi.

* **Můžu použít Vizualizér šablon mimo VS Code?**

  Vizualizér šablon je zobrazený na portálu. Další informace najdete [v této krátké relaci v sestavování](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions).

## <a name="deployment-limits"></a>Omezení nasazení

* **Kolik skupin prostředků můžu nasadit do jediné operace nasazení?**

  V minulosti byl tento limit pět skupin prostředků. Nedávno se zvýšila na 800 skupin prostředků. Další informace najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

* **Zobrazila se mi chyba omezená na 800 nasazení v historii nasazení. Co mám dělat?**

  Měníme způsob, jakým se udržuje historie nasazení pro skupinu prostředků. V minulosti jste museli z této historie ručně odstranit nasazení, aby se předešlo této chybě. Od června 2020 automaticky odstraníme nasazení z historie, protože se dostanete v blízkosti limitu. Další informace najdete v tématu [Automatické odstraňování z historie nasazení](deployment-history-deletions.md).

  Odstranění nasazení z historie nemá vliv na nasazené prostředky.

## <a name="templates-and-devops"></a>Šablony a DevOps

* **Můžu do Azure Pipelines integrovat šablony ARM?**

  Ano. Vysvětlení používání šablon a kanálů najdete v tématu [kurz: průběžná integrace šablon Azure Resource Manager s Azure Pipelines](deployment-tutorial-pipeline.md) a [integrace šablon ARM s Azure Pipelines](add-template-to-azure-pipelines.md).

* **Můžu k nasazení šablony použít akce GitHubu?**

  Ano, další informace najdete v tématu [nasazení Azure Resource Manager šablon pomocí akcí GitHubu](deploy-github-actions.md).

## <a name="next-steps"></a>Další kroky

Úvod do šablon ARM najdete v tématu [co jsou šablony ARM](overview.md).
