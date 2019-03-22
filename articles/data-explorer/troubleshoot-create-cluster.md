---
title: Chyba při vytváření clusteru v Průzkumníku dat Azure
description: Tento článek popisuje postup pro vytvoření clusteru v Průzkumníku dat Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9b8bfe2a4b9b7a8432f14fb53b3e7a4cae49a3b4
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189967"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Řešení potíží: Chyba při vytváření clusteru v Průzkumníku dat Azure

V nepravděpodobném případě, že vytvoření clusteru se nepovedlo v Průzkumníku dat Azure postupujte podle těchto kroků.

1. Ujistěte se, že máte dostatečná oprávnění. K vytvoření clusteru, musíte být členem skupiny *Přispěvatel* nebo *vlastníka* role pro předplatné Azure. Pokud nezbytné, fungují s správce předplatného, můžete je přidat do odpovídající role.

1. Ujistěte se, že neexistují žádné chyby ověření clusteru s tímto názvem souvisejí jste zadali v rámci **vytvořit cluster** na webu Azure Portal.

1. Zkontrolujte, [řídicí panel stavu služeb Azure](https://azure.microsoft.com/status/). Najděte stav Průzkumník dat Azure v oblasti, ve kterém chcete vytvořit cluster.

    Pokud není stav **dobré** (zelená značka zaškrtnutí), zkuste po zlepšuje stav vytváření clusteru.

1. Pokud stále potřebujete pomoc při řešení tohoto problému, otevřete prosím v žádosti o podporu [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).