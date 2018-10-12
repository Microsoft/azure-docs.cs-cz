---
title: Potlačení chování HTTP pomocí pravidel modulu Azure CDN | Dokumentace Microsoftu
description: Stroj pravidel umožňuje přizpůsobit zpracování požadavků HTTP pomocí Azure CDN, třeba blokování doručování určitého typu obsahu, definovat zásady ukládání do mezipaměti a změnit hlavičky protokolu HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 2ac43b472758f3403bc87bf3d64321eb97109f53
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092384"
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Potlačení chování HTTP pomocí Azure CDN stroje pravidel
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled
Stroj pravidel Azure CDN umožňuje přizpůsobit zpracování požadavků HTTP. Třeba blokování doručování určité typy obsahu, definice zásady ukládání do mezipaměti nebo úpravy hlavičky protokolu HTTP. Tento kurz ukazuje, jak vytvořit pravidlo, které mění chování ukládání do mezipaměti CDN prostředků. Další informace o syntaxi stroj pravidel najdete v tématu [referenční informace ke stroji pravidel Azure CDN](cdn-rules-engine-reference.md).

## <a name="access"></a>Access
Pro přístup k stroj pravidel, musíte nejprve vybrat **spravovat** od horního okraje **profil CDN** stránku a přístup ke stránce pro správu Azure CDN. V závislosti na tom, jestli váš koncový bod je optimalizovaná pro akcelerace dynamického webu (DSA) pak přistupujete stroj pravidel sadu pravidel, které jsou vhodné pro váš typ koncového bodu:

- Koncové body optimalizovaný pro obecné doručování webu nebo jiná optimalizace agenta DSA: 
    
    Vyberte **HTTP velké** kartu a potom vyberte **stroj pravidel**.

    ![Stroj pravidel pro protokol HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Koncové body optimalizovaná pro DSA: 
    
    Vyberte **ADN** kartu a potom vyberte **stroj pravidel**. 
    
    ADN je termín používaný v Verizon k určení obsahu DSA. Všechna pravidla, které zde vytvoříte jsou ignorovány všechny koncové body ve vašem profilu, které nejsou optimalizovány pro DSA. 

    ![Stroj pravidel pro DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Kurz
1. Z **profil CDN** stránce **spravovat**.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.

2. Vyberte **HTTP velké** kartu a potom vyberte **stroj pravidel**.
   
    Zobrazí se možnosti pro nové pravidlo.
   
    ![Nové možnosti pravidlo CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > Pořadí, ve kterém jsou uvedeny více pravidel ovlivňuje, jak se zpracovává. Akce zadané předchozí pravidlo může přepsat následující pravidlo.
   > 

3. Zadejte název do pole **název / popis** textového pole.

4. Identifikujte typy požadavků, které se pravidlo vztahuje. Použít výchozí podmínku shody, **vždy**. 
   
   ![Podmínka shody pravidla CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Více podmínky shody jsou k dispozici v rozevíracím seznamu. Informace o podmínce aktuálně vybraného shody vyberte na modrou informační ikonu na levé straně.
   > 
   >  Podrobný seznam podmíněné výrazy, naleznete v tématu [podmíněné výrazy stroje pravidel](cdn-rules-engine-reference-match-conditions.md).
   >  
   > Podrobný seznam podmínky shody, naleznete v tématu [podmínky shody stroje pravidel](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 

5. Chcete-li přidat novou funkci, **+** vedle **funkce**.  V rozevírací nabídce na levé straně vyberte **platnost vnitřní Max-Age**.  Do textového pole, která se zobrazí, zadejte **300**. Neměňte zbývající výchozí hodnoty.
   
   ![Funkce pravidlo CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Několik funkcí jsou k dispozici v rozevíracím seznamu. Informace o aktuálně vybrané funkce vyberte na modrou informační ikonu na levé straně. 
   >
   > Pro **platnost vnitřní Max-Age**, prostředku `Cache-Control` a `Expires` hlavičky jsou přepsány řídit, kdy na hraničním uzlu CDN aktualizuje prostředek ze zdroje. V tomto příkladu na hraničním uzlu CDN ukládá do mezipaměti asset 300 sekund, neboli 5 minut, než aktualizuje prostředek z původu.
   > 
   > Podrobný seznam funkcí najdete v tématu [funkce stroje pravidel](cdn-rules-engine-reference-features.md).
   > 
   > 

6. Vyberte **přidat** uložit nové pravidlo.  Nové pravidlo je nyní čeká na schválení. Poté, co byla schválena, stav se změní z **čekající XML** k **aktivní XML**.
   
   > [!IMPORTANT]
   > Změny pravidel může trvat až 10 minut na dokončení propagace přes Azure CDN.
   > 
   > 

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled služby Azure CDN](cdn-overview.md)
* [Referenční informace ke stroji pravidel](cdn-rules-engine-reference.md)
* [Podmínky shody stroje pravidel](cdn-rules-engine-reference-match-conditions.md)
* [Podmíněné výrazy stroje pravidel](cdn-rules-engine-reference-conditional-expressions.md)
* [Funkce stroje pravidel](cdn-rules-engine-reference-features.md)
* [Azure Friday: Azure CDN výkonné nové funkce verze premium](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (video)