---
title: Exportovat šablonu v Azure Portal
description: Pomocí Azure Portal můžete exportovat šablonu Azure Resource Manager z prostředků v rámci vašeho předplatného.
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: ee97953a337bbb7cc9a8d1f042a3beae7bccdcae
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185687"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Export jednoho a více prostředků do šablony v Azure Portal

Pro pomoc s vytvářením šablon Azure Resource Manager můžete exportovat šablonu z existujících prostředků. Vyexportovaná šablona vám pomůže pochopit syntaxi a vlastnosti JSON, které nasazují vaše prostředky. Pokud chcete automatizovat budoucí nasazení, začněte s exportovanou šablonou a upravte ji pro váš scénář.

Správce prostředků umožňuje vybrat jeden nebo více prostředků pro export do šablony. Můžete se soustředit přesně na prostředky, které v šabloně potřebujete.

Tento článek popisuje, jak exportovat šablony prostřednictvím portálu. Můžete použít také rozhraní příkazového [řádku Azure CLI](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)nebo [REST API](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Výběr pravé možnosti exportu

Existují dva způsoby, jak exportovat šablonu:

* **Exportujte ze skupiny prostředků nebo prostředku**. Tato možnost vygeneruje novou šablonu z existujících prostředků. Vyexportovaná šablona je "snímek" aktuálního stavu skupiny prostředků. V rámci této skupiny prostředků můžete exportovat celou skupinu prostředků nebo konkrétní prostředky.

* **Exportujte před nasazením nebo z historie**. Tato možnost načte přesnou kopii šablony, která se používá k nasazení.

V závislosti na zvolené možnosti mají exportované šablony různé kvality.

| Ze skupiny prostředků nebo prostředku | Před nasazením nebo z historie |
| --------------------- | ----------------- |
| Šablona je snímkem aktuálního stavu prostředků. Obsahuje všechny ruční změny, které jste provedli po nasazení. | Šablona zobrazuje pouze stav prostředků v době nasazení. Žádné ruční změny, které jste provedli po nasazení, nejsou zahrnuté. |
| Můžete vybrat prostředky ze skupiny prostředků, které se mají exportovat. | Součástí jsou všechny prostředky pro konkrétní nasazení. Nemůžete vybrat podmnožinu těchto prostředků nebo přidat prostředky, které byly přidány v jinou dobu. |
| Šablona obsahuje všechny vlastnosti prostředků, včetně některých vlastností, které byste při nasazení nenormálně nastavili. Tyto vlastnosti můžete chtít před použitím šablony znovu odebrat nebo vyčistit. | Šablona obsahuje pouze vlastnosti, které jsou potřebné pro nasazení. Šablona je připravená k použití. |
| Šablona pravděpodobně neobsahuje všechny parametry, které potřebujete pro opakované použití. Většina hodnot vlastností je pevně zakódována v šabloně. Aby bylo možné šablonu znovu nasadit v jiných prostředích, je třeba přidat parametry, které zvyšují možnost konfigurace prostředků.  Můžete zrušit výběr **parametrů zahrnout** , abyste mohli vytvářet vlastní parametry. | Šablona obsahuje parametry, které usnadňují opětovné nasazení v různých prostředích. |

Exportujte šablonu ze skupiny prostředků nebo prostředku, pokud:

* Je třeba zachytit změny prostředků, které byly provedeny po původním nasazení.
* Chcete vybrat, které prostředky budou exportovány.

Exportujte šablonu před nasazením nebo z historie, pokud:

* Chcete snadno použít šablonu.
* Nemusíte zahrnovat změny, které jste provedli po původním nasazení.

## <a name="limitations"></a>Omezení

Při exportu ze skupiny prostředků nebo prostředku se vyexportovaná šablona vygeneruje z [publikovaných schémat](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) pro každý typ prostředku. V některých případech nemá schéma nejnovější verzi pro typ prostředku. Zkontrolujte exportovanou šablonu a ujistěte se, že obsahuje vlastnosti, které potřebujete. V případě potřeby upravte exportovanou šablonu tak, aby používala verzi rozhraní API, kterou potřebujete.

Funkce Exportovat šablonu nepodporuje export Azure Data Factorych prostředků. Další informace o tom, jak můžete exportovat Data Factory prostředky, najdete [v tématu kopírování nebo klonování datové továrny v Azure Data Factory](../../data-factory/copy-clone-data-factory.md).

Chcete-li exportovat prostředky vytvořené prostřednictvím modelu nasazení Classic, je nutné [je migrovat do modelu nasazení Správce prostředků](../../virtual-machines/migration-classic-resource-manager-overview.md).

Pokud se zobrazí upozornění při exportu šablony, která indikuje, že se typ prostředku neexportoval, můžete stále zjistit vlastnosti daného prostředku. Další informace o různých možnostech zobrazení vlastností prostředků najdete v tématu věnovaném [zjištění vlastností prostředku](view-resources.md). Můžete se také podívat na [REST API Azure](/rest/api/azure/) pro daný typ prostředku.

Ve skupině prostředků je limit 200 prostředků, pro který vytvoříte exportovanou šablonu. Pokud se pokusíte exportovat skupinu prostředků, která má více než 200 prostředků, zobrazí se chybová zpráva `Export template is not supported for resource groups more than 200 resources` .

## <a name="export-template-from-a-resource-group"></a>Export šablony ze skupiny prostředků

Export jednoho nebo více prostředků ze skupiny prostředků:

1. Vyberte skupinu prostředků obsahující prostředky, které chcete exportovat.

1. Vyberte jeden nebo více prostředků zaškrtnutím příslušných políček.  Chcete-li vybrat vše, zaškrtněte políčko vlevo od **názvu**. Položka nabídky **Exportovat šablonu** se aktivuje jenom po výběru alespoň jednoho prostředku.

   ![Exportovat všechny prostředky](./media/export-template-portal/select-all-resources.png)

    Na snímku obrazovky je vybrán pouze účet úložiště.
1. Vyberte **Exportovat šablonu**.

1. Zobrazí se vyexportovaná šablona a je možné ji stáhnout a nasadit.

   ![Zobrazit šablonu](./media/export-template-portal/show-template.png)

   Ve výchozím nastavení je vybraná možnost **zahrnout parametry** .  Pokud je tato možnost vybrána, budou všechny parametry šablony zahrnuty při generování šablony. Pokud chcete vytvořit vlastní parametry, přepněte toto zaškrtávací políčko na nezahrnuté.

## <a name="export-template-from-a-resource"></a>Export šablony z prostředku

Export jednoho prostředku:

1. Vyberte skupinu prostředků obsahující prostředek, který chcete exportovat.

1. Vyberte prostředek, který chcete exportovat, a otevřete prostředek.

1. V případě tohoto prostředku v levém podokně vyberte **Exportovat šablonu** .

   ![Exportovat prostředek](./media/export-template-portal/export-single-resource.png)

1. Zobrazí se vyexportovaná šablona a je možné ji stáhnout a nasadit. Šablona obsahuje pouze jeden prostředek. Ve výchozím nastavení je vybraná možnost **zahrnout parametry** .  Pokud je tato možnost vybrána, budou všechny parametry šablony zahrnuty při generování šablony. Pokud chcete vytvořit vlastní parametry, přepněte toto zaškrtávací políčko na nezahrnuté.

## <a name="export-template-before-deployment"></a>Exportovat šablonu před nasazením

1. Vyberte službu Azure, kterou chcete nasadit.

1. Vyplňte hodnoty pro novou službu.

1. Po úspěšném ověření, ale před spuštěním nasazení vyberte **Stáhnout šablonu pro automatizaci**.

   ![Stažení šablony](./media/export-template-portal/download-before-deployment.png)

1. Šablona se zobrazí a je k dispozici pro stažení a nasazení.


## <a name="export-template-after-deployment"></a>Exportovat šablonu po nasazení

Můžete exportovat šablonu, která byla použita k nasazení existujících prostředků. Šablona, kterou získáte, je přesně ta, která byla použita pro nasazení.

1. Vyberte skupinu prostředků, kterou chcete exportovat.

1. Vyberte odkaz v části **nasazení**.

   ![Vybrat historii nasazení](./media/export-template-portal/select-deployment-history.png)

1. V historii nasazení vyberte jedno z nasazení.

   ![Vybrat nasazení](./media/export-template-portal/select-details.png)

1. Vyberte **šablonu**. Zobrazí se Šablona použitá pro toto nasazení a je k dispozici ke stažení.

   ![Výběr šablony](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Další kroky

- Naučte se exportovat šablony pomocí [Azure CLI](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)nebo [REST API](/rest/api/resources/resourcegroups/exporttemplate).
- Další informace o syntaxi šablon Správce prostředků naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
- Další informace o vývoji šablon najdete v [podrobných kurzech](../index.yml).
- Chcete-li zobrazit schémata šablon Azure Resource Manager, přečtěte si téma [reference šablony](/azure/templates/).