---
title: Výtah pro doporučení pro Azure Advisor
description: Získat pravidelné Shrnutí aktivních doporučení
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502461"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Konfigurace pravidelného Shrnutí pro doporučení

**Výtahy doporučení** služby Advisor poskytují snadný a proaktivní způsob, jak zůstat na aktivních doporučeních v různých kategoriích. Tato funkce nabízí možnost konfigurace pravidelných oznámení pro shrnutí všech aktivních doporučení v různých kategoriích. Můžete zvolit požadovaný kanál pro oznámení, jako je e-mail, SMS nebo jiné, pomocí skupin akcí. V tomto článku se dozvíte, jak nastavit **výtahy doporučení** pro doporučení poradce.


## <a name="setting-up-your-recommendation-digest"></a>Nastavení hodnoty Digest pro doporučení 

Průvodce vytvořením hodnoty **Digest pro doporučení** vám pomůže nakonfigurovat souhrn. Pro konfigurace můžete vybrat níže uvedené parametry:
1. Kategorie: máme Doporučené kategorie, jako jsou náklady, vysoká dostupnost, výkon a provozní kvalita. Tato funkce není zatím dostupná pro doporučení zabezpečení.
2. Frekvence Digest: frekvence souhrnných oznámení může být týdně, týdně a měsíčně.
3. Skupina akcí: můžete vybrat buď existující skupinu akcí, nebo vytvořit novou skupinu akcí. Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).
4. Jazyk pro algoritmus Digest
5. Název výtahu doporučení: k lepšímu sledování a monitorování těchto výtahů můžete použít uživatelsky přívětivý řetězec.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Postup vytvoření vydigest doporučení v Azure Portal

Tady je postup pro vytvoření **vydigest doporučení:**
* **Krok 1:** V Azure Portal klikněte na **Poradce** a v části **monitorování** vyberte **vydigest doporučení** . 

   ![Vstupní bod Digest pro doporučení](./media/digest-0.png)

* **Krok 2:** Z horního panelu vyberte **nový výtah s doporučeními** , jak je uvedeno níže:

   ![Vytvořit vydigest doporučení](./media/digest-5.png)

* **Krok 3:** V části **obor** vyberte **předplatné** pro svůj výtah.

   ![Poskytněte vstupy hodnot Digest pro doporučení](./media/digest-1.png)

* **Krok 4:** V části **Podmínka** vyberte konfigurace, jako je **kategorie**, **frekvence** a **jazyk** .

   ![Zadat vstupní podmínky Digest pro doporučení](./media/digest-2.png)

* **Krok 5:** V části **Skupina akcí** vyberte **skupinu akcí** pro daný výtah. Další informace najdete tady – [Vytvoření a Správa skupin akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) .

   ![Zadat vstupní skupinu akcí v případě vydigest doporučení](./media/digest-3.png)

* **Krok 6:** V této poslední části pro **Podrobnosti o Digest**můžete přiřadit název a stav ke svému Digest s doporučeními. Dokončete nastavení stisknutím tlačítka **Create a Digest pro doporučení** .
   ![Dokončit vytvoření hodnoty Digest doporučení](./media/digest-4.png)

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních služby Advisor najdete v těchto tématech:
* [Úvod do Azure Advisor](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Doporučení pro náklady na poradce](advisor-cost-recommendations.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
* [REST API Advisoru](https://docs.microsoft.com/rest/api/advisor/)
