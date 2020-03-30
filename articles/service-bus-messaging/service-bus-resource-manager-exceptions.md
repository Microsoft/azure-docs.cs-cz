---
title: Výjimky správce prostředků služby Azure Service Bus | Dokumenty společnosti Microsoft
description: Seznam výjimek service bus, které se objevily ve Správci prostředků Azure a navrhovaných akcích.
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
ms.openlocfilehash: 0f328651ac4422226071d2de12e9cbc787ef64be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978272"
---
# <a name="service-bus-resource-manager-exceptions"></a>Výjimky správce prostředků služby Service Bus

Tento článek uvádí výjimky generované při interakci s Azure Service Bus pomocí Azure Resource Manager – prostřednictvím šablon nebo přímých volání.

> [!IMPORTANT]
> Tento dokument je často aktualizován. Zkontrolujte, zda neobsahuje aktualizace.

Níže jsou uvedeny různé výjimky nebo chyby, které jsou uvedeny prostřednictvím Správce prostředků Azure.

## <a name="error-bad-request"></a>Chyba: Chybný požadavek

"Chybný požadavek" znamená, že požadavek přijatý správcem prostředků se nezdařil o ověření.

| Kód chyby | Chyba dílčího kódu | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Chybný požadavek | 40000 | SubCode=40000. Vlastnost *"název vlastnosti"* nelze nastavit při vytváření fronty, protože obor názvů *"název oboru názvů"* používá úroveň "Basic". Tato operace je podporována pouze ve vrstvě "Standard" nebo "Premium". | Na základní úrovni Azure Service Bus nelze nastavit nebo aktualizovat níže uvedené vlastnosti. <ul> <li> Vyžaduje odstranění duplicit </li> <li> AutodeleteOnIdle </li> <li>Vyžadujerelace</li> <li>VýchozíMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeOkno </li> <li> EnableExpress </li> <li> Vpřed </li> <li> Témata </li> </ul> | Zvažte upgrade ze základní na standardní nebo premium úroveň používat tuto funkci. |
| Chybný požadavek | 40000 | SubCode=40000. Hodnotu vlastnosti "requiresDuplicateDetection" existující ho queue(nebo topic) nelze změnit. | Vyhledávání duplicit musí být povoleno nebo zakázáno v době vytvoření entity. Parametr konfigurace vyhledávání duplicit nelze po vytvoření změnit. | Chcete-li povolit vyhledávání duplicit v dříve vytvořené frontě nebo tématu, můžete vytvořit novou frontu nebo téma s vyhledáváním duplicit a poté předat z původní fronty do nové fronty nebo tématu. |
| Chybný požadavek | 40000 | SubCode=40000. Zadaná hodnota 16384 je neplatná. Vlastnost MaxSizeInMegabytes musí být jedna z následujících hodnot: 1024;2048;3072;4096;5120. | Hodnota MaxSizeInMegabytes je neplatná. | Ujistěte se, že MaxSizeInMegabytes je jedním z následujících - 1024, 2048, 3072, 4096, 5120. |
| Chybný požadavek | 40000 | SubCode=40000. Dělení nelze změnit pro frontu nebo téma. | Dělení nelze změnit pro entitu. | Vytvořte novou entitu (frontu nebo téma) a povolte oddíly. | 
| Chybný požadavek | Žádná | Název *oboru* názvů neexistuje. | Obor názvů neexistuje v rámci předplatného Azure. | Chcete-li tuto chybu vyřešit, vyzkoušejte níže <ul> <li> Ujistěte se, že předplatné Azure je správné. </li> <li> Ujistěte se, že obor názvů existuje. </li> <li> Ověřte, zda je název oboru názvů správný (žádné pravopisné chyby nebo nulové řetězce). </li> </ul> | 
| Chybný požadavek | 40400 | Podkód=40400. Cílová entita automatického předávání neexistuje. | Cíl cílové entity automatického předávání neexistuje. | Cílová entita (fronta nebo téma) musí existovat před vytvořením zdroje. Opakujte akci po vytvoření cílové entity. |
| Chybný požadavek | 40000 | SubCode=40000. Dodaná doba uzamčení přesahuje povolený maximální počet minut "5". | Doba, po kterou může být zpráva uzamčena, musí být mezi 1 minutou (minimálně) a 5 minutami (maximálně). | Ujistěte se, že dodaný zámek je mezi 1 min a 5 min. |
| Chybný požadavek | 40000 | SubCode=40000. Vlastnost DelayedPersistence a RequiresDuplicateDetection nelze povolit společně. | Entity s povoleným vyhledáváním duplicit musí být trvalé, takže trvalost nemůže být zpožděna. | Další informace o [vyhledávání duplicit](duplicate-detection.md) |
| Chybný požadavek | 40000 | SubCode=40000. Vlastnost RequiresSession existující fronty nelze změnit. | Podpora pro relace by měla být povolena v době vytvoření entity. Po vytvoření nelze povolit nebo zakázat relace u existující entity (fronty nebo předplatného) | Odstraňte a znovu vytvořte novou frontu (nebo předplatné) s povolenou vlastností "RequiresSession". |
| Chybný požadavek | 40000 | SubCode=40000. 'URI_PATH' obsahuje znak (znaky), který není povolen Service Bus. Segmenty entit mohou obsahovat pouze písmena, čísla, tečky, pomlčky(-) a podtržítka(_). | Segmenty entit mohou obsahovat pouze písmena, čísla, tečky, pomlčky(-) a podtržítka(_). Všechny ostatní znaky způsobit selhání požadavku. | Ujistěte se, že v cestě URI nejsou žádné neplatné znaky. |


## <a name="error-code-429"></a>Kód chyby: 429

Stejně jako v protokolu HTTP označuje "Kód chyby 429" "příliš mnoho požadavků". To znamená, že konkrétní prostředek (obor názvů) je omezen z důvodu příliš mnoho požadavků (nebo z důvodu konfliktní operace) na tento prostředek.

| Kód chyby | Chyba dílčího kódu | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | SubCode=50004. Požadavek byl ukončen, protože *obor* názvů je omezen. | Tento chybový stav je přístupů, pokud počet příchozích požadavků překročí omezení prostředku. | Počkejte několik sekund a akci opakujte. <br/> <br/> Další informace o [kvótách](service-bus-quotas.md) a [limitech požadavků Azure Resource Manageru](../azure-resource-manager/management/request-limits-and-throttling.md)|
| 429 | 40901 | SubCode=40901. Probíhá další konfliktní operace. | Probíhá další konfliktní operace se stejným zdrojem/entitou. | Před opakovaným pokusem počkejte na dokončení aktuální probíhající operace. |
| 429 | 40900 | SubCode=40900. Konflikt. Požadujete operaci, která není povolena v aktuálním stavu zdroje. | Tato podmínka může být přístupů při více požadavků jsou provedeny k provedení operací na stejnou entitu (fronty, téma, odběr nebo pravidlo) ve stejnou dobu. | Počkejte několik sekund a akci opakujte. |
| 429 | 40901 | Požadavek na název entity *je* v konfliktu s jiným požadavkem. | Probíhá další konfliktní operace se stejným zdrojem/entitou. | Před opakováním operace počkejte na dokončení předchozí operace. |
| 429 | 40901 | Probíhá další požadavek na aktualizaci pro *entitu "název entity"*. | Probíhá další konfliktní operace se stejným zdrojem/entitou. | Před opakováním operace počkejte na dokončení předchozí operace. |
| 429 | Žádná | Došlo ke konfliktu prostředků. Možná probíhá další konfliktní operace. Pokud se jedná o opakování pro neúspěšnou operaci, vyčištění na pozadí stále čeká na vyřízení. Zkuste to později. | Tato podmínka může být přístupů, pokud je čekající operace proti stejné entity. | Před opakováním operace počkejte na dokončení předchozí operace. |


## <a name="error-code-not-found"></a>Kód chyby: Nebyl nalezen.

Tato třída chyb označuje, že prostředek nebyl nalezen.

| Kód chyby | Chyba dílčího kódu | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Nebyl nalezen. | Žádná | Nebyl nalezen *název entity.* | Entita, proti které nebyla operace nalezena. | Zkontrolujte, zda entita existuje, a zkuste operaci znovu. |
| Nebyl nalezen. | Žádná | Nebyl nalezen. Operace neexistuje. | Operace, kterou se pokoušíte provést, neexistuje. | Zkontrolujte operaci a akci opakujte. |
| Nebyl nalezen. | Žádná | Příchozí požadavek není rozpoznán jako požadavek na zadání zásad oboru názvů. | Text příchozího požadavku má hodnotu null, a proto jej nelze provést jako požadavek put. | Zkontrolujte tělo požadavku, abyste se ujistili, že není null. | 
| Nebyl nalezen. | Žádná | Entita zasílání zpráv *"název entity"* nebyla nalezena. | Entita, proti které se pokoušíte provést operaci, nebyla nalezena. | Zkontrolujte, zda entita existuje, a akci opakujte. |

## <a name="error-code-internal-server-error"></a>Kód chyby: Vnitřní chyba serveru

Tato třída chyb označuje, že došlo k chybě interního serveru.

| Kód chyby | Chyba dílčího kódu | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Vnitřní chyba serveru | 50000 | SubCode=50000. Vnitřní chyba serveru| Může se to stát z různých důvodů. Některé z příznaků jsou - <ul> <li> Požadavek/tělo klienta je poškozen a vede k chybě. </li> <li> Časový rozsah požadavku klienta z důvodu problémů se zpracováním služby. </li> </ul> | Chcete-li tento problém vyřešit <ul> <li> Ujistěte se, že parametry požadavků nejsou null nebo poškozený. </li> <li> Opakujte požadavek. </li> </ul> |

## <a name="error-code-unauthorized"></a>Kód chyby: Neautorizováno

Tato třída chyb označuje absenci oprávnění ke spuštění příkazu.

| Kód chyby | Chyba dílčího kódu | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Neautorizováno | Žádná | Neplatná operace v sekundárním oboru názvů. Sekundární obor názvů je jen pro čtení. | Operace byla provedena proti sekundárnímu oboru názvů, který je nastaven jako obor názvů jen pro čtení. | Opakujte příkaz proti primárnímu oboru názvů. Další informace o [sekundárním oboru názvů](service-bus-geo-dr.md) |
| Neautorizováno | Žádná | MissingToken: Hlavička autorizace nebyla nalezena. | K této chybě dochází, pokud má autorizace nulové nebo nesprávné hodnoty. | Ujistěte se, že hodnota tokenu uvedená v hlavičce autorizace je správná a není null. |