---
title: Vysvětlení úroveň kompatibility pro úlohy Azure Stream Analytics
description: Zjistěte, jak nastavit úroveň kompatibility pro úlohy Azure Stream Analytics a hlavní změny v nejnovější úroveň kompatibility
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/03/2018
ms.openlocfilehash: 136b21f026d208c09b50dfa8601de692e518774e
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43699106"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Úroveň kompatibility pro úlohy Azure Stream Analytics
 
Úroveň kompatibility odkazuje na chování specifické pro verzi služby Azure Stream Analytics. Azure Stream Analytics je spravovaná služba, se aktualizace regulární funkcí a vylepšení výkonu. Obvykle jsou automaticky provedeny aktualizace k dispozici koncovým uživatelům. Některé nové funkce, může však zavést zásadní změny těchto jako změnu v chování existující úlohy, změňte procesy využívající data z těchto úloh atd. Úroveň kompatibility se používá k reprezentování zásadní změny zavedené ve službě Stream Analytics. Důležité změny jsou vždy zavedená s novou úrovní kompatibility. 

Úroveň kompatibility zajišťuje, že stávající úlohy spustit bez jakékoli neúspěchy. Při vytváření nové úlohy Stream Analytics, je osvědčeným postupem je vytvoření s použitím nejnovější úroveň kompatibility, který je k dispozici. 
 
## <a name="set-a-compatibility-level"></a>Nastavit úroveň kompatibility 

Úroveň kompatibility řídí chování za běhu úlohy stream analytics. Můžete nastavit úroveň kompatibility pro úlohu Stream Analytics pomocí portálu nebo pomocí [vytvořit úlohu volání rozhraní REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Azure Stream Analytics v současné době podporuje dva kompatibility úrovně – "1.0" a "1.1". Ve výchozím nastavení je úroveň kompatibility nastavena na "1.0", která byla zavedena v období všeobecné dostupnosti služby Azure Stream Analytics. Pokud chcete aktualizovat výchozí hodnotu, přejděte do vaší existující úlohy Stream Analytics > vyberte **úroveň kompatibility** možnost **konfigurovat** části a změňte hodnotu. 

Ujistěte se, že zastavení úlohy před aktualizací úroveň kompatibility. Úroveň kompatibility nelze aktualizovat, pokud vaše úloha není ve spuštěném stavu. 

![Úroveň kompatibility portálu](media\stream-analytics-compatibility-level/image1.png)

 
Při aktualizaci úrovní kompatibility T-SQL compiler ověří úlohy pomocí syntaxe, která odpovídá úroveň kompatibility vybrané. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>Hlavní změny v nejnovější úroveň kompatibility (1.1)

V úrovni kompatibility 1.1 byly zavedeny následující hlavní změny:

* **Formát XML služby Service Bus**  

  * **předchozí verze:** Azure Stream Analytics používá DataContractSerializer, takže obsah zprávy zahrnout tagů XML. Příklad:
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId": "1", "teploty": 64\}\u0001 

  * **aktuální verze:** obsah zprávy obsahuje datovým proudem přímo s žádné další značky. Příklad:
  
   {"SensorId": "1", "teploty": 64} 
 
* **Zachování rozlišování názvů polí**  

  * **předchozí verze:** názvy polí byly změněny na malá písmena, když modul Azure Stream Analytics zpracovává. 

  * **aktuální verze:** rozlišování se ukládají pro názvy polí při jejich zpracování pomocí modulu Azure Stream Analytics. 

  > [!NOTE] 
  > Zachování rozlišování ještě není k dispozici pro Stream analytických úloh, které jsou hostované pomocí hraničním prostředí. Názvy všech polí v důsledku toho jsou převedeny na malá písmena, pokud vaše úloha je hostovaná na hraničních zařízeních. 

* **FloatNaNDeserializationDisabled**  

  * **předchozí verze:** příkazu CREATE TABLE není filtrovat události s NaN (Not a Number. Například nekonečno, - nekonečno) ve sloupci PLOVOUCÍ typ, protože jsou mimo rozsah zdokumentovaných pro tato čísla.

  * **aktuální verze:** CREATE TABLE umožňuje zadat silné schématu. Modul Stream Analytics ověří, že toto schéma odpovídá data. V tomto modelu můžete příkaz Filtrovat události s hodnoty NaN. 

* **Zakážete automatické přetypování nahoru pro řetězce data a času ve formátu JSON.**  

  * **předchozí verze:** by JSON analyzátor upcast automaticky řetězec hodnoty Datum/čas/pásmo informace, které data a času typu a převeďte jej na UTC. To bylo způsobeno dojde ke ztrátě informace o časovém pásmu.

  * **aktuální verze:** neexistuje žádné další automaticky upcast řetězcových hodnot s informacemi o datum/čas/pásmo na typ DateTime. V důsledku toho se ukládají informace o časovém pásmu. 

## <a name="next-steps"></a>Další postup
* [Průvodce odstraňováním potíží pro Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
* [Okno Stream Analytics Resource health](stream-analytics-resource-health.md)