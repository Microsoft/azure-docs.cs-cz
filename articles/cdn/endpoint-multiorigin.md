---
title: Azure CDN koncový bod s více zdroji
description: Začínáme s více zdroji Azure CDN koncového bodu.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 5/8/2020
ms.author: allensu
ms.openlocfilehash: 628a284335b7285a2ee989511b130fb7d4d3cde6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597874"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN koncový bod s více zdroji

Díky podpoře více zdrojů můžete vytvořit globální redundanci a odstranit výpadky tím, že v rámci Azure CDNho koncového bodu vyberete více původních míst. Redundance poskytovaná více zdroji rizika rozšíří stav každého původu a v případě potřeby převezme služby při selhání. Pro nastavení více zdrojů nastavte jednu nebo více skupin původu. Každá skupina původu je kolekce jednoho nebo více zdrojů, které mohou mít podobné úlohy.

> [!NOTE]
> V současné době je tato funkce dostupná jenom pro Azure CDN od Microsoftu. 

## <a name="create-the-origin-group"></a>Vytvoření skupiny původních souborů

1. Přihlaste se k [Azure Portal](https://portal.azure.com)

2. Vyberte profil Azure CDN a pak vyberte koncový bod, který má být nakonfigurován pro více zdrojů.

3. V části **Nastavení** v konfiguraci koncového bodu vyberte **počátek** :

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="Azure CDN více zdrojů" border="true":::

4. Chcete-li povolit více zdrojů, potřebujete alespoň jednu skupinu zdrojů. Vyberte **vytvořit zdrojovou skupinu**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Azure CDN více zdrojů" border="true":::

5. V části **přidat zdrojovou konfiguraci skupiny** zadejte nebo vyberte následující informace:

   | Nastavení           | Hodnota                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Název zdrojové skupiny | Zadejte název skupiny původních souborů.                                   |
   | Cesta testu paměti        | Cesta v původním umístění, která se používá k určení stavu. |
   | Interval testu paměti    | Vyberte interval sondy 1, 2 nebo 4 minuty.                        |
   | Protokol sondy    | Vyberte **http** nebo **https**.                                         |
   | PROBE – metoda      | Vyberte **head** nebo **Get**.                                           |
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Azure CDN více zdrojů" border="true":::

6. Vyberte **Přidat**.

7. Pokud chcete zvolit výchozí skupinu původu, vyberte **Konfigurovat skupinu původu**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-4.png" alt-text="Azure CDN více zdrojů" border="true":::

8. V rozevíracím seznamu vyberte svou zdrojovou skupinu a vyberte **OK**.

## <a name="add-multiple-origins"></a>Přidat více zdrojů

1. V nastavení zdroje pro koncový bod vyberte **+ vytvořit počátek**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Azure CDN více zdrojů" border="true":::

2. V konfiguraci **Přidat zdroj** zadejte nebo vyberte následující informace:

   | Nastavení           | Hodnota                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Typ zdroje | Vyberte **úložiště**, **cloudovou službu**, **webovou aplikaci**nebo **vlastní zdroj**.                                   |
   | Název počátečního hostitele        | Vyberte nebo zadejte název hostitele původu.  Rozevírací seznam obsahuje všechny dostupné zdroje typu, který jste zadali v předchozím nastavení. Pokud jste jako typ zdroje vybrali možnost **vlastní zdroj** , zadejte doménu zdrojového serveru zákazníka. |
   | Hlavička počátečního hostitele    | Zadejte hlavičku hostitele, kterou má Azure CDN odeslat s každou žádostí, nebo ponechte výchozí nastavení.                        |
   | Port HTTP   | Zadejte port HTTP.                                         |
   | Port HTTPS     | Zadejte port HTTPS.                                           |
   | Priorita    | Zadejte číslo od 1 do 5.       |
   | Hmotnost      | Zadejte číslo mezi 1 a 1000.   |

    > [!NOTE]
    > Když se v rámci skupiny zdrojů vytvoří počátek, musí se mu poskytnout priorita a váha. Pokud má skupina původních zdrojů pouze jeden počátek, výchozí priorita a váha jsou nastaveny na hodnotu 1. Provoz se směruje na nejvyšší prioritu, pokud je zdroj v pořádku. Pokud je zjištěno, že původ není v pořádku, připojení se přesměrují na jiný počátek v pořadí podle priority. Pokud mají dva zdroje stejné priority, bude provoz distribuován podle váhy zadané pro původní zdroj. 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Azure CDN více zdrojů" border="true":::

3. Vyberte **Přidat**.

4. Vyberte **Konfigurovat počátek** a nastavte původní cestu pro všechny zdroje:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Azure CDN více zdrojů" border="true":::

5. Vyberte **OK**.

## <a name="configure-origins-and-origin-group-settings"></a>Konfigurace původních a nastavení skupiny původních

Až budete mít několik zdrojů původu a skupinu původních, můžete přidat nebo odebrat původci do různých skupin. Počátek v rámci stejné skupiny by měl sloužit podobně jako úlohy. Provoz se bude distribuovat do těchto zdrojů na základě jejich stavu v pořádku, priority a hodnoty váhy. 

1. V nastavení původ koncového bodu Azure CDN vyberte **Konfigurovat skupinu původu**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Azure CDN více zdrojů" border="true":::

2. V rozevíracím seznamu vyberte skupinu původních dat, kterou chcete nakonfigurovat, a vyberte **OK**.

3. Ve **skupině původ aktualizace**vyberte **+ Přidat počátek**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Azure CDN více zdrojů" border="true":::

4. V rozevíracím seznamu vyberte počátek, který se má přidat do skupiny, a vyberte **OK**.

5. Ověřte, že zdroj byl přidán do skupiny, a pak vyberte **Uložit**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Azure CDN více zdrojů" border="true":::

## <a name="remove-origin-from-origin-group"></a>Odebrat původ z původní skupiny

1. V nastavení původ koncového bodu Azure CDN vyberte **Konfigurovat skupinu původu**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Azure CDN více zdrojů" border="true":::

2. V rozevíracím seznamu vyberte skupinu původních dat, kterou chcete nakonfigurovat, a vyberte **OK**.

3. Chcete-li odebrat počátek ze skupiny původ, vyberte ikonu odpadkového koše vedle zdroje a vyberte **Uložit**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Azure CDN více zdrojů" border="true":::

## <a name="next-steps"></a>Další kroky
V tomto článku jste povolili Azure CDN koncového bodu s více zdroji.

Další informace o Azure CDN a dalších službách Azure, které jsou uvedené v tomto článku, najdete v tématech:

* [Azure CDN](./cdn-overview.md)
* [Porovnání funkce Azure CDN produktu](./cdn-features.md)
