---
title: Slovník pojmů pro vlastní sdělení služba – kognitivní služby Azure | Microsoft Docs
description: Slovník pojmů pro službu vlastní vize.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/08/2017
ms.author: anroth
ms.openlocfilehash: 871617ce3c1c5a84df746c0c7d87c113b3a6f354
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342866"
---
# <a name="glossary-of-terms-for-custom-vision-service"></a>Slovník pojmů pro službu vize vlastní

Níže jsou uvedeny některé termínů používaných v vlastní vize služby a jejich význam.

## <a name="classifier"></a>Třídění

Třídění je model, který vytvoříte pomocí vlastní vize služby, pomocí bitové kopie několik školení. Po dokončení cvičení nové třídění obdržíte koncový vyhodnocení (HTTPS), můžete přidat do vaší aplikace. Je každý třídění, které vytvoříte ve vlastní projektu a lze je zobrazit všechny projekty po přihlášení.

## <a name="domain"></a>Doména

Pokud vytvoříte projekt, vyberte "domény" pro tento projekt. Domény optimalizuje třídění pro konkrétní typ objektu ve vaší bitové kopie. Například pokud váš scénář klasifikovat mezi bitové kopie apple výsečový a bitové kopie mrkvový koláč, pak vyberte "Jídlo" doménu. Pokud si nejste jistí, ve které doméně zvolit, pak vyberte "Obecné" domény.

- **Jídlo domény.** Optimalizovaná pro misky, zobrazí se v nabídce restaurace. Ho nebyl optimalizované pro rozpozná jednotlivé výsledek nebo zeleniny. Pokud chcete klasifikovat fotografií jednotlivé plody nebo zeleniny, použijte obecné doménu k tomuto účelu.
- **Významné domény.** Optimalizovaná pro rozpoznatelném zajímavá přirozené a umělé. Tato doména funguje nejlíp, když je významné jasně viditelný v fotografie, i když významné mírně bránit ve skupině uživatelů, které představují úrovních před ním.
- **Prodejní domény.** Optimalizovaná pro klasifikace obrázků v nákupní katalogu nebo nákupní web. Pokud chcete vysokou přesnost při klasifikaci šatů, kalhoty, košile atd., potom použijte prodejní domény.
- **Pro dospělé domény.** Optimalizovaná tak, aby lépe definovat mezi obsah pro dospělé a nezletilých obsah. Například pokud chcete blokovat bitové kopie osoby koupání barev, tuto doménu můžete vytvořit vlastní třídění k tomu.
- **Obecné domény.** Dobře vhodné pro širokou řadu úloh, bitovou kopii klasifikace.

Modely generované **compact domén** je možné exportovat pomocí funkce exportu iterací. Jsou optimalizované pro omezení v reálném čase klasifikace na mobilních zařízeních. Třídění vytvořené s nástroji compact domény může být mírně méně přesný standardní domény se stejnou velikostí Cvičná data. Kompromis je, že jsou dostatečně malé, aby spustit místně v téměř v reálném čase. 

## <a name="training-image"></a>Školení image

Vytvoření klasifikátoru vysokou přesnost, vlastní vize služby musí více bitových kopií školení. Bitovou kopii školení je fotografie bitové kopie má vlastní vize služba klasifikovat. Ke klasifikaci pomeranče, musíte například odeslání více bitových kopií pomerančů službě vlastní vize lze umožnit službě vytvoření klasifikátoru, který dokáže rozpoznat pomeranče. Doporučujeme, abyste nejméně 30 bitové kopie na značky.

## <a name="iteration"></a>Iterace

Každý času jste Train nebo znovu cvičení vaší třídění, vytvořte novou iteraci modelu. Budeme udržovat několik posledních iterací můžete porovnat průběh v čase. Můžete odstranit všechny iterace, které už pro vás užitečné. Mějte na paměti, že odstranění iterace je trvalá a také odstranit všechny bitové kopie nebo změny, které byly pro tento iterace jedinečné. 

## <a name="workspace"></a>Pracovní prostor

Pracovní prostor obsahuje všechny bitové kopie školení a odráží všechny změny z vaší poslední iterace například přidaly nebo odebraly bitové kopie. K výuce vaší třídění vytvoříte novou iteraci třídění, pomocí bitové kopie v pracovním prostoru.

## <a name="tags"></a>Značky

Použití značek pro označení objektů v školení obrázků. Pokud vytváříte třídění k identifikaci psi a poníci, přepnutím do bitové kopie, které obsahují psi, značku "budou hrát" image, které obsahují poníci a jak "pes" a "budou hrát" značku na bitové kopie, které obsahují pes a budou hrát značku "pes".

## <a name="evaluation"></a>Vyhodnocení

Jakmile máte Trénink vaší třídění, můžete odeslat žádný obrázek pro vyhodnocení pomocí koncový bod https automaticky generovaný. Vaše třídění vrací sadu předpokládaných značky, v pořadí podle spolehlivosti.

## <a name="predictions"></a>Predikce

Jak vaše třídění přijme nové bitové kopie ke klasifikaci, ukládá bitové kopie pro vás. Tyto Image můžete zlepšit přesnost vaší třídění správně označování nemá předpokládaných bitové kopie. Tyto nové bitové kopie pak můžete znovu cvičení vaší třídění.

## <a name="precision"></a>Přesnost

Při klasifikaci bitovou kopii, jak pravděpodobně je vaše třídění správně klasifikovat bitovou kopii? Mimo všechny Image použity při cvičení třídění (PSI a poníci) jaké procento modelu získat správné? 99 správné značky mimo 100 bitových kopií poskytuje přesností 99 %.

## <a name="recall"></a>Svolat

Mimo všechny bitové kopie, které by byly klasifikovány správně kolik vaše třídění správně identifikovat? Odvolání 100 % znamená, že pokud byly nějaké 38 PSA obrázky v obrázcích použity při cvičení třídění, 38 PSI nebyly nalezeny třídění.

## <a name="settings"></a>Nastavení

Existují dva typy nastavení, nastavení projektu a individuální nastavení.

- Nastavení projektu: 
  
  Nastavení projektu platí pro projekt nebo třídění. Patří mezi ně:

   - Projekt domény
   - Název projektu
   - Popis projektu
   - Použití:
      - Počet bitových kopií, školení
      - Počet značek vytvořen
      - Počet opakování je vytvořen

- Individuální nastavení: 
   - Předplatné klíče: jeden pro školení, jeden pro vyhodnocení nebo předpovědi.
   - Použití:
      - Počet projektů vytvořených
      - Počet provedených volání API vyhodnocení nebo předpovědi.
