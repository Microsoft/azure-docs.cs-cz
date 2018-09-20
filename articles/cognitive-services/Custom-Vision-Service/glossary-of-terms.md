---
title: Glosář termínů – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Slovník pojmů pro službu Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 05/08/2017
ms.author: anroth
ms.openlocfilehash: ae68db3de5d1f7eaacbe355133b9b7b61f145f04
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363407"
---
# <a name="glossary-of-terms-for-custom-vision-service"></a>Slovník pojmů pro službu Custom Vision Service

Tady jsou některé termíny používané ve službě Custom Vision Service a jejich význam.

## <a name="classifier"></a>Třídění

Třídění je model, které vytváříte pomocí služby Custom Vision Service s použitím několika trénovacích obrázků. Po dokončení cvičení nové třídění obdržíte koncový vyhodnocení (HTTPS), můžete přidat do vaší aplikace. Každý třídění, které vytváříte je ve svém vlastním projektu a všechny projekty můžete zobrazit po přihlášení.

## <a name="domain"></a>Domain (Doména)

Když vytvoříte projekt, vyberte "domény" pro tento projekt. Doména optimalizuje třídění pro konkrétní typ objektu vašich imagí. Například pokud váš scénář je ke klasifikaci mezi imagí apple výsečový srovnání imagí mrkvový koláček, vyberte doménu "Potravin". Pokud si nejste jistí, ve které doméně zvolit, pak vyberte doménu "Generic".

- **Doména potravin.** Optimalizovaná pro si pochutnává, které byste měli v nabídka restaurace. Nebyl optimalizován pro jednotlivá ovoce nebo zeleniny uznání. Pokud chcete klasifikovat fotografie jednotlivá ovoce nebo zeleniny, použijte obecné domény pro tento účel.
- **Památek domény.** Optimalizovaná pro rozpoznatelných památek, přirozené a umělé. Tato doména funguje nejlépe, když památek je jasně viditelný v fotografie, i v případě, že památek mírně brání skupinu osob představující před tímto prvkem.
- **Doména maloobchodního prodeje.** Optimalizovaná pro klasifikaci obrázků v nákupní katalogu nebo nákupního webu. Pokud chcete vysokou přesnost při klasifikaci šatů, pants, košile atd., pak použijte domény maloobchodního prodeje.
- **Domény pro dospělé.** Optimalizované a lépe tak definovat mezi obsah pro dospělé a obsahu jiných dospělá osoba. Například pokud chcete blokovat obrázky ve koupání barvy, tuto doménu vám umožní vytvářet vlastní třídění to udělat.
- **Obecné domény.** Vhodná pro širokou škálu obrázek úlohy klasifikace.

Modely generovaných **compact domén** je možné exportovat pomocí funkce exportu iterace. Jsou optimalizované pro omezení v reálném čase klasifikace na mobilních zařízeních. Třídění vytvořených pomocí compact domény může být méně přesné standardní domény s velkou trénovací data. Výměnou za to je, že jsou dostatečně malá, má být spuštěn místně v téměř reálném čase. 

## <a name="training-image"></a>Obrázek školení

K vytvoření klasifikátoru velmi přesné, musí službě Custom Vision Service několik trénovacích obrázků. Obrázek školení je fotografie image chcete službu Custom Vision Service ke klasifikaci. Například ke klasifikaci oranges, je třeba nahrát několik imagí oranges službu Custom Vision Service, aby byla povolena pro vytvoření klasifikátor, který dokáže rozpoznat oranges. Doporučujeme aspoň 30 obrázků na značky.

## <a name="iteration"></a>Iterace

Každý čas trénování nebo znovu klasifikátor trénovat, vytvoříte novou iteraci modelu. Budeme udržovat několik posledních iteracích, aby bylo možné porovnat průběh v čase. Můžete odstranit všechny iterace, které jste už pro vás užitečné. Mějte na paměti, že odstranění iterace je trvalá a odstraní se zároveň všechny obrázky nebo změny, které byly jedinečné pro danou iteraci. 

## <a name="workspace"></a>Pracovní prostor

Pracovní prostor obsahuje všechny trénovacích obrázků a odráží všechny změny od poslední iterace, jako přidaly nebo odebraly bitové kopie. Klasifikátor Trénovat, vytvoříte novou iteraci klasifikátoru, s použitím imagí, které jsou k dispozici ve vašem pracovním prostoru.

## <a name="tags"></a>Značky

Použití značek k označení objekty v trénovacích obrázků. Pokud vytváříte třídění k identifikaci psi a poníci, vložíte na imagích, které obsahují psy, značky "malwaru pony" na imagích, které obsahují poníci a zároveň "pes" a "malwaru pony" značku na imagích, které obsahují pes i malwaru pony značku "pes".

## <a name="evaluation"></a>Vyhodnocení

Po klasifikátoru mají školení, můžete pomocí koncový bod https pro automaticky generované odesílat žádné image pro vyhodnocení. Klasifikátor vrátí sadu předpokládané značky v pořadí výrazné zvýšení sebedůvěry.

## <a name="predictions"></a>Predikce

Jak klasifikátoru přijímá nových imagí ke klasifikaci, ukládá Image za vás. Tyto Image můžete zlepšit přesnost klasifikátoru správně označování nepředpovězené bitové kopie. Tyto nové Image pak můžete klasifikátor trénovat, znovu.

## <a name="precision"></a>Přesnost

Při klasifikaci obrázku, jak pravděpodobné je klasifikátor a klasifikaci obrázku správně? Mimo všechny Image, které využívají k tréninku třídění (PSI a poníci) jaké procento modelu jsou k dispozici správné? 99 správné značky mimo 100 imagí poskytuje 99 % přesností.

## <a name="recall"></a>Svolat

Mimo všechny bitové kopie, které by byly klasifikovány správně kolik klasifikátoru správně identifikovat? Účinnost 100 % znamená, pokud byly nějaké 38 pes imagí v imagích, které využívají k tréninku třídění, 38 PSI nebyly nalezeny třídění.

## <a name="settings"></a>Nastavení

Existují dva typy nastavení, nastavení na úrovni projektu a nastavení na úrovni uživatele.

- Nastavení na úrovni projektu: 
  
  Použít nastavení na úrovni projektu na projekt nebo třídění. Patří mezi ně:

   - Projekt domény
   - Název projektu
   - Popis projektu
   - Použití:
      - Počet trénovacích obrázků
      - Počet značek vytvořili
      - Počet opakování je vyšší vytvořili

- Individuální nastavení: 
   - Klíče předplatného: jeden pro školení, jeden pro vyhodnocení/předpovědi.
   - Použití:
      - Počet projekty vytvořené
      - Počet provedených volání rozhraní API hodnocení/předpovědi.
