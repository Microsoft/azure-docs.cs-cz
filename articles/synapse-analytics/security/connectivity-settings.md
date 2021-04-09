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
ms.openlocfilehash: e0d8a8e3320b49b6fbe3e8ab66c0b4569fac9afd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587929"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Nastavení připojení Azure synapse Analytics

Tento článek vysvětluje nastavení připojení ve službě Azure synapse Analytics a jejich konfiguraci tam, kde je to možné.


## <a name="connection-policy"></a>Zásady připojení
Zásady připojení pro synapse SQL ve službě Azure synapse Analytics jsou nastavené na *výchozí*. Tuto změnu v Azure synapse Analytics nemůžete změnit. Další informace o tom, jak to ovlivní připojení k synapse SQL v Azure synapse Analytics, najdete [tady](../../azure-sql/database/connectivity-architecture.md#connection-policy). 

## <a name="minimal-tls-version"></a>Minimální verze protokolu TLS
Synapse SQL ve službě Azure synapse Analytics umožňuje připojení pomocí všech verzí TLS. V Azure synapse Analytics nemůžete nastavit minimální verzi TLS pro synapse SQL.

## <a name="next-steps"></a>Další kroky

Vytvoření [pracovního prostoru Azure synapse](./synapse-workspace-ip-firewall.md)