---
title: Zabezpečení skóre v Azure Security Center | Dokumentace Microsoftu
description: " Prioritizujte vaše doporučení zabezpečení pomocí zabezpečení skóre ve službě Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: fc521db9ad753c4162b65abfd2f9f23c318fa994
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46131319"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Zlepšení hodnotíte zabezpečení ve službě Azure Security Center


S tolika služby nabízí výhody zabezpečení je často obtížné vědět, jaké kroky pro nejprve k zabezpečení a posílení vašich úloh. Skóre zabezpečení Azure Security Center kontroluje doporučení týkající se zabezpečení a upřednostňuje je pro vás, abyste věděli, jaké doporučení, jak provést jako první, pomáhá najít nejvážnější ohrožení zabezpečení, takže můžete určit prioritu prozkoumání. Zabezpečené skóre je měření nástroj, který pomáhá posílit ochranu zabezpečení k zajištění zabezpečeného úlohy.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]


![Řídicí panel skóre zabezpečení](./media/security-center-secure-score/secure-score-dashboard.png)

## <a name="secure-score-calculation"></a>Zabezpečení výpočtu skóre

Security Center napodobuje pracovní analytik zabezpečení, kontrola doporučení týkající se zabezpečení a použití pokročilých algoritmů jak zjistit, jak zásadní význam jednotlivých doporučení.
Azure Security center neustále kontroluje aktivní doporučení a vypočítá hodnotíte zabezpečené na jejich základě, skóre doporučení je odvozena od její závažnost a zabezpečení osvědčené postupy, které bude mít vliv na zabezpečení vašich úloh na maximum.

**Zabezpečení skóre** se počítají na základě poměru mezi prostředky v dobrém stavu a celkové množství prostředků. Pokud celkový počet prostředků rovná počtu prostředků v pořádku, můžete získat maximální zabezpečené skóre 50. A pokuste se získat zabezpečené skóre blíže na maximální skóre, opravte doporučeného prostředků není v pořádku.

Security Center také poskytuje zabezpečené skóre celkové. 

**Celkové skóre zabezpečení** je nahromadění všechna doporučení. Vaše celkové zabezpečení skóre můžete zobrazit přes vaše předplatná nebo skupiny pro správu, v závislosti na tom, co jste vybrali. Skóre se bude lišit v závislosti na vybrané předplatné a aktivní doporučení v těchto předplatných.

 

Pokud chcete zkontrolovat, které se doporučují největší vliv zabezpečené skóre, jejich činnost nejvíc ohrožují doporučení první 3 můžete zobrazit na řídicím panelu Security Center nebo můžete seřadit doporučení v okně seznamu doporučení pomocí **zabezpečené skóre dopad** sloupce.


Své skóre můžete zobrazit celkové zabezpečení:

1. Na řídicím panelu Azure, klikněte na tlačítko **Security Center** a potom klikněte na tlačítko **doporučení**.
2. V horní části uvidíte zabezpečené skóre, která představuje skóre na zásady, na vybraném předplatném. 
2. V následující tabulce, která obsahuje seznam doporučení uvidíte, že pro každé doporučení je sloupec, který představuje **zabezpečení skóre dopad**. Toto číslo představuje kolik hodnotíte celkové zabezpečení zlepší, pokud budete postupovat podle doporučení. Například na obrazovce níže, pokud jste **náprava ohrožení zabezpečení v kontejneru konfigurace zabezpečení**, zabezpečené skóre zvýší o 35 body.

   ![skóre zabezpečení](./media/security-center-secure-score/security-center-secure-score1.png)

## <a name="individual-secure-score"></a>Jednotlivé zabezpečené skóre

Kromě toho chcete-li zobrazit jednotlivé zabezpečené skóre, můžete najít v rámci okna individuální doporučení.  

**Doporučení zabezpečení skóre** se počítají na základě poměru mezi prostředky v dobrém stavu a celkové množství prostředků. Pokud celkový počet prostředků rovná počtu prostředků v pořádku, můžete získat maximální zabezpečené skóre doporučení. A pokuste se získat zabezpečené skóre blíže na maximální skóre, opravte není v pořádku prostředky podle nápravných kroků.

**Dopad doporučení** vás informuje, kolik vaše zabezpečené skóre zlepší, pokud použijete doporučené kroky. Například pokud vaše zabezpečené skóre je 42 a **dopad doporučení** + 3, pokud proveďte kroky popsané v doporučení zabezpečení skóre zlepší se 45.

Doporučení ukazuje hrozby, který je přístupný vašich úloh, pokud nejsou přijata nápravných kroků.

![skóre zabezpečení jednotlivých doporučení](./media/security-center-secure-score/indiv-recommendation-secure-score.png)

## <a name="next-steps"></a>Další postup
Tento článek vám ukázali, jak zlepšit stav vašeho zabezpečení pomocí **zabezpečené skóre** ve službě Azure Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.


