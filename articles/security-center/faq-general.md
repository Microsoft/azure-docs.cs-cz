---
title: Nejčastější dotazy týkající se Azure Security Center – obecné otázky
description: Nejčastější dotazy týkající se Azure Security Center, produktu, který pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně
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
ms.openlocfilehash: 3a8429d9dc6820b1f79c49d325872b61833f988d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095540"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Nejčastější dotazy – obecné otázky týkající se Azure Security Center

## <a name="what-is-azure-security-center"></a>Co je Azure Security Center?
Azure Security Center pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně tím, že zvyšuje přehled o zabezpečení vašich prostředků a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Security Center používá agenta Log Analytics ke shromažďování a ukládání dat. Podrobné informace najdete v tématu [shromažďování dat v Azure Security Center](security-center-enable-data-collection.md).


## <a name="how-do-i-get-azure-security-center"></a>Návody získat Azure Security Center?
Azure Security Center je povolená u vašeho předplatného Microsoft Azure a ke kterému se dostanete z [Azure Portal](https://azure.microsoft.com/features/azure-portal/). Pokud k němu chcete získat přístup, [Přihlaste se k portálu](https://portal.azure.com), vyberte **Procházet** a přejděte na **Security Center**.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Které prostředky Azure monitoruje Azure Security Center?
Azure Security Center sleduje následující prostředky Azure:

* Virtuální počítače (včetně [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Škálovací sady virtuálních počítačů
* Partnerská řešení integrovaná s vaším předplatným Azure, například firewall webových aplikací na virtuálních počítačích a na App Service Environment
* [Řada služeb Azure PaaS uvedených v přehledu produktu](features-paas.md)


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak se můžu podívat na aktuální stav zabezpečení prostředků Azure?
Na stránce **přehled Security Center** se zobrazuje celkové stav zabezpečení vašeho prostředí v členění podle COMPUTE, sítě, & úložiště dat a aplikací. Každý typ prostředku má indikátor ukazující zjištěné chyby zabezpečení. Kliknutím na každou dlaždici se zobrazí seznam problémů se zabezpečením, které identifikoval Security Center, spolu s inventářem prostředků ve vašem předplatném.



## <a name="what-is-a-security-initiative"></a>Co je iniciativa zabezpečení?
Iniciativa zabezpečení definuje sadu ovládacích prvků (zásad), které se doporučují pro prostředky v rámci zadaného předplatného. V Azure Security Center přiřadíte iniciativy pro vaše předplatná Azure podle požadavků na zabezpečení vaší společnosti a typu aplikací nebo citlivosti dat v jednotlivých předplatných.

Zásady zabezpečení, které jsou povolené v Azure Security Center, doporučení zabezpečení a monitorování. Další informace najdete v informacích o [zásadách zabezpečení, iniciativách a doporučeních](security-policy-concept.md).


## <a name="who-can-modify-a-security-policy"></a>Kdo může upravit zásady zabezpečení?
Pokud chcete upravit zásady zabezpečení, musíte být **správcem zabezpečení** nebo **vlastníkem** tohoto předplatného.

Informace o tom, jak nakonfigurovat zásady zabezpečení, najdete [v tématu Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>Co je to doporučení zabezpečení?
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když se identifikují potenciální slabá místa zabezpečení, vytvoří se doporučení. Doporučení vás provede procesem konfigurace potřebného ovládacího prvku. Příklady:

* Zřizování ochrany proti malwaru, které vám pomůžou identifikovat a odebrat škodlivý software
* [Skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md) a pravidla pro řízení provozu do virtuálních počítačů
* Zřizování firewallu webových aplikací, které vám pomůžou chránit před útoky zaměřenými na vaše webové aplikace
* Nasazení chybějících aktualizací systému
* Adresování konfigurací operačního systému, které neodpovídají doporučeným standardním hodnotám

Tady se zobrazí jenom doporučení, která jsou povolená v zásadách zabezpečení.


## <a name="what-triggers-a-security-alert"></a>Co aktivuje výstrahu zabezpečení?
Azure Security Center automaticky shromažďuje, analyzuje a uchovává data protokolu z vašich prostředků Azure, sítě a partnerských řešení, jako jsou antimalware a brány firewall. Při zjištění ohrožení zabezpečení se vytvoří výstraha zabezpečení. Příklady zahrnují zjišťování následujících situací:

* Ohrožené virtuální počítače, které komunikují se známými IP adresami se zlými úmysly
* Byl zjištěn rozšířený malware pomocí zasílání zpráv o chybách systému Windows.
* Útoky hrubou silou na virtuální počítače
* Výstrahy zabezpečení z integrovaných řešení zabezpečení partnerů, jako jsou například anti-malware nebo brány firewall webových aplikací


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaký je rozdíl mezi zjištěnými hrozbami a výstrahou z centra zabezpečení Microsoft Security Center oproti Azure Security Center?
Microsoft Security Response Center (MSRC) provádí výběr sledování zabezpečení sítě a infrastruktury Azure a přijímá analýzy hrozeb a stížnosti na zneužití od třetích stran. Když bude MSRC vědět, že k zákaznickým datům došlo v nezákonné nebo neoprávněné osobě nebo že její používání Azure nedodržuje podmínky přijatelného použití, správce incidentů zabezpečení ho upozorní zákazníka. K oznámení obvykle dochází odesláním e-mailu do kontaktů zabezpečení uvedených v Azure Security Center nebo vlastníka předplatného Azure, pokud není zadán kontakt zabezpečení.

Security Center je služba Azure, která nepřetržitě monitoruje prostředí Azure zákazníka a používá analýzu k automatické detekci široké škály potenciálně škodlivých aktivit. Tyto detekce jsou v řídicím panelu Security Center Surface výstrah zabezpečení.