---
title: Jak požádat o zvýšení kvóty pro Azure Cosmos DB prostředky
description: Přečtěte si, jak požádat o zvýšení kvóty pro Azure Cosmos DB prostředky. Naučíte se také, jak povolit předplatné pro přístup k oblasti.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: cd2bc3ec7e70049ee7e2c700731515a272e541dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096835"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>Jak požádat o zvýšení kvóty pro Azure Cosmos DB prostředky

Prostředky v Azure Cosmos DB mají [výchozí kvóty a omezení](concepts-limits.md). Může se ale jednat o případ, kdy vaše úlohy potřebují větší kvótu než výchozí hodnota. V takovém případě musíte kontaktovat tým Azure Cosmos DB a požádat o zvýšení kvóty. Tento článek vysvětluje, jak požádat o zvýšení kvóty pro Azure Cosmos DB prostředky. Naučíte se také, jak povolit předplatné pro přístup k oblasti.

## <a name="create-a-new-support-request"></a>Vytvořit novou žádost o podporu

Chcete-li požádat o zvýšení kvóty, je nutné vytvořit novou žádost o podporu s podrobnostmi o úlohách. Tým Azure Cosmos DB pak vyhodnotí vaši žádost a schválí ji. Pomocí následujícího postupu můžete vytvořit novou žádost o podporu z Azure Portal:

1. Přihlaste se k portálu Azure Portal.

1. V nabídce vlevo vyberte **help + podpora** a pak vyberte **Nová žádost o podporu**.

1. Na kartě **základy** zadejte následující podrobnosti:

   * Jako **Typ problému** vyberte **Limity služeb a předplatných (kvóty)** .
   * V poli **předplatné**vyberte předplatné, pro které chcete kvótu zvýšit.
   * Jako **typ kvóty**vyberte **Cosmos DB**

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="Vytvoření nové žádosti o podporu Cosmos DB pro zvýšení kvóty":::

1. Na kartě **Podrobnosti** zadejte podrobnosti odpovídající vaší žádosti o kvótu. Informace uvedené na této kartě se použijí k dalšímu vyhodnocení vašeho problému a k tomu, aby pomohl pracovníkovi podpory řešit potíže.

1. Do tohoto formuláře zadejte následující podrobnosti:

   * **Popis**: zadejte krátký popis vaší žádosti, jako je například vaše zatížení, proč výchozí hodnoty nejsou dostačující. V závislosti na typu prostředku, pro který chcete zvýšit kvótu, je pro zadání následujících údajů v rámci pole **Popis** nutné zadat následující údaje:

     **Žádosti o oblasti** Pokud vaše žádost odpovídá Přidání oblasti do seznamu povolených, nezapomeňte zadat následující hodnoty:

        * Název oblasti
        * ID předplatného

     **Žádosti o omezení propustnosti** Pokud vaše žádost odpovídá zvýšení kvóty pro propustnost, nezapomeňte zadat následující hodnoty:

        * Název databáze
        * ID předplatného
        * Nové omezení propustnosti

     **Požadavky na limit databázového účtu** Pokud vaše žádost odpovídá zvýšení kvóty pro počet databázových účtů v rámci předplatného, nezapomeňte zadat následující hodnoty:

       * ID předplatného
       * Nový limit databázového účtu

   * **Nahrání souboru**: nahrání diagnostických souborů nebo jakýchkoli dalších souborů, které považujete za relevantní pro žádost o podporu. Další informace o doprovodné příručce k nahrávání souborů najdete v článku věnovaném [podpoře Azure]( ../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files) .

   * **Závažnost**: vyberte jednu z dostupných úrovní závažnosti na základě dopadu na firmu.

   * **Upřednostňovaná metoda kontaktu**: můžete zvolit, že se má kontaktovat **e-mailem** nebo **telefonicky**.

1. Vyplňte zbývající údaje, jako je vaše dostupnost, jazyk podpory, kontaktní údaje, e-mail a telefonní číslo ve formuláři.

1. Vyberte **Další: zkontrolovat + vytvořit**. Ověřte poskytnuté informace a vyberte **vytvořit** a vytvořte žádost o podporu.

Během 24 hodin tým podpory Azure Cosmos DB vyhodnotí vaši žádost a vrátí se vám.

## <a name="next-steps"></a>Další kroky

* Zobrazit [výchozí kvóty služby Azure Cosmos DB](concepts-limits.md)
