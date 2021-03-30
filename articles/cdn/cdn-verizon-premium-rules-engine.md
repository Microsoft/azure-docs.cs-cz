---
title: Přepsání chování HTTP pomocí stroje Azure CDN-Verizon Premium Rules
description: Modul pravidel umožňuje přizpůsobit, jak jsou požadavky HTTP zpracovávány Azure CDN od Verizon Premium, jako je blokování doručování určitých typů obsahu, definování zásad ukládání do mezipaměti a úprava hlaviček protokolu HTTP.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: a49912bc2275e478d657f06587c4ddc830210d3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87040218"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Přepsat chování HTTP pomocí Azure CDN z modulu pravidel Verizon Premium

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled

Modul pravidel Azure CDN umožňuje přizpůsobit zpracování požadavků HTTP. Například blokování doručování určitých typů obsahu, definování zásad ukládání do mezipaměti nebo změna hlavičky protokolu HTTP. V tomto kurzu se dozvíte, jak vytvořit pravidlo, které mění chování ukládání prostředků CDN do mezipaměti. Další informace o syntaxi modulu pravidla najdete v tématu [Referenční dokumentace modulu Azure CDN Rules](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Access

Chcete-li získat přístup k modulu pravidel, je nutné nejprve vybrat možnost **Spravovat** v horní části stránky **profilu CDN** a získat přístup ke stránce pro správu Azure CDN. V závislosti na tom, jestli je váš koncový bod optimalizovaný pro akceleraci dynamického webu (DSA), získáte přístup ke stroji pravidel se sadou pravidel, která jsou vhodná pro váš typ koncového bodu:

- Koncové body optimalizované pro obecné doručování webu nebo jiné optimalizace bez DSA:
    
    Vyberte možnost **Velká karta http** a pak vyberte **modul pravidel**.

    ![Modul pravidel pro HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Koncové body optimalizované pro DSA:
    
    Vyberte kartu **a** a pak vyberte **modul pravidel**.
    
    A je termín používaný Verizon k určení obsahu DSA. Všechna pravidla, která tady vytvoříte, budou ignorována všemi koncovými body v profilu, které nejsou optimalizované pro DSA.

    ![Modul pravidel pro DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Kurz

1. Na stránce **profil CDN** vyberte **Spravovat**.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.

2. Vyberte možnost **Velká karta http** a pak vyberte **modul pravidel**.
   
    Zobrazí se možnosti pro nové pravidlo.
   
    ![Možnosti nového pravidla CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > Pořadí, ve kterém jsou uvedeny více pravidel, má vliv na způsob zpracování. Následné pravidlo může přepsat akce zadané předchozím pravidlem. Máte-li například pravidlo, které umožňuje přístup k prostředku na základě vlastnosti žádosti a pravidla, které odepře přístup všem požadavkům, druhé pravidlo přepíše první z nich. Pravidla přepíšou předchozí pravidla pouze v případě, že budou pracovat se stejnými vlastnostmi.
   >

3. Do textového pole **název/popis** zadejte název.

4. Identifikujte typ požadavků, na které se pravidlo vztahuje. Použijte výchozí podmínku shody, **vždy**.
   
   ![Podmínka shody pravidla CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > V rozevíracím seznamu jsou k dispozici různé podmínky shody. Pokud chcete získat informace o aktuálně vybrané podmínce shody, vyberte modrou informační ikonu vlevo.
   >
   >  Podrobný seznam podmíněných výrazů najdete v tématu [podmíněné výrazy stroje pravidel](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >  
   > Podrobný seznam podmínek shody najdete v tématu [podmínky shody stroje pravidel](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Chcete-li přidat novou funkci, vyberte **+** tlačítko vedle **položky funkce**.  V rozevíracím seznamu na levé straně vyberte **Vynutit interní maximum – stáří**.  Do textového pole, které se zobrazí, zadejte **300**. Neměňte zbývající výchozí hodnoty.
   
   ![Funkce pravidla CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > V rozevíracím seznamu je k dispozici více funkcí. Pokud chcete získat informace o aktuálně vybrané funkci, vyberte modrou informační ikonu vlevo.
   >
   > Pro **vynucení interního maximálního stáří** se v `Cache-Control` případě, že `Expires` hraniční uzel CDN aktualizuje Asset z původního zdroje, přepíše a hlavičky assetu se přepíšou. V tomto příkladu hraniční uzel CDN uloží Asset do mezipaměti po dobu 300 sekund nebo 5 minut, než se aktivuje ze svého původu.
   >
   > Podrobný seznam funkcí najdete v tématu [funkce stroje pravidel](cdn-verizon-premium-rules-engine-reference-features.md).
   >
   >

6. Vyberte **Přidat** a uložte nové pravidlo.  Nové pravidlo nyní čeká na schválení. Po schválení se stav změní z **PENDING XML** na **aktivní XML**.
   
   > [!IMPORTANT]
   > Provedení změn pravidel může trvat až 10 minut, než se Azure CDN rozšíří.
   >
   >

## <a name="see-also"></a>Viz také

- [Přehled Azure CDN](cdn-overview.md)
- [Odkazy na modul pravidel](cdn-verizon-premium-rules-engine-reference.md)
- [Podmínky shody stroje pravidel](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Podmíněné výrazy stroje pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkce stroje pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure pátek: výkonné nové funkce Premium](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (video) Azure CDN