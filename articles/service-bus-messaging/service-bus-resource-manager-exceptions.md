---
title: Výjimky Azure Service Bus Správce prostředků | Microsoft Docs
description: Seznam výjimek Service Bus Azure Resource Manager a navrhovaných akcí
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: darosa
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2019
ms.author: aschhab
ms.openlocfilehash: e666503b9e8888e7d61445639fe0f3adeeffe55f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329302"
---
# <a name="service-bus-resource-manager-exceptions"></a>Výjimky Service Bus Správce prostředků

V tomto článku jsou vygenerovány výjimky, které jsou vygenerovány při interakci s Azure Service Bus Azure Resource Manager prostřednictvím šablon nebo přímých volání.

> [!IMPORTANT]
> Tento dokument se často aktualizuje. Vraťte se prosím na aktualizace.

Níže jsou uvedeny různé výjimky a chyby, které jsou provedené prostřednictvím Azure Resource Manager.

## <a name="error-bad-request"></a>Chyba: Chybný požadavek

Chybný požadavek znamená, že žádost přijatá Správce prostředků neprošla ověřením.

| Kód chyby | Chybový kód | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Chybný požadavek | 40000 | Subcode = 40 000. Vlastnost *' name '* nemůže být nastavena při vytváření fronty, protože obor názvů *' název oboru názvů '* používá vrstvu ' Basic '. Tato operace je podporovaná jenom v úrovni Standard nebo Premium. | Na úrovni Basic Azure Service Bus nelze nastavit ani aktualizovat tyto vlastnosti – <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>requiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Témata </li> </ul> | Pokud chcete tuto funkci využít, zvažte upgrade z úrovně Basic na úroveň Standard nebo Premium. |
| Chybný požadavek | 40000 | Subcode = 40 000. Hodnotu vlastnosti ' requiresDuplicateDetection ' existující fronty nelze změnit. | Zjišťování duplicitních hodnot musí být povoleno/zakázáno v době vytváření entity. Po vytvoření nelze konfigurační parametr detekce duplicit změnit. | Pokud chcete povolit detekci duplicit u dřív vytvořené fronty nebo předplatného, můžete vytvořit novou frontu s detekcí duplicit a pak přesměrovat z původní fronty do nové fronty. |
| Chybný požadavek | 40000 | Subcode = 40 000. Zadaná hodnota 16384 je neplatná. Vlastnost ' MaxSizeInMegabytes ' musí mít jednu z následujících hodnot: 1024; 2048; 3072/95; 4096; 5 120. | Hodnota MaxSizeInMegabytes je neplatná. | Ujistěte se, že MaxSizeInMegabytes je jedním z následujících 1024, 2048, 3072, 4096, 5120. |
| Chybný požadavek | 40000 | Subcode = 40 000. Pro frontu nelze změnit rozdělení na oddíly. | Dělení nelze změnit pro entitu. | Vytvořte novou entitu a povolte oddíly. | 
| Chybný požadavek | žádný | Obor názvů *"název oboru názvů"* neexistuje. | Obor názvů v rámci vašeho předplatného Azure neexistuje. | Pokud chcete tuto chybu vyřešit, zkuste prosím níže. <ul> <li> Ujistěte se, že je předplatné Azure správné. </li> <li> Ujistěte se, že obor názvů existuje. </li> <li> Ověřte, že je název oboru názvů správný (žádné chyby pravopisu nebo řetězce s hodnotou null). </li> </ul> | 
| Chybný požadavek | 40400 | Subcode = 40400. Cílová entita automatického předávání neexistuje. | Cíl pro cílovou entitu autopřesměrovává neexistuje. | Cílovou entitu (frontu nebo téma) musí existovat před vytvořením zdroje. Po vytvoření cílové entity zkuste akci zopakovat. |


## <a name="error-code-429"></a>Kód chyby: 429

| Kód chyby | Chybový kód | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Subcode = 50004. Žádost se ukončila, protože obor názvů *váš obor názvů* se omezuje. | K tomuto chybovému stavu dojde, když počet příchozích požadavků překročí omezení prostředku. | Počkejte několik sekund a zkuste to znovu. <br/> <br/> Další informace o [kvótách](service-bus-quotas.md) a [Azure Resource Manager omezeních požadavků](../azure-resource-manager/resource-manager-request-limits.md)|
| 429 | 40901 | Subcode = 40901. Probíhá jiná konfliktní operace. | U stejného prostředku nebo entity probíhá jiná konfliktní operace. | Než budete zkoušet znovu, počkejte, než se aktuální probíhající operace dokončí. |
| 429 | 40900 | Subcode = 40900. Došlo. Požadujete operaci, která není v aktuálním stavu prostředku povolena. | K tomuto stavu může dojít, když se k provádění operací u stejné entity (fronty, tématu, předplatného nebo pravidla) provede více požadavků. | Počkejte několik sekund a zkuste to znovu. |
| 429 | žádný | Došlo ke konfliktu prostředků. Může probíhat jiná konfliktní operace. Pokud se jedná o opakování operace selhání, bude vyčištění na pozadí stále čeká na vyřízení. Opakujte akci později. | K tomuto stavu může dojít, pokud se u stejné entity čeká na operaci. | Než budete operaci opakovat, počkejte na dokončení předchozí operace. |

