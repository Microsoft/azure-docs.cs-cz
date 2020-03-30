---
title: Poradce při potížích s chybami při vytváření clusteru Azure Data Explorer
description: Tento článek popisuje kroky řešení potíží při vytváření clusteru v Průzkumníku dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562407"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Poradce při potížích: Neúspěšné vytvoření Průzkumníka dat Azure v clusteru

V nepravděpodobném případě, že se v Průzkumníku dat Azure nezdaří vytvoření clusteru, postupujte takto.

1. Ujistěte se, že máte odpovídající oprávnění. Chcete-li vytvořit cluster, musíte být členem role *přispěvatele* nebo *vlastníka* pro předplatné Azure. V případě potřeby spolupracujte se správcem předplatného, aby vás mohl přidat do příslušné role.

1. Ujistěte se, že neexistují žádné chyby ověření související s názvem clusteru, který jste zadali v části **Vytvořit cluster** na webu Azure Portal.

1. Zkontrolujte [řídicí panel stavu služeb Azure](https://azure.microsoft.com/status/). Vyhledejte stav Průzkumníka dat Azure v oblasti, kde se pokoušíte vytvořit cluster.

    Pokud stav není **dobrý** (zelená značka zaškrtnutí), zkuste vytvořit cluster po zlepšení stavu.

1. Pokud stále potřebujete pomoc při řešení problému, otevřete žádost o podporu na [webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
