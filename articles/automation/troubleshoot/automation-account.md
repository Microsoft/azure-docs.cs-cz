---
title: Řešení potíží s Azure Automation účtu
description: Tento článek popisuje, jak řešit potíže s účtem Azure a řešit problémy s nimi.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: bebc89514da7f28015da5cb145ae50bf3fccdb6a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83680076"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Řešení potíží s Azure Automation účtu

Tento článek popisuje řešení problémů, se kterými se můžete setkat při použití účtu Azure Automation. Obecné informace o účtech Automation najdete v tématu [Přehled ověřování účtů Azure Automation](../automation-security-overview.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scénář: nejde zaregistrovat poskytovatele prostředků služby Automation pro předplatná.

### <a name="issue"></a>Problém

Když pracujete s funkcemi správy, například Update Management ve vašem účtu Automation, narazíte na následující chybu:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Příčina

Zprostředkovatel prostředků Automation není v předplatném zaregistrován.

### <a name="resolution"></a>Řešení

Chcete-li zaregistrovat poskytovatele prostředků služby Automation, postupujte podle následujících kroků v Azure Portal:

1. V prohlížeči přejdete na [Azure Portal](https://portal.azure.com).

2. Přejít na **předplatná**a vybrat své předplatné.   

3. V části **Nastavení**vyberte **poskytovatelé prostředků**.

4. V seznamu poskytovatelů prostředků ověřte, že je zaregistrován poskytovatel prostředků **Microsoft. Automation** .

5. Pokud poskytovatel není uveden, zaregistrujte ho tak, jak je popsáno v tématu [řešení chyb pro registraci poskytovatele prostředků](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Další kroky

Pokud tento článek problém nevyřeší, zkuste další podporu vyzkoušet v jednom z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) . Jedná se o oficiální Microsoft Azure účet pro připojení komunity Azure ke správným zdrojům: odpovědi, podpora a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**.