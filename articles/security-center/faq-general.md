---
title: Nejčastější dotazy k Centru zabezpečení Azure – obecné otázky
description: Nejčastější obecné otázky týkající se Azure Security Center, produktu, který vám pomůže předcházet hrozbám, zjišťovat je a reagovat na ně.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: f9043ae1414b63f25583d52100774f3e87754a74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661835"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Nejčastější dotazy – obecné dotazy týkající se Azure Security Center

## <a name="what-is-azure-security-center"></a>Co je Azure Security Center?
Azure Security Center vám pomůže předcházet, zjišťovat a reagovat na hrozby se zvýšeným přehledem a kontrolou zabezpečení vašich prostředků. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Security Center používá Microsoft Monitoring Agent ke shromažďování a ukládání dat. Podrobné podrobnosti najdete v [tématu Shromažďování dat v Azure Security Center](security-center-enable-data-collection.md).


## <a name="how-do-i-get-azure-security-center"></a>Jak získám Azure Security Center?
Azure Security Center je povolené s předplatným Microsoft Azure a přístupné z [portálu Azure](https://azure.microsoft.com/features/azure-portal/). Chcete-li k němu získat přístup, [přihlaste se k portálu](https://portal.azure.com), vyberte **procházet**a přejděte na **Centrum zabezpečení**.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Které prostředky Azure monitoruje Azure Security Center?
Azure Security Center monitoruje následující prostředky Azure:

* Virtuální počítače (virtuální počítače) (včetně [cloudových služeb)](../cloud-services/cloud-services-choose-me.md)
* Škálovací sady virtuálních počítačů
* Virtuální sítě Azure
* Kontejnery
* Služba Azure SQL
* Účet služby Azure Storage
* Azure Web Apps (v [prostředí služby App Service)](../app-service/environment/intro.md)
* Partnerská řešení integrovaná s vaším předplatným Azure, jako je brána firewall webových aplikací na virtuálních počítačích a prostředí app service

Kromě toho můžete služby Azure Security Center monitorovat také pomocí Azure Security Center. Počítače [se systémem Windows](./quick-onboard-windows-computer.md) i [linuxové počítače](./quick-onboard-linux-computer.md) jsou podporovány.


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak můžu zobrazit aktuální stav zabezpečení mých prostředků Azure?
Stránka **Přehled Centra zabezpečení** zobrazuje celkový stav zabezpečení vašeho prostředí rozdělené podle výpočetních prostředků, sítí, dat úložiště & a aplikací. Každý typ prostředku má indikátor zobrazující identifikované chyby zabezpečení. Kliknutím na každou dlaždici se zobrazí seznam problémů se zabezpečením zjištěných centrem zabezpečení spolu s inventářem prostředků ve vašem předplatném.



## <a name="what-is-a-security-policy"></a>Co je zásada zabezpečení?
Zásady zabezpečení definuje sadu ovládacích prvků, které jsou doporučeny pro prostředky v rámci zadaného předplatného. V Azure Security Center definujete zásady pro vaše předplatná Azure podle požadavků na zabezpečení vaší společnosti a typu aplikací nebo citlivosti dat v každém předplatném.

Zásady zabezpečení povolené v Azure Security Center řídit doporučení zabezpečení a monitorování. Další informace o zásadách zabezpečení najdete [v tématu Monitorování stavu zabezpečení v Centru zabezpečení Azure](security-center-monitoring.md).


## <a name="who-can-modify-a-security-policy"></a>Kdo může změnit zásady zabezpečení?
Chcete-li změnit zásady zabezpečení, musíte být správcem zabezpečení nebo vlastníkem nebo přispěvatelem tohoto předplatného.

Informace o konfiguraci zásad zabezpečení najdete v tématu [Nastavení zásad zabezpečení v Centru zabezpečení Azure](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>Co je bezpečnostní doporučení?
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Pokud jsou identifikovány potenciální chyby zabezpečení, jsou vytvořena doporučení. Doporučení vás provedou procesem konfigurace potřebného ovládacího prvku. Můžete například:

* Zřizování antimalwaru za účelem identifikace a odstranění škodlivého softwaru
* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md) a pravidla pro řízení provozu na virtuálních počítačích
* Zřizování brány firewall webových aplikací, které pomáhá bránit se útokům zaměřeným na vaše webové aplikace
* Nasazení chybějících aktualizací systému
* Adresování konfigurací operačního systému, které neodpovídají doporučeným standardním hodnotám

Zobrazí se zde pouze doporučení, která jsou povolena v zásadách zabezpečení.



## <a name="what-triggers-a-security-alert"></a>Co spustí výstrahu zabezpečení?
Azure Security Center automaticky shromažďuje, analyzuje a pojipuje data protokolu z vašich prostředků Azure, sítě a partnerských řešení, jako je antimalwarový a bránfirewall. Při zjištění ohrožení zabezpečení se vytvoří výstraha zabezpečení. Příklady zahrnují zjišťování následujících situací:

* Ohrožené virtuální počítače, které komunikují se známými IP adresami se zlými úmysly
* Pokročilý malware zjištěný pomocí zasílání zpráv o chybách systému Windows
* Útoky hrubou silou na virtuální počítače
* Výstrahy zabezpečení z integrovaných partnerských řešení zabezpečení, jako je antimalwar nebo brány firewall webových aplikací


## <a name="why-did-secure-score-values-change"></a>Proč se hodnoty zabezpečeného skóre změnily? <a name="secure-score-faq"></a>
Od února 2019 security center upravilo skóre několika doporučení, aby lépe odpovídalo jejich závažnosti. V důsledku této úpravy může dojít ke změnám v celkových hodnotách skóre zabezpečení.  Další informace o zabezpečeném skóre naleznete v [tématu Výpočet zabezpečeného skóre](security-center-secure-score.md).


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaký je rozdíl mezi zjištěnými a upozornit na hrozby Microsoft Security Response Center versus Azure Security Center?
Centrum Microsoft Security Response Center (MSRC) provádí vybrané monitorování zabezpečení sítě a infrastruktury Azure a přijímá zprávy o hrozbách a stížnosti na zneužití od třetích stran. Když MSRC uvědomí, že k zákaznickým datům má přístup nezákonná nebo neautorizovaná strana nebo že používání Azure zákazníkem nedodržuje podmínky přijatelného použití, upozorní to správce bezpečnostních incidentů zákazníka. K oznámení obvykle dochází odesláním e-mailu bezpečnostním kontaktům určeným v Centru zabezpečení Azure nebo vlastníkovi předplatného Azure, pokud není zadán kontakt zabezpečení.

Security Center je služba Azure, která nepřetržitě monitoruje prostředí Azure zákazníka a používá analýzy k automatickému rozpoznání široké škály potenciálně škodlivých aktivit. Tato detekce jsou vykreslovány jako výstrahy zabezpečení na řídicím panelu Centra zabezpečení.