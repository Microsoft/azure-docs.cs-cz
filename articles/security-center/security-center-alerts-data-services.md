---
title: Detekce hrozeb pro datové služby v Azure Security Center | Microsoft Docs
description: Toto téma představuje výstrahy datových služeb dostupné v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: memildin
ms.openlocfilehash: 763349c11a7777bfc807a60e1806c3bb4010245e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202579"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Detekce hrozeb pro datové služby v Azure Security Center

 Azure Security Center analyzuje protokoly služeb datových úložišť a vyvolá upozornění, když detekuje hrozbu pro vaše datové prostředky. V tomto tématu jsou uvedeny výstrahy, které Security Center generuje pro následující služby:

* [Azure SQL Database a Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database a SQL Data Warehouse<a name="data-sql"></a>

Detekce hrozeb SQL identifikuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Security Center analyzuje protokoly auditu SQL a spustí se nativně v modulu SQL.

|Výstrahy|Popis|
|---|---|
|**Zranitelnost vůči injektáže SQL**|Aplikace vygenerovala v databázi chybný příkaz SQL. To může znamenat možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Existují dva možné důvody chybného příkazu. Závada v kódu aplikace může být vytvořena v chybném příkazu jazyka SQL. Nebo, kód aplikace nebo uložené procedury neopravily uživatelský vstup při vytváření chybného příkazu SQL, který lze zneužít pro vkládání SQL.|
|**Potenciální injektáže SQL**|K aktivnímu zneužití došlo v případě, že je zjištěná aplikace zranitelná pomocí injektáže SQL. To znamená, že se útočník pokouší vložit škodlivé příkazy SQL pomocí ohroženého kódu aplikace nebo uložených procedur.|
|**Přístup z neobvyklého umístění**|Došlo ke změně vzoru přístupu pro SQL Server, kdy se někdo přihlásil k serveru z neobvyklého zeměpisného umístění. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec nebo externí útočník).|
|**Přístup z neznámého objektu zabezpečení**|Došlo ke změně vzoru přístupu pro SQL Server. Někdo se k serveru přihlásil pomocí neobvyklého objektu zabezpečení (uživatel). V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec nebo externí útočník).|
|**Přístup z potenciálně škodlivé aplikace**|Pro přístup k databázi byla použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok, který využívá běžné nástroje.|
|**Pověření hrubou silou SQL**|Došlo k neobvyklému vysokému počtu neúspěšných přihlášení s různými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.|

Další informace o výstrahách detekce hrozeb SQL najdete v tématu [Azure SQL Database detekce hrozeb](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview). Konkrétně si přečtěte část výstrahy detekce hrozeb. Podívejte se také na to, [jak Azure Security Center pomáhá odhalit cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) a zobrazit příklad toho, jak Security Center při zjišťování útoku používali škodlivou detekci aktivit SQL.

## Pamì<a name="azure-storage"></a>

>[!NOTE]
> Rozšířená ochrana před internetovými útoky pro úložiště je aktuálně dostupná jenom pro úložiště objektů BLOB.

Rozšířená ochrana před internetovými útoky pro úložiště poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům úložiště nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby, aniž byste museli být odborníkem na zabezpečení a mohli spravovat systémy monitorování zabezpečení.

Security Center analyzuje diagnostické protokoly žádostí o čtení, zápis a odstranění do úložiště objektů blob za účelem detekce hrozeb a aktivuje výstrahy při výskytu anomálií v aktivitě. Další informace najdete v tématu [Konfigurace protokolování analýza úložiště](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging).

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Neobvyklá anomálie přístupu k poloze**|Ukázková Analýza provozu v síti zjistila komunikaci neobvyklé odchozího protokol RDP (Remote Desktop Protocol) (RDP), která pochází z prostředku ve vašem nasazení. Tato aktivita se pro toto prostředí považuje za neobvyklou. Může to znamenat, že došlo k ohrožení zabezpečení prostředku a teď se používá k útoku hrubou silou na externí koncový bod RDP. Upozorňujeme, že tento typ aktivity může způsobit, že vaše IP adresa bude označená jako škodlivá externími entitami.|
|**Anomálie přístupu k aplikacím**|Indikuje, že se k tomuto účtu úložiště přistupovala neobvyklá aplikace. Možnou příčinou je, že útočník získal k vašemu účtu úložiště pomocí nové aplikace.|
|**Anomálie anonymního přístupu**|Indikuje, že došlo ke změně vzoru přístupu k účtu úložiště. K účtu se například přistupuje anonymně (bez ověřování), které se ve srovnání s nedávným vzorem přístupu na tomto účtu neočekávalo. Možnou příčinou je, že útočník zneužije veřejný přístup pro čtení kontejneru, který obsahuje úložiště objektů BLOB.|
|**Anomálie pro mandát**|Indikuje, že se tento účet úspěšně přistupoval z IP adresy, která se označuje jako aktivní odesílací uzel systému (anonymizace proxy server). Závažnost této výstrahy je považována za použitý typ ověřování (pokud existuje) a zda se jedná o první případ takového přístupu. Možnou příčinou může být útočník, který získal účet úložiště pomocí systému pro práci, nebo legitimního uživatele, který získal účet úložiště pomocí systému.|
|**Anomálie exfiltrace dat**|Indikuje, že se v porovnání s poslední aktivitou tohoto kontejneru úložiště extrahuje neobvykle velké množství dat. Možnou příčinou je, že útočník extrahuje velké množství dat z kontejneru, který obsahuje úložiště objektů BLOB.|
|**Neočekávaná anomálie odstranění**|Označuje, že v účtu úložiště se vyskytla jedna nebo víc neočekávaných operací odstranění v porovnání s poslední aktivitou tohoto účtu. Možnou příčinou je, že útočník odstranil data z vašeho účtu úložiště.|
|**Nahrát balíček Azure Cloud Services**|Indikuje, že balíček Azure Cloud Services (soubor. cspkg) se neobvyklým způsobem nahrál do účtu úložiště, a to v porovnání s poslední aktivitou na tomto účtu. Možnou příčinou je to, že se útočník připravuje na nasazení škodlivého kódu z vašeho účtu úložiště do cloudové služby Azure.|
|**Anomálie přístupu k oprávněním**|Indikuje, že oprávnění k přístupu tohoto kontejneru úložiště se změnila neobvyklým způsobem. Možnou příčinou je to, že útočník změnil oprávnění kontejneru pro oslabení stav zabezpečení nebo získání trvalosti.|
|**Anomálie přístupu ke kontrole**|Označuje, že přístupová oprávnění účtu úložiště byla v porovnání s poslední aktivitou tohoto účtu prověřena neobvyklým způsobem. Možnou příčinou je, že útočník provedl rekognoskace k budoucímu útoku.|
|**Anomálie průzkumu dat**|Označuje, že objekty blob nebo kontejnery v účtu úložiště jsou v porovnání s poslední aktivitou tohoto účtu vyhodnoceny neobvyklým způsobem. Možnou příčinou je, že útočník provedl rekognoskace k budoucímu útoku.|

>[!NOTE]
>Rozšířená ochrana před internetovými útoky pro úložiště není v současnosti dostupná v oblastech cloudu Azure pro státní správu a svrchované oblasti.

Další informace o výstrahách pro úložiště najdete v tématu [Rozšířená ochrana před internetovými útoky pro Azure Storage](../storage/common/storage-advanced-threat-protection.md). Konkrétně si přečtěte část "výstrahy ochrany".

## Azure Cosmos DB<a name="cosmos-db"></a>

Následující výstrahy jsou generovány neobvyklými a potenciálně škodlivými pokusy o přístup k účtům Azure Cosmos DB nebo jejich zneužití:

|Výstrahy|Popis|
|---|---|
|**Přístup z neobvyklého umístění**|Indikuje, že došlo ke změně vzoru přístupu k účtu Azure Cosmos DB. Někdo k tomuto účtu přistupoval z neznámé IP adresy v porovnání s poslední aktivitou. K účtu byl přidaný útočník nebo oprávněný uživatel k němu přistupoval z nového a neobvyklého zeměpisného umístění. Příkladem druhé z nich je Vzdálená údržba z nové aplikace nebo vývojáře.|
|**Neobvyklé exfiltrace dat**|Indikuje, že došlo ke změně vzoru extrakce dat z Azure Cosmos DB účtu. Někdo v porovnání s poslední aktivitou extrahovali neobvyklý objem dat. Útočník mohl extrahovat velké množství dat z databáze Azure Cosmos DB (například exfiltrace nebo únik dat nebo neoprávněný přenos dat). Nebo legitimní uživatel nebo aplikace mohl extrahovat neobvyklé množství dat z kontejneru (například pro aktivitu zálohování údržby).|

Další informace najdete v tématu [Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB](../cosmos-db/cosmos-db-advanced-threat-protection.md).