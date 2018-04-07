---
title: Pochopení úroveň kompatibility pro úlohy Azure Stream Analytics
description: Zjistěte, jak nastavit úroveň kompatibility pro úlohu služby Azure Stream Analytics a hlavní změny v nejnovější úroveň kompatibility
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/03/2018
ms.openlocfilehash: 32e73918b2dd98822d42d74002b705ff730145d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Úroveň kompatibility pro úlohy Azure Stream Analytics
 
Úroveň kompatibility odkazuje na konkrétní verzi chování služby Azure Stream Analytics. Azure Stream Analytics je spravovaná služba, s aktualizacemi regulární funkce a vylepšení výkonu. Obvykle jsou automaticky provedeny aktualizace k dispozici koncovým uživatelům. Některé nové funkce, ale může zavést hlavní změny takové jako změny v chování nástroje stávající úloze, změny v procesech použití dat z těchto úloh atd. Úroveň kompatibility se používá k reprezentování hlavní změny zavedené v Stream Analytics. Hlavní změny se vždy zavedly s novou úroveň kompatibility. 

Úroveň kompatibility zajišťuje, že stávající úlohy spouštět bez jakékoli chyby. Při vytváření nové úlohy Stream Analytics, je vhodné vytvořit pomocí nejnovější úroveň kompatibility, která je pro vás k dispozici. 
 
## <a name="set-a-compatibility-level"></a>Nastavit úroveň kompatibility 

Úroveň kompatibility řídí chování za běhu úlohy stream analytics. Můžete nastavit úroveň kompatibility pro úlohu služby Stream Analytics pomocí portálu nebo pomocí [vytvořit úlohu volání rozhraní API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Azure Stream Analytics teď podporuje dva kompatibility úrovně-"1.0" a "1.1". Úroveň kompatibility je standardně nastavena na "1.0", která byla představena při obecné dostupnosti Azure Stream Analytics. Chcete-li aktualizovat výchozí hodnota, přejděte na vaše stávající úlohy služby Stream Analytics > vyberte **úroveň kompatibility** možnost v **konfigurace** části a změňte hodnotu. 

Zajistěte, aby zastavit úlohu před aktualizací úroveň kompatibility. Úroveň kompatibility nelze aktualizovat, pokud vaše úlohy je v běžícím stavu. 

![Úroveň kompatibility portálu](media\stream-analytics-compatibility-level/image1.png)

 
Když aktualizujete úroveň kompatibility, ověří kompilátoru T-SQL úlohy se syntaxí, která odpovídá úroveň kompatibility vybrané. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>Hlavní změny v nejnovější úroveň kompatibility (1.1)

V úroveň kompatibility 1.1 byly zavedeny následující hlavní změny:

* **Formát Service Bus XML**  

  * **předchozí verze:** Azure Stream Analytics používá DataContractSerializer, takže obsah zprávy zahrnuty značky XML. Příklad:
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001 {"SensorId": "1", "teploty": 64\}\u0001 

  * **aktuální verze:** obsahu zprávy obsahuje datový proud přímo s žádné další značky. Příklad:
  
   {"SensorId": "1", "teploty": 64} 
 
* **Zachování rozlišování pro názvy polí**  

  * **předchozí verze:** na malá písmena při zpracování modulu Azure Stream Analytics se změnily názvy polí. 

  * **aktuální verze:** rozlišování je trvalá pro názvy polí, když se zpracovávají modulem Azure Stream Analytics. 

  > [!NOTE] 
  > Zachování rozlišování ještě není k dispozici pro datový proud analytické úlohy, které jsou hostované pomocí prostředí hraniční. Názvy všech polí v důsledku toho se převedou na malá písmena, pokud vaše úlohy je hostována na okraj. 

* **FloatNaNDeserializationDisabled**  

  * **předchozí verze:** příkazu CREATE TABLE není filtrování událostí s NaN (není a-Number. Například Infinity, - Infinity) ve sloupci FLOAT typu, protože jsou mimo rozsah zdokumentovaných pro tato čísla.

  * **aktuální verze:** CREATE TABLE umožňuje zadat silné schématu. Modul služby Stream Analytics ověří, že data vyhovuje toto schéma. V tomto modelu příkaz můžete filtrovat události s hodnoty NaN. 

* **Zakážete automatické přetypování nahoru pro řetězce data a času ve formátu JSON.**  

  * **předchozí verze:** analyzátor JSON by automaticky přetypování nahoru řetězec zadejte hodnoty s informacemi o datum/čas/zóny na typ DateTime a pak ho převést na UTC. To mělo za následek ztrátu informace o časovém pásmu.

  * **aktuální verze:** neexistuje žádné další automaticky přetypování nahoru řetězcových hodnot s informacemi o datum/čas/zóny na typ DateTime. V důsledku toho se ukládají informace o časovém pásmu. 

## <a name="next-steps"></a>Další postup
* [Průvodce řešením potíží pro Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
* [Okna stavu prostředků analýzy datového proudu](stream-analytics-resource-health.md)