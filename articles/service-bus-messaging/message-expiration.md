---
title: Azure Service Bus – vypršení platnosti zprávy
description: Tento článek vysvětluje vypršení platnosti a dobu provozu Azure Service Busch zpráv. Po uplynutí tohoto termínu se zpráva už nedoručuje.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: a2a568f04c2607832a1fa2a8e32bc6ce8331da4d
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652067"
---
# <a name="message-expiration-time-to-live"></a>Vypršení platnosti zpráv (hodnota TTL)
Datová část ve zprávě nebo příkaz nebo dotaz, který zpráva předává přijímači, je téměř vždy v souladu s některými formami konečného termínu vypršení platnosti na úrovni aplikace. Po uplynutí tohoto termínu se obsah už nedoručuje nebo požadovaná operace se už nespustí.

Pro vývojová a testovací prostředí, ve kterých se fronty a témata často používají v kontextu částečných spuštění aplikací nebo částí aplikace, je také žádoucí, aby byly automaticky uvolněny do paměti pro vybrané testovací zprávy, aby bylo možné začít čistit další testovací běh.

Vypršení platnosti pro jednotlivé zprávy lze ovládat nastavením systémové vlastnosti **Time-to-Live** , která určuje relativní dobu trvání. Vypršení platnosti se bude absolutní okamžitá, když je zpráva zařazená do fronty. V tuto chvíli má vlastnost **Expires-on-UTC** hodnotu TTL (Time- **to-UTC)**  +  . Nastavení TTL (Time-to-Live) u zprostředkované zprávy se nevynutilo, pokud neaktivně nenaslouchá žádní klienti.

Po **vypršení doby platnosti v čase UTC** se zprávy stanou neoprávněnými pro načtení. Vypršení platnosti nemá vliv na zprávy, které jsou momentálně uzamčené pro doručení. Tyto zprávy jsou stále zpracovávány normálně. Pokud zámek vyprší nebo dojde k opuštění zprávy, bude vypršení platnosti platit okamžitě.

I když je zpráva uzamčená, může být aplikace v držbě zprávy, jejíž platnost vypršela. Zda je aplikace ochotna pokračovat se zpracováním nebo se rozhodnete zrušit zprávu pro implementátora.

## <a name="entity-level-expiration"></a>Vypršení platnosti úrovně entity
Všechny zprávy odeslané do fronty nebo tématu podléhají výchozímu vypršení platnosti, které je nastaveno na úrovni entity. Dá se nastavit i na portálu během vytváření a později ho upravovat. Výchozí doba platnosti se používá pro všechny zprávy odesílané do entity, kde Time-to-Live není explicitně nastaveno. Výchozí hodnota vypršení platnosti funguje také jako mez pro hodnotu TTL (Time to Live). Zprávy, které mají delší dobu, než je výchozí hodnota, jsou před zařazováním do fronty tiše upraveny na výchozí hodnotu TTL (Time to Live).

> [!NOTE]
> Výchozí hodnota TTL (Time to Live) pro zprostředkovanou zprávu je největší možnou hodnotou pro samostatně 64 celé číslo, pokud není uvedeno jinak.
>
> U entit zasílání zpráv (fronty a témata) je výchozí doba vypršení platnosti také největší možnou hodnotou pro Service Bus úrovně Standard a Premium pro každé 64ové celé číslo. Pro úroveň **Basic** je výchozí (také maximální) doba vypršení platnosti **14 dní**.

Zprávy s vypršenou platností lze volitelně přesunout do [fronty nedoručených](service-bus-dead-letter-queues.md)zpráv. Toto nastavení můžete nakonfigurovat programově nebo pomocí Azure Portal. Pokud je možnost ponecháno zakázaná, zprávy s vypršenou platností se vynechává. Zprávy s vypršenou platností přesunuté do fronty nedoručených zpráv je možné odlišit od jiných nedoručených zpráv vyhodnocením vlastnosti [důvod nedoručených](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) zpráv, kterou zprostředkovatel ukládá do části vlastnosti uživatele. 

Ve výše uvedeném případě, kdy je zpráva chráněná před vypršením platnosti, a pokud je příznak nastaven na entitě, zpráva se přesune do fronty nedoručených zpráv, protože zámek je zrušený nebo vyprší jeho platnost. Není však přesunuta, pokud je zpráva úspěšně vyrovnána, což pak předpokládá, že aplikace ji úspěšně zpracovala navzdory nominálnímu vypršení platnosti.

Kombinací možností Time-to-Live a Automatic (a transactioning) po vypršení platnosti je cenný nástroj pro zajištění důvěry v tom, jestli se úloha poskytnutá obslužné rutině nebo skupině obslužných rutin pod konečným termínem načítá ke zpracování jako konečný termín.

Představte si například web, který potřebuje spolehlivě spouštět úlohy v back-endu s omezeným škálováním a občas se v nich vyskytují špičky provozu nebo se má v případě dostupnosti tohoto back-endu izolované. V běžném případě obslužná rutina na straně serveru pro odeslaná uživatelská data předává informace do fronty a následně obdrží odpověď potvrzující úspěšné zpracování transakce do fronty odpovědí. Pokud existuje špička provozu a obslužná rutina back-end nemůže zpracovat své nevyřízené položky v čase, úlohy s vypršenou platností se vrátí do fronty nedoručených zpráv. Interaktivní uživatel může obdržet oznámení o tom, že požadovaná operace bude trvat trochu déle než obvykle, a požadavek je pak možné umístit do jiné fronty pro cestu zpracování, kde je konečný výsledek zpracování odeslán uživateli e-mailem. 


## <a name="temporary-entities"></a>Dočasné entity

Service Bus fronty, témata a odběry je možné vytvořit jako dočasné entity, které se automaticky odeberou, když se v zadaném časovém období nepoužijí.
 
Automatické čištění je užitečné ve scénářích vývoje a testování, ve kterých se entity vytvářejí dynamicky a nejsou vyčištěny po použití, kvůli nějakému přerušení testu nebo běhu ladění. Je také užitečné, když aplikace vytvoří dynamické entity, jako je například fronta odpovědí, pro příjem odpovědí zpět do procesu webového serveru nebo do jiného poměrně krátkodobého nenáročného objektu, kde je obtížné spolehlivě vyčistit tyto entity, když se instance objektu zmizí.

Tato funkce je povolená pomocí vlastnosti **Automatické odstranění při nečinnosti** v oboru názvů. Tato vlastnost je nastavená na dobu, po kterou musí být entita nečinná (nepoužívá se), než se automaticky odstraní. Minimální hodnota této vlastnosti je 5.
 
## <a name="idleness"></a>Nečinnost

Tady je postup, který se považuje za nečinnost entit (fronty, témata a odběry):

- Fronty
    - Žádná odeslání  
    - Žádná přijetí  
    - Ve frontě nejsou žádné aktualizace.  
    - Žádné naplánované zprávy  
    - Bez procházení/prohlížení 
- Témata  
    - Žádná odeslání  
    - Žádné aktualizace tématu  
    - Žádné naplánované zprávy 
- Předplatná
    - Žádná přijetí  
    - Žádné aktualizace předplatného  
    - K předplatnému se nepřidala žádná nová pravidla.  
    - Bez procházení/prohlížení  
 

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)