---
title: Co je nového v Počítačové zpracování obrazu?
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje novinky o Počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/13/2021
ms.author: pafarley
ms.openlocfilehash: 78746e7623f58af5ae9df829b48245295dc39f01
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487096"
---
# <a name="whats-new-in-computer-vision"></a>Co je nového v Počítačové zpracování obrazu

Zjistěte, co je nového ve službě. Tyto položky můžou být poznámky k verzi, videa, příspěvky na blogu a další typy informací. Tuto stránku můžete označit tak, aby byla stále aktuální se službou.

## <a name="march-2021"></a>Březen 2021

### <a name="computer-vision-32-public-preview-update"></a>Aktualizace Počítačové zpracování obrazu 3,2 Public Preview

Verze Public Preview pro rozhraní API pro počítačové zpracování obrazu v 3.2 byla aktualizována. Verze Preview obsahuje všechny funkce Počítačové zpracování obrazu společně s aktualizovanými rozhraními API pro čtení a analýzu.

> [!div class="nextstepaction"]
> [Viz Počítačové zpracování obrazu v 3.2 Public Preview 3](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>Únor 2021

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Čtení rozhraní API v 3.2 Public Preview s podporou OCR pro jazyky 73
Počítačové zpracování obrazu Read API verze 3.2 Public Preview, který je k dispozici jako cloudová služba a kontejner Docker, zahrnuje tyto aktualizace:
* [Optické rozpoznávání znaků pro jazyky 73](./language-support.md#optical-character-recognition-ocr) , včetně zjednodušených a tradičních jazyků v čínštině, japonštině, korejštině a latince.
* Přirozené pořadí čtení pro výstup textového řádku (pouze jazyky latinky)
* Klasifikace stylu rukopisu pro textové řádky spolu se stupněm spolehlivosti (jenom pro jazyky latinky)
* Extrahuje text pouze pro vybrané stránky pro vícestránkové dokument.
* K dispozici jako [Distroless kontejner](./computer-vision-how-to-install-containers.md?tabs=version-3-2) pro místní nasazení.

[Přečtěte si další informace](concept-recognizing-text.md) o rozhraní API pro čtení.

> [!div class="nextstepaction"]
> [Použijte rozhraní Read API v 3.2 Public Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>Leden 2021

### <a name="spatial-analysis-container-update"></a>Aktualizace kontejneru prostorových analýz

Nová verze [kontejneru prostorových analýz](spatial-analysis-container.md) byla vydaná s novou sadou funkcí. Tento kontejner Docker umožňuje analyzovat streamování videa v reálném čase, abyste pochopili prostorové vztahy mezi lidmi a jejich pohybem prostřednictvím fyzických prostředí. 

* [Operace prostorových analýz](spatial-analysis-operations.md) se teď dají nakonfigurovat tak, aby se zjistilo, jestli má osoba ochrannou plošku, jako je například maska. 
    * Klasifikátor masky lze povolit pro `personcount` `personcrossingline` `personcrossingpolygon` operace a pomocí konfigurace `ENABLE_FACE_MASK_CLASSIFIER` parametru.
    * Atributy `face_mask` a `face_noMask` budou vráceny jako metadata s hodnocením spolehlivosti pro každou osobu zjištěnou ve streamu videa.
* Operace *personcrossingpolygon* se rozšířila tak, aby umožňovala výpočet doby bydlení, kterou osoba v zóně stráví. Můžete nastavit `type` parametr v konfiguraci zóny pro operaci na `zonedwelltime` a nová událost typu *personZoneDwellTimeEvent* bude obsahovat `durationMs` pole naplněné počtem milisekund, které osoba strávila v zóně.
* **Průlomová změna**: událost *personZoneEvent* byla přejmenována na *personZoneEnterExitEvent*. Tato událost je vyvolána operací *personcrossingpolygon* , když uživatel zadá nebo ukončí zónu a poskytne směrové informace s očíslovanou stranou zóny, která byla překročena.
* Adresa URL videa se dá v všech operacích zadat jako "soukromý parametr/zakódováno". Zmatení je nyní volitelné a bude fungovat pouze v případě `KEY` , že a `IV` jsou zadány jako proměnné prostředí.
* Ve výchozím nastavení je kalibrace povolena pro všechny operace. Nastavte `do_calibration: false` ho tak, aby se zakázal.
* Přidala se podpora pro automatickou rekalibraci (ve výchozím nastavení je zakázaná) prostřednictvím parametru. Podrobnosti najdete v `enable_recalibration` tématu věnovaném [operacím prostorové analýzy](./spatial-analysis-operations.md) .
* Parametry kalibrace kamery na `DETECTOR_NODE_CONFIG` . Podrobnosti najdete v tématu věnovaném [operacím prostorové analýzy](./spatial-analysis-operations.md) .


## <a name="october-2020"></a>Říjen 2020

### <a name="computer-vision-api-v31-ga"></a>Rozhraní API pro počítačové zpracování obrazu v 3.1 GA

Rozhraní API pro počítačové zpracování obrazu při obecné dostupnosti byl upgradován na verzi v 3.1.

## <a name="september-2020"></a>Září 2020

### <a name="spatial-analysis-container-preview"></a>Náhled kontejneru prostorových analýz

[Kontejner prostorové analýzy](spatial-analysis-container.md) je nyní ve verzi Preview. Funkce prostorové analýzy Počítačové zpracování obrazu umožňuje analyzovat streamování videa v reálném čase, abyste pochopili prostorové vztahy mezi lidmi a jejich pohybem prostřednictvím fyzických prostředí. Prostorová analýza je kontejner Docker, který můžete použít místně. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Čtecí rozhraní API v 3.1 Public Preview přidává rozpoznávání OCR pro japonštinu
Verze Public Preview rozhraní API pro Počítačové zpracování obrazu v 3.1 přidává tyto možnosti:
* Rozpoznávání OCR pro japonské jazyky
* Pro každý textový řádek uveďte, zda se jedná o styl rukopisu nebo tisku, spolu s hodnocením spolehlivosti (pouze jazyky latinky).
* Pro vícestránkové dokument extrahuje text pouze pro vybrané stránky nebo rozsah stránek.

* Tato verze Preview rozhraní API pro čtení podporuje angličtina, holandština, francouzština, němčina, italština, japonština, portugalština, zjednodušená čínština a španělština.

Další informace najdete v tématu [Přehled rozhraní API pro čtení](concept-recognizing-text.md) .

> [!div class="nextstepaction"]
> [Další informace o rozhraní Read API v 3.1 Public Preview 2](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Červenec 2020

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Číst rozhraní API v 3.1 Public Preview s rozpoznáváním OCR pro zjednodušenou čínštinu
Verze Public Preview rozhraní API pro čtení v Počítačové zpracování obrazu přidává podporu pro zjednodušenou čínštinu.

* Tato verze Preview rozhraní API pro čtení podporuje angličtinu, holandštinu, francouzštinu, němčinu, italštinu, portugalštinu, zjednodušenou čínštinu a španělštinu.

Další informace najdete v tématu [Přehled rozhraní API pro čtení](concept-recognizing-text.md) .

> [!div class="nextstepaction"]
> [Další informace o rozhraní Read API v 3.1 Public Preview 1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Květen 2020
Rozhraní API pro počítačové zpracování obrazu v 3.0 vstoupila všeobecná dostupnost s aktualizacemi [rozhraní API pro čtení](concept-recognizing-text.md):

* Podpora pro angličtina, holandština, francouzština, němčina, italština, portugalština a španělštinu
* Vylepšená přesnost
* Hodnocení spolehlivosti pro každé extrahované slovo
* Nový výstupní formát

## <a name="march-2020"></a>Březen 2020

* Pro všechny požadavky HTTP na tuto službu se teď vynutilo TLS 1,2. Další informace najdete v tématu [zabezpečení Azure Cognitive Services](../cognitive-services-security.md).

## <a name="january-2020"></a>Leden 2020

### <a name="read-api-30-public-preview"></a>Přečíst Public Preview rozhraní API 3,0

Nyní máte možnost použít rozhraní API pro čtení verze 3,0 k extrakci vytištěného nebo rukopisného textu z obrázků. V porovnání s předchozími verzemi 3,0 poskytuje:
* Vylepšená přesnost
* Nový výstupní formát
* Hodnocení spolehlivosti pro každé extrahované slovo
* Podpora pro španělštinu i anglické jazyky s parametrem dalšího jazyka

Pokud chcete začít používat rozhraní 3,0 API, postupujte podle pokynů k [rychlému zprovoznění pro extrakci textu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3) .

## <a name="cognitive-service-updates"></a>Aktualizace služby vnímání

[Oznámení o aktualizacích Azure pro Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)