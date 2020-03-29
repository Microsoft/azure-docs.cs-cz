---
title: Poznámky k verzi – služba Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Získejte nejnovější informace o nových verzích od týmu Custom Vision.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: b9606c63ec7b53fb0b69918c21bdd9206f34c555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647564"
---
# <a name="custom-vision-service-release-notes"></a>Poznámky k verzi služby Custom Vision Service

## <a name="may-2-2019-and-may-10-2019"></a>2. května 2019 a 10.

- Vylepšení oprav chyb a back-endu

## <a name="may-23-2019"></a>23. května 2019

- Vylepšené prostředí uživatelského prostředí portálu související s předplatným azure, což usnadňuje výběr adresářů Azure.

## <a name="april-18-2019"></a>18. dubna 2019 

- Přidán export detekce objektů pro sadu Vision AI Dev Kit.
- Vylepšení ui, včetně vyhledávání v projektu.

## <a name="april-3-2019"></a>3. dubna 2019

- Zvýšený limit počtu ohraničovacích rámečků na obrázek na 200. 
- Opravy chyb, včetně podstatné aktualizace výkonu pro modely exportované do TensorFlow. 

## <a name="march-26-2019"></a>26. března 2019

- Služba Custom Vision Service zadala obecné dostupnosti v Azure!
- Přidána funkce Pokročilé školení s novým back-endem strojového učení pro lepší výkon, zejména u náročných datových sad a jemně odstupňované klasifikace. S pokročilým školením můžete určit časový rozpočet výpočetnídoby pro školení a vlastní vize experimentálně určí nejlepší nastavení školení a rozšíření. Pro rychlé iterace můžete pokračovat v používání existující rychlé školení.
- Zavedená 3.0 API. 1. října 2019 bylo oznámeno vyřazení api před 3.0. Podívejte se na rychlé starty dokumentace pro [.Net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [Uzel](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial)nebo [Go](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) příklady o tom, jak začít.
- Nahrazeno "Výchozí iterace" s Publikovat/Zrušit publikování v 3.0 API.
- Byly přidány nové cíle exportu modelu. Export Dockerfile byl upgradován na podporu ARM pro Raspberry Pi 3. Do [sady Vision AI Dev Kit.](https://visionaidevkit.com/)byla přidána podpora vývozu.
- Zvýšený limit značek na projekt na 500 pro úroveň S0. Zvýšený limit počtu bitových kopií na projekt na 100 000 pro úroveň S0.
- Odebrána doména pro dospělé. Místo toho se doporučuje obecná doména.
- Oznámené [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) pro všeobecnou dostupnost.  

## <a name="february-25-2019"></a>25. února 2019 February 25, 2019

- Bylo oznámeno ukončení projektů omezené zkušební verze (projekty, které nejsou přidruženy k prostředku Azure), protože custom vision se blíží dokončení přechodu do Azure Public Preview. března 2019 bude web CustomVision.ai podporovat jenom zobrazení projektů přidružených k prostředku Azure, jako je například bezplatný prostředek vlastní vize. října 2019 budete mít stále přístup ke stávajícím omezenézkušební projekty prostřednictvím vlastní vize API. To vám poskytne čas na aktualizaci klíčů rozhraní API pro všechny aplikace, které jste napsali pomocí vlastního vidění. října 2019 se odstraní všechny omezené zkušební projekty, které jste do Azure nepřesunuli.

## <a name="january-22-2019"></a>22. ledna 2019

- Přidána podpora pro nové oblasti Azure: Západní USA 2, Východní USA, Východní USA 2, Západní Evropa, Severní Evropa, Jihovýchodní Asie, Austrálie – východ, Střední Indie, Velká Británie – jih, Japonsko – východ a Severní střed USA. Podpora pokračuje pro jižní střední USA.

## <a name="december-12-2018"></a>12. prosince 2018December 12, 2018

- Podpora exportu pro modely detekce objektů (zavedená kompaktní doména pro detekci objektů).
- Opravena řada problémů s usnadněním přístupu pro vylepšenou podporu čtečky obrazovky a navigace pomocí klávesnice.
- Aktualizace uživatelského prostředí pro prohlížeč obrázků a vylepšené prostředí pro zjišťování objektů pro rychlejší označování.  
- Byl aktualizován základní model pro doménu detekce objektů pro lepší kvalitu detekce objektů.
- Opravy chyb.

## <a name="november-6-2018"></a>6. listopadu 2018November 6, 2018

- Přidána podpora pro doménu loga v detekci objektů.

## <a name="october-9-2018"></a>9. října 2018

- Detekce objektů zadá placený náhled. Teď můžete vytvářet projekty detekce objektů s prostředkem Azure.
- Přidána funkce "Přesunout do Azure" na web, aby bylo snazší upgradovat projekt omezené zkušební verze na propojení s Azure. projekt propojený se zdrojem (F0 nebo S0.) Najdete ji na stránce Nastavení vašeho produktu.  
- Přidán export do ONNX 1.2 pro podporu verze Windows 2018 October Update systému Windows ML.
Opravy chyb, včetně exportu ONNX se speciálními znaky.

## <a name="august-14-2018"></a>14. srpna 2018August 14, 2018

- Přidán widget "Začínáme" pro customvision.ai stránky, které uživatele provedou školením projektu.
- Další vylepšení kanálu strojového učení ve prospěch vícelabelových projektů (nová úroveň ztrát).

## <a name="june-28-2018"></a>28. června 2018

- Opravy chyb & vylepšení back-endu.
- Povolená klasifikace více tříd pro projekty, kde mají obrázky přesně jeden popisek. V předpovědi pro režim více tříd pravděpodobnosti se součet na jeden (všechny obrázky jsou zařazeny mezi zadané značky).

## <a name="june-13-2018"></a>13. června 2018

- UX refresh, zaměřený na snadné použití a přístupnost.
- Vylepšení kanálu strojového učení, které má prospěch z vícelabelových projektů s velkým počtem značek.
- Opravena chyba v exportu TensorFlow. Povolená exportovaná správa verzí modelu, takže iterace lze exportovat více než jednou.

## <a name="may-7-2018"></a>7. května 2018

- Zavedení funkce detekce objektů ve verzi Preview pro projekty využívající omezenou zkušební verzi
- Upgrade na rozhraní API verze 2.0
- Rozšíření úrovně S0 až na 250 značek a 50 000 obrázků
- Významná vylepšení back-endu kanálu strojového učení pro projekty klasifikace obrázků. Výhody těchto aktualizací budou moct využít projekty natrénované po 27. dubnu 2018.
- Přidání možnosti exportu modelů do ONNX pro použití s Windows ML
- Přidání možnosti exportu modelů do souboru Dockerfile. Díky tomu si můžete stáhnout artefakty pro sestavení vlastních kontejnerů Windows nebo Linuxu, včetně souboru Dockerfile, modelu TensorFlow a kódu služby.
- U nově vyzývaných modelů exportovaných do tensorflow v obecných (kompaktních) a oborových (kompaktních) doménách [jsou nyní střední hodnoty (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample)pro konzistenci ve všech projektech.

## <a name="march-1-2018"></a>1. března 2018

- Zadali placený náhled a zalomi lihovu na portál Azure. Projekty je teď možné připojit k prostředkům Azure s úrovní F0 (Free) nebo S0 (Standard). Zavedení projektů úrovně S0, které umožňují až 100 značek a 25 000 obrázků.
- Změny back-endu kanálu strojového učení a parametru normalizace. Zákazníci tak získají lepší kontrolu nad kompromisy mezi přesností a opětovným voláním při úpravě prahové hodnoty pravděpodobnosti. Součástí těchto změn je nastavení prahové hodnoty pravděpodobnosti na portálu CustomVision.ai na 50 %.

## <a name="december-19-2017"></a>19. prosince 2017

- Export do Androidu (TensorFlow) přidán, kromě dříve vydaného exportu do iOS (CoreML.) To umožňuje export trénovanékompaktní model spustit offline v aplikaci.
- Přidání kompaktních domén Retail (Maloobchod) a Landmark (Památka) umožňujících export modelů pro tyto domény.
- Vydání [rozhraní API pro trénování verze 1.2](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) a [rozhraní API pro predikce verze 1.1](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Aktualizovaná rozhraní API podporují export modelů a novou operaci predikce, která neukládá obrázky do složky Predictions. V rozhraní API pro trénování se také zavedly dávkové operace.
- Vylepšení uživatelského rozhraní, včetně možnosti zobrazit, která doména se použila k trénování iterace.
- Aktualizace sady [SDK pro jazyk C# a ukázky](https://github.com/Microsoft/Cognitive-CustomVision-Windows).