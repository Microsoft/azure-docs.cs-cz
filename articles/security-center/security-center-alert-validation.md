---
title: Upozornění ověření (soubor testu EICAR) ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže s ověřováním výstrah zabezpečení ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: rkarlin
ms.openlocfilehash: f65b4b74a1a91fa081bd9c0d8146d055cebb0de6
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626303"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Ověřování výstrah (soubor testu EICAR) ve službě Azure Security Center
Pomocí tohoto dokumentu se naučíte ověřovat, jestli je váš systém správně nakonfigurovaný pro výstrahy služby Azure Security Center.

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?
Upozornění jsou typem oznámení, které Security Center se vygeneruje při zjištění hrozeb pro vaše prostředky. Upřednostňuje a obsahuje seznam upozornění společně s informacemi potřebnými pro vás k rychlému prozkoumání problému. Security Center také poskytuje doporučení, jak můžete nápravě útoku.
Další informace najdete v tématu [výstrah zabezpečení ve službě Azure Security Center](security-center-alerts-overview.md) a [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Ověřování výstrah

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## Ověření upozornění na virtuálním počítači Windows <a name="validate-windows"></a>

Po Security Center, které je agent nainstalovaný v počítači následujícím postupem z počítače, ve které chcete mít napadený prostředek výstrahy:

1. Zkopírujte spustitelný soubor (třeba **calc.exe**) na plochu počítače nebo do jiného adresáře usnadnění práce a přejmenujte jej jako **ASC_AlertTest_662jfi039N.exe**.
1. Otevřete příkazový řádek a spusťte tento soubor s argumentem (pouze vymyšlený název argumentu), jako například: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Počkejte 5 až 10 minut a otevřete výstrahy služby Security Center. Podobně jako upozornění [příklad](#alert-validate) níže má zobrazit:

> [!NOTE]
> Při kontrole této výstrahy testu pro Windows, ujistěte se, že pole **auditování argumentů povoleno** je **true**. Pokud je **false**, pak je nutné povolit auditování argumentů příkazového řádku. Ho Pokud chcete povolit, použijte následující příkazový řádek:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Ověření upozornění na virtuální počítač s Linuxem <a name="validate-linux"></a>

Po Security Center, které je agent nainstalovaný v počítači následujícím postupem z počítače, ve které chcete mít napadený prostředek výstrahy:
1. Zkopírujte spustitelný soubor do vhodného umístění a přejmenujte ho na **. / asc_alerttest_662jfi039n**, například:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Otevřete příkazový řádek a spusťte tento soubor:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Počkejte 5 až 10 minut a otevřete výstrahy služby Security Center. Podobně jako upozornění [příklad](#alert-validate) níže má zobrazit:

### Příklad výstrahy <a name="alert-validate"></a>

![Příklad upozornění ověření](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>Viz také:
V tomto článku jste se seznámili s procesem ověřování výstrah. Teď, když už jste obeznámeni s tímto ověřováním, zkuste následující články:

* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Monitorování stavu zabezpečení ve službě Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Principy výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Průvodce odstraňováním potíží pro službu Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Zjistěte, jak řešit běžné problémy ve službě Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
