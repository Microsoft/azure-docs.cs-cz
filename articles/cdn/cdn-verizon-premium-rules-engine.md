---
title: Přepsat chování PROTOKOLU HTTP pomocí modulu pravidel Azure CDN – Verizon Premium
description: Modul pravidel umožňuje přizpůsobit, jak jsou požadavky HTTP zpracovány Azure CDN z Verizon Premium, jako je blokování doručování určitých typů obsahu, definovat zásady ukládání do mezipaměti a upravit hlavičky HTTP.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: aa0606eafb8fe4c517b0c18e0137058a120115ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082958"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Přepsat chování PROTOKOLU HTTP pomocí modulu pravidel Azure CDN z verizon premium

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled

Modul pravidel Azure CDN umožňuje přizpůsobit způsob zpracování požadavků HTTP. Například blokování doručování určitých typů obsahu, definování zásad ukládání do mezipaměti nebo úprava hlavičky HTTP. Tento kurz ukazuje, jak vytvořit pravidlo, které změní chování ukládání do mezipaměti prostředků CDN. Další informace o syntaxi modulu pravidel naleznete v [tématu Azure CDN rules engine reference](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Access

Chcete-li získat přístup k modulu pravidel, musíte **nejprve** vybrat spravovat z horní části stránky **profilu CDN** pro přístup ke stránce správy Azure CDN. V závislosti na tom, zda je koncový bod optimalizován pro dynamickou akceleraci webu (DSA), pak získáte přístup k modulu pravidel se sadou pravidel vhodných pro váš typ koncového bodu:

- Koncové body optimalizované pro obecné doručování webu nebo jinou optimalizaci mimo DSA:
    
    Vyberte kartu **Velký protokol HTTP** a potom vyberte Modul **pravidel**.

    ![Modul pravidel pro protokol HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Koncové body optimalizované pro DSA:
    
    Vyberte kartu **ADN** a pak vyberte **Modul pravidel**.
    
    ADN je termín používaný společností Verizon k určení obsahu DSA. Všechna pravidla, která zde vytvoříte, budou ignorována všemi koncovými body ve vašem profilu, které nejsou optimalizovány pro DSA.

    ![Pravidla motorpro DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Kurz

1. Na stránce **profilu CDN** vyberte **Spravovat**.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.

2. Vyberte kartu **Velký protokol HTTP** a potom vyberte Modul **pravidel**.
   
    Zobrazí se možnosti nového pravidla.
   
    ![Nové možnosti pravidel CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > Pořadí, ve kterém jsou uvedeny více pravidel ovlivňuje způsob jejich zpracování. Následné pravidlo může přepsat akce určené předchozím pravidlem.
   >

3. Zadejte název do textového pole **Název / Popis.**

4. Určete typ požadavků, na které se pravidlo vztahuje. Použijte výchozí podmínku shody **Vždy**.
   
   ![Podmínka shody pravidla CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > V rozevíracím seznamu je k dispozici více podmínek shody. Informace o aktuálně vybrané podmínce shody získáte výběrem modré informační ikony vlevo.
   >
   >  Podrobný seznam podmíněných výrazů naleznete v tématu [Pravidla modulu podmíněné výrazy](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >  
   > Podrobný seznam podmínek shody naleznete v tématu [Pravidla, které jsou v souladu s podmínkami shody motoru](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Chcete-li přidat novou **+** funkci, vyberte tlačítko vedle **položky Funkce**.  V rozevíracím souboru vlevo vyberte **Vynutit vnitřní maximální stáří**.  Do textového pole, které se zobrazí, zadejte **300**. Neměňte zbývající výchozí hodnoty.
   
   ![Funkce pravidla CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > V rozevíracím seznamu je k dispozici více funkcí. Informace o aktuálně vybrané funkci získáte výběrem modré informační ikony vlevo.
   >
   > V **případě vynucení vnitřní maximální hodnoty max-age**jsou přepsány `Cache-Control` a `Expires` záhlaví datového zdroje jsou přepsány, aby bylo možné určit, kdy hraniční uzel CDN aktualizuje datový zdroj od počátku. V tomto příkladu uzel okraje CDN ukládá prostředek do mezipaměti po dobu 300 sekund nebo 5 minut, než aktualizuje datový zdroj od jeho počátku.
   >
   > Podrobný seznam funkcí naleznete v tématu [Pravidla funkce motoru](cdn-verizon-premium-rules-engine-reference-features.md).
   >
   >

6. Vyberte **Přidat,** chcete-li uložit nové pravidlo.  Nové pravidlo nyní čeká na schválení. Po schválení se stav změní z **Čekající XML** na **Active XML**.
   
   > [!IMPORTANT]
   > Šíření pravidel prostřednictvím Azure CDN může trvat až 10 minut.
   >
   >

## <a name="see-also"></a>Viz také

- [Přehled azure cdn](cdn-overview.md)
- [Odkaz na modul pravidel](cdn-verizon-premium-rules-engine-reference.md)
- [Pravidla, které jsou v souladu s podmínkami motoru](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Podmíněné výrazy modulu pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkce modulu pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure Fridays: Nové výkonné prémiové funkce Azure CDN](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (video)