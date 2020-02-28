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
ms.openlocfilehash: f9043ae1414b63f25583d52100774f3e87754a74
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661835"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Nejčastější dotazy – obecné otázky týkající se Azure Security Center

## <a name="what-is-azure-security-center"></a>Co je Azure Security Center?
Azure Security Center pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně tím, že zvyšuje přehled o zabezpečení vašich prostředků a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Security Center používá Microsoft Monitoring Agent ke shromažďování a ukládání dat. Podrobné informace najdete v tématu [shromažďování dat v Azure Security Center](security-center-enable-data-collection.md).


## <a name="how-do-i-get-azure-security-center"></a>Jak získat Azure Security Center?
Azure Security Center je povolená u vašeho předplatného Microsoft Azure a ke kterému se dostanete z [Azure Portal](https://azure.microsoft.com/features/azure-portal/). Pokud k němu chcete získat přístup, [Přihlaste se k portálu](https://portal.azure.com), vyberte **Procházet**a přejděte na **Security Center**.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Které prostředky Azure jsou monitorovány pomocí služby Azure Security Center?
Azure Security Center monitoruje prostředky Azure následující:

* Virtuální počítače (včetně [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Škálovací sady virtuálních počítačů
* Virtuální sítě Azure
* Kontejnery
* Služba Azure SQL
* Účet Azure Storage
* Azure Web Apps (v [App Service Environment](../app-service/environment/intro.md))
* Partnerských řešení integrovaných ve vašem předplatném Azure, jako je například firewallu webových aplikací na virtuálních počítačích a ve službě App Service Environment

Kromě toho je možné monitorovat také počítače mimo Azure (včetně místních) pomocí Azure Security Center. Podporují se [počítače s Windows](./quick-onboard-windows-computer.md) i počítače se systémem [Linux](./quick-onboard-linux-computer.md) .


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak lze zobrazit aktuální stav zabezpečení svoje prostředky Azure?
Na stránce **přehled Security Center** se zobrazuje celkové stav zabezpečení vašeho prostředí v členění podle COMPUTE, sítě, & úložiště dat a aplikací. Každý typ prostředku má indikátor ukazující zjištěné chyby zabezpečení. Kliknutím na každé dlaždici se zobrazí seznam zabezpečení problémů zjištěných pomocí Security Center spolu s inventář prostředků ve vašem předplatném.



## <a name="what-is-a-security-policy"></a>Co jsou zásady zabezpečení?
Zásady zabezpečení definují sadu ovládacích prvků, které se doporučují pro prostředky v rámci zadaného předplatného. Ve službě Azure Security Center určíte zásady pro vaše předplatná Azure podle požadavků zabezpečení vaší společnosti a podle typu aplikací nebo citlivosti dat v každém předplatném.

Zásady zabezpečení povolené ve službě monitorování a doporučení pro zabezpečení Azure Security Center jednotky. Další informace o zásadách zabezpečení najdete v tématu [monitorování stavu zabezpečení v Azure Security Center](security-center-monitoring.md).


## <a name="who-can-modify-a-security-policy"></a>Kdo může upravit zásady zabezpečení?
Pokud chcete upravit nějakou zásadu zabezpečení, musíte být správce zabezpečení nebo vlastníka nebo přispěvatele daného předplatného.

Informace o tom, jak nakonfigurovat zásady zabezpečení, najdete [v tématu Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>Co je doporučeným zabezpečením?
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Pokud se identifikují potenciální ohrožení zabezpečení, vytvoří se doporučení. Doporučení vás provede procesem konfigurace potřebných ovládacího prvku. Můžete například:

* Zřizování antimalwarového pomáhají identifikovat a odebrat škodlivý software
* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md) a pravidla pro řízení provozu do virtuálních počítačů
* Zřízení firewallu webových aplikací, které pomáhají bránit útokům, které cílí na vaše webové aplikace
* Nasazení chybějících aktualizací systému
* Adresování konfigurací operačního systému, které neodpovídají doporučeným standardním hodnotám

Jenom o doporučení, které jsou povolené v zásadách zabezpečení se tady zobrazí.



## <a name="what-triggers-a-security-alert"></a>Co se aktivuje upozornění na zabezpečení?
Azure Security Center automaticky shromažďuje, analyzuje a fuses data protokolu z vašich prostředků Azure, sítě a řešení partnerů, jako jsou antimalwarové zásady a brány firewall. Při zjištění ohrožení zabezpečení se vytvoří výstraha zabezpečení. Příklady zahrnují zjišťování následujících situací:

* Ohrožené virtuální počítače, které komunikují se známými IP adresami se zlými úmysly
* Pokročilý malware zjištěný pomocí zasílání zpráv o chybách Windows
* Útoky hrubou silou na virtuální počítače
* Výstrahy zabezpečení z integrovaných partnerských řešení zabezpečení, jako je Anti-Malware nebo brány firewall webových aplikací


## Proč se hodnoty zabezpečeného skóre změnily? <a name="secure-score-faq"></a>
Od února 2019 Security Center upraveno skóre několika doporučení, aby lépe vyhovoval jejich závažnosti. Výsledkem této úpravy můžou být změny v celkových hodnotách bezpečného skóre.  Další informace o zabezpečeném skóre najdete v tématu [Secure skore Calculation](security-center-secure-score.md).


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaký je rozdíl mezi hrozeb zjištěných a oznamovaných na podle Microsoft Security Response Center a Azure Security Center?
Microsoft Security Response Center (MSRC) provádí monitorování zabezpečení vyberte síť Azure a infrastrukturou a přijímá threat intelligence a zneužití stížností od třetích stran. Při MSRC zjistí, že zákaznická data přistupovala nezákonně nebo neoprávněně strany nebo, že využití Azure pro zákazníka není v souladu s podmínkami přijatelné použití, upozorní správce incidentu zabezpečení zákazníka. Oznámení se obvykle dochází, pošlete e-mail na kontakty zabezpečení zadané v Azure Security Center nebo vlastník předplatného Azure, pokud není zadán kontaktu zabezpečení.

Security Center je služba Azure nepřetržitě monitoruje prostředí Azure zákazníka a použije analytics mohla zjišťovat automaticky širokou škálu potenciálně škodlivé aktivity. Tyto detekce jsou prezentované jako výstrahy zabezpečení v řídicím panelu Security Center.