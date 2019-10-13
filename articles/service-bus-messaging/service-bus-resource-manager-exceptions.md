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
ms.openlocfilehash: 9a2d25aba03156d6d14fe5ef9aa58b3748033b85
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296382"
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
| Nesprávná žádost | 40000 | Subcode = 40 000. Vlastnost *' name '* nemůže být nastavena při vytváření fronty, protože obor názvů *' název oboru názvů '* používá vrstvu ' Basic '. Tato operace je podporovaná jenom v úrovni Standard nebo Premium. | Na úrovni Basic Azure Service Bus nelze nastavit ani aktualizovat tyto vlastnosti – <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Témata </li> </ul> | Pokud chcete používat tuto funkci, zvažte upgrade z úrovně Basic na úroveň Standard nebo Premium. |
| Nesprávná žádost | 40000 | Subcode = 40 000. Hodnotu vlastnosti ' requiresDuplicateDetection ' existující fronty (nebo tématu) nelze změnit. | Zjišťování duplicitních hodnot musí být povoleno/zakázáno v době vytváření entity. Konfigurační parametr detekce duplicit nelze po vytvoření změnit. | Pokud chcete povolit detekci duplicit u dříve vytvořené fronty nebo tématu, můžete vytvořit novou frontu nebo téma s detekcí duplicit a pak přesměrovat z původní fronty do nové fronty nebo tématu. |
| Nesprávná žádost | 40000 | Subcode = 40 000. Zadaná hodnota 16384 je neplatná. Vlastnost ' MaxSizeInMegabytes ' musí mít jednu z následujících hodnot: 1024; 2048; 3072/95; 4096; 5 120. | Hodnota MaxSizeInMegabytes je neplatná. | Ujistěte se, že MaxSizeInMegabytes je jedním z následujících 1024, 2048, 3072, 4096, 5120. |
| Nesprávná žádost | 40000 | Subcode = 40 000. Dělení nelze změnit pro frontu či téma. | Dělení nelze změnit pro entitu. | Vytvořte novou entitu (frontu nebo téma) a povolte oddíly. | 
| Nesprávná žádost | Žádná | Obor názvů *"název oboru názvů"* neexistuje. | Obor názvů v rámci vašeho předplatného Azure neexistuje. | Pokud chcete tuto chybu vyřešit, zkuste prosím níže. <ul> <li> Ujistěte se, že je předplatné Azure správné. </li> <li> Ujistěte se, že obor názvů existuje. </li> <li> Ověřte, že je název oboru názvů správný (žádné chyby pravopisu nebo řetězce s hodnotou null). </li> </ul> | 
| Nesprávná žádost | 40400 | Subcode = 40400. Cílová entita automatického předávání neexistuje. | Cíl pro cílovou entitu autopřesměrovává neexistuje. | Cílovou entitu (frontu nebo téma) musí existovat před vytvořením zdroje. Po vytvoření cílové entity zkuste akci zopakovat. |
| Nesprávná žádost | 40000 | Subcode = 40 000. Zadaný čas uzamčení překračuje povolené maximum 5 minut. | Čas, kdy může být zpráva uzamčena, musí být v rozmezí 1 minuty (minimálně) až 5 minut (maximum). | Zajistěte, aby zadaný čas uzamčení byl mezi 1 min a 5 minutami. |
| Nesprávná žádost | 40000 | Subcode = 40 000. Vlastnost DelayedPersistence i RequiresDuplicateDetection nelze současně povolit. | Entity s povoleným vyhledáváním duplicit musí být trvalé, takže stálost nelze zpozdit. | Další informace o [detekci duplicit](duplicate-detection.md) |
| Nesprávná žádost | 40000 | Subcode = 40 000. Hodnotu vlastnosti RequiresSession existující fronty nelze změnit. | V době vytváření entity by měla být povolená podpora pro relace. Po vytvoření nemůžete povolit nebo zakázat relace u existující entity (fronty nebo předplatného). | Odstraňte a znovu vytvořte novou frontu (nebo předplatné) s povolenou vlastností "RequiresSession". |
| Nesprávná žádost | 40000 | Subcode = 40 000. ' URI_PATH ' obsahuje znaky, které nejsou povoleny Service Bus. Segmenty entit můžou obsahovat jenom písmena, číslice, tečky (.), spojovníky (-) a podtržítka (_). | Segmenty entit můžou obsahovat jenom písmena, číslice, tečky (.), spojovníky (-) a podtržítka (_). Jakékoli jiné znaky způsobí selhání požadavku. | Zajistěte, aby v cestě identifikátoru URI neexistovaly žádné neplatné znaky. |


## <a name="error-code-429"></a>Kód chyby: 429

Stejně jako v HTTP, "kód chyby 429" indikuje "příliš mnoho požadavků". Implikuje omezení konkrétního prostředku (namespace) z důvodu příliš velkého počtu požadavků (nebo z důvodu konfliktních operací) na tomto prostředku.

| Kód chyby | Chybový kód | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Subcode = 50004. Žádost se ukončila, protože obor názvů *váš obor názvů* se omezuje. | K tomuto chybovému stavu dojde, když počet příchozích požadavků překročí omezení prostředku. | Počkejte několik sekund a zkuste to znovu. <br/> <br/> Další informace o [kvótách](service-bus-quotas.md) a [Azure Resource Manager omezeních požadavků](../azure-resource-manager/resource-manager-request-limits.md)|
| 429 | 40901 | Subcode = 40901. Probíhá jiná konfliktní operace. | U stejného prostředku nebo entity probíhá jiná konfliktní operace. | Než budete zkoušet znovu, počkejte, než se aktuální probíhající operace dokončí. |
| 429 | 40900 | Subcode = 40900. Došlo. Požadujete operaci, která není v aktuálním stavu prostředku povolena. | K tomuto stavu může dojít, když se k provádění operací u stejné entity (fronty, tématu, předplatného nebo pravidla) provede více požadavků. | Počkejte několik sekund a zkuste to znovu. |
| 429 | 40901 | Požadavek na entitu *název entity* je v konfliktu s jiným požadavkem. | U stejného prostředku nebo entity probíhá jiná konfliktní operace. | Než budete zkoušet znovu, počkejte na dokončení předchozí operace. |
| 429 | 40901 | Pro entitu *název entity*je zpracovávána jiná žádost o aktualizaci. | U stejného prostředku nebo entity probíhá jiná konfliktní operace. | Než budete zkoušet znovu, počkejte na dokončení předchozí operace. |
| 429 | Žádná | Došlo ke konfliktu prostředků. Může probíhat jiná konfliktní operace. Pokud se jedná o opakování operace selhání, bude vyčištění na pozadí stále čeká na vyřízení. Zkuste to znovu později. | K tomuto stavu může dojít, pokud se u stejné entity čeká na operaci. | Než budete operaci opakovat, počkejte na dokončení předchozí operace. |


## <a name="error-code-not-found"></a>Kód chyby: Nenalezeno

Tato třída chyb indikuje, že prostředek nebyl nalezen.

| Kód chyby | Chybový kód | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Nenalezeno | Žádná | Entitu *název entity* se nepovedlo najít. | Entita, proti které nebyla operace nalezena. | Ověřte, zda entita existuje, a operaci opakujte. |
| Nenalezeno | Žádná | Nenalezeno. Operace neexistuje. | Operace, kterou se pokoušíte provést, neexistuje. | Ověřte operaci a zkuste to znovu. |
| Nenalezeno | Žádná | Příchozí žádost není rozpoznaná jako požadavek PUT zásady oboru názvů. | Text příchozího požadavku má hodnotu null, a proto jej nelze provést jako požadavek PUT. | Zkontrolujte text žádosti, abyste se ujistili, že není null. | 
| Nenalezeno | Žádná | Entita zasílání zpráv *' název entity '* nebyla nalezena. | Entita, u které se pokoušíte provést operaci, nebyla nalezena. | Zkontrolujte prosím, zda entita existuje, a operaci opakujte. |
