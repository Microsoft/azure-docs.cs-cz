---
title: Použití modulu pravidel k vymáhání HTTPS ve standardu Azure CDN | Microsoft Docs
description: Pomocí modulu pravidel pro Microsoft Standard Azure Content Delivery Network (Azure CDN) můžete přizpůsobit způsob, jakým Azure CDN zpracovává požadavky HTTP, včetně blokování doručování určitých typů obsahu, definování zásad ukládání do mezipaměti a změny hlaviček protokolu HTTP. V tomto článku se dozvíte, jak vytvořit pravidlo pro přesměrování uživatelů na HTTPS.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 724861305d7a25db409072200ac2bc3bd83f0682
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171571"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Nastavení modulu Standard rules pro Azure CDN

Tento článek popisuje, jak nastavit a používat modul Standard rules pro Azure Content Delivery Network (Azure CDN).

## <a name="standard-rules-engine"></a>Modul standardních pravidel

K přizpůsobení způsobu zpracování požadavků HTTP můžete použít modul Standard rules pro Azure CDN. Například můžete použít modul pravidel k vymáhání doručování obsahu pro použití specifických protokolů, k definování zásad ukládání do mezipaměti nebo k úpravě hlavičky protokolu HTTP. Tento článek ukazuje, jak vytvořit pravidlo, které automaticky přesměruje uživatele na HTTPS. 

> [!NOTE]
> Stroj pravidel, který je popsaný v tomto článku, je k dispozici pouze pro standardní Azure CDN od společnosti Microsoft. 

## <a name="redirect-users-to-https"></a>Přesměrování uživatelů na HTTPS

1. Ve svých profilech Microsoftu se přečtěte do Azure Content Delivery Network.

1. Na stránce **profil CDN** vyberte koncový bod, pro který chcete vytvořit pravidla.
  
1. Vyberte kartu **modul pravidel** .
   
    Otevře se podokno **modul pravidel** , ve kterém se zobrazí seznam dostupných globálních pravidel. 
   
    [Stránka ![Azure CDN nová pravidla](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > Pořadí, ve kterém jsou uvedeny více pravidel, má vliv na způsob zpracování pravidel. Akce, které jsou zadány v pravidle, mohou být přepsány následujícím pravidlem.
   >

1. Vyberte **Přidat pravidlo** a zadejte název pravidla. Názvy pravidel musí začínat písmenem a můžou obsahovat jenom číslice a písmena.

1. Chcete-li určit typ požadavků, na které se pravidlo vztahuje, vytvořte podmínku shody:
    1. Vyberte **Přidat podmínku**a pak vyberte podmínku shody **protokolu požadavků** .
    1. Jako **operátor**vyberte **Equals (rovná**se).
    1. Jako **hodnotu**vyberte **http**.
   
   [Podmínka shody pravidla Azure CDN ![](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > V rozevíracím seznamu **Přidat podmínku** můžete vybrat z několika podmínek shody. Podrobný seznam podmínek shody najdete v tématu [podmínky shody v modulu Standard Rules](cdn-standard-rules-engine-match-conditions.md).
   
1. Vyberte akci, která se má použít u požadavků splňujících podmínky shody:
   1. Vyberte **přidat akci**a pak vyberte **přesměrování adresy URL**.
   1. Jako **typ**vyberte **Nalezeno (302)** .
   1. V případě **protokolu**vyberte **https**.
   1. Pokud chcete použít příchozí hodnoty, ponechte všechna ostatní pole prázdná.
   
   [akce pravidla ![Azure CDN](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > V rozevíracím seznamu **přidat akci** můžete vybrat z několika akcí. Podrobný seznam akcí najdete [v tématu akce v modulu Standard Rules](cdn-standard-rules-engine-actions.md).

6. Kliknutím na **Uložit** uložte nové pravidlo. Pravidlo je nyní k dispozici pro použití.
   
   > [!IMPORTANT]
   > Provedení změn pravidel může trvat až 15 minut, než se Azure CDN rozšíří.
   >
   

## <a name="next-steps"></a>Další kroky

- [Přehled Azure CDN](cdn-overview.md)
- [Referenční příručka stroje standardních pravidel](cdn-standard-rules-engine-reference.md)
- [Podmínky shody v modulu Standard Rules](cdn-standard-rules-engine-match-conditions.md)
- [Akce v modulu Standard Rules](cdn-standard-rules-engine-actions.md)
