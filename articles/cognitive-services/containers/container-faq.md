---
title: Nejčastější dotazy k Cognitive Services Containers (FAQ)
titleSuffix: Azure Cognitive Services
description: Nejčastější dotazy a odpovědi
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 3d35a1f6913d0b657956489d0e57836a05f9eb1d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900056"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Nejčastější dotazy k Azure Cognitive Services Containers (FAQ)

## <a name="general-questions"></a>Obecné otázky

**Otázka: co je k dispozici?**

**A:** Kontejnery Azure Cognitive Services umožňují vývojářům používat stejné inteligentní rozhraní API, která jsou k dispozici v Azure, ale s [výhodami](../cognitive-services-container-support.md#features-and-benefits) kontejneru. Některé kontejnery jsou k dispozici jako ověřovaná verze Preview, které mohou vyžadovat přístup k aplikaci. Další kontejnery jsou veřejně dostupné jako nebrané verze Preview nebo jsou všeobecně dostupné. Úplný seznam kontejnerů a jejich dostupnosti najdete v článku [Podpora kontejnerů v Azure Cognitive Services](../cognitive-services-container-support.md#container-availability-in-azure-cognitive-services) . Kontejnery můžete zobrazit také v [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

**Otázka: existuje nějaký rozdíl mezi Cognitive Services cloudem a kontejnery?**

**A:** Kontejnery Cognitive Services jsou alternativou ke cloudu Cognitive Services. Kontejnery nabízejí stejné možnosti jako odpovídající cloudové služby. Zákazníci můžou kontejnery nasazovat místně nebo v Azure. Základní technologie AI, cenové úrovně, klíče rozhraní API a signatura rozhraní API jsou stejné mezi kontejnerem a odpovídajícími cloudovou službou. Tady jsou [funkce a výhody](../cognitive-services-container-support.md#features-and-benefits) pro výběr kontejnerů přes svůj ekvivalent cloudové služby.

**Otázka: Návody přístupu a použití chráněného kontejneru Preview?**

**A:** Dříve byly do úložiště hostované kontejnery ověřovaného náhledu `containerpreview.azurecr.io` . Od září 22 2020 se tyto kontejnery hostují na Container Registry Microsoftu a jejich stažení nevyžaduje použití příkazu Docker Login. Pokud byl prostředek Azure vytvořený pomocí schváleného ID předplatného Azure, budete moct spustit kontejner ověřovaného náhledu. Pokud se vaše předplatné Azure po vyplnění [formuláře žádosti](https://aka.ms/csgate)neschválilo, nebudete moct kontejner spustit.


**Otázka: budou kontejnery k dispozici pro všechny Cognitive Services a jaké jsou další sady kontejnerů, které bychom měli očekávat?**

**A:** Rádi bychom Cognitive Services k dispozici jako nabídky kontejnerů. Pokud chcete získat aktualizace nových verzí kontejnerů a dalších oznámení Cognitive Services, kontaktujte svého místního správce účet Microsoft.

**Otázka: co bude smlouva SLA (Service-level agreement) pro kontejnery Cognitive Services?**

**A:** Cognitive Services kontejnery nemají smlouvu SLA.

Cognitive Services konfigurace kontejnerů prostředků se řídí zákazníky, takže Microsoft nenabídne smlouvu SLA pro obecnou dostupnost (GA). Zákazníkům je zdarma nasazovat kontejnery, takže definují prostředí hostitele.

> [!IMPORTANT]
> Další informace o Cognitive Services smluv o úrovni služeb [najdete na naší stránce věnované smlouvě SLA](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

**Otázka: jsou tyto kontejnery k dispozici v cloudech z svrchovaného místa?**

**A:** S termínem "svrchovaného cloudu" nemusíte mít všechny uživatele, takže začneme s definicí:

> "Svrchovaná Cloud" se skládá z cloudů [Azure Government](../../azure-government/documentation-government-welcome.md), [Azure Německo](../../germany/germany-welcome.md)a [Azure Čína 21Vianet](https://docs.microsoft.com/azure/china/overview-operations) .

Kontejnery Cognitive Services v cloudech svrchovaného *nativně nepodporují.* Kontejnery mohou být spuštěny v těchto cloudech, ale budou vyžádány z veřejného cloudu a musí odesílat data o využití do veřejného koncového bodu.

### <a name="versioning"></a>Správa verzí

**Otázka: jak jsou kontejnery aktualizovány na nejnovější verzi?**

**A:** Zákazníci si můžou vybrat, kdy se mají aktualizovat kontejnery, které nasadili. Kontejnery budou označeny standardními [značkami Docker](https://docs.docker.com/engine/reference/commandline/tag/) , jako `latest` je například, aby označovaly nejnovější verzi. Zákazníkům doporučujeme, aby při jejich vydávání vyčetli nejnovější verzi kontejnerů, zaregistrujte [Azure Container Registry webhooků](../../container-registry/container-registry-webhook.md) , abyste získali podrobnosti o tom, jak se při aktualizaci obrázku zobrazí oznámení.
 
**Otázka: Jaké verze budou podporovány?**

**A:** Bude podporována aktuální a poslední hlavní verze kontejneru. Doporučujeme však zákazníkům, aby si získali nejnovější technologii.
 
**Otázka: jak se verze aktualizují?**

**A:** Hlavní změny verze znamenají, že došlo k zásadní změně podpisu rozhraní API. Předpokládáme, že se obecně shoduje se změnami hlavní verze v odpovídající nabídce cloudové služby pro rozpoznávání. Změny podverze označují opravy chyb, aktualizace modelů nebo nové funkce, které nedělají zásadní změnu v signatuře rozhraní API.

## <a name="technical-questions"></a>Technické dotazy

**Otázka: Jak mám spustit kontejnery Cognitive Services na zařízeních IoT?**

**A:** Bez ohledu na to, jestli nemáte spolehlivé připojení k Internetu, nebo pokud chcete ušetřit náklady na šířku pásma. Nebo pokud mají požadavky na nízkou latenci nebo pracujete s citlivými daty, která je potřeba analyzovat na pracovišti, [Azure IoT Edge s kontejnery Cognitive Services](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) poskytuje konzistenci s cloudem.

**Otázka: jsou tyto kontejnery kompatibilní s OpenShift?** 

Netestujeme kontejnery pomocí OpenShift, ale obecně by Cognitive Services kontejnery měly běžet na jakékoli platformě podporující image Docker. Pokud používáte OpenShift, doporučujeme spouštět kontejnery jako `root-user` .

**Otázka: Návody poskytovat zpětnou vazbu k produktu a doporučení funkcí?**

**A:** Zákazníkům se doporučuje [hlasovat své obavy](https://cognitive.uservoice.com/) veřejně a hlasovat pro ostatní, kteří provedli stejný postup jako při překrytí potenciálních problémů. Nástroj pro telefonování uživatelů se dá použít pro zpětnou vazbu k produktu i pro doporučení funkcí.

**Otázka: Jaké stavové zprávy a chyby jsou vráceny kontejnery Cognitive Services?**

**A:** Seznam stavových zpráv a chyb naleznete v následující tabulce.

|Status  | Popis  |
|---------|---------|
| `Valid` | Klíč rozhraní API je platný, není třeba provádět žádnou akci. |
| `Invalid` |   Váš klíč rozhraní API je neplatný. Aby bylo možné spustit kontejner, je nutné zadat platný klíč rozhraní API. Klíč a oblast rozhraní API najdete v části **klíče a koncový bod** pro váš prostředek Azure Cognitive Services v Azure Portal. |
| `Mismatch` | Zadali jste klíč rozhraní API nebo koncový bod pro jiný druh prostředku služby pro rozpoznávání. Vyhledejte klíč rozhraní API a oblast služby v části **klíče a koncový bod** pro váš prostředek Azure Cognitive Services. |
| `CouldNotConnect` | Kontejner se nemůže připojit ke koncovému bodu fakturace. `Retry-After`Před provedením dalších požadavků ověřte hodnotu a počkejte na dokončení tohoto období. |
| `OutOfQuota` | Klíč rozhraní API nemá kvótu. Můžete buď upgradovat cenovou úroveň, nebo počkat na zpřístupnění dalších kvót. V tomto Azure Portal v části **cenová úroveň** svého prostředku služby Azure prohledat svou vrstvu. |
| `BillingEndpointBusy` | Koncový bod fakturace je aktuálně zaneprázdněný. `Retry-After`Před provedením dalších požadavků ověřte hodnotu a počkejte na dokončení tohoto období. |
| `ContainerUseUnauthorized` | Poskytnutý klíč rozhraní API není autorizovaný pro použití s tímto kontejnerem. Pravděpodobně používáte ověřovaný kontejner, proto se ujistěte, že je vaše předplatné Azure schválené, a to odesláním [online žádosti](https://aka.ms/csgate). |
| `Unknown` | Server momentálně nemůže zpracovat žádosti o fakturaci. |


**Otázka: koho se mám obrátit na podporu?**

**A:** Kanály zákaznické podpory jsou stejné jako u cloudové nabídky Cognitive Services. Všechny kontejnery Cognitive Services zahrnují funkce protokolování, které nám pomůžou a komunita podpory pro zákazníky. Další podporu najdete v následujících možnostech.

### <a name="customer-support-plan"></a>Plán zákaznické podpory

Zákazníci by se měli obrátit na svůj [plán podpory Azure](https://azure.microsoft.com/support/plans/) , aby mohli zjistit, kdo má kontaktovat podporu.

### <a name="azure-knowledge-center"></a>Centrum znalostí Azure

Zákazníkům je zdarma prozkoumat [Centrum znalostí Azure](https://azure.microsoft.com/resources/knowledge-center/) , které odpoví na otázky a problémy s podporou.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) je web otázek a odpovědí pro programátory Professional a patříte mezi fanoušky.

Seznamte se s případnými otázkami a odpověďmi, které odpovídají vašim potřebám, v následujících značkách.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Rozpoznávání Microsoftu](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**Otázka: Jak funguje fakturace?**

**A:** Zákazníkům se účtují na základě spotřeby, podobně jako u Cognitive Servicesho cloudu. Kontejnery je potřeba nakonfigurovat tak, aby odesílaly data měření do Azure, a odpovídajícím způsobem se účtují transakce. Prostředky používané v rámci hostovaných a místních služeb se budou přidávat k jedné kvótě s vrstvenými cenami a počítají se v obou použitích. Další podrobnosti najdete na stránce s cenami odpovídající nabídky.

* [Detektor anomálií][ad-containers-billing]
* [Počítačové zpracování obrazu][cv-containers-billing]
* [Rozpoznávání tváře][fa-containers-billing]
* [Rozpoznávání formulářů][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [Rozhraní API služby Speech][sp-containers-billing]
* [Analýza textu][ta-containers-billing]

> [!IMPORTANT]
> Nemusíte spouštět kontejnery Cognitive Services bez připojení k Azure pro měření. Zákazníci musí povolit kontejnerům, aby ve všech časech komunikovaly informace o fakturaci. Kontejnery Cognitive Services neodesílají zákaznická data společnosti Microsoft.
 
**Otázka: jaká je aktuální záruka podpory kontejnerů?**

**A:** Pro verze Preview není k dispozici žádná záruka. Standardní záruka společnosti Microsoft pro podnikový software se použije, pokud jsou kontejnery formálně vyhlášené jako obecně dostupné (GA).
 
**Otázka: co se stane s Cognitive Services kontejnery, když dojde ke ztrátě připojení k Internetu?**

**A:** Nemusíte *not licensed* spouštět kontejnery Cognitive Services bez připojení k Azure pro měření. Zákazníci musí povolit kontejnerům nepřetržitě komunikovat se službou měření dat.

**Otázka: jak dlouho může kontejner fungovat bez připojení k Azure?**

**A:** Nemusíte *not licensed* spouštět kontejnery Cognitive Services bez připojení k Azure pro měření. Zákazníci musí povolit kontejnerům nepřetržitě komunikovat se službou měření dat.
 
**Otázka: co je aktuální hardware potřebný ke spuštění těchto kontejnerů?**

**A:** Kontejnery Cognitive Services jsou kontejnery založené na platformě x64, které můžou spouštět libovolný kompatibilní uzel Linux, virtuální počítač a hraniční zařízení, které podporují kontejnery Docker platformy x64 Linux. Všechny vyžadují procesorové procesory. Minimální a doporučené konfigurace pro jednotlivé nabídky kontejnerů jsou k dispozici níže:

* [Detektor anomálií][ad-containers-recommendations]
* [Počítačové zpracování obrazu][cv-containers-recommendations]
* [Rozpoznávání tváře][fa-containers-recommendations]
* [Rozpoznávání formulářů][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [Rozhraní API služby Speech][sp-containers-recommendations]
* [Analýza textu][ta-containers-recommendations]
 
**Otázka: jsou tyto kontejnery aktuálně podporovány ve Windows?**

**A:** Kontejnery Cognitive Services jsou kontejnery pro Linux, ale v systému Windows je k dispozici podpora kontejnerů pro Linux. Další informace o kontejnerech Linux ve Windows najdete v [dokumentaci k Docker](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**Otázka: Návody zjišťování kontejnerů?**

**A:** Kontejnery Cognitive Services jsou k dispozici v různých umístěních, jako je Azure Portal, centrum Docker a Registry kontejnerů Azure. Pro nejnovější umístění kontejnerů se podívejte na [úložiště kontejnerů a image](../cognitive-services-container-support.md#container-repositories-and-images).

**Otázka: jak se Cognitive Services kontejnery porovnávají s AWS a Google nabídkami?**

**A:** Microsoft je prvním poskytovatelem cloudu, který umožňuje přesunout své předem připravené modely AI do kontejnerů s jednoduchou fakturací na transakci, jako by zákazníci používali cloudovou službu. Microsoft se domnívá, že hybridní cloud nabízí zákazníkům více možností.

**Otázka: Jaké certifikáty pro certifikace dodržování předpisů mají kontejnery?**

**A:** Kontejnery služby pro rozpoznávání neobsahují žádné certifikace dodržování předpisů.

**Otázka: Jaké oblasti jsou Cognitive Services kontejnery k dispozici v nástroji?**

**A:** Kontejnery je možné spustit kdekoli v libovolné oblasti, ale potřebují klíč a zavolat zpátky do Azure pro měření. Všechny podporované oblasti pro cloudovou službu se podporují pro volání měření kontejneru.

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
