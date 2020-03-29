---
title: Poradce při potížích s chybami připojení clusteru Průzkumníka dat Azure
description: Tento článek popisuje kroky řešení potíží pro připojení ke clusteru v Průzkumníku dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60827032"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Poradce při potížích: Nepodařilo se připojit ke clusteru v Průzkumníku dat Azure

Pokud se v Azure Data Exploreru nemůžete připojit ke clusteru, postupujte následovně.

1. Ujistěte se, že je správný připojovací řetězec. Měl by být ve `https://<ClusterName>.<Region>.kusto.windows.net`formě: , například v následujícím příkladu: `https://docscluster.westus.kusto.windows.net`.

1. Ujistěte se, že máte odpovídající oprávnění. Pokud nemáte, vrátí se odpověď typu *Neautorizováno*.

    Další informace o oprávněních najdete v tématu [Správa databázových oprávnění](manage-database-permissions.md). V případě potřeby se spojte se správcem vašeho clusteru a požádejte ho, aby vás přidal do odpovídající role.

1. Projděte si protokol aktivit ve vašem předplatném a ověřte, že nedošlo k odstranění clusteru.

1. Zkontrolujte [řídicí panel stavu služeb Azure](https://azure.microsoft.com/status/). Vyhledejte stav Azure Data Exploreru v oblasti, ve které se pokoušíte připojit ke clusteru.

    Pokud stav není **dobrý** (zelená značka zaškrtnutí), zkuste se připojit ke clusteru po zlepšení stavu.

1. Pokud stále potřebujete pomoc při řešení problému, otevřete žádost o podporu na [webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).