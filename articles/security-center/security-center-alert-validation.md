---
title: Ověřování výstrah v Azure Security Center | Microsoft Docs
description: Naučte se, jak ověřit, jestli jsou výstrahy zabezpečení správně nakonfigurované v Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 74323c63ab8985dee2391a546d82258dcb8d0114
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099518"
---
# <a name="alert-validation-in-azure-security-center"></a>Ověřování výstrah v Azure Security Center
Pomocí tohoto dokumentu se naučíte ověřovat, jestli je váš systém správně nakonfigurovaný pro výstrahy služby Azure Security Center.

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?
Výstrahy jsou oznámení, která Security Center generuje, když zjistí ohrožení vašich prostředků. Tato nastavení upřednostní a vypíše výstrahy spolu s informacemi potřebnými k rychlému prozkoumání problému. Security Center také poskytuje doporučení pro nápravu útoku.
Další informace najdete v tématech [výstrahy zabezpečení v Security Center](security-center-alerts-overview.md) a [Správa a reakce na výstrahy zabezpečení](security-center-managing-and-responding-alerts.md) .


## <a name="generate-sample-azure-defender-alerts"></a>Generování ukázkových upozornění Azure Defenderu

Pokud používáte nové prostředí výstrah ve verzi Preview, jak je popsáno v tématu [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md), můžete pomocí několika kliknutí na stránce výstrahy zabezpečení v Azure Portal vytvořit ukázkové výstrahy.

Použijte ukázkové výstrahy pro:

- vyhodnocení hodnoty a možností v Azure Defenderu
- Ověřte všechny konfigurace, které jste udělali pro výstrahy zabezpečení (například integrace SIEM, automatizace pracovních postupů a e-mailová oznámení).

Postup vytvoření ukázkových výstrah:

1. Na panelu nástrojů na stránce Výstrahy vyberte **Vytvořit Ukázkové výstrahy**. 
1. Vyberte předplatné.
1. Vyberte příslušné plány Azure Defenderu, pro které chcete zobrazit výstrahy. 
1. Vyberte **Vytvořit Ukázkové výstrahy**.

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="Postup vytvoření ukázkových výstrah v Azure Security Center":::
    
    Zobrazí se oznámení s informací, že se vytváří ukázková upozornění:

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="Oznámení, že se generují ukázková upozornění.":::

    Po několika minutách se výstrahy zobrazí na stránce výstrahy zabezpečení. Zobrazí se také kdekoli jinde, které jste nakonfigurovali pro příjem výstrah zabezpečení Azure Security Center (připojená systémů Siem, e-mailová oznámení atd.).

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="Ukázkové výstrahy v seznamu výstrah zabezpečení":::

    > [!TIP]
    > Výstrahy jsou pro simulované prostředky.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Simulace výstrah na virtuálních počítačích Azure (Windows) <a name="validate-windows"></a>

Po instalaci agenta Security Center do počítače postupujte podle těchto kroků z počítače, ve kterém chcete být napadeným prostředkem výstrahy:

1. Zkopírujte spustitelný soubor (například **calc.exe**) do desktopu počítače nebo do jiného adresáře, který chcete snadno přejmenovat, a přejmenujte ho jako **ASC_AlertTest_662jfi039N.exe**.
1. Otevřete příkazový řádek a spusťte tento soubor s argumentem (pouze falešný název argumentu), například: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Počkejte 5 až 10 minut a otevřete výstrahy služby Security Center. Měla by se zobrazit výstraha.

> [!NOTE]
> Při kontrole tohoto testovacího upozornění pro systém Windows se ujistěte, že je **povoleno auditování argumentů** pole je **pravdivé**. Pokud je **hodnota false**, je nutné povolit auditování argumentů příkazového řádku. K povolení použijte následující příkaz: .
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Simulace výstrah na virtuálních počítačích Azure (Linux) <a name="validate-linux"></a>

Po instalaci agenta Security Center do počítače postupujte podle těchto kroků z počítače, ve kterém chcete být napadeným prostředkem výstrahy:
1. Zkopírujte spustitelný soubor do vhodného umístění a přejmenujte ho na **./asc_alerttest_662jfi039n**, například:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Otevřete příkazový řádek a spusťte tento soubor:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Počkejte 5 až 10 minut a otevřete výstrahy služby Security Center. Měla by se zobrazit výstraha.


## <a name="simulate-alerts-on-kubernetes"></a>Simulace výstrah na Kubernetes <a name="validate-kubernetes"></a>

Pokud jste Kubernetes službu Azure s Security Center, můžete otestovat, zda vaše výstrahy pracují s následujícím příkazem kubectl:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Další informace o tom, jak chránit Kubernetes uzly a clustery, najdete v tématu [Úvod do Azure Defenderu pro Kubernetes](defender-for-kubernetes-introduction.md) .

## <a name="next-steps"></a>Další kroky
V tomto článku jste se seznámili s procesem ověřování výstrah. Teď, když už jste obeznámeni s tímto ověřováním, zkuste následující články:

* [Ověřování detekce hrozeb pro službu Azure Key Vault ve službě Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení v Security Center.
* [Principy výstrah zabezpečení v Azure Security Center](./security-center-alerts-overview.md) – Přečtěte si o různých typech výstrah zabezpečení.