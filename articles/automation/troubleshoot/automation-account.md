---
title: Řešení potíží s Azure Automationm účtem
description: Naučte se řešit problémy a řešit potíže s účtem Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 6a7defdaa9b4b0f0b3580a3ac6b1a0487434a97c
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864126"
---
# <a name="troubleshoot-an-azure-automation-account"></a>Řešení potíží s účtem Azure Automation

Tento článek popisuje řešení problémů, se kterými se můžete setkat při použití účtu Azure Automation. Obecné informace o účtech Automation najdete v tématu [Vytvoření účtu Azure](../automation-quickstart-create-account.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scénář: nejde zaregistrovat poskytovatele prostředků služby Automation pro předplatná.

### <a name="issue"></a>Problém

Při práci s řešeními pro správu v účtu Automation dojde k následující chybě:

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
* Připojte se [@AzureSupport](https://twitter.com/azuresupport)pomocí. Jedná se o oficiální Microsoft Azure účet pro připojení komunity Azure ke správným zdrojům: odpovědi, podpora a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**.