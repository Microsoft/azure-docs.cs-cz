---
title: Doporučení digest pro Azure Advisor
description: Získejte pravidelný přehled aktivních doporučení
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502461"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Konfigurace pravidelného souhrnu doporučení

**Advisor recommendation digests** poskytují snadný a proaktivní způsob, jak zůstat na vrcholu aktivních doporučení v různých kategoriích. Tato funkce umožňuje konfigurovat pravidelná oznámení pro souhrn všech aktivních doporučení v různých kategoriích. Můžete si vybrat požadovaný kanál pro oznámení, jako je e-mail, SMS nebo jiné, pomocí akčních skupin. V tomto článku se zobrazí, jak nastavit **digesty doporučení** pro doporučení poradce.


## <a name="setting-up-your-recommendation-digest"></a>Nastavení vašeho doporučení digest 

Prostředí pro vytváření **algoritmů pro vytváření výtahů doporučení** vám pomůže nakonfigurovat souhrn. Můžete vybrat níže parametry pro konfigurace:
1. Kategorie: Máme kategorie doporučení, jako jsou náklady, vysoká dostupnost, výkon a provozní dokonalost. Tato funkce zatím není k dispozici pro doporučení zabezpečení.
2. Frekvence digest: Frekvence pro souhrnná oznámení může být týdenní, dvakrát týdně a měsíčně.
3. Skupina akcí: Můžete buď vybrat existující skupinu akcí, nebo vytvořit novou skupinu akcí. Další informace o skupinách akcí najdete v [tématu Vytváření a správa skupin akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).
4. Jazyk pro digest
5. Název digest doporučení: Pomocí uživatelsky přívětivého řetězce můžete lépe sledovat a sledovat digests.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Kroky k vytvoření výtahu doporučení na webu Azure Portal

Zde jsou kroky k vytvoření **doporučení digest:**
* **Krok 1:** Na webu Azure Portal přejděte na **Advisor** a v části **Monitorování** vyberte **Doporučení digest.** 

   ![Doporučení digest vstupní bod](./media/digest-0.png)

* **Krok 2:** Vyberte **nové doporučení digest** z horní lišty, jak je uvedeno níže:

   ![Vytvořit doporučení digest](./media/digest-5.png)

* **Krok 3:** V části **oboru** vyberte **předplatné** pro vaše digest

   ![Poskytnout vstupy výtahu doporučení](./media/digest-1.png)

* **Krok 4:** V části **podmínky** vyberte konfigurace, jako **je kategorie**, **frekvence** a **jazyk**

   ![Poskytnout podmínky pro zadání výtahu](./media/digest-2.png)

* **Krok 5:** V části **skupiny akcí** vyberte **skupinu akcí** pro digest. Další informace naleznete zde – [Vytváření a správa skupin akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

   ![Poskytnout skupinu akcí vstupního výtahu doporučení](./media/digest-3.png)

* **Krok 6:** V této poslední části pro **podrobnosti digest**, můžete přiřadit název a stav na vaše doporučení digest. Stisknutím **klávesy vytvořit doporučení digest** dokončit nastavení.
   ![Kompletní doporučení digest vytvoření](./media/digest-4.png)

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních poradců naleznete v tématu:
* [Úvod do Azure Advisoru](advisor-overview.md)
* [Začínáme s poradcem](advisor-get-started.md)
* [Doporučení nákladů poradce](advisor-cost-recommendations.md)
* [Doporučení pro výkon poradce](advisor-performance-recommendations.md)
* [Doporučení pro zabezpečení poradce](advisor-security-recommendations.md)
* [Doporučení poradce pro provozní excelenci](advisor-operational-excellence-recommendations.md)
* [Rozhraní REST PORADCE](https://docs.microsoft.com/rest/api/advisor/)
