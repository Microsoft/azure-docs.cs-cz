---
title: Řešení potíží s účtem automatizace
description: Přečtěte si, jak řešit a řešit problémy s účtem Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c66b1728144b8517f6ac444059b3a8def956c6e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301002"
---
# <a name="automation-account-troubleshooting"></a>Řešení potíží s účtem automatizace

Tento článek popisuje řešení problémů, se kterými se můžete setkat při použití účtu Automation. V následujících částech jsou zvýrazněny konkrétní chybové zprávy a možná řešení pro každou z nich. Obecné informace o účtech Automation najdete [v tématu Vytvoření účtu Azure](../automation-quickstart-create-account.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scénář: Nelze zaregistrovat zprostředkovatele prostředků automatizace pro předplatná

### <a name="issue"></a>Problém

Při práci s řešeními pro správu v účtu Automation narazíte na následující chybu:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Příčina

Zprostředkovatel prostředků automatizace není registrován v předplatném.

### <a name="resolution"></a>Řešení

Pokud chcete zaregistrovat zprostředkovatele prostředků automatizace, postupujte takto na webu Azure Portal:

1. V prohlížeči přejděte na [portál Azure](https://portal.azure.com).

2. Přejděte na **Předplatná** a vyberte předplatné na stránce Předplatná.   

3. V části **Nastavení**vyberte **zprostředkovatele prostředků**.

4. Ze seznamu poskytovatelů prostředků ověřte, zda je poskytovatel prostředků **Microsoft.Automation** registrován.

5. Pokud není uvedena, zaregistrujte poskytovatele **Microsoft.Automation** podle pokynů na [řešení chyb pro registraci poskytovatele prostředků](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Další kroky

Pokud jste problém neuviděli nebo se vám nepodaří problém vyřešit, vyzkoušejte další podporu jedním z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
* Spojte [@AzureSupport](https://twitter.com/azuresupport)se s oficiálním účtem Microsoft Azure a vylepšete tak zákaznickou zkušenost propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.