---
title: Vynutilit HTTPS pomocí modulu Azure CDN Standard Rules Engine | Microsoft Docs
description: Modul Standard Rules umožňuje přizpůsobit, jak jsou požadavky HTTP zpracovávány Azure CDN od společnosti Microsoft, jako je například blokování doručování určitých typů obsahu, definování zásad ukládání do mezipaměti a úprava hlaviček protokolu HTTP.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: b24c4a04e0c02258a918ee075066d90c22ea0c75
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615920"
---
# <a name="azure-cdn-standard-rules-engine"></a>Modul Standard Rules Azure CDN

> [!NOTE]
> Modul Standard Rules je k dispozici pouze pro Azure CDN od společnosti Microsoft. 

Modul Standard Rules Azure CDN umožňuje přizpůsobit zpracování požadavků HTTP. Například vynucování doručování obsahu přes konkrétní protokoly, definování zásad ukládání do mezipaměti nebo změna hlavičky protokolu HTTP. V tomto kurzu se dozvíte, jak vytvořit pravidlo, které automaticky přesměruje uživatele na HTTPS. 


## <a name="tutorial"></a>Kurz

1. Na stránce **profilu CDN** v Azure CDN v části profily Microsoftu vyberte koncový bod, pro který chcete pravidla nakonfigurovat.
  
2. Na levé straně vyberte kartu **modul pravidel** .
   
    Zobrazí se okno modul pravidel s globálním pravidlem. 
   
    [Stránka ![nové pravidla CDN](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > Pořadí, ve kterém jsou uvedeny více pravidel, má vliv na způsob zpracování. Následné pravidlo může přepsat akce zadané předchozím pravidlem.
   >

3. Klikněte na tlačítko **Přidat pravidlo** a zadejte název pravidla. Názvy pravidel musí začínat písmenem a obsahovat jenom číslice a písmena.

4. Identifikujte typ požadavků, na které se pravidlo vztahuje. Pomocí rozevírací nabídky vyberte podmínku shody **protokolu požadavků** a použijte hodnotu **rovná** se **http**.
   
   [Podmínka shody pravidla ![CDN](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > V rozevíracím seznamu jsou k dispozici různé podmínky shody. Podrobný seznam podmínek shody najdete v tématu [podmínky shody stroje pravidel](cdn-standard-rules-engine-match-conditions.md).
   
5. Vyberte akci, která bude použita u identifikovaných požadavků. Pomocí rozevíracího seznamu vyberte akci **přesměrování adresy URL** a pro protokol použijte hodnotu **found (302)** pro typ a **https** . Pokud chcete použít příchozí hodnoty, ponechte všechna ostatní pole prázdná.
   
   [akce pravidla CDN ![](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > V rozevíracím seznamu je k dispozici více akcí. Podrobný seznam akcí najdete v tématu věnovaném [činnostem modulu pravidel](cdn-standard-rules-engine-actions.md).

6. Kliknutím na **Uložit** uložte nové pravidlo.  Nové pravidlo bude nyní nasazeno.
   
   > [!IMPORTANT]
   > Šíření změn pravidel může trvat až 15 minut, než se Azure CDN.
   >
   

## <a name="see-also"></a>Viz také

- [Přehled Azure CDN](cdn-overview.md)
- [Referenční příručka stroje standardních pravidel](cdn-standard-rules-engine-reference.md)
- [Podmínky shody stroje standardních pravidel](cdn-standard-rules-engine-match-conditions.md)
- [Akce stroje standardních pravidel](cdn-standard-rules-engine-actions.md)
