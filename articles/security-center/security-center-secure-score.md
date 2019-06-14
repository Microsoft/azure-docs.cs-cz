---
title: Zabezpečení skóre v Azure Security Center | Dokumentace Microsoftu
description: " Prioritizujte vaše doporučení zabezpečení pomocí zabezpečení skóre ve službě Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: v-mohabe
ms.openlocfilehash: c23c9a2b9af1947c397b96431ae3c3bcd0e30aaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968302"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Zlepšení hodnotíte zabezpečení ve službě Azure Security Center


S tolika služby nabízí výhody zabezpečení je často obtížné vědět, jaké kroky pro nejprve k zabezpečení a posílení vašich úloh. Azure zabezpečené skóre zkontroluje doporučení týkající se zabezpečení a upřednostňuje za vás, abyste věděli, jaké doporučení, jak provést jako první. To vám pomůže najít nejvážnější ohrožení zabezpečení, můžete určit prioritu prozkoumání. Skóre zabezpečení je nástroj, který vám pomůže vyhodnotit stav zabezpečení vašich úloh.

## <a name="secure-score-calculation"></a>Zabezpečení výpočtu skóre

Security Center napodobuje pracovní bezpečnostní analytik, zkontrolujte doporučení týkající se zabezpečení a použití pokročilých algoritmů zjistit, jak důležitá je jednotlivá doporučení.
Azure Security center neustále kontroluje aktivní doporučení a vypočítá hodnotíte zabezpečené na jejich základě, skóre doporučení je odvozený od jeho závažnosti a osvědčené postupy zabezpečení, které bude mít vliv na zabezpečení vašich úloh na maximum.

Security Center také poskytuje **celkové zabezpečení skóre**. 

**Celkové skóre zabezpečení** je nahromadění skóre doporučení. Vaše celkové zabezpečení skóre můžete zobrazit přes vaše předplatná nebo skupiny pro správu, v závislosti na tom, co jste vybrali. Skóre se bude lišit v závislosti na vybrané předplatné a aktivní doporučení v těchto předplatných.

 
Pokud chcete zkontrolovat, které doporučení ovlivnit zabezpečení skóre nejvíce, můžete zobrazit doporučení pro jejich činnost nejvíc ohrožují první tři v řídicím panelu Security Center nebo můžete seřadit doporučení v okně seznamu doporučení pomocí **zabezpečené skóre dopad** sloupce.


Své skóre můžete zobrazit celkové zabezpečení:

1. Na řídicím panelu Azure, klikněte na tlačítko **Security Center** a potom klikněte na tlačítko **zabezpečené skóre**.
2. V horní části uvidíte Secure skóre stručný přehled:
   - **Celkové bezpečnostní skóre** představuje skóre pro zásady a pro zvolené předplatné.
   - **Skóre zabezpečení podle kategorie** se dozvíte, které prostředky vyžadují pozornost na maximum
   - **Nejčastějších doporučení zabezpečení podle skóre dopad** vám poskytne seznam doporučení, která vylepší vaše zabezpečené skóre na maximum, pokud jejich implementaci.
 
   ![skóre zabezpečení](./media/security-center-secure-score/secure-score-dashboard.png)

3. V následující tabulce vidíte každé z vašich předplatných a celkově zabezpečené skóre pro každý.

   > [!NOTE]
   > Součet skóre zabezpečení u jednotlivých předplatných se nerovná celkovému skóre zabezpečení. Skóre zabezpečení se počítá na základě poměru mezi prostředky, které jsou v pořádku, a vašimi celkovými prostředky v rámci doporučení, nikoli jako součet skóre zabezpečení napříč předplatnými. 
   >
4. Klikněte na tlačítko **zobrazit doporučení** zobrazíte doporučení pro dané předplatné, které může napravit pro zlepšení zabezpečení skóre.
4. V seznamu doporučení uvidíte, že pro každé doporučení je sloupec, který představuje **zabezpečení skóre dopad**. Toto číslo představuje kolik hodnotíte celkové zabezpečení zlepší, pokud budete postupovat podle doporučení. Například na obrazovce níže, pokud jste **náprava ohrožení zabezpečení v kontejneru konfigurace zabezpečení**, zabezpečené skóre zvýší o 35 body.

   ![skóre zabezpečení](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Jednotlivé zabezpečené skóre

Kromě toho chcete-li zobrazit jednotlivé zabezpečené skóre, můžete najít v rámci okna individuální doporučení.  

**Doporučené skóre zabezpečení** se počítá na základě poměru mezi prostředky, které jsou v pořádku, a vašimi celkovými prostředky. Pokud se počet prostředků, které jsou v pořádku, rovná celkovému počtu prostředků, bude mít maximální skóre zabezpečení u doporučení hodnotu 50. Pokuste se dosáhnout skóre zabezpečení, které bude blíže maximální hodnotě tak, že opravíte prostředky, které nejsou v pořádku, podle následujících doporučení.

**Dopad doporučení** vám dává informace, o kolik se zlepší skóre zabezpečení, pokud použijete doporučený postup. Pokud je například vaše skóre zabezpečení 42 a **Dopad doporučení** je +3, zvýšíte provedením kroků v doporučení skóre na hodnotu 45.

Doporučení ukazuje hrozby, který je přístupný vašich úloh, pokud nejsou přijata nápravných kroků.

![skóre zabezpečení jednotlivých doporučení](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Další postup
Tento článek vám ukázali, jak zlepšit stav vašeho zabezpečení pomocí **zabezpečené skóre** ve službě Azure Security Center. Další informace o službě Security Center najdete v tématu:

* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.


