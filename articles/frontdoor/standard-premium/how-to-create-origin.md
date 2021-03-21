---
title: Nastavit počátek služby Azure front – Standard/Premium (Preview)
description: V tomto článku se dozvíte, jak nakonfigurovat počátek pomocí Správce koncových bodů.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ebc71ea2d354caf0c8f31b1231ecc1487237dd29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741884"
---
# <a name="set-up-an-azure-front-door-standardpremium-preview-origin"></a>Nastavit počátek služby Azure front – Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

V tomto článku se dozvíte, jak vytvořit počátek služby Azure front-end úrovně Standard/Premium v existující skupině zdrojů. 

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Předpoklady

Než budete moct vytvořit Azure front-end úrovně Standard/Premium, musíte mít vytvořenou aspoň jednu skupinu zdrojů.

## <a name="create-a-new-azure-front-door-standardpremium-origin"></a>Vytvořit nový počátek služby Azure front dveří úrovně Standard/Premium

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k profilu Standard/Premium pro Azure front.

1. Vyberte **skupinu původ**. Pak vyberte **+ Přidat** a vytvořte novou skupinu původní.
   
    :::image type="content" source="../media/how-to-create-origin/select-add-origin.png" alt-text="Snímek cílové stránky zdrojové skupiny":::

1. Na stránce **přidat zdrojovou skupinu** zadejte jedinečný **název** nové skupiny původní.

1. Pak vyberte **+ Přidat počátek** a přidejte do této skupiny původních zdrojů nový počátek. 

    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="Snímek obrazovky s přidáním zdrojové stránky":::
  
    | Nastavení | Hodnota |
    | --- | --- |
    | Název | Zadejte jedinečný název pro nový počátek služby Azure pro začátek dveří. |   
    | Typ zdroje | Typ prostředku, který chcete přidat. Služba Azure front bran Standard/Premium podporuje automatické zjišťování původu vaší aplikace ze služby App Service, cloudové služby nebo úložiště. Pokud chcete jiný prostředek v Azure nebo back-endu mimo Azure, vyberte **vlastní hostitel**. |
    | Název hostitele  | Pokud jste nevybrali možnost **vlastní hostitel** pro typ původu hostitele, vyberte back-end tak, že v rozevíracím seznamu vyberete název zdrojového hostitele. |
    | Hlavička počátečního hostitele | Zadejte hodnotu hlavičky hostitele, která se odesílá do back-endu pro každý požadavek. Další informace najdete v části [záhlaví hostitele původu](concept-origin.md#hostheader). |
    | Port HTTP | Zadejte hodnotu portu, který zdroj podporuje pro protokol HTTP. |
    | Port HTTPS | Zadejte hodnotu portu, který zdroj podporuje pro protokol HTTPS. |
    | Priorita | Přiřaďte priority k vašemu jinému zdroji, pokud chcete použít primární zdroj služby pro veškerý provoz. Dodejte také zálohy, pokud není k dispozici primární nebo záložní původ zálohy. Další informace najdete v tématu [Priorita](concept-origin.md#priority). |
    | Hmotnost | Přiřaďte váhy různým zdrojům k distribuci provozu napříč sadou původních míst, a to buď rovnoměrně, nebo podle váhy. Další informace najdete v tématu [váhy](concept-origin.md#weighted). |
    | Status | Tuto možnost vyberte, pokud chcete zdroj povolit. |
    | Pravidlo | Vyberte sady pravidel, které budou aplikovány na tuto trasu. Další informace o tom, jak nakonfigurovat pravidla, najdete v tématu [Konfigurace sady pravidel pro přední dveře Azure](how-to-configure-rule-set.md) . | 

    > [!IMPORTANT]
    > Při konfiguraci rozhraní API neověřuje, jestli je zdroj nepřístupný z prostředí front-dveří. Ujistěte se, že přední dveře můžou dosáhnout svého původu.

1. Vyberte **Přidat** a vytvořte nový počátek. Počátek by měl být v seznamu počátek se skupinou.
  
    :::image type="content" source="../media/how-to-create-origin/origin-list-view.png" alt-text="Snímek obrazovky s počátkem v zobrazení seznamu":::

1. Výběrem **Přidat** přidejte skupinu původ do aktuálního koncového bodu. Původní skupina by se měla zobrazit na panelu skupiny původní.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete odstranit skupinu původní, pokud ji už nepotřebujete, klikněte na **...** a pak v rozevíracím seznamu vyberte **Odstranit** .

:::image type="content" source="../media/how-to-create-origin/delete-origin-group.png" alt-text="Snímek obrazovky s postupem, jak odstranit skupinu původních souborů":::

Pokud ho chcete odstranit, pokud ho už nepotřebujete, klikněte na **...** a pak v rozevíracím seznamu vyberte **Odstranit** . 

:::image type="content" source="../media/how-to-create-origin/delete-origin-view.png" alt-text="Snímek obrazovky s postupem, jak odstranit počátek":::

## <a name="next-steps"></a>Další kroky

Další informace o vlastních doménách najdete v tématu [Přidání vlastní domény](how-to-add-custom-domain.md) do koncového bodu služby Azure front-end Standard/Premium.
