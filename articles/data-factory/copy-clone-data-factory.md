---
title: Kopírování nebo klonování datové továrny v Azure Data Factory
description: Naučte se kopírovat nebo klonovat datovou továrnu v Azure Data Factory
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 1ea16785502ce8a82087b79dd8e7f0014c0059fc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375234"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopírování nebo klonování datové továrny v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak zkopírovat nebo klonovat datovou továrnu v Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Případy použití při klonování objektu pro vytváření dat

Tady jsou některé okolnosti, za kterých může být užitečné zkopírovat nebo klonovat datovou továrnu:

- **Přesuňte data Factory** do nové oblasti. Pokud chcete přesunout Data Factory do jiné oblasti, nejlepším způsobem je vytvořit kopii v cílové oblasti a odstranit tu existující.

- **Přejmenování Data Factory**. Azure nepodporuje přejmenování prostředků. Pokud chcete objekt pro vytváření dat přejmenovat, můžete objekt pro vytváření dat naklonovat s jiným názvem a odstranit existující.

- **Ladění změn** , když funkce ladění nestačí. Ve většině scénářů můžete použít [ladění](iterative-development-debugging.md). Při testování změn v naklonovaném prostředí izolovaného prostoru (sandbox) je lepší smysl. Například způsob, jakým se parametrizované kanály ETL budou chovat, když se Trigger aktivuje při příchodu souboru versus překročení časového období, nemusí být snadno testovatelné prostřednictvím samotného ladění. V těchto případech může být vhodné klonovat prostředí izolovaného prostoru (sandbox) pro experimentování. Vzhledem k tomu, že se Azure Data Factory poplatky primárně podle počtu spuštění, druhá továrna nevede na žádné další poplatky.

## <a name="how-to-clone-a-data-factory"></a>Jak klonovat datovou továrnu

1. Jako součást je nejdřív potřeba vytvořit cílovou datovou továrnu z Azure Portal.

1. Pokud jste v režimu GIT:
    1. Pokaždé, když publikujete z portálu, šablona Správce prostředků továrny se uloží do GITU ve \_ větvi publikování ADF.
    1. Připojte novou továrnu ke _stejnému_ úložišti a sestavte z \_ větve publikování ADF. Prostředky, jako jsou kanály, datové sady a triggery, se provedou prostřednictvím

1. Pokud jste v živém režimu:
    1. Data Factory uživatelské rozhraní umožňuje exportovat celou datovou část vaší datové továrny do souboru šablony Správce prostředků a souboru parametrů. Jsou k nim přistupované na portálu **ARM šablona \ exportovat správce prostředků šablonu** na portálu.
    1. Můžete provést příslušné změny v souboru parametrů a prohodit nové hodnoty pro novou továrnu.
    1. V dalším kroku je můžete nasadit pomocí standardních metod nasazení Správce prostředků šablon.

1. Pokud máte ve zdrojové továrně SelfHosted IntegrationRuntime, musíte ho v cílové továrně vytvořit se stejným názvem. Pokud chcete sdílet SelfHosted Integration Runtime mezi různými továrnami, můžete použít vzor publikovaný [tady](create-shared-self-hosted-integration-runtime-powershell.md) při sdílení SelfHosted IR.

1. Z bezpečnostních důvodů nebudou generované šablony Správce prostředků obsahovat žádné tajné informace, například hesla pro propojené služby. Proto je nutné zadat přihlašovací údaje jako parametry nasazení. Pokud nechcete, aby se přihlašovací údaje pro vaše nastavení zadaly ručně, zvažte prosím, že se místo toho načítají připojovací řetězce a hesla z Azure Key Vault. [Zobrazit více](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si pokyny k vytvoření datové továrny v Azure Portal v části [Vytvoření datové továrny pomocí uživatelského rozhraní Azure Data Factory](quickstart-create-data-factory-portal.md).
