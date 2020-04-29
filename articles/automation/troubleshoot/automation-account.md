---
title: Řešení potíží s účtem služby Automation
description: Naučte se řešit problémy a řešit potíže s účtem Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679385"
---
# <a name="troubleshoot-the-automation-account"></a>Řešení potíží s účtem Automation

Tento článek popisuje řešení problémů, se kterými se můžete setkat při použití účtu Automation. V následujících částech jsou vysvětlené konkrétní chybové zprávy a možná řešení pro každé z nich. Obecné informace o účtech Automation najdete v tématu [Vytvoření účtu Azure](../automation-quickstart-create-account.md).

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

2. Přejděte na **odběry** a vyberte své předplatné ze stránky předplatná.   

3. V části **Nastavení**vyberte **poskytovatelé prostředků**.

4. V seznamu poskytovatelů prostředků ověřte, že je zaregistrován poskytovatel prostředků **Microsoft. Automation** .

5. Pokud poskytovatel není uveden, zaregistrujte ho tak, jak je popsáno v tématu [řešení chyb pro registraci poskytovatele prostředků](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Další kroky

Pokud nevidíte výše uvedený problém nebo nemůžete problém vyřešit, zkuste pro další podporu použít jeden z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se [@AzureSupport](https://twitter.com/azuresupport)k, oficiální Microsoft Azure účet pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.