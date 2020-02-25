---
title: Řešení chyb při vytváření clusteru Azure Průzkumník dat
description: Tento článek popisuje postup řešení potíží při vytváření clusteru v Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562407"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Řešení potíží: Vytvoření clusteru Azure Průzkumník dat se nezdařilo

V nepravděpodobném případě, že se vytvoření clusteru v Azure Průzkumník dat nezdařilo, postupujte podle těchto kroků.

1. Ujistěte se, že máte odpovídající oprávnění. Pokud chcete vytvořit cluster, musíte být členem role *Přispěvatel* nebo *vlastník* pro předplatné Azure. V případě potřeby Spolupracujte se správcem předplatného, aby vás mohli přidat k příslušné roli.

1. Zajistěte, aby nedocházelo k chybám ověřování souvisejících s názvem clusteru, který jste zadali v části **vytvořit cluster** v Azure Portal.

1. Zkontrolujte [řídicí panel stavu služeb Azure](https://azure.microsoft.com/status/). Vyhledejte stav služby Azure Průzkumník dat v oblasti, ve které se pokoušíte vytvořit cluster.

    Pokud stav není **dobrý** (zelená značka zaškrtnutí), zkuste cluster vytvořit, až se stav zlepší.

1. Pokud stále potřebujete pomoc s vyřešením problému, otevřete prosím žádost o podporu v [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
