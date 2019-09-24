---
title: Ochrana App Services v Azure Security Center | Microsoft Docs
description: Tento článek vám pomůže začít chránit App Services v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 68f7c47f0a0f56085d632f1c1741318f440b41ee
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202480"
---
# <a name="protect-app-service-with-azure-security-center"></a>Ochrana App Service s využitím Azure Security Center
Tento článek vám pomůže použít Azure Security Center k monitorování a ochraně aplikací, které běží nad App Service.

App Service umožňuje sestavovat a hostovat webové aplikace v programovacím jazyce podle vašeho výběru bez nutnosti spravovat infrastrukturu. App Service nabízí automatické škálování a vysokou dostupnost, podporuje Windows i Linux i automatizované nasazení z GitHubu, Azure DevOps nebo nějakého úložiště Git. 

Ohrožení zabezpečení webových aplikací se často využívají útočníky, protože mají společné a dynamické rozhraní pro skoro každou organizaci na internetu. Žádosti o aplikace běžící na App Service procházejí několika branami nasazenými v datových centrech Azure po celém světě a zodpovídají za směrování jednotlivých požadavků do příslušné aplikace. 

Azure Security Center můžou na svých aplikacích spuštěných v App Service spustit posouzení a doporučení v izolovaném prostoru (sandboxech) ve virtuálních počítačích nebo instancích na vyžádání. Díky využití viditelnosti, kterou má Azure jako poskytovatel cloudu, Security Center analyzuje vaše App Service interní protokoly pro monitorování běžných útoků webových aplikací, které se často spouštějí napříč více cíli.

Security Center využívá škálování cloudu k identifikaci útoků na vaše App Service aplikace a Zaměřte se na nově vznikající útoky, zatímco útočníci jsou ve fázi rekognoskace, hledají, jak identifikovat chyby zabezpečení napříč několika weby, které se hostují v Azure. Security Center používá analýzy a modely strojového učení k pokrytí všech rozhraní, která zákazníkům umožňují interakci s aplikacemi, ať už přes protokol HTTP nebo prostřednictvím metod správy. Kromě toho je jako služba první strany v Azure Security Center taky na jedinečné pozici, která nabízí analýzy zabezpečení založené na hostiteli, které se týkají základních výpočetních uzlů pro tento PaaS, což umožňuje Security Center detekovat útoky na webové aplikace, které byly již zneužití.

## <a name="prerequisites"></a>Požadavky

K monitorování a zabezpečení vaší služby App Service musíte mít plán služby App Service, který je spojený s vyhrazenými počítači. Jedná se o tyto plány: Basic, Standard, Premium, Isolated nebo Linux. Azure Security Center nepodporuje plány Free, Shared ani Consumption. Další informace najdete v tématu [plány App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

## <a name="security-center-protection"></a>Ochrana Security Center

Azure Security Center chrání instanci virtuálního počítače, ve které je spuštěný App Service a rozhraní pro správu. Monitoruje také požadavky a odpovědi odeslané do aplikací spuštěných v App Service a z nich.

Security Center je nativně integrovaná s App Service a odstraňuje nutnost nasazení a registraci – integrace je zcela transparentní.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>Povolení monitorování a ochrany App Service

1. V Azure vyberte Security Center.
2. Přejít na **Nastavení cenové &** a vyberte předplatné.
3. V části **cenová úroveň**na řádku **App Service** přepněte plán na **povoleno**.

![přepínač služby App Service](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> Počet instancí uvedených pro vaše množství prostředků představuje počet relevantních instancí App Service, které jsou v tuto chvíli aktivní, když jste otevřeli okno cenová úroveň. Vzhledem k tomu, že se toto číslo může změnit na základě vybraných možností škálování, upraví se odpovídající počet instancí, které se vám účtují.

Pokud chcete zakázat monitorování a doporučení pro App Service, opakujte tento postup a přepněte plán **App Service** na **zakázáno**.



## <a name="see-also"></a>Viz také:
V tomto článku jste zjistili, jak ve službě Azure Security Center používat funkce sledování. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md): Naučte se konfigurovat nastavení zabezpečení v Azure Security Center.
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat a zpracovávat výstrahy zabezpečení.
* [Aplikační služby](security-center-virtual-machine-protection.md#app-services):  Podívejte se na seznam prostředí služby App Service s shrnutími stavu.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak monitorovat stav partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md): Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
