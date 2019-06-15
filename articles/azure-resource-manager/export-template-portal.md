---
title: Export šablony Azure Resource Manageru pomocí webu Azure portal
description: Export šablony Azure Resource Manageru z prostředků ve vašem předplatném pomocí webu Azure portal.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: ea9499da3dac67635a48704f439f6592c6ed467e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65515381"
---
# <a name="single-and-multi-resource-export-to-template-in-azure-portal"></a>Export jednoho a víc prostředků šablony na webu Azure portal

Chcete-li pomoci v oblastech vytváření šablon Azure Resource Manageru, můžete vyexportovat šablonu z existujících prostředků. Vyexportované šablony vám pomůže pochopit syntaxi JSON a vlastnosti, které prostředky nasazovat. K automatizaci budoucí nasazení, začněte s vyexportované šablony a upravit ji pro váš scénář.

Resource Manager umožňuje vybrat jeden nebo více prostředků pro export do šablony. Můžete se zaměřit na přesně prostředky, které potřebujete v šabloně.

## <a name="choose-the-right-export-option"></a>Zvolte možnost správné exportu

Existují dva způsoby, jak exportovat šablonu:

* **Exportujte ze skupiny prostředků nebo prostředek**. Tato možnost vygeneruje novou šablonu z existujících prostředků. Exportovaná šablona je "snímek" aktuální stav skupiny prostředků. Můžete exportovat celou skupinu prostředků nebo konkrétní prostředky v příslušné skupině prostředků.

* **Export před nasazením nebo z historie**. Tato možnost zkopíruje přesnou kopii Šablona použitá pro nasazení.

V závislosti na vámi zvolené možnosti exportované šablony mají různé vlastnosti.

| Ze skupiny prostředků nebo prostředek | Před nasazením nebo z historie |
| --------------------- | ----------------- |
| Šablona je snímek aktuálního stavu prostředky. Zahrnuje všechny ruční změny, které provedete po nasazení. | Šablona zobrazuje jenom stav prostředků v době nasazování. Všech ručních změn, které provedete po nasazení nejsou zahrnuté. |
| Prostředků, které můžete vybrat ze skupiny prostředků pro export. | Všechny prostředky pro konkrétní nasazení jsou zahrnuty. Nelze vybrat podmnožinu těchto prostředků nebo přidejte prostředky, které byly přidány v jinou dobu. |
| Šablona zahrnuje všechny vlastnosti pro prostředky, včetně některé vlastnosti, které by normálně sadě během nasazování. Můžete chtít odebrat nebo vyčištění tyto vlastnosti před opětovným použitím šablony. | Šablona obsahuje pouze vlastnosti, které jsou potřebné pro nasazení. Šablona je připravené k použití. |
| Šablona pravděpodobně neobsahuje všechny parametry, které potřebujete pro opakované použití. Většina hodnoty vlastností jsou pevně zakódované v šabloně. Opětovné nasazení šablony v jiných prostředích, budete muset přidat parametry, které zvyšují schopnost konfiguraci prostředků. | Šablona obsahuje parametry, které usnadňují znovu nasadit v různých prostředích. |

Export šablony ze skupiny prostředků nebo prostředek, pokud:

* Je potřeba zaznamenat změny zdrojů, které byly provedeny po původního nasazení.
* Jestli chcete použít, které prostředky jsou exportovány.

Exportovat šablonu před nasazením nebo z historie, když:

* Chcete šablonu snadno znovu.
* Nemusíte zahrnovat změny provedené po původního nasazení.

## <a name="export-template-from-resource-group"></a>Export šablony ze skupiny prostředků

Chcete-li exportovat jeden nebo více prostředků ze skupiny prostředků:

1. Vyberte skupinu prostředků obsahující prostředky, které chcete exportovat.

1. Pokud chcete exportovat všechny prostředky ve skupině prostředků, vyberte všechny a pak **exportovat šablonu**. **Exportovat šablonu** možnost stane aktivní jenom po vámi zvolená nejméně jeden prostředek.

   ![Exportovat všechny prostředky](./media/export-template-portal/select-all-resources.png)

1. Vybrat specifické prostředky pro export, zaškrtněte políčka vedle tyto prostředky. Vyberte **exportovat šablonu**.

   ![Vybrat prostředky pro export](./media/export-template-portal/select-resources.png)

1. Exportované šablony se zobrazí a je k dispozici ke stažení.

   ![Zobrazit šablonu](./media/export-template-portal/show-template.png)

## <a name="export-template-from-resource"></a>Export šablony z prostředku

Chcete-li exportovat jeden prostředek:

1. Vyberte skupinu prostředků obsahující zdroj, který chcete exportovat.

1. Vyberte prostředek, který chcete exportovat.

   ![Vybrat prostředek](./media/export-template-portal/select-link-resource.png)

1. Pro daný prostředek vyberte **exportovat šablonu** v levém podokně.

   ![Export prostředků](./media/export-template-portal/export-single-resource.png)

1. Exportované šablony se zobrazí a je k dispozici ke stažení. Šablona obsahuje pouze jeden prostředek.

## <a name="export-template-before-deployment"></a>Exportovat šablonu před nasazením

1. Vyberte službu Azure, které chcete nasadit.

1. Zadejte hodnoty pro novou službu.

1. Po úspěšném ověření, ale před zahájením nasazení, vyberte **stáhnout šablonu pro automatizaci**.

   ![Stažení šablony](./media/export-template-portal/download-before-deployment.png)

1. Šablona se zobrazí a je k dispozici ke stažení.

   ![Zobrazit šablonu](./media/export-template-portal/show-template-before-deployment.png)

## <a name="export-template-after-deployment"></a>Exportovat šablonu po nasazení

Můžete exportovat šablonu, která byla použita k nasazení existujících prostředků. Šablona, kterou získáte je přesně tu, která byla použita pro nasazení.

1. Vyberte skupinu prostředků, kterou chcete exportovat.

1. Vyberte odkaz pod **nasazení**.

   ![Vyberte historie nasazení](./media/export-template-portal/select-deployment-history.png)

1. Vyberte jedno z nasazení z historie nasazení.

   ![Výběr možnosti nasazení](./media/export-template-portal/select-details.png)

1. Vyberte **šablony**. Šablona použitá pro toto nasazení se zobrazí a je k dispozici ke stažení.

   ![Vyberte šablonu](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Další postup

- Další Azure Resource Manageru najdete v tématu [přehled Azure Resource Manageru](./resource-group-overview.md).
- Seznamte se se syntaxí šablony Resource Manageru, najdete v článku [Princip struktury a syntaxe šablon Azure Resource Manageru](./resource-group-authoring-templates.md).
- Zjistěte, jak vyvíjet šablony, najdete v článku [podrobné kurzy](/azure/azure-resource-manager/).
- Schémata šablon Azure Resource Manageru najdete v tématu [referenčními informacemi k šablonám](/azure/templates/).