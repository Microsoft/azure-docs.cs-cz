---
title: Příjmy – Nástroj pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Přečtěte si o konceptech souvisejících s analýzou příjmů pomocí rozhraní API pro rozpoznávání formulářů – využití a omezení.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 7a14b3c93a6c545648faf28c991764e990e487b1
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88724967"
---
# <a name="receipt-concepts"></a>Koncepce pro příjem

Nástroj pro rozpoznávání AzureForm může analyzovat účtenky pomocí jednoho z předdefinovaných modelů. Rozhraní API pro příjem dat extrahuje klíčové informace z prodejních příjmů v angličtině, jako je například název obchodníka, datum transakce, součet transakcí, položky řádků a další. 

## <a name="understanding-receipts"></a>Porozumění příjemkám 

Mnohé firmy a jednotlivci se stále spoléhají na ruční extrakci dat z jejich prodejních příjmů, ať už pro obchodní výdaje, náhrady, daňové účely, rozpočtování nebo jiné účely. V těchto scénářích se často pro účely ověření vyžadují image fyzického příjmu.  

Automatické extrakce dat z těchto příjmů může být složité. Příjmy můžou být Crumpled a těžko se číst a smartphony se můžou dostat do nízké kvality. Také je možné, že se šablony a pole účtenky můžou významně lišit podle trhu, oblasti a obchodníka. Tyto problémy při extrakci dat i při detekci polí umožňují zpracování jedinečného problému.  

Rozhraní API pro příjem, které používá optické rozpoznávání znaků (OCR) a náš předem sestavený model pro příjem, umožňuje tyto scénáře zpracování příjmu. Vzhledem k tomu, že je model předem vyškolený pro naše data, můžete svoje účtenky snadno analyzovat v jednom kroku &mdash; bez nutnosti školení modelů nebo štítků.

![Ukázka účtenky](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>Co dělá rozhraní API pro příjem? 

Předem vytvořené rozhraní API pro příjem extrakce vyextrahuje obsah prodejních příjmů &mdash; na základě typu příjemky, který byste běžně získali v restauracích, maloobchodníkech nebo prodejnách.

### <a name="fields-extracted"></a>Extrahovaná pole

* Název obchodníka 
* Adresa obchodníka 
* Telefonní číslo pro obchodní telefon 
* Datum transakce 
* Čas transakce 
* Mezisoučet 
* Daň 
* Celkem 
* Tip 
* Extrakce položek řádku (například množství položky, cena za položku, název položky)

### <a name="additional-features"></a>Další funkce

Rozhraní API pro příjem také vrátí následující informace:

* Typ účtenky (například podle položky, platební karta atd.)
* Úroveň spolehlivosti pole (každé pole vrátí přidruženou hodnotu spolehlivosti)
* Nezpracovaný text OCR (OCR – textový výstup extrahovaný pro celou příjem)

## <a name="input-requirements"></a>Požadavky na vstup

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Podporovaná národní prostředí 

* **Předem sestavená příjem v 2.0** (GA) podporuje prodejní příjeme v národním prostředí en-US.
* **Předem sestavená příjem v 2.1-Preview. 1** (Public Preview) přidává další podporu následujících národních prostředí pro příjem hodnot EN: 
  * EN-AU 
  * EN-CA 
  * EN-GB 
  * EN-IN 

  > [!NOTE]
  > Vstup jazyka 
  >
  > Předem sestavená příjem verze 2.1-Preview. 1 má volitelný parametr Request pro určení národního prostředí pro příjem z dalších anglických trhů. Pro prodejní příjeme v angličtině z Austrálie (EN-AU), Kanada (EN-CA), Velká Británie (EN-GB) a Indie (EN-IN), můžete určit národní prostředí a získat tak lepší výsledky. Pokud není zadané žádné národní prostředí v rámci verze 2.1-Preview. 1, model se nastaví jako výchozí pro model EN-US.

## <a name="customer-scenarios"></a>Scénáře zákazníků  

Data extrahovaná pomocí rozhraní API pro příjem lze použít k provádění různých úloh. Následuje několik příkladů toho, co naši zákazníci dokončí s rozhraním API pro příjem. 

### <a name="business-expense-reporting"></a>Vykazování obchodních výdajů  

Pracovní výdaje se často dodávají do doby trvání útraty ručním zadáváním dat z imagí příjmů. Pomocí rozhraní API pro příjem můžete použít extrahovaná pole pro částečně automatizaci tohoto procesu a rychle analyzovat vaše účtenky.  

Vzhledem k tomu, že rozhraní API pro příjem má jednoduchý výstup JSON, můžete hodnoty extrahovaných polí použít několika způsoby. Integrujte s interními výdajovými aplikacemi a předem naplnit sestavy výdajů. Další informace o tomto scénáři najdete v článku o tom, jak Acumatica využívá rozhraní API pro příjem, aby [vykazují náklady s méně bolestivý procesy](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure).  

### <a name="auditing-and-accounting"></a>Auditování a monitorování účtů 

Výstup rozhraní API pro příjem se dá také použít k analýze velkého počtu výdajů v různých fázích vykazování výdajů a procesu refundace. Můžete zpracovávat příjmy pro ruční audit nebo rychlé schvalování.  

Výstup příjemky je také užitečný pro obecné obchodní nebo osobní použití. Pomocí rozhraní API pro příjem použijte k transformaci všech nezpracovaných dat příjmu dat nebo souborů PDF do digitálního výstupu, který je napadnutelný.

### <a name="consumer-behavior"></a>Chování uživatele 

Příjmy obsahují užitečná data, která můžete použít k analýze chování zákazníků a nákupních trendů.

