---
title: Nepodařilo se vytvořit nebo odstranit DB nebo tabulku v Průzkumníku dat Azure
description: Tento článek popisuje kroky řešení potíží při vytváření a odstraňování databází a tabulek v Průzkumníku dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 67e4c33498b05884fe667a7977ddb40e647ab4c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562390"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Poradce při potížích: Nepodařilo se vytvořit nebo odstranit databázi nebo tabulku v Průzkumníku dat Azure

V Průzkumníku dat Azure pravidelně pracujete s databázemi a tabulkami. Tento článek obsahuje postupy řešení potíží, které se mohou stát.

## <a name="creating-a-database"></a>Vytvoření databáze

1. Ujistěte se, že máte odpovídající oprávnění. Chcete-li vytvořit databázi, musíte být členem role *přispěvatele* nebo *vlastníka* pro předplatné Azure. V případě potřeby spolupracujte se správcem předplatného, aby vás mohl přidat do příslušné role.

1. Ujistěte se, že neexistují žádné chyby ověření názvu pro název databáze. Název musí být alfanumerický, s maximální délkou 260 znaků.

1. Ujistěte se, že hodnoty uchovávání a ukládání do mezipaměti databáze jsou v povolených rozsahech. Uchovávání musí být mezi 1 a 36500 dny (100 let). Ukládání do mezipaměti musí být mezi 1 a maximální hodnotou nastavenou pro uchovávání.

## <a name="deleting-or-renaming-a-database"></a>Odstranění nebo přejmenování databáze

Ujistěte se, že máte odpovídající oprávnění. Chcete-li odstranit nebo přejmenovat databázi, musíte být členem role *přispěvatele* nebo *vlastníka* pro předplatné Azure. V případě potřeby spolupracujte se správcem předplatného, aby vás mohl přidat do příslušné role.

## <a name="creating-a-table"></a>Vytvoření tabulky

1. Ujistěte se, že máte odpovídající oprávnění. Chcete-li vytvořit tabulku, musíte být členem role *správce databáze* nebo *uživatele databáze* v databázi nebo role *přispěvatele* nebo *vlastníka* pro předplatné Azure. V případě potřeby spolupracujte se správcem předplatného nebo clusteru, aby vás mohl přidat do příslušné role.

    Další informace o oprávněních najdete v tématu [Správa databázových oprávnění](manage-database-permissions.md).

1. Ujistěte se, že tabulka se stejným názvem ještě neexistuje. Pokud existuje, můžete: Vytvořte tabulku s jiným názvem; přejmenovat existující tabulku (vyžaduje roli *správce tabulky);* nebo přetáhněte existující tabulku (vyžaduje roli *správce databáze).* Použijte následující příkazy.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Odstranění nebo přejmenování tabulky

Ujistěte se, že máte odpovídající oprávnění. Chcete-li odstranit nebo přejmenovat tabulku, musíte být členem role *správce databáze* nebo role *správce tabulky* v databázi. V případě potřeby spolupracujte se správcem předplatného nebo clusteru, aby vás mohl přidat do příslušné role.

Další informace o oprávněních najdete v tématu [Správa databázových oprávnění](manage-database-permissions.md).

## <a name="general-guidance"></a>Obecné pokyny

1. Zkontrolujte [řídicí panel stavu služeb Azure](https://azure.microsoft.com/status/). Vyhledejte stav Průzkumníka dat Azure v oblasti, kde se pokoušíte pracovat s databází nebo tabulkou.

    Pokud stav není **dobrý** (zelená značka zaškrtnutí), zkuste to znovu po zlepšení stavu.

1. Pokud stále potřebujete pomoc při řešení problému, otevřete žádost o podporu na [webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Další kroky

[Kontrola stavu clusteru](check-cluster-health.md)