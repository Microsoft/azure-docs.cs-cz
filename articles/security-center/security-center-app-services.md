---
title: Ochrana rozhraní API a webových aplikací Azure App Service
description: Tento článek vám pomůže začít chránit vaše Azure App Service webové aplikace a rozhraní API v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: ec3fb8609612f3920e330da7922fdd1eb8883305
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459772"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Ochrana rozhraní API a webových aplikací Azure App Service

Azure App Service je plně spravovaná platforma pro vytváření a hostování webových aplikací a rozhraní API, aniž byste se museli starat o správu infrastruktury. Poskytuje správu, monitorování a Operational Insights pro splnění požadavků na výkon, zabezpečení a dodržování předpisů na podnikové úrovni. Další informace najdete v tématu [Azure App Service](https://azure.microsoft.com/services/app-service/).

Pokud chcete pro svůj Azure App Service plán povolit rozšířenou ochranu před internetovými útoky, musíte:

* Přihlášení k odběru cenové úrovně Standard Azure Security Center
* Povolte plán App Service, jak je znázorněno níže. Security Center je nativně integrovaná s App Service a odstraňuje nutnost nasazení a registraci – integrace je transparentní.
* Mít App Service plán, který je přidružený k vyhrazeným počítačům. Podporované plány jsou: Basic, Standard, Premium, Isolated nebo Linux. Security Center nepodporuje plány Free, Shared a spotřeb. Další informace najdete v tématu [plány App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

Když je povolený plán App Service, Security Center vyhodnocuje prostředky, na které se vztahuje plán App Service, a vygeneruje doporučení zabezpečení na základě jejich zjištění. Security Center chrání instanci virtuálního počítače, ve které je spuštěný App Service a rozhraní pro správu. Monitoruje také požadavky a odpovědi odeslané do aplikací spuštěných v App Service a z nich.

Security Center využívá škálování cloudu a viditelnost, kterou má Azure jako poskytovatel cloudu, ke sledování běžných útoků na webové aplikace. Security Center může zjišťovat útoky na vaše aplikace a identifikovat nově vznikající útoky – i když se útočníci nacházejí ve fázi rekognoskace, hledají chyby zabezpečení napříč několika aplikacemi hostovanými v Azure. Jako služba Azure-Native je Security Center také v jedinečné pozici, která nabízí analýzy zabezpečení založené na hostiteli, které se týkají základních výpočetních uzlů pro tento PaaS, což umožňuje Security Center detekovat útoky na webové aplikace, které už byly zneužity. Další podrobnosti najdete v tématu [Ochrana před hrozbami pro Azure App Service](threat-protection.md#app-services).


## <a name="enable-monitoring-and-protection-of-app-service"></a>Povolit monitorování a ochranu App Service

1. V Azure Portal vyberte možnost Security Center.
2. Přejít na **Nastavení cenové &** a vyberte předplatné.
3. V části **cenová úroveň**na řádku **App Service** přepněte plán na **povoleno**.

    [![Povolení služby App Services v předplatném úrovně Standard](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> Počet instancí uvedených pro vaše **množství prostředků** představuje celkový počet výpočetních instancí ve všech App Service plánech v tomto předplatném, který běží v okamžiku, kdy jste otevřeli okno cenová úroveň.
>
> Azure App Service nabízí celou řadu plánů. Plán App Service definuje sadu výpočetních prostředků, které má webová aplikace běžet. Jsou ekvivalentní se serverovými farmami v konvenčním webovém hostování. Jednu nebo více aplikací je možné nakonfigurovat tak, aby běžely na stejných výpočetních prostředcích (nebo ve stejném plánu App Service).
>
>Pokud chcete na webu Azure Portal ověřit počet výpočetních instancí, přejděte na "App Service plány", kde můžete zobrazit počet výpočetních instancí používaných jednotlivými plány. 






Pokud chcete zakázat monitorování a doporučení pro App Service, opakujte tento postup a přepněte plán **App Service** na **zakázáno**.



## <a name="see-also"></a>Viz také
V tomto článku jste zjistili, jak ve službě Azure Security Center používat funkce sledování. Další informace o Azure Security Center najdete v následujících článcích:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md): Zjistěte, jak se v Azure Security Center konfiguruje nastavení zabezpečení.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Aplikační služby](security-center-virtual-machine-protection.md#app-services): zobrazení seznamu služby App Service Environment s přehledy stavu.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak sledovat stav vašich partnerských řešení.
* [Blog o zabezpečení Azure](https://docs.microsoft.com/archive/blogs/azuresecurity/): Přečtěte si příspěvky o zabezpečení a dodržování předpisů Azure.
