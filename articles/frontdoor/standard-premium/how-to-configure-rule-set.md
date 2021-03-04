---
title: 'Přední dvířka Azure: konfigurace sady pravidel pro přední dveře'
description: Tento článek poskytuje pokyny ke konfiguraci sady pravidel.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 6863c492059ccee152ecf3d03a09e61793576bcb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715594"
---
# <a name="configure-a-rule-set-with-azure-front-door-standardpremium-preview"></a>Konfigurace sady pravidel pomocí Azure front-dveří Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Tento článek ukazuje, jak vytvořit sadu pravidel a první sadu pravidel v Azure Portal. Pak se dozvíte, jak přidružit sadu pravidel ke trase ze stránky sady pravidel nebo ze Správce koncových bodů.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

* Než budete moct nakonfigurovat sadu pravidel, musíte nejdřív vytvořit frontu Azure na úrovni Standard/Premium. Další informace najdete v tématu [rychlý Start: vytvoření profilu služby Azure front-end Standard/Premium](create-front-door-portal.md).

## <a name="configure-rule-set-in-azure-portal"></a>Konfigurovat sadu pravidel v Azure Portal

1. V rámci profilu front-dveří vyberte v části **Nastavení** možnost **sada pravidel** . Vyberte **Přidat** a zadejte název sady pravidel.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="Snímek obrazovky s cílovou stránkou sady pravidel":::
    
1. Vyberte **Přidat pravidlo** a vytvořte své první pravidlo. Zadejte název pravidla. Pak vyberte **Přidat podmínku** nebo **přidat akci** a definujte pravidlo. Můžete přidat až 10 podmínek a 5 akcí pro jedno pravidlo. V tomto příkladu používáme proměnnou serveru k přidání hlavičky odpovědi 8Geo-Country * pro žádosti, které v adrese URL obsahují *Contoso* .

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="Snímek obrazovky se stránkou konfigurace sady pravidel":::
    
    > [!NOTE]
    > * Pokud chcete odstranit podmínku nebo akci z pravidla, můžete použít koš na pravé straně konkrétní podmínky nebo akce.
    > * Pokud chcete vytvořit pravidlo, které platí pro veškerý příchozí provoz, nezadávejte žádné podmínky.
    > * Pokud chcete zastavit vyhodnocení zbývajících pravidel v případě splnění konkrétního pravidla, klikněte na **zastavit vyhodnocení zbývajícího pravidla**. Pokud je tato možnost zaškrtnutá a všechna zbývající pravidla v sadě pravidel nebudou provedena bez ohledu na to, zda byly splněny podmínky porovnání.  

1. Můžete určit prioritu pravidel v rámci sady pravidel pomocí tlačítek se šipkami a přesunout pravidla na vyšší nebo nižší prioritu. Seznam je ve vzestupném pořadí, takže nejdůležitější pravidlo je uvedené jako první.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="Snímek obrazovky s prioritou sady pravidel" lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. Po vytvoření jednoho nebo více pravidel vyberte Save ( **Uložit** ) a dokončete vytvoření sady pravidel.

1. Teď přidružte sadu pravidel k trase, aby se mohla projevit. Můžete přidružit sadu pravidel prostřednictvím stránky sady pravidel, nebo můžete přejít na správce koncových bodů a vytvořit přidružení.
 
    **Stránka sady pravidel**: 
    
    1. Vyberte sadu pravidel, která se má přidružit.
    
    1. Vyberte *nepřidružený* odkaz.
     

    1. Pak na stránce **přidružit trasu** vyberte koncový bod a trasu, kterou chcete přidružit k sadě pravidel. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="Snímek obrazovky s vytvořením stránky trasy":::    
        
    1. Pokud je v rámci vybrané trasy více sad pravidel, vyberte možnost *Další* pro změnu pořadí sad pravidel. Sada pravidel se spustí shora dolů. Objednávky můžete změnit výběrem sady pravidel a přesunutím nahoru nebo dolů. Pak vyberte *přidružit*.
    
        > [!Note]
        > K jedné trase na této stránce můžete přidružit jenom jednu sadu pravidel. Pokud chcete přidružit sadu pravidel s více trasami, použijte prosím správce koncových bodů.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="Snímek obrazovky s pořadím sady pravidel":::
    
    1. Sada pravidel je teď přidružená k trase. Můžete se podívat na hlavičku odpovědi a zobrazit geografickou zemi.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="Snímek obrazovky s pravidlem přidruženým k trase":::

   **Správce koncového bodu**: 
    
    1. V části správce koncového bodu vyberte koncový bod, který chcete přidružit k sadě pravidel.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="Snímek obrazovky s výběrem koncového bodu ve Správci koncových bodů" lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. Vyberte *Upravit koncový bod*.  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="Snímek obrazovky s výběrem možnosti upravit koncový bod ve Správci koncových bodů" lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. Vyberte trasu. 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="Snímek obrazovky s výběrem trasy":::
    
    1. Na stránce *aktualizovat postup* vyberte v části *pravidla* sady pravidel, které chcete přidružit k trase z rozevíracího seznamu. Pak můžete změnit objednávky přesunutím nastavení pravidla směrem nahoru a dolů. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="Snímek obrazovky aktualizace stránky trasy":::
    
    1. Pak vyberte *aktualizovat* nebo *Přidat* a dokončete přidružení.

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>Odstraní sadu pravidel z vašeho profilu front-dveří Azure.

V předchozích krocích jste nakonfigurovali a přidružili sadu pravidel ke své trase. Pokud už nechcete sadu pravidel spojenou s vašimi předními dveřmi, můžete sadu pravidel odebrat provedením následujících kroků:

1. Na **stránce sada pravidel** v části **Nastavení** zrušte přidružení sady pravidel ze všech přidružených tras.

1. Rozbalte cestu, vyberte tři tečky. Pak vyberte *Upravit trasu*.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="Snímek obrazovky s trasou rozbalenou v sadě pravidel":::

1. V části přejít na pravidla na stránce trasa vyberte sadu pravidel a vyberte na tlačítku *Odstranit* . 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="Snímek stránky aktualizační trasa pro odstranění sady pravidel" lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. Vyberte *aktualizovat* a sada pravidel se odřadí z trasy.

1. Zopakováním kroků 2-5 zrušte přidružení jiných tras, které jsou přidružené k této sadě pravidel, dokud se nezobrazí stav trasy *Nepřidruženo*.

1. Pro sadu pravidel, která je *nepřidružená*, můžete sadu pravidel odstranit kliknutím na tři tečky na pravé straně a výběrem *Odstranit*. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="Snímek obrazovky s postupem, jak odstranit sadu pravidel":::

1. Sada pravidel je nyní odstraněna.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak přidat [záhlaví zabezpečení se sadou pravidel](how-to-add-security-headers.md).
