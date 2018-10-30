---
title: Nepodařilo se vytvořit nebo odstranit databázi nebo tabulku v Průzkumníku dat Azure
description: Tento článek popisuje postup pro vytvoření a odstranění databází a tabulek v Průzkumníku dat Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: ec66066fe51af97f6355b78dd7af3480a39a5a03
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215082"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Řešení potíží: Selhání vytvoření nebo odstranění databáze nebo tabulky v Průzkumníku dat Azure

V Průzkumníku dat Azure pravidelně pracujete s databází a tabulek. Tento článek obsahuje pokyny k odstraňování potíží pro problémům, ke kterým můžete.

## <a name="creating-a-database"></a>Vytvoření databáze

1. Ujistěte se, že máte dostatečná oprávnění. K vytvoření databáze, musíte být členem skupiny *Přispěvatel* nebo *vlastníka* role pro předplatné Azure. Pokud nezbytné, fungují s správce předplatného, můžete je přidat do odpovídající role.

1. Ujistěte se, že neexistují žádné chyby ověření název pro název databáze. Název musí být alfanumerický, s maximální délku 260 znaků.

1. Ujistěte se, že databáze uchovávání a ukládání do mezipaměti hodnoty jsou v rámci povolených rozsahů. Uchování musí být mezi 1 a 36500 dny (100 let). Ukládání do mezipaměti musí být mezi 1 a maximální hodnotu nastavenou pro uchovávání informací.

## <a name="deleting-or-renaming-a-database"></a>Odstranění nebo přejmenování databáze

Ujistěte se, že máte dostatečná oprávnění. Pokud chcete odstranit nebo přejmenovat databázi, musíte být členem skupiny *Přispěvatel* nebo *vlastníka* role pro předplatné Azure. Pokud nezbytné, fungují s správce předplatného, můžete je přidat do odpovídající role.

## <a name="creating-a-table"></a>Vytvoření tabulky

1. Ujistěte se, že máte dostatečná oprávnění. K vytvoření tabulky, musí být členem skupiny *správce databáze* nebo *uživatele databáze* role v databázi nebo *Přispěvatel* nebo *vlastníka* role pro předplatné Azure. V případě potřeby fungovat s vaším předplatným nebo Správce clusteru, můžete je přidat do odpovídající role.

    Další informace o oprávněních najdete v tématu [spravovat oprávnění k databázi](manage-database-permissions.md).

1. Ujistěte se, že tabulka se stejným názvem už neexistuje. Pokud existuje, pak můžete: vytvořte tabulku s jiným názvem; přejmenujte existující tabulce (vyžaduje *tabulky správce* role); nebo zrušit existující tabulku (vyžaduje *správce databáze* role). Pomocí následujících příkazů.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Odstranit nebo přejmenovat tabulku

Ujistěte se, že máte dostatečná oprávnění. Pokud chcete odstranit nebo přejmenovat tabulku, musíte být členem skupiny *správce databáze* nebo *tabulky správce* role v databázi. V případě potřeby fungovat s vaším předplatným nebo Správce clusteru, můžete je přidat do odpovídající role.

Další informace o oprávněních najdete v tématu [spravovat oprávnění k databázi](manage-database-permissions.md).

## <a name="general-guidance"></a>Obecné pokyny

1. Zkontrolujte, [řídicí panel stavu služeb Azure](https://azure.microsoft.com/status/>). Najděte stav Průzkumník dat Azure v oblasti, kde se pokoušíte pracovat databáze nebo tabulky.

    Pokud není stav **dobré** (zelená značka zaškrtnutí), zkuste to znovu za stav zlepší.

1. Pokud stále potřebujete pomoc při řešení tohoto problému, otevřete prosím v žádosti o podporu [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Další postup

[Kontrola stavu clusteru](check-cluster-health.md)