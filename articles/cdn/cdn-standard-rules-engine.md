---
title: Vynucení protokolu HTTPS ve standardním azure cdn pomocí modulu pravidel | Dokumenty společnosti Microsoft
description: Pomocí modulu pravidel pro Microsoft Standard Azure Content Delivery Network (Azure CDN) můžete přizpůsobit způsob, jakým Azure CDN zpracovává požadavky HTTP, včetně blokování doručování určitých typů obsahu, definování zásad ukládání do mezipaměti a úpravy hlaviček HTTP. V tomto článku se dozvíte, jak vytvořit pravidlo pro přesměrování uživatelů na protokol HTTPS.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 724861305d7a25db409072200ac2bc3bd83f0682
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171571"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Nastavení modulu standardních pravidel pro Azure CDN

Tento článek popisuje, jak nastavit a používat modul standardních pravidel pro síť doručování obsahu Azure (Azure CDN).

## <a name="standard-rules-engine"></a>Modul standardních pravidel

Modul standardních pravidel pro Azure CDN můžete přizpůsobit způsob zpracování požadavků HTTP. Modul pravidel můžete například použít k vynucení doručování obsahu k použití určitých protokolů, k definování zásad ukládání do mezipaměti nebo k úpravě hlavičky PROTOKOLU HTTP. Tento článek ukazuje, jak vytvořit pravidlo, které automaticky přesměruje uživatele na protokol HTTPS. 

> [!NOTE]
> Modul pravidel, který je popsán v tomto článku je k dispozici pouze pro standardní Azure CDN od společnosti Microsoft. 

## <a name="redirect-users-to-https"></a>Přesměrovat uživatele na protokol HTTPS

1. Ve svých profilech Microsoftu přejděte do sítě Pro doručování obsahu Azure.

1. Na stránce **profilu CDN** vyberte koncový bod, pro který chcete vytvořit pravidla.
  
1. Vyberte kartu **Modul pravidel.**
   
    Otevře se podokno **Modul pravidel** a zobrazí seznam dostupných globálních pravidel. 
   
    [![Stránka nových pravidel Azure CDN](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > Pořadí, ve kterém jsou uvedeny více pravidel ovlivňuje způsob zpracování pravidel. Akce, které jsou zadány v pravidle, mohou být přepsány následným pravidlem.
   >

1. Vyberte **Přidat pravidlo** a zadejte název pravidla. Názvy pravidel musí začínat písmenem a mohou obsahovat pouze čísla a písmena.

1. Chcete-li určit typ požadavků, na které se pravidlo vztahuje, vytvořte podmínku shody:
    1. Vyberte **Přidat podmínku**a pak vyberte podmínku **shody protokolu Požadavku.**
    1. Jako **Operátor** vyberte **Rovná se**.
    1. V **popřípadě Hodnota**vyberte **možnost HTTP**.
   
   [![Podmínka shody pravidla azure CDN](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > V rozevíracím seznamu **Přidat podmínky** můžete vybrat z více podmínek shody. Podrobný seznam podmínek shody naleznete [v tématu Podmínky shody v modulu standardních pravidel](cdn-standard-rules-engine-match-conditions.md).
   
1. Vyberte akci, která se má použít pro požadavky, které splňují podmínku shody:
   1. Vyberte **Přidat akci**a pak vyberte **přesměrování adresy URL**.
   1. V **pole Typ**vyberte **Položku Nalezeno (302).**
   1. V **případě protokolu**vyberte možnost **HTTPS**.
   1. Chcete-li použít příchozí hodnoty, ponechejte všechna ostatní pole prázdná.
   
   [![Akce pravidla Azure CDN](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > V rozevíracím seznamu **Přidat akce** můžete vybrat z více akcí. Podrobný seznam akcí naleznete [v tématu Akce v modulu standardních pravidel](cdn-standard-rules-engine-actions.md).

6. Chcete-li uložit nové pravidlo, vyberte **uložit.** Pravidlo je nyní k dispozici pro použití.
   
   > [!IMPORTANT]
   > Změny pravidel může trvat až 15 minut k šíření prostřednictvím Azure CDN.
   >
   

## <a name="next-steps"></a>Další kroky

- [Přehled azure cdn](cdn-overview.md)
- [Reference ke stroji pravidel Standard](cdn-standard-rules-engine-reference.md)
- [Podmínky shody v modulu standardních pravidel](cdn-standard-rules-engine-match-conditions.md)
- [Akce v modulu standardních pravidel](cdn-standard-rules-engine-actions.md)
