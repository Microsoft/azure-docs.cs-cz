---
title: Potlačení chování HTTP pomocí stroj pravidel Azure CDN | Microsoft Docs
description: Stroj pravidel umožňuje přizpůsobit způsob zpracování požadavků HTTP pomocí Azure CDN, třeba blokování doručování určité typy obsahu, definovat zásady ukládání do mezipaměti a změnit hlavičky protokolu HTTP.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: df8114aaf5b4672ea51482978abde6f0ce724528
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261045"
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Potlačení chování HTTP pomocí stroj pravidel Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Stroj pravidel Azure CDN umožňuje přizpůsobit způsob zpracování požadavků HTTP. Například blokování doručování určité typy obsahu, definování zásady ukládání do mezipaměti nebo úprava záhlaví HTTP. Tento kurz ukazuje, jak vytvořit pravidlo, které změní chování ukládání do mezipaměti CDN prostředků. Další informace o syntaxi stroj pravidel najdete v tématu [odkaz na modul Azure CDN pravidla](cdn-rules-engine-reference.md).

## <a name="access"></a>Access
Pro přístup k stroj pravidel, musíte nejdřív vybrat **spravovat** z horní části **profil CDN** stránky pro přístup na stránce Správa Azure CDN. V závislosti na tom, jestli váš koncový bod je optimalizovaná pro dynamických webů akcelerace (DSA) pak přístup stroj pravidel s sadu pravidel, které jsou vhodné pro váš typ koncového bodu:

- Koncové body optimalizované pro doručení obecné webové nebo další optimalizace agenta DSA: 
    
    Vyberte **HTTP velké** a potom vyberte **stroj pravidel**.

    ![Stroj pravidel pro protokol HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Koncové body optimalizované pro agenta DSA: 
    
    Vyberte **ADN** a potom vyberte **stroj pravidel**. 
    
    ADN je termín Verizon používá k určení DSA obsah. Všechna pravidla, které zde vytvoříte jsou ignorovány všechny koncovými body v profilu aplikace, které nejsou optimalizovány pro DSA. 

    ![Stroj pravidel pro agenta DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Kurz
1. Z **profil CDN** vyberte **spravovat**.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.

2. Vyberte **HTTP velké** a potom vyberte **stroj pravidel**.
   
    Zobrazí se možnosti pro nové pravidlo.
   
    ![Nové možnosti pravidlo CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > Pořadí, ve kterém jsou uvedeny víc pravidel, ovlivňuje způsob zpracování. Následující pravidlo může přepsat akce zadané předchozí pravidlem.
   > 

3. Zadejte název do pole **název nebo popis** textové pole.

4. Určete typ požadavků, které se pravidlo vztahuje. Použít výchozí podmínku shoda, **vždy**. 
   
   ![Stav shody pravidlo CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Více podmínek shody jsou k dispozici v rozevíracím seznamu. Informace o stavu aktuálně vybrané shody vyberte ikonu blue informační na levé straně.
   > 
   >  Podrobný seznam podmíněné výrazy, najdete v části [pravidla modul podmíněné výrazy](cdn-rules-engine-reference-match-conditions.md).
   >  
   > Podrobný seznam podmínky shody, najdete v části [pravidla podmínky shody modul](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 

5. Chcete-li přidat nové funkce, vyberte **+** vedle položky **funkce**.  V rozevírací nabídce na levé straně vyberte **Force interní Max-Age**.  Do textového pole, která se zobrazí, zadejte **300**. Neměňte zbývající výchozí hodnoty.
   
   ![Funkce pravidlo CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Více funkcí jsou k dispozici v rozevíracím seznamu. Informace o aktuálně vybrané funkce vyberte ikonu blue informační na levé straně. 
   >
   > Pro **Force interní Max-Age**, assetu `Cache-Control` a `Expires` hlavičky jsou přepsaná řídit, kdy hraničního uzlu CDN aktualizuje prostředek z tohoto počátku. V tomto příkladu hraničního uzlu CDN ukládá do mezipaměti na 300 sekund, neboli 5 minut, než se aktualizuje prostředek z jeho počátku asset.
   > 
   > Podrobný seznam funkcí najdete v tématu [pravidla modul funkce](cdn-rules-engine-reference-features.md).
   > 
   > 

6. Vyberte **přidat** uložit nové pravidlo.  Nové pravidlo je nyní čeká na schválení. Po schválení, stav se změní z **čekající XML** k **Active XML**.
   
   > [!IMPORTANT]
   > Změny pravidel může trvat až 10 minut rozšíří v rámci Azure CDN.
   > 
   > 

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled služby Azure CDN](cdn-overview.md)
* [Referenční informace ke stroji pravidel](cdn-rules-engine-reference.md)
* [Podmínky shody stroje pravidel](cdn-rules-engine-reference-match-conditions.md)
* [Podmíněné výrazy stroje pravidel](cdn-rules-engine-reference-conditional-expressions.md)
* [Funkce stroje pravidel](cdn-rules-engine-reference-features.md)
* [Azure pátek: Azure CDN výkonné nové prémiové funkce](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (video)