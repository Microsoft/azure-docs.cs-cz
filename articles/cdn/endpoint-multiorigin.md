---
title: Azure CDN koncový bod s více zdroji (Preview)
description: Začínáme s více zdroji Azure CDN koncového bodu.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 9/06/2020
ms.author: allensu
ms.openlocfilehash: f9293206526778f8c3de8a368a1916a2cb3f88c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504669"
---
# <a name="azure-cdn-endpoint-multi-origin-preview"></a>Azure CDN koncový bod s více zdroji (Preview)

Podpora více zdrojů eliminuje prostoje a zavádí globální redundanci. 

Pokud v rámci Azure CDNho koncového bodu vyberete více původních míst, zajištěné redundance toto riziko rozšíří při zjišťování stavu každého původu a v případě potřeby dojde k převzetí služeb při selhání.

Nastavte jednu nebo více skupin původu a vyberte výchozí skupinu původní. Každá skupina původu je kolekce jednoho nebo více zdrojů, které mohou mít podobné úlohy.

> [!NOTE]
> V současné době je tato funkce dostupná jenom pro Azure CDN od Microsoftu. 

> [!IMPORTANT]
> Azure CDN koncový bod s více zdroji je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-the-origin-group"></a>Vytvoření skupiny původních souborů

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

2. Vyberte profil Azure CDN a pak vyberte koncový bod, který má být nakonfigurován pro více zdrojů.

3. V části **Nastavení** v konfiguraci koncového bodu vyberte **počátek** :

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="Koncový bod CDN" border="true":::

4. Chcete-li povolit více zdrojů, potřebujete alespoň jednu skupinu zdrojů. Vyberte **vytvořit zdrojovou skupinu**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Koncový bod CDN" border="true":::

5. V části **přidat zdrojovou konfiguraci skupiny** zadejte nebo vyberte následující informace:

   | Nastavení           | Hodnota                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Název zdrojové skupiny | Zadejte název skupiny původních souborů.                                   |
   | Stav testu paměti      | Vyberte **Povoleno**. </br> Azure CDN spustí sondy stavu z různých míst na celém světě a určí stav původu. Nepovolujte, pokud aktuální skupina původních zdrojů není aktivní, aby se předešlo dalším nákladům.
   | Cesta testu paměti        | Cesta v původním umístění, která se používá k určení stavu. |
   | Interval testu paměti    | Vyberte interval sondy 1, 2 nebo 4 minuty.                        |
   | Protokol sondy    | Vyberte **http** nebo **https**.                                         |
   | PROBE – metoda      | Vyberte **head** nebo **Get**.                                           |
   | Výchozí skupina původních souborů | Zaškrtněte políčko, které chcete nastavit jako výchozí skupinu původních souborů.
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Koncový bod CDN" border="true":::

6. Vyberte **Přidat**.

## <a name="add-multiple-origins"></a>Přidat více zdrojů

1. V nastavení zdroje pro koncový bod vyberte **+ vytvořit počátek**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Koncový bod CDN" border="true":::

2. V konfiguraci **Přidat zdroj** zadejte nebo vyberte následující informace:

   | Nastavení           | Hodnota                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Název        | Zadejte název původu.        |
   | Typ zdroje | Vyberte **úložiště**, **cloudovou službu**, **webovou aplikaci**nebo **vlastní zdroj**.                                   |
   | Název počátečního hostitele        | Vyberte nebo zadejte název hostitele původu.  Rozevírací seznam obsahuje všechny dostupné zdroje typu, který jste zadali v předchozím nastavení. Pokud jste jako typ zdroje vybrali možnost **vlastní zdroj** , zadejte doménu zdrojového serveru zákazníka. |
   | Hlavička počátečního hostitele    | Zadejte hlavičku hostitele, kterou má Azure CDN odeslat s každou žádostí, nebo ponechte výchozí nastavení.                        |
   | Port HTTP   | Zadejte port HTTP.                                         |
   | Port HTTPS     | Zadejte port HTTPS.                                           |
   | Priorita    | Zadejte číslo od 1 do 5.       |
   | Hmotnost      | Zadejte číslo mezi 1 a 1000.   |

    > [!NOTE]
    > Když se v rámci skupiny zdrojů vytvoří počátek, musí se mu poskytnout priorita a váha. Pokud má skupina původních zdrojů pouze jeden počátek, výchozí priorita a váha jsou nastaveny na hodnotu 1. Provoz se směruje na nejvyšší prioritu, pokud je zdroj v pořádku. Pokud je zjištěno, že původ není v pořádku, připojení se přesměrují na jiný počátek v pořadí podle priority. Pokud mají dva zdroje stejné priority, bude provoz distribuován podle váhy zadané pro původní zdroj. 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Koncový bod CDN" border="true":::

3. Vyberte **Přidat**.

4. Vyberte **Konfigurovat počátek** a nastavte původní cestu pro všechny zdroje:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Koncový bod CDN" border="true":::

5. Vyberte **OK**.

## <a name="configure-origins-and-origin-group-settings"></a>Konfigurace původních a nastavení skupiny původních

Až budete mít několik zdrojů původu a skupinu původních, můžete přidat nebo odebrat původci do různých skupin. Počátek v rámci stejné skupiny by měl sloužit podobně jako úlohy. Provoz se bude distribuovat do těchto zdrojů na základě jejich stavu v pořádku, priority a hodnoty váhy. 

1. V nastavení původ koncového bodu Azure CDN vyberte název skupiny původní, kterou chcete nakonfigurovat:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Koncový bod CDN" border="true":::

2. Ve **skupině původ aktualizace**vyberte **+ Vybrat počátek**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Koncový bod CDN" border="true":::

4. V rozevíracím seznamu vyberte počátek, který se má přidat do skupiny, a vyberte **OK**.

5. Ověřte, že zdroj byl přidán do skupiny, a pak vyberte **Uložit**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Koncový bod CDN" border="true":::

## <a name="remove-origin-from-origin-group"></a>Odebrat původ z původní skupiny

1. V nastavení původ koncového bodu Azure CDN vyberte název skupiny původních:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Koncový bod CDN" border="true":::

2. Chcete-li odebrat počátek ze skupiny původ, vyberte ikonu odpadkového koše vedle zdroje a vyberte **Uložit**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Koncový bod CDN" border="true":::

## <a name="override-origin-group-with-rules-engine"></a>Přepsat původovou skupinu pomocí stroje pravidel

Pomocí modulu Standard Rules můžete přizpůsobit způsob distribuce provozu do různých skupin zdrojů.

Distribuci provozu do jiné skupiny podle adresy URL požadavku.

1. V koncovém bodě CDN vyberte v části **Nastavení**možnost **modul pravidel** :

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="Koncový bod CDN" border="true":::

2. Vyberte **+ Přidat pravidlo**.

3. Do pole **název**zadejte název pravidla.

4. Vyberte **+ Podmínka**a pak vyberte **cesta URL**.

5. V rozevíracím seznamu **operátora** vyberte **Contains**.

6. Do **hodnoty**zadejte **/images**.

7. Vyberte **+ přidat akci**a pak vyberte **přepsání původní skupiny**.

8. V části **Skupina původ**vyberte skupinu původ v rozevíracím seznamu.

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="Koncový bod CDN" border="true":::

Pro všechny příchozí požadavky, pokud cesta URL obsahuje **/images**, se požadavek přiřadí do skupiny původ v části akce **(myorigingroup)**. 

## <a name="next-steps"></a>Další kroky
V tomto článku jste povolili Azure CDN koncového bodu s více zdroji.

Další informace o Azure CDN a dalších službách Azure, které jsou uvedené v tomto článku, najdete v tématech:

* [Azure CDN](./cdn-overview.md)
* [Porovnání funkce Azure CDN produktu](./cdn-features.md)
