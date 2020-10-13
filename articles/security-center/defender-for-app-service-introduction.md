---
title: Azure Defender pro App Service – výhody a funkce
description: Přečtěte si o výhodách a funkcích Azure Defenderu pro App Service.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a7026757651d1b0510293101203f41a651c7b851
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850961"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Seznámení s Azure Defenderem pro App Service

Azure App Service je plně spravovaná platforma pro vytváření a hostování webových aplikací a rozhraní API, aniž byste se museli starat o správu infrastruktury. Poskytuje správu, monitorování a Operational Insights pro splnění požadavků na výkon, zabezpečení a dodržování předpisů na podnikové úrovni. Další informace najdete v tématu [Azure App Service](https://azure.microsoft.com/services/app-service/).

**Azure Defender pro App Service** využívá škálování cloudu k identifikaci útoků cílících na aplikace běžící přes App Service. Útočníci hledají webové aplikace a využívají slabých míst. Před směrováním do konkrétních prostředí požadavky na aplikace běžící v Azure procházejí několika branami, kde jsou zkontrolovány a protokolovány. Tato data se pak používají k identifikaci zneužití a útočníků a k získání nových vzorů, které se použijí později.

Díky viditelnosti, kterou Azure nabízí jako poskytovatel cloudu, Security Center analyzuje App Service interní protokoly k identifikaci metodologie útoku na více cílů. Například metodologie zahrnuje rozšířenou kontrolu a distribuované útoky. Tento typ útoku obvykle pochází z malé podmnožiny IP adres a ukazuje vzory procházení k podobným koncovým bodům na více hostitelích. Útoky hledají zranitelnou stránku nebo modul plug-in a nelze je identifikovat z hlediska jednoho hostitele.


## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|[Azure Defender pro App Service](azure-defender.md) se účtuje tak, jak je znázorněno na [stránce s cenami](security-center-pricing.md) .|
|Podporované plány App Service:|![Ano, ](./media/icons/yes-icon.png) Basic, Standard, Premium, izolovaný režim nebo Linux<br>![Žádná ](./media/icons/no-icon.png) volná, sdílená ani spotřebovaná<br>[Další informace o plánech App Service](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>Co je Azure Defender pro App Service chránit?

Když je povolený plán App Service, Security Center vyhodnocuje prostředky, na které se vztahuje plán App Service, a vygeneruje doporučení zabezpečení na základě jejich zjištění. Security Center chrání instanci virtuálního počítače, ve které je spuštěný App Service a rozhraní pro správu. Monitoruje také požadavky a odpovědi odeslané do aplikací spuštěných v App Service a z nich.

Pokud používáte plán App Service založený na Windows, Security Center má také přístup k podkladovým izolovaným prostorům a virtuálním počítačům. Spolu s výše uvedenými daty protokolu může infrastruktura sdělit příběh z nového útoku, který je v případě ohrožení bezpečnosti v zákaznických počítačích v nestejné přírodě. Proto i v případě, že je Security Center nasazena po zneužití webové aplikace, může být schopna detekovat průběžné útoky.


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Ochrana rozhraní API a webových aplikací Azure App Service
Ochrana Azure App Serviceho plánu pomocí Azure Defenderu pro App Service:

- Ujistěte se, že máte podporovaný plán App Service, který je spojený s vyhrazenými počítači. Podporované plány jsou uvedené výše v [dostupnosti](#availability).

- Povolte v předplatném **Azure Defender** (Volitelně můžete povolit jenom plán **Azure Defender pro App Service** ), jak je popsáno v tématu [ceny Azure Security Center](security-center-pricing.md)

Security Center je nativně integrovaná s App Service a odstraňuje nutnost nasazení a registraci – integrace je transparentní.

>[!NOTE]
> Stránka ceny a nastavení obsahuje počet instancí pro **množství prostředků**. To představuje celkový počet výpočetních instancí ve všech App Service plánech v tomto předplatném, který běží v okamžiku, kdy jste otevřeli stránku s cenovou úrovní.
>
> Azure App Service nabízí celou řadu plánů. Plán App Service definuje sadu výpočetních prostředků, které má webová aplikace běžet. Jsou ekvivalentní se serverovými farmami v konvenčním webovém hostování. Jednu nebo více aplikací je možné nakonfigurovat tak, aby běžely na stejných výpočetních prostředcích (nebo ve stejném plánu App Service).
>
>Pokud chcete na webu Azure Portal ověřit počet výpočetních instancí, přejděte na "App Service plány", kde můžete zobrazit počet výpočetních instancí používaných jednotlivými plány. 



## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Defenderu pro App Service. 

Související materiály najdete v následujících článcích: 

- Bez ohledu na to, jestli je výstraha vygenerovaná Security Center nebo přijatá Security Center z jiného bezpečnostního produktu, můžete ji exportovat. Pokud chcete exportovat výstrahy do Azure Sentinel, všech SIEM třetích stran nebo jakýchkoli jiných externích nástrojů, postupujte podle pokynů v tématu [Export výstrah do Siem](continuous-export.md).
- Seznam výstrah Azure App Service najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azureappserv).
- Další informace o plánech App Service najdete v tématu [plány App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).
- > [!div class="nextstepaction"]
    > [Povolit Azure Defender](security-center-pricing.md)