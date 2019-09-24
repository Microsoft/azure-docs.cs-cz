---
title: Bezpečné skóre v Azure Security Center | Microsoft Docs
description: " Určete prioritu doporučení zabezpečení pomocí zabezpečeného skóre v Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 0a39fd1870ae803f2cf694e01c67d125fc96cac9
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200970"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Vylepšit vaše zabezpečené skóre v Azure Security Center


Díky mnoha službám, které nabízí výhody zabezpečení, je často obtížné zjistit, jaké kroky je potřeba nejdřív zabezpečit a posílit vaše úlohy. Skóre zabezpečení Azure kontroluje vaše doporučení zabezpečení a určí jejich prioritu za vás, takže víte, jaká doporučení je třeba provést jako první. To vám pomůže najít závažná slabá místa zabezpečení, abyste mohli určit prioritu šetření. Secure Score je nástroj, který pomáhá vyhodnotit stav zabezpečení vašich úloh.

## <a name="secure-score-calculation"></a>Výpočet skóre zabezpečení

Security Center napodobuje práci s analytikem zabezpečení, kontroluje doporučení zabezpečení a pomocí pokročilých algoritmů určuje, jak je každé doporučení důležité.
Azure Security Center neustále kontroluje aktivní doporučení a počítá vaše zabezpečené skóre na základě nich, skóre doporučení je odvozeno od závažnosti a osvědčených postupů zabezpečení, které mají vliv na zabezpečení vašich úloh.

Security Center také poskytuje **celkové skóre**. 

**Celkově bezpečné skóre** je akumulace všech hodnocení vašich doporučení. V závislosti na tom, co jste vybrali, si můžete v rámci předplatných nebo skupin pro správu zobrazit celkové bezpečné skóre. Skóre se bude lišit v závislosti na vybraném předplatném a aktivních doporučeních pro tyto odběry.

 
Pokud chcete zjistit, která doporučení mají dopad na vaše zabezpečené skóre, můžete na řídicím panelu Security Center zobrazit první tři nejdůležitější doporučení, případně můžete uvedená doporučení seřadit v okně se seznamem doporučení s použitím **dopadu na zabezpečený výsledek** . kolo.


Zobrazení celkového zabezpečeného skóre:

1. Na řídicím panelu Azure klikněte na **Security Center** a pak na **zabezpečené skóre**.
2. V horní části uvidíte nejdůležitější body zabezpečeného skóre:
   - **Celkové bezpečnostní skóre** představuje skóre pro zásady a pro zvolené předplatné.
   - V části **zabezpečení skóre podle kategorie** se dozvíte, které prostředky potřebují nejvíc pozornosti.
   - **Nejdůležitější doporučení podle zabezpečeného dopadu na skóre** poskytují seznam doporučení, která budou vylepšit vaše zabezpečené skóre, pokud je budete implementovat.
 
   ![Bezpečné skóre](./media/security-center-secure-score/secure-score-dashboard.png)

3. V následující tabulce vidíte každé z vašich předplatných a celkové bezpečné skóre pro každý z nich.

   > [!NOTE]
   > Součet skóre zabezpečení u jednotlivých předplatných se nerovná celkovému skóre zabezpečení. Skóre zabezpečení se počítá na základě poměru mezi prostředky, které jsou v pořádku, a vašimi celkovými prostředky v rámci doporučení, nikoli jako součet skóre zabezpečení napříč předplatnými. 
   >
4. Kliknutím na **Zobrazit doporučení** zobrazíte doporučení k tomuto předplatnému, které můžete opravit, aby se zlepšilo vaše zabezpečené skóre.
4. V seznamu doporučení vidíte, že pro každé doporučení existuje sloupec, který představuje **dopad na zabezpečený skóre**. Toto číslo představuje, kolik vašich celkových bezpečných skóre bude vylepší, pokud budete postupovat podle doporučení. Například na následující obrazovce, pokud v **konfiguraci zabezpečení kontejneru opravíte chyby zabezpečení**, vaše zabezpečené skóre se zvýší o 35 bodů.

   ![Bezpečné skóre](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Individuální bezpečnostní skóre

Pokud chcete zobrazit jednotlivá zabezpečená skóre, najdete je v okně individuální doporučení.  

**Doporučené skóre zabezpečení** se počítá na základě poměru mezi prostředky, které jsou v pořádku, a vašimi celkovými prostředky. Pokud se počet prostředků, které jsou v pořádku, rovná celkovému počtu prostředků, bude mít maximální skóre zabezpečení u doporučení hodnotu 50. Pokuste se dosáhnout skóre zabezpečení, které bude blíže maximální hodnotě tak, že opravíte prostředky, které nejsou v pořádku, podle následujících doporučení.

**Dopad doporučení** vám dává informace, o kolik se zlepší skóre zabezpečení, pokud použijete doporučený postup. Pokud je například vaše skóre zabezpečení 42 a **Dopad doporučení** je +3, zvýšíte provedením kroků v doporučení skóre na hodnotu 45.

Doporučení ukazuje, které hrozby vaše zatížení budou vystaveny, pokud nejsou podniknuty kroky k nápravě.

![bezpečnostní skóre jednotlivých doporučení](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Další kroky
Tento článek vám ukázal, jak vylepšit stav zabezpečení pomocí **zabezpečeného skóre** v Azure Security Center. Další informace o Security Center najdete v těchto tématech:

* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.


