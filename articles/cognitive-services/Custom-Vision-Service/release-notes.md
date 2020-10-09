---
title: Poznámky k verzi – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Získejte nejnovější informace o nových vydáních týmu Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: pafarley
ms.openlocfilehash: f7259fe486a473f994d7795de5926b86717bb1b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85391702"
---
# <a name="custom-vision-service-release-notes"></a>Poznámky k verzi Custom Vision Service

## <a name="may-2-2019-and-may-10-2019"></a>2. května 2019 a 10. května 2019

- Vylepšení oprav a back-endu

## <a name="may-23-2019"></a>23. května 2019

- Vylepšené uživatelské prostředí portálu související s předplatnými Azure, které usnadňuje výběr adresářů Azure.

## <a name="april-18-2019"></a>18. dubna 2019 

- Přidání exportu detekce objektu pro sadu Vision AI dev Kit
- Vylepšení uživatelského rozhraní, včetně vyhledávání projektů.

## <a name="april-3-2019"></a>3. dubna 2019

- Zvýšený limit počtu ohraničujících polí na obrázek na 200. 
- Oprav, včetně podstatné aktualizace výkonu pro modely exportované do TensorFlow. 

## <a name="march-26-2019"></a>26. března 2019

- Custom Vision Service do Azure vstoupila Obecná dostupnost!
- Přidali jsme pokročilou výuku do nového back-endu Machine Learning pro zlepšení výkonu, zejména u náročných datových sad a jemně odstupňované klasifikace. S pokročilým školením můžete určit rozpočet výpočetního času pro školení a Custom Vision bude experimentálním postupem Upřesnit i to, co nejlépe proškolení a rozšíření nastavení. Pro rychlé iterace můžete dál používat stávající rychlé školení.
- Představena rozhraní API 3,0. Oznámili jsme vyřazení pre-3,0 rozhraní API od 1. října 2019. Příklady toho, jak začít, najdete v dokumentaci k rychlým startům pro [.NET](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [Node](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial)nebo na [webu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) .
- V rozhraních API 3,0 se nahradila výchozí iterace s publikováním/zrušením publikování.
- Přidaly se nové cíle exportu modelu. Export souboru Dockerfile byl upgradován tak, aby podporoval ARM pro malin 3. Do sady [Vision AI dev Kit](https://visionaidevkit.com/)se přidala Podpora exportu.
- Zvýšený limit značek na projekt na 500 pro S0 vrstvu. Vyšší limit imagí na projekt až 100 000 pro S0 vrstvu.
- Doména pro dospělé se odebrala. Místo toho se doporučuje Obecná doména.
- Ohlášené [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) pro obecnou dostupnost.  

## <a name="february-25-2019"></a>25. února 2019

- Oznámili jsme konec omezených zkušebních projektů (projekty, které nejsou přidružené k prostředku Azure), jak Custom Vision se blíží dokončení jeho přesunutí do Azure Public Preview. Od 25. března 2019 podporuje Web CustomVision.ai jenom zobrazení projektů přidružených k prostředku Azure, jako je třeba bezplatný Custom Vision prostředek. Do 1. října 2019 budete mít stále přístup k vašim existujícím testovacím projektům prostřednictvím rozhraní API Custom Vision. Tím získáte čas k aktualizaci klíčů rozhraní API pro všechny aplikace, které jste napsali pomocí Custom Vision. Od 1. října 2019 se odstraní všechny omezené zkušební projekty, které jste nepřesunuli do Azure.

## <a name="january-22-2019"></a>22. ledna 2019

- Přidání podpory pro nové oblasti Azure: Západní USA 2, Východní USA, Východní USA 2, Západní Evropa, Severní Evropa, jihovýchodní Asie, Austrálie – východ, Střed Indie, Velká Británie – jih, Japonsko – východ a Střed USA – sever. Podpora bude pro Střed USA – jih pokračovat.

## <a name="december-12-2018"></a>12. prosince 2018

- Podpora exportu pro modely detekce objektů (zavádí se kompaktní doména detekce objektů).
- Opravili jsme několik problémů s přístupností pro vylepšenou čtečku obrazovky a podporu pro navigaci klávesnicí.
- Aktualizace uživatelského rozhraní pro prohlížeč obrázků a vylepšené prostředí značek pro rozpoznávání objektů pro rychlejší označování.  
- Byl aktualizován základní model pro doménu detekce objektu pro lepší detekci objektu Quality.
- Opravy chyb.

## <a name="november-6-2018"></a>6. listopadu 2018

- Přidání podpory pro doménu loga v rozpoznávání objektů

## <a name="october-9-2018"></a>9. října 2018

- Rozpoznávání objektů vstupuje do placené verze Preview. Nyní můžete vytvořit projekty detekce objektů pomocí prostředku Azure.
- Přidali jsme funkci přesunout do Azure na web, která usnadňuje upgrade omezeného zkušebního projektu pro připojení k Azure. projekt propojený s prostředky (F0 nebo S0.) Najdete ho na stránce nastavení pro svůj produkt.  
- Přidání exportu do ONNX 1,2 pro podporu Windows 2018 říjen aktualizuje verzi Windows ML.
Opravy chyb, včetně exportu ONNX se speciálními znaky.

## <a name="august-14-2018"></a>14. srpna 2018

- Přidání widgetu "Začínáme" na web customvision.ai, který uživatelům pomůže prostřednictvím školení k projektu.
- Další vylepšení kanálu strojového učení pro zvýhodnění projektů s více štítky (Nová vrstva ztrát).

## <a name="june-28-2018"></a>28. června 2018

- Opravy chyb & vylepšení back-endu.
- Povolenou klasifikaci více tříd pro projekty, ve kterých obrázky obsahují přesně jeden popisek. V předpovědi pro režim více tříd se pravděpodobnosti sčítají na jeden (všechny obrázky jsou klasifikovány mezi vašimi specifikovanými značkami).

## <a name="june-13-2018"></a>13. června 2018

- Aktualizace uživatelského rozhraní zaměřené na snadné použití a přístupnost.
- Vylepšení kanálu strojového učení pro zvýhodnění projektů s více štítky a velkým počtem značek.
- Opravená chyba při exportu TensorFlow Povoleno exportování verzí modelů, takže iterace lze exportovat více než jednou.

## <a name="may-7-2018"></a>7. května 2018

- Zavedení funkce detekce objektů ve verzi Preview pro projekty využívající omezenou zkušební verzi
- Upgrade na rozhraní API verze 2.0
- Rozšíření úrovně S0 až na 250 značek a 50 000 obrázků
- Významná vylepšení back-endu kanálu strojového učení pro projekty klasifikace obrázků. Výhody těchto aktualizací budou moct využít projekty natrénované po 27. dubnu 2018.
- Přidání možnosti exportu modelů do ONNX pro použití s Windows ML
- Přidání možnosti exportu modelů do souboru Dockerfile. Díky tomu si můžete stáhnout artefakty pro sestavení vlastních kontejnerů Windows nebo Linuxu, včetně souboru Dockerfile, modelu TensorFlow a kódu služby.
- U nově vyškolených modelů vyexportovaných do TensorFlow v obecných (kompaktních) a orientačních (kompaktních) doménách jsou hodnoty pro konzistenci ve všech projektech [nyní (0, 0, 0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample).

## <a name="march-1-2018"></a>1. března 2018

- Do Azure Portal se zadala placená verze Preview a připojená. Projekty je teď možné připojit k prostředkům Azure s úrovní F0 (Free) nebo S0 (Standard). Zavedení projektů úrovně S0, které umožňují až 100 značek a 25 000 obrázků.
- Změny back-endu kanálu strojového učení a parametru normalizace. Zákazníci tak získají lepší kontrolu nad kompromisy mezi přesností a opětovným voláním při úpravě prahové hodnoty pravděpodobnosti. Součástí těchto změn je nastavení prahové hodnoty pravděpodobnosti na portálu CustomVision.ai na 50 %.

## <a name="december-19-2017"></a>19. prosince 2017

- Exportovat do Androidu (TensorFlow) přidané kromě dříve vydaných exportů do iOS (CoreML.) To umožňuje exportovat vyškolený kompaktní model, který se má spustit offline v aplikaci.
- Přidání kompaktních domén Retail (Maloobchod) a Landmark (Památka) umožňujících export modelů pro tyto domény.
- Vydání [rozhraní API pro trénování verze 1.2](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) a [rozhraní API pro predikce verze 1.1](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Aktualizovaná rozhraní API podporují export modelů a novou operaci predikce, která neukládá obrázky do složky Predictions. V rozhraní API pro trénování se také zavedly dávkové operace.
- Vylepšení uživatelského rozhraní, včetně možnosti zobrazit, která doména se použila k trénování iterace.
- Aktualizace sady [SDK pro jazyk C# a ukázky](https://github.com/Microsoft/Cognitive-CustomVision-Windows).