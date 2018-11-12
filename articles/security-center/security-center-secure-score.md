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
ms.date: 11/5/2018
ms.author: rkarlin
ms.openlocfilehash: 3a377441758fcd7dd91deefb5cae91579e881498
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007043"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Zlepšení hodnotíte zabezpečení ve službě Azure Security Center


S tolika služby nabízí výhody zabezpečení je často obtížné vědět, jaké kroky pro nejprve k zabezpečení a posílení vašich úloh. Azure zabezpečené skóre zkontroluje doporučení týkající se zabezpečení a upřednostňuje za vás, abyste věděli, jaké doporučení, jak provést jako první. To vám pomůže najít nejvážnější ohrožení zabezpečení, můžete určit prioritu prozkoumání. Skóre zabezpečení je nástroj, který vám pomůže vyhodnotit stav zabezpečení vašich úloh.

![Řídicí panel skóre zabezpečení](./media/security-center-secure-score/secure-score-dashboard.png)

## <a name="secure-score-calculation"></a>Zabezpečení výpočtu skóre

Security Center napodobuje pracovní bezpečnostní analytik, zkontrolujte doporučení týkající se zabezpečení a použití pokročilých algoritmů zjistit, jak důležitá je jednotlivá doporučení.
Azure Security center neustále kontroluje aktivní doporučení a vypočítá hodnotíte zabezpečené na jejich základě, skóre doporučení je odvozený od jeho závažnosti a osvědčené postupy zabezpečení, které bude mít vliv na zabezpečení vašich úloh na maximum.

Security Center také poskytuje **celkové zabezpečení skóre**. 

**Celkové skóre zabezpečení** je nahromadění skóre doporučení. Vaše celkové zabezpečení skóre můžete zobrazit přes vaše předplatná nebo skupiny pro správu, v závislosti na tom, co jste vybrali. Skóre se bude lišit v závislosti na vybrané předplatné a aktivní doporučení v těchto předplatných.

 
Pokud chcete zkontrolovat, které doporučení ovlivnit zabezpečení skóre nejvíce, můžete zobrazit doporučení pro jejich činnost nejvíc ohrožují první tři v řídicím panelu Security Center nebo můžete seřadit doporučení v okně seznamu doporučení pomocí **zabezpečené skóre dopad** sloupce.


Své skóre můžete zobrazit celkové zabezpečení:

1. Na řídicím panelu Azure, klikněte na tlačítko **Security Center** a potom klikněte na tlačítko **doporučení**.
2. V horní části uvidíte zabezpečené skóre, která představuje skóre na zásady, na vybraném předplatném. 
2. V následující tabulce, která obsahuje seznam doporučení uvidíte, že pro každé doporučení je sloupec, který představuje **zabezpečení skóre dopad**. Toto číslo představuje kolik hodnotíte celkové zabezpečení zlepší, pokud budete postupovat podle doporučení. Například na obrazovce níže, pokud jste **náprava ohrožení zabezpečení v kontejneru konfigurace zabezpečení**, zabezpečené skóre zvýší o 35 body.

   ![skóre zabezpečení](./media/security-center-secure-score/security-center-secure-score1.png)

## <a name="individual-secure-score"></a>Jednotlivé zabezpečené skóre

Kromě toho chcete-li zobrazit jednotlivé zabezpečené skóre, můžete najít v rámci okna individuální doporučení.  

**Doporučení zabezpečení skóre** se počítají na základě poměru mezi prostředky v dobrém stavu a celkové množství prostředků. Pokud celkový počet prostředků rovná počtu prostředků v pořádku, můžete získat maximální zabezpečené skóre doporučení 50. A pokuste se získat zabezpečené skóre blíže na maximální skóre, opravte doporučeného prostředků není v pořádku.

**Dopad doporučení** vás informuje, kolik zlepšuje zabezpečení skóre Pokud použijete doporučené kroky. Například, pokud vaše zabezpečené skóre je 42 a **dopad doporučení** je + 3, provedením kroků v doporučení zlepšit vaše skóre se 45.

Doporučení ukazuje hrozby, který je přístupný vašich úloh, pokud nejsou přijata nápravných kroků.

![skóre zabezpečení jednotlivých doporučení](./media/security-center-secure-score/indiv-recommendation-secure-score.png)

## <a name="next-steps"></a>Další postup
Tento článek vám ukázali, jak zlepšit stav vašeho zabezpečení pomocí **zabezpečené skóre** ve službě Azure Security Center. Další informace o službě Security Center najdete v tématu:

* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.


