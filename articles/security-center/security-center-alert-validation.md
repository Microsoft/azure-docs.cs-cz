---
title: Ověření výstrahy (testovací soubor EICAR) v Centru zabezpečení Azure | Dokumenty společnosti Microsoft
description: Tento dokument vám pomůže s ověřováním výstrah zabezpečení ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5146878adf10e452f38fecb115ec40792ffa84f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139993"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Ověřování upozornění (testovací soubor EICAR) ve službě Azure Security Center
Pomocí tohoto dokumentu se naučíte ověřovat, jestli je váš systém správně nakonfigurovaný pro výstrahy služby Azure Security Center.

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?
Výstrahy jsou oznámení, která Security Center generuje, když zjistí ohrožení vašich prostředků. Upřednostňuje a uvádí výstrahy spolu s informacemi potřebnými k rychlému prošetření problému. Security Center také poskytuje doporučení pro nápravu útoku.
Další informace naleznete [v tématu Výstrahy zabezpečení v Centru zabezpečení](security-center-alerts-overview.md) a správa a reakce na [výstrahy zabezpečení](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Ověřování výstrah

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Ověření výstrah na virtuálních počítačích se systémem Windows<a name="validate-windows"></a>

Po instalaci agenta Centra zabezpečení v počítači postupujte takto z počítače, ve kterém chcete být napadeným zdrojem výstrahy:

1. Zkopírujte spustitelný soubor (například **calc.exe)** na plochu počítače nebo jiný vlastní adresář a přejmenujte jej na **ASC_AlertTest_662jfi039N.exe**.
1. Otevřete příkazový řádek a spusťte tento soubor s argumentem (pouze falešným názvem argumentu), například:```ASC_AlertTest_662jfi039N.exe -foo```
1. Počkejte 5 až 10 minut a otevřete výstrahy služby Security Center. Měla by se zobrazit výstraha podobná níže uvedenému [příkladu:](#alert-validate)

> [!NOTE]
> Při kontrole této testovací výstrahy pro systém Windows zkontrolujte, zda je **pole** **Auditování argumentů povoleno** . Pokud je **false**, pak je třeba povolit auditování argumentů příkazového řádku. Chcete-li jej povolit, použijte následující příkaz:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Ověření výstrah na virtuálních počítačích s Linuxem<a name="validate-linux"></a>

Po instalaci agenta Centra zabezpečení v počítači postupujte takto z počítače, ve kterém chcete být napadeným zdrojem výstrahy:
1. Zkopírujte spustitelný soubor do vhodného umístění a přejmenujte jej na **./asc_alerttest_662jfi039n**, například:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Otevřete příkazový řádek a spusťte tento soubor:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Počkejte 5 až 10 minut a otevřete výstrahy služby Security Center. Měla by se zobrazit výstraha podobná níže uvedenému [příkladu:](#alert-validate)

### <a name="alert-example"></a>Příklad výstrahy<a name="alert-validate"></a>

![Příklad ověření výstrahy](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## <a name="validate-alerts-on-kubernetes"></a>Ověření upozornění na Kubernetes<a name="validate-kubernetes"></a>

Pokud používáte funkci náhledu Centra zabezpečení integrace služby Azure Kubernetes Service, spusťte následující příkaz kubectl a otestujte, že vaše výstrahy fungují:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Další informace o integraci služby Azure Kubernetes Service a Azure Security Center najdete v [tomto článku](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>Další kroky
V tomto článku jste se seznámili s procesem ověřování výstrah. Teď, když už jste obeznámeni s tímto ověřováním, zkuste následující články:

* [Ověření azure key vault detekce hrozeb v Centru zabezpečení Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) – Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení v Centru zabezpečení.
* [Monitorování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak sledovat stav prostředků Azure.
* [Principy výstrah zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) – Přečtěte si o různých typech výstrah zabezpečení.
