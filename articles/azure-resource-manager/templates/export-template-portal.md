---
title: Exportovat šablonu na webu Azure Portal
description: Pomocí portálu Azure můžete exportovat šablonu Azure Resource Manageru z prostředků ve vašem předplatném.
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8cdba58a7a2ba998bac7fc0225ff957047cd69b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273732"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Export s jedním a více prostředky do šablony na webu Azure Portal

Chcete-li pomoci s vytvářením šablon Azure Resource Manager, můžete exportovat šablonu z existujících prostředků. Exportovaná šablona vám pomůže pochopit syntaxi JSON a vlastnosti, které nasazují vaše prostředky. Chcete-li automatizovat budoucí nasazení, začněte s exportovnou šablonou a upravte ji pro váš scénář.

Správce prostředků umožňuje vybrat jeden nebo více prostředků pro export do šablony. Můžete se zaměřit na přesně potřebné prostředky v šabloně.

Tento článek ukazuje, jak exportovat šablony prostřednictvím portálu. Můžete taky použít [Azure CLI](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)nebo [REST API](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Výběr správné možnosti exportu

Existují dva způsoby, jak exportovat šablonu:

* **Export ze skupiny prostředků nebo prostředku**. Tato možnost generuje novou šablonu z existujících prostředků. Exportovaná šablona je "snímek" aktuálního stavu skupiny prostředků. V rámci této skupiny prostředků můžete exportovat celou skupinu prostředků nebo určité prostředky.

* **Exportujte před nasazením nebo z historie**. Tato možnost načte přesnou kopii šablony používané pro nasazení.

V závislosti na zvolené možnosti mají exportované šablony různé vlastnosti.

| Ze skupiny prostředků nebo zdroje | Před nasazením nebo z historie |
| --------------------- | ----------------- |
| Šablona je snímek aktuálního stavu prostředků. Obsahuje všechny ruční změny, které jste provedli po nasazení. | Šablona zobrazuje pouze stav prostředků v době nasazení. Nejsou zahrnuty žádné ruční změny provedené po nasazení. |
| Můžete vybrat prostředky ze skupiny prostředků, které chcete exportovat. | Všechny prostředky pro konkrétní nasazení jsou zahrnuty. Nelze vybrat podmnožinu těchto prostředků nebo přidat prostředky, které byly přidány v jinou dobu. |
| Šablona obsahuje všechny vlastnosti pro prostředky, včetně některých vlastností, které byste normálně nastavit během nasazení. Před opětovnou použitím šablony můžete tyto vlastnosti odebrat nebo vyčistit. | Šablona obsahuje pouze vlastnosti potřebné pro nasazení. Šablona je připravena k použití. |
| Šablona pravděpodobně neobsahuje všechny parametry, které potřebujete pro opakované použití. Většina hodnot vlastností je v šabloně pevně zakódována. Chcete-li znovu nasadit šablonu v jiných prostředích, je třeba přidat parametry, které zvyšují možnost konfigurace prostředků.  Můžete zrušit výběr **zahrnout parametry,** abyste mohli vytvářet vlastní parametry. | Šablona obsahuje parametry, které usnadňují opětovné nasazení v různých prostředích. |

Exportovat šablonu ze skupiny prostředků nebo prostředku, pokud:

* Je třeba zachytit změny prostředků, které byly provedeny po původním nasazení.
* Chcete vybrat, které zdroje budou exportovány.

Exportujte šablonu před nasazením nebo z historie, když:

* Chcete snadno použít šablonu.
* Není nutné zahrnout změny provedené po původním nasazení.

## <a name="limitations"></a>Omezení

Při exportu ze skupiny prostředků nebo prostředku je exportovaná šablona generována z [publikovaných schémat](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) pro každý typ prostředku. V některých případě schéma nemá nejnovější verzi pro typ prostředku. Zkontrolujte exportovnou šablonu, abyste se ujistili, že obsahuje vlastnosti, které potřebujete. V případě potřeby upravte exportovnou šablonu tak, aby používala verzi rozhraní API, kterou potřebujete.

Funkce šablony exportu nepodporuje export prostředků Azure Data Factory. Informace o tom, jak exportovat prostředky Data Factory, najdete v [tématu Kopírování nebo klonování datové továrny v Azure Data Factory](https://aka.ms/exportTemplateViaAdf).

Chcete-li exportovat prostředky vytvořené prostřednictvím klasického modelu nasazení, musíte [je migrovat do modelu nasazení Správce prostředků](https://aka.ms/migrateclassicresourcetoarm).

## <a name="export-template-from-a-resource-group"></a>Export šablony ze skupiny prostředků

Export jednoho nebo více prostředků ze skupiny prostředků:

1. Vyberte skupinu prostředků obsahující prostředky, které chcete exportovat.

1. Zaškrtnutím políček vyberte jeden nebo více zdrojů.  Chcete-li vybrat vše, zaškrtněte políčko vlevo od **položky Název**. Položka **nabídky šablony exportu** se aktivuje až po výběru alespoň jednoho prostředku.

   ![Export ovat všechny zdroje](./media/export-template-portal/select-all-resources.png)

    Na snímku obrazovky je vybrán pouze účet úložiště.
1. Vyberte **Exportovat šablonu**.

1. Exportovaná šablona je zobrazena a je k dispozici ke stažení a nasazení.

   ![Zobrazit šablonu](./media/export-template-portal/show-template.png)

   **Zahrnout parametry** je vybrána ve výchozím nastavení.  Pokud je tato možnost vybrána, budou při generování šablony zahrnuty všechny parametry šablony. Pokud chcete vytvořit vlastní parametry, zapněte toto zaškrtávací políčko tak, aby je neobsahovalo.

## <a name="export-template-from-a-resource"></a>Export šablony ze zdroje

Export jednoho zdroje:

1. Vyberte skupinu prostředků obsahující prostředek, který chcete exportovat.

1. Vyberte zdroj, který chcete exportovat, chcete-li zdroj otevřít.

1. U tohoto prostředku vyberte **exportovat šablonu** v levém podokně.

   ![Exportovat zdroj](./media/export-template-portal/export-single-resource.png)

1. Exportovaná šablona je zobrazena a je k dispozici ke stažení a nasazení. Šablona obsahuje pouze jeden prostředek. **Zahrnout parametry** je vybrána ve výchozím nastavení.  Pokud je tato možnost vybrána, budou při generování šablony zahrnuty všechny parametry šablony. Pokud chcete vytvořit vlastní parametry, zapněte toto zaškrtávací políčko tak, aby je neobsahovalo.

## <a name="export-template-before-deployment"></a>Exportovat šablonu před nasazením

1. Vyberte službu Azure, kterou chcete nasadit.

1. Vyplňte hodnoty pro novou službu.

1. Po absolvování ověření, ale před zahájením nasazení vyberte **stáhnout šablonu pro automatizaci**.

   ![Stažení šablony](./media/export-template-portal/download-before-deployment.png)

1. Šablona je zobrazena a je k dispozici ke stažení a nasazení.


## <a name="export-template-after-deployment"></a>Exportovat šablonu po nasazení

Šablonu, která byla použita k nasazení existujících prostředků, můžete exportovat. Šablona, kterou získáte, je přesně ten, který byl použit pro nasazení.

1. Vyberte skupinu prostředků, kterou chcete exportovat.

1. Vyberte odkaz v části **Nasazení**.

   ![Vybrat historii nasazení](./media/export-template-portal/select-deployment-history.png)

1. Vyberte jedno z nasazení z historie nasazení.

   ![Vybrat nasazení](./media/export-template-portal/select-details.png)

1. Vyberte **šablonu**. Šablona použitá pro toto nasazení je zobrazena a je k dispozici ke stažení.

   ![Výběr šablony](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak exportovat šablony pomocí [rozhraní API Azure](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), Azure [PowerShellu](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)nebo [rozhraní REST API](/rest/api/resources/resourcegroups/exporttemplate).
- Syntaxe syntaxe šablony Správce prostředků najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](template-syntax.md).
- Chcete-li se dozvědět, jak vyvíjet šablony, podívejte se na [podrobné výukové programy](/azure/azure-resource-manager/).
- Pokud chcete zobrazit schémata šablon Azure Resource Manager, přečtěte si [odkaz na šablonu](/azure/templates/).
