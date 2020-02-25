---
title: Nepovedlo se vytvořit nebo odstranit databázi nebo tabulku v Azure Průzkumník dat
description: Tento článek popisuje postup řešení potíží při vytváření a odstraňování databází a tabulek v Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 67e4c33498b05884fe667a7977ddb40e647ab4c8
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562390"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Řešení potíží: Chyba při vytváření nebo odstraňování databáze nebo tabulky v Azure Průzkumník dat

V Azure Průzkumník dat pravidelně pracujete s databázemi a tabulkami. Tento článek popisuje kroky pro řešení problémů, které se můžou nacházet.

## <a name="creating-a-database"></a>Vytvoření databáze

1. Ujistěte se, že máte odpovídající oprávnění. Pokud chcete vytvořit databázi, musíte být členem role *Přispěvatel* nebo *vlastník* pro předplatné Azure. V případě potřeby Spolupracujte se správcem předplatného, aby vás mohli přidat k příslušné roli.

1. Ujistěte se, že pro název databáze nejsou k dispozici žádné chyby ověření názvu. Název musí být alfanumerický a nesmí obsahovat maximální délku 260 znaků.

1. Zajistěte, aby hodnoty uchování a ukládání databáze byly v povolených oblastech. Doba uchování musí být mezi 1 a 36500 dny (100 let). Ukládání do mezipaměti musí být mezi 1 a maximální nastavenou hodnotou pro uchování.

## <a name="deleting-or-renaming-a-database"></a>Odstranění nebo přejmenování databáze

Ujistěte se, že máte odpovídající oprávnění. Pokud chcete databázi odstranit nebo přejmenovat, musíte být členem role *Přispěvatel* nebo *vlastník* pro předplatné Azure. V případě potřeby Spolupracujte se správcem předplatného, aby vás mohli přidat k příslušné roli.

## <a name="creating-a-table"></a>Vytvoření tabulky

1. Ujistěte se, že máte odpovídající oprávnění. Chcete-li vytvořit tabulku, musíte být členem role *správce databáze* nebo *databázového uživatele* v databázi nebo v roli *Přispěvatel* nebo *vlastník* předplatného Azure. V případě potřeby Spolupracujte se svým předplatným nebo správcem clusteru, abyste vás mohli přidat k příslušné roli.

    Další informace o oprávněních najdete v tématu [Správa databázových oprávnění](manage-database-permissions.md).

1. Zajistěte, aby tabulka se stejným názvem ještě neexistovala. Pokud existuje, můžete: vytvořit tabulku s jiným názvem. Přejmenování existující tabulky (vyžaduje roli *správce tabulky* ); nebo vyřaďte existující tabulku (vyžaduje roli *správce databáze* ). Použijte následující příkazy.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Odstranění nebo přejmenování tabulky

Ujistěte se, že máte odpovídající oprávnění. Chcete-li odstranit nebo přejmenovat tabulku, musíte být členem role *správce databáze* nebo *správce tabulky* v databázi. V případě potřeby Spolupracujte se svým předplatným nebo správcem clusteru, abyste vás mohli přidat k příslušné roli.

Další informace o oprávněních najdete v tématu [Správa databázových oprávnění](manage-database-permissions.md).

## <a name="general-guidance"></a>Obecné pokyny

1. Zkontrolujte [řídicí panel stavu služeb Azure](https://azure.microsoft.com/status/). Vyhledejte stav služby Azure Průzkumník dat v oblasti, ve které se snažíte pracovat s databází nebo tabulkou.

    Pokud stav není **dobrý** (zelená značka zaškrtnutí), zkuste to znovu, až se stav zlepší.

1. Pokud stále potřebujete pomoc s vyřešením problému, otevřete prosím žádost o podporu v [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Další kroky

[Zkontroluje stav clusteru.](check-cluster-health.md)