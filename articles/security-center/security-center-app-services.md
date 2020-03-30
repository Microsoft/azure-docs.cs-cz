---
title: Ochrana webových aplikací a virtuálních api služby Azure App Service
description: Tento článek vám pomůže začít chránit vaše webové aplikace azure app service a api v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 2d81e1a1218add504e1e35015276b6924da0e3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616480"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Ochrana webových aplikací a virtuálních api služby Azure App Service

Azure App Service je plně spravovaná platforma pro vytváření a hostování webových aplikací a api bez obav z nutnosti spravovat infrastrukturu. Poskytuje přehledy pro správu, monitorování a provoz, které splňují požadavky na výkon, zabezpečení a dodržování předpisů na úrovni organizace. Další informace najdete v [tématu Azure App Service](https://azure.microsoft.com/services/app-service/).

Chcete-li povolit pokročilou ochranu před hrozbami pro váš plán služby Azure App Service, musíte:

* Přihlášení k odběru standardní cenové úrovně Centra zabezpečení Azure
* Povolte plán služby App Service, jak je znázorněno níže. Security Center je nativně integrován s App Service, což eliminuje potřebu nasazení a registrace - integrace je transparentní.
* Mít plán služby App Service, který je spojen s vyhrazené počítače. Podporované plány jsou: Základní, Standardní, Premium, Izolované nebo Linux. Centrum zabezpečení nepodporuje plány Zdarma, Sdílené nebo Spotřeba. Další informace naleznete v tématu [Plány služby App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

Když je povolen plán Služby Aplikací, Security Center vyhodnotí prostředky, na které se vztahuje váš plán služby App Service, a na základě svých zjištění vygeneruje doporučení zabezpečení. Security Center chrání instanci virtuálního počítače, ve kterém je spuštěna vaše služba App Service a rozhraní pro správu. Také monitoruje požadavky a odpovědi odeslané do a z vašich aplikací spuštěných ve službě App Service.

Security Center využívá škálování cloudu a viditelnost, kterou Má Azure jako poskytovatel cloudu, ke sledování běžných útoků webových aplikací. Security Center může odhalit útoky na vaše aplikace a identifikovat vznikající útoky – i když jsou útočníci ve fázi průzkumu, prohledávat chyby zabezpečení ve více aplikacích hostovaných v Azure. Jako nativní služba Azure je Centrum zabezpečení také v jedinečné pozici, aby nabízelo analýzu zabezpečení založenou na hostiteli pokrývající základní výpočetní uzly pro tento PaaS, což umožňuje Centru zabezpečení zjišťovat útoky proti webovým aplikacím, které již byly zneužity. Další podrobnosti najdete [v tématu Ochrana před hrozbami pro službu Azure App Service](threat-protection.md#app-services).


## <a name="enabling-monitoring-and-protection-of-app-service"></a>Povolení monitorování a ochrany služby App Service

1. Na webu Azure Portal zvolte Security Center .
2. Přejděte na **Nastavení cenové&** a zvolte předplatné.
3. V části **Cenová úroveň**v řádku **Služby Aplikace** přepněte plán na **Povoleno**.

    [![Povolení služeb aplikace v předplacené službě Standard](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> Počet instancí uvedených pro **množství prostředků** představuje celkový počet výpočetních instancí ve všech plánech služby App Service v tomto předplatném, které běží v okamžiku, kdy jste otevřeli okno cenové úrovně.
>
> Služba Azure App Service nabízí celou řadu plánů. Váš plán služby App Service definuje sadu výpočetních prostředků pro spuštění webové aplikace. Jedná se o ekvivalent serverových farem v konvenčním web hostingu. Jednu nebo více aplikací lze nakonfigurovat tak, aby se spouštěla na stejných výpočetních prostředcích (nebo ve stejném plánu služby App Service).
>
>Chcete-li ověřit počet, přejdete na "Plány služby App Service" na webu Azure Portal, kde můžete zobrazit počet výpočetních instancí používaných jednotlivými plány. 






Chcete-li zakázat monitorování a doporučení pro službu App Service, opakujte tento postup a přepněte plán **služby App Service** na **Zakázáno**.



## <a name="see-also"></a>Viz také
V tomto článku jste zjistili, jak ve službě Azure Security Center používat funkce sledování. Další informace o Azure Security Center najdete v následujících článcích:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md): Zjistěte, jak se v Azure Security Center konfiguruje nastavení zabezpečení.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Služby aplikací](security-center-virtual-machine-protection.md#app-services): Zobrazení seznamu prostředí služby App service se souhrny stavu.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak sledovat stav vašich partnerských řešení.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení a dodržování předpisů Azure.
