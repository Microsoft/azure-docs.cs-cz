---
title: Nastavení připojení Azure synapse
description: Článek, který vás učí, abyste nakonfigurovali nastavení připojení v Azure synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 03/15/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ce1a4808833cbd897da17f9ad75af346538d23d1
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103473083"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Nastavení připojení Azure synapse Analytics

Tento článek vysvětluje nastavení připojení ve službě Azure synapse Analytics a jejich konfiguraci tam, kde je to možné.


## <a name="connection-policy"></a>Zásady připojení
Zásady připojení pro synapse SQL ve službě Azure synapse Analytics jsou nastavené na *výchozí*. Tuto změnu v Azure synapse Analytics nemůžete změnit. Další informace o tom, jak to ovlivní připojení k synapse SQL v Azure synapse Analytics, najdete [tady](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy). 

## <a name="minimal-tls-version"></a>Minimální verze protokolu TLS
Synapse SQL ve službě Azure synapse Analytics umožňuje připojení pomocí všech verzí TLS. V Azure synapse Analytics nemůžete nastavit minimální verzi TLS pro synapse SQL.

## <a name="next-steps"></a>Další kroky

Vytvoření [pracovního prostoru Azure synapse](./synapse-workspace-ip-firewall.md)
