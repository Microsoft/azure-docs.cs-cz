---
title: Kognitivní služby kontejnery často kladené otázky (FAQ)
titleSuffix: Azure Cognitive Services
description: Nejčastější dotazy a odpovědi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8aae650065e8de11ccdc55a8a056c379a219fcb4
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876502"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Azure Cognitive Services kontejnery nejčastější dotazy (FAQ)

## <a name="general-questions"></a>Obecné otázky

**Otázka: Co je k dispozici?**

**A:** [Kontejnerová podpora ve službě Azure Cognitive Services](../cognitive-services-container-support.md) umožňuje vývojářům používat stejná inteligentní rozhraní API, která jsou dostupná v Azure, ale s [výhodami](../cognitive-services-container-support.md#features-and-benefits) kontejnerizace. Podpora kontejnerů je momentálně dostupná ve verzi Preview pro podmnožinu služeb Azure Cognitive Services, včetně částí:

> [!div class="checklist"]
> * [Detektor anomálií][ad-containers]
> * [Computer Vision][cv-containers]
> * [Tvář][fa-containers]
> * [Rozpoznávání formulářů][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Rozhraní API služby Speech][sp-containers]
> * [Analýza textu][ta-containers]

**Otázka: Existuje nějaký rozdíl mezi cloudem služeb Cognitive Services a kontejnery?**

**A:** Kontejnery služeb Cognitive Services jsou alternativou ke cloudu služeb Cognitive Services. Kontejnery nabízejí stejné funkce jako odpovídající cloudové služby. Zákazníci můžou nasadit kontejnery místně nebo v Azure. Základní technologie AI, cenové úrovně, klíče rozhraní API a podpis rozhraní API jsou stejné mezi kontejnerem a odpovídajícími cloudovými službami. Tady jsou [funkce a výhody](../cognitive-services-container-support.md#features-and-benefits) pro výběr kontejnerů před jejich ekvivalentem cloudové služby.

**Otázka: Budou kontejnery k dispozici pro všechny služby Cognitive Services a jaké jsou další sady kontejnerů, které bychom měli očekávat?**

**A:** Rádi bychom, aby více cognitive services k dispozici jako kontejner nabídky. Obraťte se na místního správce účtů Microsoft a získejte aktuální informace o nových verzích kontejnerů a dalších oznámeních služeb Cognitive Services.

**Otázka: Jaká bude smlouva o úrovni služeb (SLA) pro kontejnery služeb Cognitive Services?**

**A:** Kontejnery služeb Cognitive Services nemají sla.

Konfigurace prostředků kontejneru služby Cognitive Services jsou řízeny zákazníky, takže společnost Microsoft nebude nabízet sla pro obecnou dostupnost (GA). Zákazníci mohou nasadit kontejnery místně, a proto definují hostitelská prostředí.

> [!IMPORTANT]
> Další informace o smlouvách o úrovni služeb služeb cognitive services [najdete na naší stránce smlouvy SLA](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

**Otázka: Jsou tyto kontejnery k dispozici v suverénních cloudech?**

**A:** Ne každý je obeznámen s termínem "suverénní mrak", takže začněme s definicí:

> "Suverénní cloud" se skládá z cloudů [Azure Government](../../azure-government/documentation-government-welcome.md), [Azure Germany](../../germany/germany-welcome.md)a Azure [China 21Vianet.](https://docs.microsoft.com/azure/china/overview-operations)

Bohužel kontejnery cognitive services *nejsou* nativně podporovány v suverénních cloudech. Kontejnery lze spustit v těchto cloudech, ale budou vyňaty z veřejného cloudu a je třeba odeslat data o využití do veřejného koncového bodu.

### <a name="versioning"></a>Správa verzí

**Otázka: Jak jsou kontejnery aktualizovány na nejnovější verzi?**

**A:** Zákazníci si mohou vybrat, kdy mají aktualizovat kontejnery, které nasadili. Kontejnery budou označeny standardními `latest` [značkami Dockeru,](https://docs.docker.com/engine/reference/commandline/tag/) například k označení nejnovější verze. Doporučujeme zákazníkům, aby vytahovali nejnovější verzi kontejnerů po jejich vydání, pokladní [webhooky Azure Container Registry](../../container-registry/container-registry-webhook.md) pro podrobnosti o tom, jak získat upozornění při aktualizaci bitové kopie.
 
**Otázka: Jaké verze budou podporovány?**

**A:** Aktuální a poslední hlavní verze kontejneru bude podporována. Doporučujeme však zákazníkům, aby zůstali aktuální, aby získali nejnovější technologie.
 
**Otázka: Jak jsou aktualizace verzí?**

**A:** Hlavní verze změny označují, že je narušující změny podpisu rozhraní API. Předpokládáme, že se to obecně bude shodovat s hlavními změnami verzí odpovídající nabídky cloudu služby Cognitive Service. Dílčí verze změny označují opravy chyb, aktualizace modelu nebo nové funkce, které neprovedou narušující změny podpisu rozhraní API.

## <a name="technical-questions"></a>Technické otázky

**Otázka: Jak mám spouštět kontejnery služeb Cognitive Services na zařízeních IoT?**

Ať už nemáte spolehlivé připojení k internetu, nebo chcete ušetřit na ceně šířky pásma. Nebo pokud máte požadavky na nízkou latenci nebo máte co do činění s citlivými daty, která je potřeba analyzovat na místě, [Azure IoT Edge s kontejnery kognitivních služeb](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) vám dává konzistenci s cloudem.

**Otázka: Jak mohu poskytnout zpětnou vazbu k produktu a doporučení funkcí?**

**A:** Zákazníci jsou povzbuzováni, aby [vyjádřili své obavy](https://cognitive.uservoice.com/) veřejně, a up-hlasování ostatní, kteří učinili totéž, kde potenciální problémy překrývají. Uživatelský hlasový nástroj lze použít jak pro zpětnou vazbu produktu, tak pro doporučení funkcí.

**Otázka: Na koho se mám obrátit s žádostí o podporu?**

**A:** Kanály zákaznické podpory jsou stejné jako cloudová nabídka služeb Cognitive Services. Všechny kontejnery služeb Cognitive Services obsahují funkce protokolování, které nám a zákazníkům komunitní podpory pomohou. Další podporu naleznete v následujících možnostech.

### <a name="customer-support-plan"></a>Plán zákaznické podpory

Zákazníci by měli odkazovat na jejich [plán podpory Azure](https://azure.microsoft.com/support/plans/) a zjistit, koho kontaktovat pro podporu.

### <a name="azure-knowledge-center"></a>Znalostní centrum Azure

Zákazníci si mohou prohlédnout [znalostní centrum Azure](https://azure.microsoft.com/resources/knowledge-center/) a zodpovědět otázky a problémy s podporou.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) je otázka a odpověď stránky pro profesionální a nadšence programátory.

Prozkoumejte následující značky pro potenciální otázky a odpovědi, které odpovídají vašim potřebám.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft Kognitivní](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**Otázka: Jak funguje fakturace?**

**A:** Zákazníkům se účtuje účtovaná spotřeba, podobně jako cloud služeb Cognitive Services. Kontejnery musí být nakonfigurované pro odesílání dat měření do Azure a transakce se budou účtovat odpovídajícím způsobem. Prostředky používané v hostovaných a místních službách přidají k jedné kvótě s odstupňovanými cenami a započítá se do obou použití. Další podrobnosti naleznete na stránce s cenami odpovídající nabídky.

* [Detektor anomálií][ad-containers-billing]
* [Computer Vision][cv-containers-billing]
* [Tvář][fa-containers-billing]
* [Rozpoznávání formulářů][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [Rozhraní API služby Speech][sp-containers-billing]
* [Analýza textu][ta-containers-billing]

> [!IMPORTANT]
> Kontejnery služeb Cognitive Services nejsou licencovány ke spuštění bez připojení k Azure pro měření. Zákazníci musí povolit kontejnery komunikovat fakturační údaje se službou měření za všech okolností. Kontejnery služeb Cognitive Services neodesílají zákaznická data společnosti Microsoft.
 
**Otázka: Jaká je aktuální záruka podpory pro kontejnery?**

**A:** Na náhledy se nevztahuje žádná záruka. Standardní záruka společnosti Microsoft na podnikový software bude platit, pokud jsou kontejnery formálně oznámeny jako obecné dostupnosti (GA).
 
**Otázka: Co se stane s kontejnery služeb Cognitive Services, když dojde ke ztrátě připojení k internetu?**

**A:** Kontejnery služeb Cognitive Services *nejsou licencovány* ke spuštění bez připojení k Azure pro měření. Zákazníci musí povolit, aby kontejnery mohly vždy komunikovat se službou měření.

**Otázka: Jak dlouho může kontejner fungovat, aniž by byl připojen k Azure?**

**A:** Kontejnery služeb Cognitive Services *nejsou licencovány* ke spuštění bez připojení k Azure pro měření. Zákazníci musí povolit, aby kontejnery mohly vždy komunikovat se službou měření.
 
**Otázka: Jaký je aktuální hardware potřebný ke spuštění těchto kontejnerů?**

**A:** Kontejnery služeb Cognitive Services jsou kontejnery založené na x64, které můžou spouštět libovolné kompatibilní linuxové uzly, virtuální počítače a hraniční zařízení, které podporuje kontejnery x64 Linux Docker. Všechny vyžadují procesory. Minimální a doporučené konfigurace pro každou nabídku kontejnerů jsou k dispozici níže:

* [Detektor anomálií][ad-containers-recommendations]
* [Computer Vision][cv-containers-recommendations]
* [Tvář][fa-containers-recommendations]
* [Rozpoznávání formulářů][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [Rozhraní API služby Speech][sp-containers-recommendations]
* [Analýza textu][ta-containers-recommendations]
 
**Otázka: Jsou tyto kontejnery aktuálně podporovány v systému Windows?**

**A:** Kontejnery Cognitive Services jsou kontejnery Linux, ale existuje určitá podpora pro kontejnery Linux u systému Windows. Další informace o kontejnerech Linuxu v systému Windows naleznete v [dokumentaci k Dockeru](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**Otázka: Jak zjistím kontejnery?**

**A:** Kontejnery služeb Cognitive Services jsou dostupné na různých místech, jako je například portál Azure, centrum Dockeru a registry kontejnerů Azure. Nejnovější umístění kontejnerů naleznete v [úložištích kontejnerů a nastoupení obrázků](../cognitive-services-container-support.md#container-repositories-and-images).

**Otázka: Jak se kontejnery služeb Cognitive Services porovnávají s nabídkami AWS a Google?**

**A:** Microsoft je prvním poskytovatelem cloudu, který přesouvá své předem vycvičené modely AI v kontejnerech s jednoduchou fakturací za transakci, jako by zákazníci používali cloudovou službu. Microsoft věří, že hybridní cloud dává zákazníkům větší výběr.

**Otázka: Jaké certifikace dodržování předpisů kontejnery mají?**

**A:** Kontejnery služeb Cognitive Services nemají žádné certifikace dodržování předpisů

**Otázka: V jakých oblastech jsou kontejnery služeb Cognitive Services k dispozici?**

**A:** Kontejnery lze spustit kdekoli v libovolné oblasti, ale potřebují klíč a volat zpět do Azure pro měření. Všechny podporované oblasti pro cloudovou službu jsou podporovány pro volání měření kontejnerů.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
