---
title: Zabezpečené skóre v Azure Security Center | Dokumenty společnosti Microsoft
description: " Upřednostněte doporučení zabezpečení pomocí Zabezpečené skóre v Centru zabezpečení Azure. "
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
ms.openlocfilehash: 30405ce5cc875144fcd1cf83d4a3f883a0304989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415769"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Vylepšete své skóre zabezpečení v Azure Security Center

> [!NOTE]
> Tento článek pojednává o předchozí verzi zabezpečeného skóre. Toto prostředí zabezpečené skóre je stále k dispozici z uživatelského rozhraní, ale bude postupně vyřazena v průběhu času. Dvě možnosti zabezpečenéskóre běží vedle sebe, aby umožnily hladší přechod.
>
> Podrobnosti o novějším zabezpečeném skóre naleznete [zde](secure-score-security-controls.md).
>

S tolika službami, které nabízejí výhody zabezpečení, je často těžké vědět, jaké kroky je třeba nejprve podniknout k zabezpečení a posílení pracovního vytížení. Skóre zabezpečení zkontroluje vaše bezpečnostní doporučení a upřednostní je za vás, abyste věděli, která doporučení chcete provést jako první. To vám pomůže najít nejzávažnější chyby zabezpečení, abyste mohli upřednostnit vyšetřování. Secure Score je nástroj, který vám pomůže posoudit stav zabezpečení pracovního vytížení.

## <a name="secure-score-calculation"></a>Výpočet zabezpečeného skóre

Security Center napodobuje práci analytika zabezpečení, kontroluje vaše doporučení zabezpečení a používá pokročilé algoritmy k určení, jak zásadní je každé doporučení.
Azure Security Center neustále kontroluje vaše aktivní doporučení a vypočítá vaše skóre zabezpečení na základě nich, skóre doporučení je odvozeno z jeho závažnosti a zabezpečení osvědčených postupů, které budou mít vliv na zabezpečení úlohy nejvíce.

Security Center také poskytuje **celkové skóre zabezpečení**. 

**Celkové skóre Secure je** akumulace všech vašich doporučení skóre. Celkové skóre zabezpečení můžete zobrazit v rámci předplatných nebo skupin pro správu v závislosti na tom, co vyberete. Skóre se bude lišit v závislosti na vybraném předplatném a aktivních doporučeních pro tato předplatná.

Chcete-li zkontrolovat, která doporučení mají největší vliv na vaše skóre zabezpečení, můžete zobrazit tři nejpůsobivější doporučení na řídicím panelu Centra zabezpečení nebo můžete seřadit doporučení v okně seznamu doporučení pomocí sloupce **Dopad na zabezpečené skóre.**

Zobrazení celkového skóre zabezpečení:

1. Na řídicím panelu Azure klikněte na **Security Center** a potom klikněte na **Zabezpečené skóre**.

2. V horní části můžete vidět Secure Score zdůrazňuje:
   - **Celkové skóre zabezpečení** představuje skóre podle zásad na vybrané předplatné.
   - **Bezpečné skóre podle kategorií** ukazuje, které zdroje vyžadují největší pozornost
   - **Nejlepší doporučení podle dopadu zabezpečeného skóre** vám poskytne seznam doporučení, která nejvíce zlepší vaše skóre zabezpečení, pokud je implementujete.
 
   ![Bezpečnostní skóre](./media/security-center-secure-score/secure-score-dashboard.png)

3. V následující tabulce si můžete prohlédnout každé z vašich předplatných a celkové skóre zabezpečení pro každé z nich.

   > [!NOTE]
   > Součet skóre zabezpečení každého předplatného se nerovná celkovému skóre zabezpečení. Zabezpečené skóre je výpočet založený na poměru mezi prostředky v pořádku a celkovými prostředky na doporučení, nikoli součtem zabezpečeného skóre napříč vašimi předplatnými. 
   >
4. Kliknutím na **Zobrazit doporučení** zobrazíte doporučení pro toto předplatné, která můžete napravit, abyste zlepšili své skóre zabezpečení.
4. V seznamu doporučení uvidíte, že pro každé doporučení je sloupec, který představuje **vliv zabezpečenéskóre**. Toto číslo představuje, o kolik se vaše celkové skóre zabezpečení zlepší, pokud budete postupovat podle doporučení. Pokud například na obrazovce níže **napravíte slabá místa v konfiguracích zabezpečení kontejneru**, vaše skóre Zabezpečení se zvýší o 35 bodů.

   ![Bezpečnostní skóre](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Individuální skóre zabezpečení

Kromě toho můžete zobrazit jednotlivé zabezpečené skóre, najdete je v rámci jednotlivých doporučení.  

**Skóre zabezpečení doporučení** je výpočet založený na poměru mezi prostředky v pořádku a celkovými zdroji. Pokud se počet prostředků v pořádku rovná celkovému počtu zdrojů, získáte maximální skóre zabezpečení doporučení 50. Chcete-li se pokusit získat zabezpečené skóre blíže k maximálnískóre, opravit nezdravé prostředky podle doporučení.

**Dopad doporučení** vám umožní zjistit, jak moc se vaše skóre zabezpečení zlepšuje, pokud použijete kroky doporučení. Pokud je například vaše skóre zabezpečení 42 a **dopad doporučení** je +3, provedení kroků uvedených v doporučení zlepší vaše skóre tak, aby se stalo 45.

Doporučení ukazuje, které hrozby vaše úloha je vystavena, pokud nejsou podniknuty nápravné kroky.

![individuální doporučení bezpečné skóre](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>Další kroky
V tomto článku se ukazuje, jak zlepšit stav zabezpečení pomocí **zabezpečeného skóre** v Centru zabezpečení Azure. Další informace o Centru zabezpečení najdete v tématu:

* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
