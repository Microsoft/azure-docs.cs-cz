---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 6890c71ac7c265d46cc77751786fea4d0b228588
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175400"
---
### <a name="troubleshoot-azure-diagnostics"></a>Odstraňování potíží Diagnostiky Azure

Pokud se zobrazí následující chybová zpráva, poskytovatel prostředků Microsoft.insights není registrovaný:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Pokud chcete poskytovatele prostředků registrovat, proveďte na webu Azure Portal následující kroky:

1.  V navigačním podokně na levé straně klikněte na *Předplatná*.
2.  Vyberte předplatné identifikované v chybové zprávě.
3.  Klikněte na *Poskytovatelé prostředků*.
4.  Vyhledejte poskytovatele *Microsoft.insights*.
5.  Klikněte na odkaz *Zaregistrovat*.

![Zaregistrujte poskytovatele prostředků Microsoft.insights.](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Jakmile bude poskytovatel prostředků *Microsoft.insights* zaregistrovaný, zopakujte konfiguraci diagnostiky.


Pokud se v PowerShellu zobrazí následující chybová zpráva, musíte aktualizovat svou verzi PowerShellu:

`Set-AzDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Aktualizujte svou verzi Azure PowerShell postupujte podle pokynů v článku [instalace Azure PowerShell](/powershell/azure/install-az-ps) .
