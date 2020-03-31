---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79538828"
---
### <a name="the-data-model-of-the-schema"></a>Datový model schématu

|Pole|Popis|
|----|----|
|**Název_výstrahy**|Zobrazovaný název výstrahy|
|**Typ výstrahy**|jedinečný identifikátor výstrahy|
|**Úroveň spolehlivosti**|(Nepovinné) Úroveň spolehlivosti tohoto upozornění (vysoká/nízká)|
|**Skóre spolehlivosti**|(Nepovinné) Číselný indikátor spolehlivosti výstrahy zabezpečení|
|**Popis**|Text popisu výstrahy|
|**Displayname**|Zobrazovaný název výstrahy|
|**Endtime**|Čas ukončení dopadu výstrahy (čas poslední události přispívající k výstraze)|
|**Entity**|Seznam entit souvisejících s výstrahou. Tento seznam může obsahovat směs entit různých typů|
|**Rozšířené odkazy**|(Nepovinné) Taška pro všechny odkazy související s výstrahou. Tato taška může obsahovat směs odkazů pro různé typy|
|**ExtendedProperties**|Sáček dalších polí, která jsou relevantní pro výstrahu|
|**IsIncident**|Určuje, zda se jedná o incident nebo pravidelnou výstrahu. Incident je výstraha zabezpečení, která agreguje více výstrah do jednoho incidentu zabezpečení.|
|**ProcessingEndTime**|Časové razítko utc, ve kterém byla výstraha vytvořena|
|**ProductComponentName**|(Nepovinné) Název součásti uvnitř produktu, který vygeneroval výstrahu.|
|**Productname**|konstanta ("Azure Security Center")|
|**Providername**|Nepoužité|
|**Remediasteps**|Ruční akce položky, které mají být podnikána k nápravě ohrožení zabezpečení|
|**ResourceId**|Úplný identifikátor ohroženého zdroje|
|**Závažnost**|Závažnost výstrahy (Vysoká/Střední/Nízká/Informační)|
|**SourceComputerId**|jedinečný identifikátor GUID pro ohrožený server (pokud je výstraha generována na serveru)|
|**SourceSystem**|Nepoužité|
|**Starttime**|Čas spuštění záznamu nárazu (čas první události přispívající k výstraze)|
|**Id systému výstrah**|Jedinečný identifikátor této instance výstrahy zabezpečení|
|**TenantId**|identifikátor nadřazeného klienta služby Azure Active directory předplatného, pod kterým je naskenovaný prostředek umístěn|
|**TimeGenerated**|Časové razítko UTC, na kterém došlo k vyhodnocení (doba skenování centra zabezpečení) (totožné s DIČ)|
|**Typ**|konstanta ("Výstraha zabezpečení")|
|**Název_dodavatele**|Název dodavatele, který výstrahu poskytl (např.|
|**VendorOriginalId**|Nepoužité|
|**Skupina prostředků pracovního prostoru**|v případě, že výstraha je generována na virtuálnípočítač, server, virtuální počítač škálovací sada nebo app service instance, která hlásí do pracovního prostoru, obsahuje název skupiny prostředků pracovního prostoru, který|
|**Id předplatného pracovního prostoru**|v případě, že výstraha je generována na virtuálnípočítač, server, škálování virtuálních počítačů set nebo app service instance, která hlásí do pracovního prostoru, obsahuje, že odběr pracovního prostoruId|
|||
