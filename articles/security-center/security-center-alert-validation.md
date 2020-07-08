---
title: Ověření výstrahy (soubor testu EICAR) v Azure Security Center | Microsoft Docs
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
ms.openlocfilehash: cf732b92c1a208dd4c312ae442969ef958a021b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791176"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Ověřování upozornění (testovací soubor EICAR) ve službě Azure Security Center
Pomocí tohoto dokumentu se naučíte ověřovat, jestli je váš systém správně nakonfigurovaný pro výstrahy služby Azure Security Center.

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?
Výstrahy jsou oznámení, která Security Center generuje, když zjistí ohrožení vašich prostředků. Tato nastavení upřednostní a vypíše výstrahy spolu s informacemi potřebnými k rychlému prozkoumání problému. Security Center také poskytuje doporučení pro nápravu útoku.
Další informace najdete v tématech [výstrahy zabezpečení v Security Center](security-center-alerts-overview.md) a [Správa a reakce na výstrahy zabezpečení](security-center-managing-and-responding-alerts.md) .

## <a name="alert-validation"></a>Ověřování výstrah

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Ověřit výstrahy na virtuálních počítačích s Windows<a name="validate-windows"></a>

Po instalaci agenta Security Center do počítače postupujte podle těchto kroků z počítače, ve kterém chcete být napadeným prostředkem výstrahy:

1. Zkopírujte spustitelný soubor (například **calc.exe**) do desktopu počítače nebo do jiného adresáře, který chcete snadno přejmenovat, a přejmenujte ho jako **ASC_AlertTest_662jfi039N.exe**.
1. Otevřete příkazový řádek a spusťte tento soubor s argumentem (pouze falešný název argumentu), například:```ASC_AlertTest_662jfi039N.exe -foo```
1. Počkejte 5 až 10 minut a otevřete výstrahy služby Security Center. Měla by se zobrazit výstraha.

> [!NOTE]
> Při kontrole tohoto testovacího upozornění pro systém Windows se ujistěte, že je **povoleno auditování argumentů** pole je **pravdivé**. Pokud je **hodnota false**, je nutné povolit auditování argumentů příkazového řádku. Pokud ho chcete povolit, použijte následující příkaz:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Ověřit výstrahy pro virtuální počítače se systémem Linux<a name="validate-linux"></a>

Po instalaci agenta Security Center do počítače postupujte podle těchto kroků z počítače, ve kterém chcete být napadeným prostředkem výstrahy:
1. Zkopírujte spustitelný soubor do vhodného umístění a přejmenujte ho na **./asc_alerttest_662jfi039n**, například:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Otevřete příkazový řádek a spusťte tento soubor:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Počkejte 5 až 10 minut a otevřete výstrahy služby Security Center. Měla by se zobrazit výstraha.


## <a name="validate-alerts-on-kubernetes"></a>Ověřit výstrahy na Kubernetes<a name="validate-kubernetes"></a>

Pokud používáte funkci Security Center Preview pro integraci služby Azure Kubernetes, spusťte následující příkaz kubectl, abyste otestovali, že vaše výstrahy fungují:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Další informace o integraci služby Azure Kubernetes a Azure Security Center najdete v [tomto článku](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>Další kroky
V tomto článku jste se seznámili s procesem ověřování výstrah. Teď, když už jste obeznámeni s tímto ověřováním, zkuste následující články:

* [Ověřování Azure Key Vault detekce hrozeb v Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) – Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení v Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Principy výstrah zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) – Přečtěte si o různých typech výstrah zabezpečení.
