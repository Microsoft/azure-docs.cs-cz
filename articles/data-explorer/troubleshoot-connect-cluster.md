---
title: Nepodařilo se připojit ke clusteru v Průzkumníku dat Azure
description: Tento článek popisuje kroky pro připojení ke clusteru v Průzkumníku Azure.Data řešení potíží.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f66dcc55b01b407c59c65ea300757ab4ee1002ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965054"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Řešení potíží: Nepodařilo se připojit ke clusteru v Průzkumníku služby Azure Data

Pokud se nemůžete připojit ke clusteru v Průzkumníku dat Azure, postupujte podle těchto kroků.

1. Ujistěte se, že je správný připojovací řetězec. Měla by být ve tvaru: `https://<ClusterName>.<Region>.kusto.windows.net`, jako v následujícím příkladu: `https://docscluster.westus.kusto.windows.net`.

1. Ujistěte se, že máte dostatečná oprávnění. Pokud to neuděláte, dostanete odpověď *neoprávněné*.

    Další informace o oprávněních najdete v tématu [spravovat oprávnění k databázi](manage-database-permissions.md). Pokud nezbytné, fungují s Správce clusteru, můžete je přidat do odpovídající role.

1. Ověřte, že nebyla odstraněna clusteru: zkontrolovat protokol aktivit ve vašem předplatném.

1. Zkontrolujte, [řídicí panel stavu služeb Azure](https://azure.microsoft.com/status/>). Najděte stav Průzkumník dat Azure v oblasti, ve kterém se snažíte připojit ke clusteru.

    Pokud není stav **dobré** (zelená značka zaškrtnutí), zkuste se připojit ke clusteru po stav zlepší.

1. Pokud stále potřebujete pomoc při řešení tohoto problému, otevřete prosím v žádosti o podporu [webu Azure portal](https://portal.azure.com).