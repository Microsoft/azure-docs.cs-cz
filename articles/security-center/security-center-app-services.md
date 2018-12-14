---
title: Ochrana služby App Services ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento článek pomáhá Začínáme s ochranou služby App Services ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/9/2018
ms.author: rkarlin
ms.openlocfilehash: b38ba140a1e2b853c07e1ffe53405b58663653b5
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343866"
---
# <a name="protect-app-service-with-azure-security-center"></a>Ochrana služby App Service pomocí Azure Security Center
Tento článek vám pomůže používat Azure Security Center k monitorování a chránit vaše aplikace běžící na App Service.

App Service umožňuje vytvářet a hostovat webové aplikace v programovacím jazyce podle vašeho výběru bez nutnosti spravovat infrastrukturu. App Service nabízí automatické škálování a vysoká dostupnost, podporují jak Windows a Linuxem, tak i automatizované nasazení z Githubu, službě Azure DevOps nebo v libovolném adresáři Gitu. 

Chyby ve webových aplikacích zneužívají útočníci, často, protože mají běžné a dynamického rozhraní pro téměř každé organizaci na Internetu. Požadavky na aplikace běžící na App Service prochází několika Gateway nasazené v datových centrech Azure po celém světě, zodpovědná za směrování každý požadavek jeho příslušné aplikace. 

Azure Security Center můžete spustit posouzení a doporučení na vaše aplikace spuštěná ve službě App Service v izolovaných prostorů v virtuálního počítače nebo instance na vyžádání. S využitím viditelnost, které Azure nabízí jako poskytovatel cloudu, Security Center analyzuje protokoly interní služby App Service pro monitorování běžnými útoky webové aplikace, které často spouštět napříč několika cíly.

Security Center bude využívat škálovatelnosti cloudu k identifikaci útoků na vaše aplikace služby App Service a soustředit se na nově vznikající útoky, protože útočníci na fáze rekognoskace prohledávání identifikovat ohrožení zabezpečení napříč více webů hostovaných na Azure. Security Center pomocí analýz a modelů strojového učení k pokrytí všech rozhraní umožňuje zákazníkům interakci s svých aplikací, ať už prostřednictvím protokolu HTTP nebo prostřednictvím metody správy. Kromě toho jako první strany služby v Azure Security Center také jedinečné pozici nabízí analýzy založené na hostiteli zabezpečení, které pokrývají základní výpočetní uzly pro tento PaaS umožňuje Security Center můžete detekovat útoky na webové aplikace, které byly již zneužít.

## <a name="prerequisites"></a>Požadavky

K monitorování a zabezpečení vaší služby App Service, budete muset mít plán služby App Service, která souvisí s vyhrazených počítačů. Tyto plány jsou: Basic, Standard, Premium a izolované nebo Linux. Azure Security Center nepodporuje plány Free, Shared ani spotřeby. Další informace najdete v tématu [plány služby App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

## <a name="security-center-protection"></a>Security Center ochrany

Azure Security Center chrání instance virtuálního počítače, ve kterém běží služby App Service a rozhraní pro správu. Také sleduje žádostí a odpovědí odeslaných do a z vašich aplikací ve službě App Service.

Security Center je nativně integrováno s App Service, takže odpadá potřeba nasazení a zprovoznění – integrace je dokonale transparentní.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>Povolení monitorování a ochranu služby App Service

1. V Azure zvolte Security Center.
2. Přejděte na **zásady zabezpečení** a zvolte předplatné.
3. Na konci řádku předplatné, klikněte na tlačítko **upravit nastavení**.
4. V části **cenová úroveň**v **služby App service** řádek, plán přepnete **povoleno**.

![Přepnout App service](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> Počet instancí uvedené pro vaši množství prostředků, které představuje počet instancí relevantní aktivní v tuto chvíli po otevření okna cenovou úroveň služby App Service. Protože toto číslo může změnit v závislosti na vybrané možnosti škálování, počet instancí, které se vám účtovat odpovídajícím způsobem změněny.

Chcete-li zakázat monitorování a doporučení pro službu App Service, opakujte tento proces a přepnout vaše **služby App Service** plánujete **zakázané**.



## <a name="see-also"></a>Další informace najdete v tématech
V tomto článku jste zjistili, jak ve službě Azure Security Center používat funkce sledování. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md): Zjistěte, jak konfigurovat nastavení zabezpečení ve službě Azure Security Center.
* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat a zpracovávat výstrahy zabezpečení.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak monitorovat stav partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md): Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
