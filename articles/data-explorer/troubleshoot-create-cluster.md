---
title: Řešení potíží s selhání vytvoření clusteru Průzkumník dat Azure
description: Tento článek popisuje postup pro vytvoření clusteru v Průzkumníku dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9e6b3f53f07ac86d6b648a8562be4ef45879c37e
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044105"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Řešení potíží: Vytvoření clusteru se nezdařilo Průzkumníka dat služby Azure

V nepravděpodobném případě, že vytvoření clusteru se nepovedlo v Průzkumníku dat Azure postupujte podle těchto kroků.

1. Ujistěte se, že máte dostatečná oprávnění. K vytvoření clusteru, musíte být členem skupiny *Přispěvatel* nebo *vlastníka* role pro předplatné Azure. Pokud nezbytné, fungují s správce předplatného, můžete je přidat do odpovídající role.

1. Ujistěte se, že neexistují žádné chyby ověření clusteru s tímto názvem souvisejí jste zadali v rámci **vytvořit cluster** na webu Azure Portal.

1. Zkontrolujte, [řídicí panel stavu služeb Azure](https://azure.microsoft.com/status/). Najděte stav Průzkumník dat Azure v oblasti, ve kterém chcete vytvořit cluster.

    Pokud není stav **dobré** (zelená značka zaškrtnutí), zkuste po zlepšuje stav vytváření clusteru.

1. Pokud stále potřebujete pomoc při řešení tohoto problému, otevřete prosím v žádosti o podporu [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
