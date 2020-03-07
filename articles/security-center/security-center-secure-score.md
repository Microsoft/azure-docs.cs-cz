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
ms.openlocfilehash: 15a94a9724fac29d36f6bb88ee4810b3bc7ca607
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394649"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Vylepšit vaše zabezpečené skóre v Azure Security Center

> [!NOTE]
> Ve verzi Preview je k dispozici vylepšené zabezpečené skóre. Rozšířené zabezpečené skóre nakonec nahradí stávající bezpečné skóre, ale v době, kdy budou spuštěny souběžně, aby se přechod usnadnil.
>
> Podrobnosti o výhodách rozšířeného zabezpečeného skóre najdete [tady](secure-score-security-controls.md).
>
> Pokud se chcete účastnit verze Preview, otevřete portál Azure, spusťte Azure Security Center a vyberte zabezpečené skóre. Odtud se zobrazí banner v horní části stránky, která nabízí nové možnosti bezpečného skóre. Případně klikněte [sem](https://aka.ms/ascnewscore).

Díky mnoha službám, které nabízí výhody zabezpečení, je často obtížné zjistit, jaké kroky je potřeba nejdřív zabezpečit a posílit vaše úlohy. Bezpečnostní skóre kontroluje vaše doporučení zabezpečení a určí jejich prioritu za vás, takže víte, jaká doporučení je třeba provést jako první. To vám pomůže najít závažná slabá místa zabezpečení, abyste mohli určit prioritu šetření. Bezpečné skóre je nástroj, který vám pomůže vyhodnotit stav zabezpečení zatížení.

## <a name="secure-score-calculation"></a>Výpočet skóre zabezpečení

Security Center napodobuje práci s analytikem zabezpečení, kontroluje doporučení zabezpečení a pomocí pokročilých algoritmů určuje, jak je každé doporučení důležité.
Azure Security Center neustále kontroluje aktivní doporučení a počítá vaše zabezpečené skóre na základě nich, skóre doporučení je odvozeno od závažnosti a osvědčených postupů zabezpečení, které mají vliv na zabezpečení vašich úloh.

Security Center také poskytuje **celkové skóre**. 

**Celkově bezpečné skóre** je akumulace všech hodnocení vašich doporučení. V závislosti na tom, co jste vybrali, si můžete v rámci předplatných nebo skupin pro správu zobrazit celkové bezpečné skóre. Skóre se bude lišit v závislosti na vybraném předplatném a aktivních doporučeních pro tyto odběry.

Pokud chcete zjistit, která doporučení mají největší dopad na vaše zabezpečené skóre, můžete na řídicím panelu Security Center zobrazit první tři nejdůležitější doporučení, nebo můžete tato doporučení seřadit v okně se seznamem doporučení pomocí sloupce **zabezpečený dopad skóre** .

Zobrazení celkového zabezpečeného skóre:

1. Na řídicím panelu Azure klikněte na **Security Center** a pak na **zabezpečené skóre**.

2. V horní části uvidíte nejdůležitější body zabezpečeného skóre:
   - **Celkový počet bodů zabezpečení** představuje skóre na základě jednotlivých zásad na vybrané předplatné.
   - V části **zabezpečení skóre podle kategorie** se dozvíte, které prostředky potřebují nejvíc pozornosti.
   - **Nejdůležitější doporučení podle zabezpečeného dopadu na skóre** poskytují seznam doporučení, která budou vylepšit vaše zabezpečené skóre, pokud je budete implementovat.
 
   ![Bezpečné skóre](./media/security-center-secure-score/secure-score-dashboard.png)

3. V následující tabulce vidíte každé z vašich předplatných a celkové bezpečné skóre pro každý z nich.

   > [!NOTE]
   > Součet zabezpečeného skóre každého předplatného se nerovná celkovému počtu zabezpečených bodů. Bezpečné skóre je výpočet založený na poměru mezi vašimi dobrými prostředky a celkovými prostředky na doporučení, nikoli součtem bezpečných skóre v rámci vašich předplatných. 
   >
4. Kliknutím na **Zobrazit doporučení** zobrazíte doporučení k tomuto předplatnému, které můžete opravit, aby se zlepšilo vaše zabezpečené skóre.
4. V seznamu doporučení vidíte, že pro každé doporučení existuje sloupec, který představuje **dopad na zabezpečený skóre**. Toto číslo představuje, kolik vašich celkových bezpečných skóre bude vylepší, pokud budete postupovat podle doporučení. Například na následující obrazovce, pokud v **konfiguraci zabezpečení kontejneru opravíte chyby zabezpečení**, vaše zabezpečené skóre se zvýší o 35 bodů.

   ![Bezpečné skóre](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Individuální bezpečnostní skóre

Pokud chcete zobrazit jednotlivá zabezpečená skóre, najdete je v okně individuální doporučení.  

**Skóre bezpečné pro doporučení** je výpočet založený na poměru mezi dobrými a celkovými prostředky. Pokud se počet zdravých prostředků rovná celkovému počtu prostředků, získáte maximální bezpečné skóre doporučení 50. Pokud se chcete pokusit získat zabezpečené skóre Blíže k maximálnímu skóre, opravte prostředky, které nejsou v pořádku, pomocí doporučených postupů.

**Dopad na doporučení** vám umožní zjistit, kolik bezpečného skóre se zlepšuje, pokud použijete postup doporučení. Pokud je například vaše zabezpečené skóre 42 a **dopad na doporučení** je + 3, provedením kroků popsaných v doporučení Vylepšete Vaše skóre, aby se staly 45.

Doporučení ukazuje, které hrozby vaše zatížení budou vystaveny, pokud nejsou podniknuty kroky k nápravě.

![Bezpečnostní skóre jednotlivých doporučení](./media/security-center-secure-score/indiv-recommendation-secure-score.png)




## <a name="next-steps"></a>Další kroky
Tento článek vám ukázal, jak vylepšit stav zabezpečení pomocí **zabezpečeného skóre** v Azure Security Center. Další informace o Security Center najdete v těchto tématech:

* [Nejčastější dotazy Azure Security Center](faq-general.md)– Přečtěte si nejčastější dotazy týkající se služby a zabezpečení skóre.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Bezpečné skóre – vylepšené](secure-score-security-controls.md)– Přečtěte si o výhodách aktuálně vylepšeného zabezpečeného skóre v současnosti ve verzi Preview.