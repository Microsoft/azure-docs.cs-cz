---
title: Integrace pracovních položek (Preview) – Application Insights
description: Naučte se vytvářet pracovní položky v GitHubu nebo v Azure DevOps s Application Insights daty, která jsou v nich vložená.
ms.topic: conceptual
ms.date: 02/9/2021
ms.openlocfilehash: ba0a67bad3ba47191414d6b406ab6cb4e6b7da78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731914"
---
# <a name="work-item-integration-preview"></a>Integrace pracovních položek (Preview)

Funkce integrace pracovních položek vám umožní snadno vytvářet pracovní položky v GitHubu nebo v Azure DevOps, které mají relevantní data Application Insights v nich vložená.

> [!IMPORTANT]
> Integrace pracovních položek je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-and-configure-a-work-item-template"></a>Vytvoření a konfigurace šablony pracovní položky

1. Šablonu pracovní položky vytvoříte tak, že přejdete na svůj prostředek Application Insights a na levé straně v části *Konfigurovat* vybrané **pracovní položky** a potom v horní části vyberte **vytvořit novou šablonu** .

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" Snímek karty pracovní položky s vybranou položkou vytvořit novou šablonu" lightbox="./media/work-item-integration/create-work-item-template.png":::

    Šablonu pracovní položky můžete vytvořit také na kartě Podrobnosti transakce od začátku až do konce, pokud aktuálně neexistuje žádná šablona. Vyberte událost a na pravé straně vyberte **vytvořit pracovní položku** a pak **začít se šablonou sešitu**.

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" Snímek obrazovky se záložkou podrobnosti o celkové transakci s vytvořením pracovní položky, začněte s vybraným šablonou sešitu." lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. Po výběru možnosti **vytvořit novou šablonu** můžete zvolit systémy sledování, pojmenovat sešit, vytvořit odkaz na vybraný systém sledování a zvolit oblast pro ukládání šablony (výchozí je oblast, ve které je prostředek Application Insights umístěný). Parametry adresy URL představují výchozí adresu URL vašeho úložiště, například `https://github.com/myusername/reponame` nebo `https://mydevops.visualstudio.com/myproject` .

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" Snímek obrazovky s vytvořením nové šablony sešitu pracovní položky":::

## <a name="create-a-work-item"></a>Vytvořit pracovní položku

 K nové šabloně můžete přistupovat z jakékoli koncové podrobnosti transakce, ke kterým můžete přistupovat z výkonu, chyb, dostupnosti nebo jiných karet.

1. Chcete-li vytvořit pracovní položku Přejít na podrobnosti transakce, vyberte událost, vyberte možnost **vytvořit pracovní položku** a zvolte šablonu pracovní položky.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" Snímek obrazovky s podrobnostmi o koncovém konci transakce s vybranou položkou vytvořit pracovní položku." lightbox="./media/work-item-integration/create-work-item.png":::

1. Otevře se nová karta v prohlížeči, na které se bude vybírat systém sledování. V Azure DevOps můžete vytvořit chybu nebo úlohu a na GitHubu můžete vytvořit nový problém v úložišti. Nová pracovní položka se automaticky vytvoří s kontextové informace, které poskytuje Application Insights.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" Snímek obrazovky s automaticky vytvořeným problémem GitHubu" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Snímek obrazovky s automatickou tvorbou chyb v Azure DevOps." lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>Úprava šablony

Chcete-li upravit šablonu, v části *Konfigurovat* klikněte na kartu **pracovní položky** a vyberte ikonu tužky vedle sešitu, který chcete aktualizovat.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" Snímek karty pracovní položky s vybranou ikonou upravit tužku":::

![V horní části vyberte upravit ikonu úprav ](./media/work-item-integration/edit-icon.png) a začněte upravovat šablonu. Šablony pracovních položek jsou založené na [Azure monitor sešitech](../visualize/workbooks-overview.md). Informace o pracovní položce se generují pomocí dotazovacího jazyka pro klíčové slovo. Dotazy můžete upravit a přidat tak pro svůj tým další kontext. Po dokončení úprav sešit uložte kliknutím na ikonu Uložit ikonu ![ Uložit ](./media/work-item-integration/save-icon.png) na horním panelu nástrojů.

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" Snímek sešitu šablony pracovní položky v režimu úprav" lightbox="./media/work-item-integration/edit-workbook.png":::

Můžete vytvořit více než jednu konfiguraci pracovní položky a mít vlastní sešit, který bude splňovat jednotlivé scénáře. Sešity je také možné nasadit pomocí Azure Resource Manager zajišťovat standardní implementace napříč vašimi prostředími.