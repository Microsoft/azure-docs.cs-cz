---
title: Zpráva k vydání verze – Custom Vision Service
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: 4b4e449b9260debbe90523141cacc4ed76219490
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787952"
---
# <a name="custom-vision-service-release-notes"></a>Zpráva k vydání verze služby Custom Vision

## <a name="may-2-2019-and-may-10-2019"></a>2. května 2019 a 10. května 2019

- Vylepšení opravy chyb a back-endu

## <a name="may-23-2019"></a>23. května 2019

- Vylepšené prostředí uživatelského rozhraní portálu související s předplatnými Azure, což usnadňuje výběr adresářům Azure.

## <a name="april-18-2019"></a>18. dubna 2019 

- Přidání objektu zjišťování export pro Dev Kit AI pro zpracování obrazu.
- Vylepšení uživatelského rozhraní, včetně vyhledávání projektu.

## <a name="april-3-2019"></a>3. dubna 2019

- Zvýšení limitu počtu ohraničující polí na bitovou kopii do 200. 
- Opravy chyb, včetně aktualizace značného výkonu pro modely exportovat do TensorFlow. 

## <a name="march-26-2019"></a>26. března 2019

- Služba Custom Vision Service přešla obecné dostupnosti v Azure!
- Školení pokročilé funkce s novou strojového učení back-end za účelem vylepšení výkonu, zejména u náročné datové sady a podrobných klasifikace. Pokročilé školení, můžete určit, že výpočetní čas rozpočet pro trénování a vlastní zpracování obrazu experimentálně určují nastavení nejlepší školení a rozšíření. Pro rychlé iterace můžete dál používat existující rychlé školení.
- Zavedená 3.0 rozhraní API. Oznámili nadcházejících zastarání rozhraní API 3.0 před 1. října 2019. Najdete v dokumentaci rychlých startů pro [.Net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [uzel](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial), nebo [Přejít](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) příklady o tom, jak začít pracovat.
- Nahradí "Výchozí iterace" s publikování a zrušení publikování ve 3.0 rozhraní API.
- Přidali jsme nový model export cíle. Soubor Dockerfile export byl upgradován na podporu ARM pro Raspberry Pi 3. Byla přidána podpora export do [Dev Kit AI pro zpracování obrazu.](https://visionaidevkit.com/).
- Vyšší omezení značek na projekt na 500 pro úroveň S0. Vyšší omezení obrázků na projekt na 100 000 pro úroveň S0.
- Odebrat domény pro dospělé. Obecné domény, doporučujeme místo toho.
- Oznámili [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) pro obecnou dostupnost.  

## <a name="february-25-2019"></a>25. února 2019

- Oznámili konec omezenou zkušební verzí projekty (projekty není přidružena k prostředku Azure), protože Custom Vision přiblíží dokončení jeho přesun do Azure ve verzi public preview. Od 25. března 2019 CustomVision.ai lokality se podporují jenom zobrazení projektů v souvislosti s prostředky Azure, jako je bezplatný zdroj Custom Vision. Prostřednictvím 1. října 2019 stále budete mít přístup k existující projekty omezenou zkušební prostřednictvím vlastní rozhraní API pro zpracování obrazu. To vám poskytne vám čas k aktualizaci klíčů rozhraní API pro všechny aplikace, který jste napsali s Custom Vision. Po 1 říjnu 2019 se odstraní všechny omezenou zkušební projekty, které nebyly přesunuta do Azure.

## <a name="january-22-2019"></a>22. ledna 2019

- Byla přidána podpora pro nové oblasti Azure: Západní USA 2, USA – východ, USA – východ 2, západní Evropa, Severní Evropa, jihovýchodní Asie, Austrálie – východ, střed Indie, Velká Británie – Jih, Japonsko – východ a USA (střed) – sever. Podpora pokračuje, střed USA – jih.

## <a name="december-12-2018"></a>12. prosince 2018

- Export podporu modely detekce objektů (zavedení objektu zjišťování Compact domény).
- Opravili jsme několik problémů s přístupností pro lepší obrazovky a navigace podpora klávesnice.
- Aktualizace uživatelského rozhraní pro prohlížeč obrázků a vylepšené detekce označování prostředí pro rychlejší označování.  
- Aktualizované základní model pro doménu objektu zjišťování pro zjišťování objektů lepší kvality.
- Opravy chyb.

## <a name="november-6-2018"></a>6. listopadu 2018

- Přidání podpory pro doménu Logo při zjišťování objektu.

## <a name="october-9-2018"></a>9. října 2018

- Detekce objektů zadá placená verze preview. Nyní můžete vytvořit objekt detekce projekty k prostředku Azure.
- "Přesun do Azure" funkce na webu, aby bylo snazší upgradovat projekt omezenou zkušební verzí propojit s Azure. Odkazovaný projekt prostředků (F0 nebo S0.) To můžete najít na stránce nastavení pro svůj produkt.  
- Přidání export ONNX 1.2, podporovat Windows Update. října 2018 verzi Windows ML.
Opravy chyb, včetně pro export ONNX speciální znaky.

## <a name="august-14-2018"></a>14. srpna 2018

- Přidání "Začínáme" widget, který se customvision.ai webu provedou uživatele školení projektu.
- Další vylepšení kanálu strojového učení prospěch multilabel projektů (Nová vrstva ztráty).

## <a name="june-28-2018"></a>28. června 2018

- Opravy chyb a vylepšení back-endu.
- Povolit klasifikace víc tříd pro projekty, kde mají přesně jeden popisek Image. V předpovědi pro režim s více třídami, bude do jednoho součet pravděpodobnosti (všechny bitové kopie jsou klasifikovány mezi zadaným značkám).

## <a name="june-13-2018"></a>13. června 2018

- Aktualizace uživatelského rozhraní, zaměřuje na snadnost použití a přístupnost.
- Vylepšení kanálu strojového učení prospěch multilabel projektů s velkým množstvím značek.
- Oprava chyby v TensorFlow exportu. Povolit exportované model správy verzí, tak iterace je možné exportovat více než jednou.

## <a name="may-7-2018"></a>7. května 2018

- Zavedení funkce detekce objektů ve verzi Preview pro projekty využívající omezenou zkušební verzi
- Upgrade na rozhraní API verze 2.0
- Rozšíření úrovně S0 až na 250 značek a 50 000 obrázků
- Významná vylepšení back-endu kanálu strojového učení pro projekty klasifikace obrázků. Výhody těchto aktualizací budou moct využít projekty natrénované po 27. dubnu 2018.
- Přidání možnosti exportu modelů do ONNX pro použití s Windows ML
- Přidání možnosti exportu modelů do souboru Dockerfile. Díky tomu si můžete stáhnout artefakty pro sestavení vlastních kontejnerů Windows nebo Linuxu, včetně souboru Dockerfile, modelu TensorFlow a kódu služby.
- Pro nově vycvičené modely exportovat do TensorFlow obecně (Compact) a památek (Compact) domény [znamenat hodnoty jsou nyní (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), pro zajištění konzistence ve všech projektech.

## <a name="march-1-2018"></a>1. března 2018

- Zadané placená verze preview a připojit na webu Azure portal. Projekty je teď možné připojit k prostředkům Azure s úrovní F0 (Free) nebo S0 (Standard). Zavedení projektů úrovně S0, které umožňují až 100 značek a 25 000 obrázků.
- Změny back-endu kanálu strojového učení a parametru normalizace. Zákazníci tak získají lepší kontrolu nad kompromisy mezi přesností a opětovným voláním při úpravě prahové hodnoty pravděpodobnosti. Součástí těchto změn je nastavení prahové hodnoty pravděpodobnosti na portálu CustomVision.ai na 50 %.

## <a name="december-19-2017"></a>19. prosince 2017

- Přidání možnosti exportu na Android (TensorFlow) navíc k dříve vydané možnosti exportu na iOS (CoreML). To umožňuje exportovat natrénovaný kompaktní model pro offline spouštění v aplikaci.
- Přidání kompaktních domén Retail (Maloobchod) a Landmark (Památka) umožňujících export modelů pro tyto domény.
- Vydání [rozhraní API pro trénování verze 1.2](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) a [rozhraní API pro predikce verze 1.1](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Aktualizovaná rozhraní API podporují export modelů a novou operaci predikce, která neukládá obrázky do složky Predictions. V rozhraní API pro trénování se také zavedly dávkové operace.
- Vylepšení uživatelského rozhraní, včetně možnosti zobrazit, která doména se použila k trénování iterace.
- Aktualizace sady [SDK pro jazyk C# a ukázky](https://github.com/Microsoft/Cognitive-CustomVision-Windows).