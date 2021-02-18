---
title: Konfigurace trasy front-dveří Azure
description: V tomto článku se dozvíte, jak nakonfigurovat trasu mezi vašimi doménami a skupinami původu.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 94c22ffd423c32ba5f489298267464ca36abaecd
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098921"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>Konfigurace trasy služby Azure front-end Standard/Premium

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Tento článek vysvětluje každé nastavení používané při vytváření trasy front-AFD (Azure front-dveří) pro existující koncový bod. Po přidání vlastní domény a původu do stávajícího koncového bodu služby Azure front-end je potřeba nakonfigurovat trasu, abyste definovali přidružení mezi doménami a zdroji, aby bylo možné směrovat provoz mezi nimi.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Než budete moct nakonfigurovat trasu pro frontu Azure, musíte v rámci aktuálního koncového bodu vytvořit alespoň jednu skupinu zdrojů a jednu vlastní doménu. 

Pokud chcete nastavit skupinu původem, přečtěte si téma [Vytvoření nové skupiny zdrojů Azure front-end Standard/Premium](how-to-create-origin.md). 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>Vytvořit novou trasu služby Azure front-end Standard/Premium

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k profilu Standard/Premium pro Azure front.

1. V části **Nastavení** vyberte **správce koncových bodů** .
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Snímek obrazovky s nastavením správce koncových bodů předních dveří" lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. Pak vyberte **Upravit koncový bod** pro koncový bod, pro který chcete nakonfigurovat trasu.
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="Snímek obrazovky s výběrem možnosti upravit koncový bod":::

1. Zobrazí se stránka **Upravit koncový bod** . Vyberte **+ Přidat** pro trasy.
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="Snímek obrazovky s přidáním trasy na stránce Upravit koncový bod":::    
    
1. Na stránce **Přidat trasu** zadejte nebo vyberte následující informace.

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="Snímek obrazovky s přidáním stránky trasy" lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | Nastavení | Hodnota |
    | --- | --- |
    | Název | Zadejte jedinečný název nové trasy. |   
    | Doména| Vyberte jednu nebo více domén, které byly ověřeny a nejsou přidruženy k jiné trase. |
    | Vzorce, které se mají spárovat  | Nakonfigurujte všechny vzory cest URL, které bude tato trasa přijímat. Například můžete nastavit, `/images/*` aby se přijímaly všechny žádosti na adrese URL `www.contoso.com/images/*` . AFD se pokusí určit provoz na základě přesné shody, pokud žádné cesty přesně neshodují, a vyhledat zástupnou cestu, která odpovídá. Pokud se nenaleznou žádná pravidla směrování s porovnávací cestou, zamítnout žádost a vrátí 400: Chybná odpověď HTTP. |
    | Přijaté protokoly | Zadejte protokoly, které mají služby Azure front-Request přijmout, když klient vydává požadavek. |
    | Přesměrování | Určete, jestli se pro příchozí požadavek s požadavkem HTTP vynutil protokol HTTPS. |
    | Skupina původu | Vyberte, která skupina původu má být předána do okamžiku, kdy dojde k žádosti zpět na původ. |
    | Cesta ke zdroji | Zadejte cestu k prostředkům, které chcete uložit do mezipaměti. Pokud chcete v doméně dovolit ukládání jakéhokoli prostředku do mezipaměti, nechte toto nastavení prázdné. |
    | Protokol předávání | Vyberte protokol použitý pro přesměrování požadavku. |
    | Ukládání do mezipaměti | Tuto možnost vyberte, pokud chcete povolit ukládání statického obsahu do mezipaměti přes dvířka Azure. |
    | Pravidlo | Vyberte sady pravidel, které budou aplikovány na tuto trasu. Další informace o tom, jak nakonfigurovat pravidla, najdete v tématu [Konfigurace sady pravidel pro přední dveře Azure](how-to-configure-rule-set.md) . | 

1. Vyberte **Přidat** a vytvořte novou trasu. Trasa se zobrazí v seznamu tras pro koncový bod.
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="Snímek obrazovky se seznamem tras":::  
    
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete trasu odstranit, pokud ji už nepotřebujete, vyberte trasu a pak vyberte **Odstranit**. 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="Snímek obrazovky s postupem odstranění trasy":::  

## <a name="next-steps"></a>Další kroky
Další informace o vlastních doménách najdete v kurzu pro přidání vlastní domény do koncového bodu služby Azure front-dveří.

> [!div class="nextstepaction"]
> [Přidání vlastní domény]()
