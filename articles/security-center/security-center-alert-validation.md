---
title: Ověřování výstrah v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže s ověřováním výstrah zabezpečení ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: yurid
ms.openlocfilehash: 1eb1bc78f233cbe37e8fdb1b05ac3cb90e8574da
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="alerts-validation-in-azure-security-center"></a>Ověřování výstrah ve službě Azure Security Center
Pomocí tohoto dokumentu se naučíte ověřovat, jestli je váš systém správně nakonfigurovaný pro výstrahy služby Azure Security Center.

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?
Služba Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a řešení ochrany koncových bodů, aby zjistila hrozby a mohla vás na ně upozornit. Přečtěte si téma [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts), kde najdete další informace o výstrahách zabezpečení, a téma [Principy výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), kde najdete další informace o různých typech výstrah.

## <a name="alert-validation"></a>Ověřování výstrah
Po nainstalování agenta Security Center do vašeho počítače postupujte na počítači, na kterém chcete mít napadený prostředek výstrahy, podle následujících kroků:

1. Zkopírujte spustitelný soubor (například calc.exe) na plochu počítače nebo do jiného adresáře, který vám vyhovuje.
2. Přejmenujte tento soubor na **ASC_AlertTest_662jfi039N.exe**.
3. Otevřete příkazový řádek a spusťte tento soubor s argumentem (pouze vymyšlený název argumentu), například: *ASC_AlertTest_662jfi039N.exe -foo*
4. Počkejte 5 až 10 minut a otevřete výstrahy služby Security Center. Měla by se tam zobrazit výstraha podobá této:

    ![Ověřování výstrah](./media/security-center-alert-validation/security-center-alert-validation-fig2.png)

Při kontrole této výstrahy ověřte, že se v poli Arguments Auditing Enabled (Auditování argumentů povoleno) zobrazuje hodnota true. Pokud se zobrazuje hodnota false, je potřeba povolit auditování argumentů příkazového řádku. Tuto možnost můžete povolit pomocí následujícího příkazového řádku:

*reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"*


> [!NOTE]
> Pokud chcete vidět ukázku této funkce, podívejte se na video [Ověřování výstrah v Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Alert-Validation-in-Azure-Security-Center). 

## <a name="see-also"></a>Viz také
V tomto článku jste se seznámili s procesem ověřování výstrah. Teď, když už jste obeznámeni s tímto ověřováním, zkuste následující články:

* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Monitorování stavu zabezpečení ve službě Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Principy výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Průvodce odstraňováním potíží pro službu Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Zjistěte, jak řešit běžné problémy ve službě Security Center. 
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

