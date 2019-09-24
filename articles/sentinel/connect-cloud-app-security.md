---
title: Připojit Cloud App Security dat ke službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak připojit Cloud App Security dat ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6e71028855344d083dedf3493682e1e27685de48
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240124"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Připojení dat z Microsoft Cloud App Security 



Pomocí jediného kliknutí můžete streamovat protokoly z [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) do Azure Sentinel. Toto připojení umožňuje streamovat výstrahy z Cloud App Security do Azure Sentinel. 

## <a name="prerequisites"></a>Požadavky

- Uživatel s oprávněními globálního správce nebo správce zabezpečení

## <a name="connect-to-cloud-app-security"></a>Připojení k Cloud App Security

Pokud už máte Cloud App Security, ujistěte se, že je [ve vaší síti povolená](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Pokud je Cloud App Security nasazená a ingestují vaše data, můžou se data výstrah do Azure Sentinel snadno streamovat.


1. V Azure Sentinel vyberte **datové konektory** a pak klikněte na dlaždici **Cloud App Security** .

1. Vyberte protokoly, které chcete streamovat do Azure Sentinel, a můžete zvolit **výstrahy**. 

1. Můžete vybrat, jestli chcete, aby výstrahy z Microsoft Cloud App Security automaticky generovaly incidenty v rámci služby Azure Sentinel automaticky. V části **vytvořit incidenty** vyberte **Povolit** , pokud chcete povolit výchozí analytické pravidlo, které automaticky vytvoří incidenty z výstrah vygenerovaných v připojené službě zabezpečení. Toto pravidlo pak můžete upravit v části **Analýza** a pak na **aktivní pravidla**.

1. Klikněte na **Připojit**.

1. Pokud chcete pro Cloud App Security výstrahy použít příslušné schéma v Log Analytics, vyhledejte **SecurityAlert**.




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Microsoft Cloud App Security ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
