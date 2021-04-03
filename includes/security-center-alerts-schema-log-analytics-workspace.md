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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "79538828"
---
### <a name="the-data-model-of-the-schema"></a>Datový model schématu

|Pole|Description|
|----|----|
|**AlertName**|Zobrazované jméno výstrahy|
|**AlertType**|Jedinečný identifikátor výstrahy|
|**ConfidenceLevel**|Volitelné Úroveň spolehlivosti této výstrahy (vysoká/nízká)|
|**ConfidenceScore**|Volitelné Číselný indikátor spolehlivosti výstrahy zabezpečení|
|**Popis**|Text popisu výstrahy|
|**DisplayName**|Zobrazované jméno výstrahy|
|**EndTime**|Koncový čas dopadu výstrahy (čas poslední události přispívající k výstraze)|
|**Entity**|Seznam entit souvisejících s výstrahou. Tento seznam může obsahovat kombinaci entit různých typů.|
|**ExtendedLinks**|Volitelné Penalta pro všechny odkazy související s výstrahou. Tento kontejner může obsahovat kombinaci odkazů pro různé typy.|
|**ExtendedProperties**|Kontejner dalších polí, která jsou relevantní pro danou výstrahu.|
|**Incident**|Určuje, zda je výstraha incidentem nebo běžnou výstrahou. Incident je výstraha zabezpečení, která agreguje více výstrah do jednoho incidentu zabezpečení.|
|**ProcessingEndTime**|Časové razítko UTC, ve kterém se výstraha vytvořila|
|**ProductComponentName**|Volitelné Název součásti v rámci produktu, který výstrahu vygeneroval.|
|**NázevVýrobku**|konstanta (' Azure Security Center ')|
|**ProviderName**|nepoužívané|
|**RemediationSteps**|Položky ručních akcí, které je potřeba provést k nápravě bezpečnostní hrozby|
|**ResourceId**|Úplný identifikátor ovlivněného prostředku|
|**Závažnost**|Závažnost výstrahy (vysoká/střední/nízká/informativní)|
|**SourceComputerId**|Jedinečný identifikátor GUID pro ovlivněný Server (Pokud je výstraha vygenerována na serveru)|
|**SourceSystem**|nepoužívané|
|**Spuštění**|Počáteční čas dopadu výstrahy (čas první události přispívající k výstraze)|
|**SystemAlertId**|Jedinečný identifikátor této instance výstrahy zabezpečení|
|**TenantId**|identifikátor nadřazeného tenanta služby Azure Active Directory předplatného, pod kterým se nachází kontrolovaný prostředek|
|**TimeGenerated**|Časové razítko UTC, na kterém došlo k posouzení (Security Center čas kontroly) (shodné s DiscoveredTimeUTC)|
|**Typ**|konstanta (' SecurityAlert ')|
|**Dodavatel**|Název dodavatele, který poskytl výstrahu (např. Microsoft)|
|**VendorOriginalId**|nepoužívané|
|**WorkspaceResourceGroup**|v případě, že se výstraha vygenerovala na virtuálním počítači, serveru, sadě škálování virtuálního počítače nebo App Service instance, která hlásí do pracovního prostoru, obsahuje název skupiny prostředků v pracovním prostoru.|
|**WorkspaceSubscriptionId**|v případě, že je výstraha vygenerována na virtuálním počítači, serveru, sadě škálování virtuálního počítače nebo App Service instance, která hlásí do pracovního prostoru, obsahuje ID předplatného pracovního prostoru|
|||
