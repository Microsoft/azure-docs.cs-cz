---
title: Detekce hrozeb pro datové služby v Azure Security Center | Microsoft Docs
description: Tento článek uvádí výstrahy datových služeb, které jsou dostupné v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: memildin
ms.openlocfilehash: 8b99d89e8895cd1c8d8e9fe3961f0db9855fb7ee
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196122"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Detekce hrozeb pro datové služby v Azure Security Center

 Azure Security Center analyzuje protokoly služeb datových úložišť a vyvolá upozornění, když detekuje hrozbu pro vaše datové prostředky. V tomto článku jsou uvedené výstrahy, které Security Center generuje pro následující služby:

* [Azure SQL Database a Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database a SQL Data Warehouse<a name="data-sql"></a>

Detekce hrozeb SQL identifikuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. 

|Výstrahy|Popis|
|---|---|
|**Možnou chybu zabezpečení při vkládání SQL**|Aplikace vygenerovala v databázi chybný příkaz SQL. To může znamenat možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Existují dva možné důvody chybného příkazu. Závada v kódu aplikace může být vytvořena v chybném příkazu jazyka SQL. Nebo, kód aplikace nebo uložené procedury neopravily uživatelský vstup při vytváření chybného příkazu SQL, který lze zneužít pro vkládání SQL.|
|**Potenciální injektáže SQL**|K aktivnímu zneužití došlo v případě, že je zjištěná aplikace zranitelná pomocí injektáže SQL. To znamená, že se útočník pokouší vložit škodlivé příkazy SQL pomocí ohroženého kódu aplikace nebo uložených procedur.|
|**Přihlášení z neobvyklého umístění**|Došlo ke změně vzoru přístupu pro SQL Server, kdy se někdo přihlásil k serveru z neobvyklého zeměpisného umístění. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec nebo externí útočník).|
|**Přihlášení neznámým objektem zabezpečení**|Došlo ke změně vzoru přístupu pro SQL Server. Někdo se k serveru přihlásil pomocí neobvyklého objektu zabezpečení (uživatel). V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec nebo externí útočník).|
|**Došlo k pokusu o přihlášení potenciálně škodlivou aplikací.**|Pro přístup k databázi byla použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok, který využívá běžné nástroje.|
|**Potenciální pokus o útok hrubou silou SQL**|Došlo k neobvyklému vysokému počtu neúspěšných přihlášení s různými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.|

Další informace o výstrahách detekce hrozeb SQL najdete v tématu [Azure SQL Database detekce hrozeb](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview). Konkrétně si přečtěte část výstrahy detekce hrozeb. Podívejte se také na to, [jak Azure Security Center pomáhá odhalit cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) a zobrazit příklad toho, jak Security Center při zjišťování útoku používali škodlivou detekci aktivit SQL.

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Rozšířená ochrana před internetovými útoky pro úložiště je aktuálně dostupná jenom pro úložiště objektů BLOB.

Rozšířená ochrana před internetovými útoky pro úložiště poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům úložiště nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby, aniž byste museli být odborníkem na zabezpečení a mohli spravovat systémy monitorování zabezpečení.

Security Center analyzuje diagnostické protokoly žádostí o čtení, zápis a odstranění do úložiště objektů blob za účelem detekce hrozeb a aktivuje výstrahy při výskytu anomálií v aktivitě. Další informace najdete v tématu [Konfigurace protokolování analýza úložiště](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging).

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Přístup z neobvyklého umístění do účtu úložiště**|Indikuje, že došlo ke změně vzoru přístupu k účtu Azure Storage. Někdo k tomuto účtu přistupoval z IP adresy považované za neznámou, pokud je v porovnání s poslední aktivitou. Buď útočník získal přístup k účtu, nebo byl oprávněný uživatel připojen z nového nebo neobvyklého zeměpisného umístění. Příkladem druhé z nich je Vzdálená údržba z nové aplikace nebo vývojáře.|
|**Neobvyklá aplikace, ke kterým se získal účet úložiště**|Indikuje, že se k tomuto účtu úložiště přistupovala neobvyklá aplikace. Možnou příčinou je, že útočník získal k vašemu účtu úložiště pomocí nové aplikace.|
|**Anonymní přístup k účtu úložiště**|Indikuje, že došlo ke změně vzoru přístupu k účtu úložiště. K účtu se například přistupuje anonymně (bez ověřování), které se ve srovnání s nedávným vzorem přístupu na tomto účtu neočekávalo. Možnou příčinou je, že útočník zneužije veřejný přístup pro čtení kontejneru, který obsahuje úložiště objektů BLOB.|
|**Přístup z uzlu pro ukončení mandátu do účtu úložiště**|Indikuje, že se tento účet úspěšně přistupoval z IP adresy, která se označuje jako aktivní odesílací uzel systému (anonymizace proxy server). Závažnost této výstrahy je považována za použitý typ ověřování (pokud existuje) a zda se jedná o první případ takového přístupu. Možnou příčinou může být útočník, který získal účet úložiště pomocí systému pro práci, nebo legitimního uživatele, který získal účet úložiště pomocí systému.|
|**Neobvyklé množství dat extrahovaných z účtu úložiště**|Indikuje, že se v porovnání s poslední aktivitou tohoto kontejneru úložiště extrahuje neobvykle velké množství dat. Možnou příčinou je, že útočník extrahuje velké množství dat z kontejneru, který obsahuje úložiště objektů BLOB.|
|**Neobvyklé odstranění v účtu úložiště**|Označuje, že v účtu úložiště se vyskytla jedna nebo víc neočekávaných operací odstranění v porovnání s poslední aktivitou tohoto účtu. Možnou příčinou je, že útočník odstranil data z vašeho účtu úložiště.|
|**Neobvyklé nahrání. cspkg do účtu úložiště**|Indikuje, že balíček Azure Cloud Services (soubor. cspkg) se neobvyklým způsobem nahrál do účtu úložiště, a to v porovnání s poslední aktivitou na tomto účtu. Možnou příčinou je to, že se útočník připravuje na nasazení škodlivého kódu z vašeho účtu úložiště do cloudové služby Azure.|
|**Neobvyklé změny přístupových oprávnění v účtu úložiště**|Indikuje, že oprávnění k přístupu tohoto kontejneru úložiště se změnila neobvyklým způsobem. Možnou příčinou je to, že útočník změnil oprávnění kontejneru pro oslabení stav zabezpečení nebo získání trvalosti.|
|**Neobvyklá kontrola přístupu v účtu úložiště**|Označuje, že přístupová oprávnění účtu úložiště byla v porovnání s poslední aktivitou tohoto účtu prověřena neobvyklým způsobem. Možnou příčinou je, že útočník provedl rekognoskace k budoucímu útoku.|
|**Neobvyklé zkoumání dat v účtu úložiště**|Označuje, že objekty blob nebo kontejnery v účtu úložiště jsou v porovnání s poslední aktivitou tohoto účtu vyhodnoceny neobvyklým způsobem. Možnou příčinou je, že útočník provedl rekognoskace k budoucímu útoku.|
|**Náhled – potenciální malware nahraný do účtu úložiště**|Indikuje, že se do účtu úložiště nahrál objekt BLOB obsahující potenciální malware. Možné příčiny můžou zahrnovat úmyslné nahrání malwaru útočníkem nebo neúmyslné nahrání potenciálně škodlivého objektu BLOB oprávněným uživatelem.|

>[!NOTE]
>Rozšířená ochrana před internetovými útoky pro úložiště není v současnosti dostupná v oblastech cloudu Azure pro státní správu a svrchované oblasti.

Další informace o výstrahách pro úložiště najdete v tématu [Rozšířená ochrana před internetovými útoky pro Azure Storage](../storage/common/storage-advanced-threat-protection.md). Konkrétně si přečtěte část "výstrahy ochrany".

## Azure Cosmos DB<a name="cosmos-db"></a>

Následující výstrahy jsou generovány neobvyklými a potenciálně škodlivými pokusy o přístup k účtům Azure Cosmos DB nebo jejich zneužití:

|Výstrahy|Popis|
|---|---|
|**Přístup z neobvyklého umístění na účet Cosmos DB**|Indikuje, že došlo ke změně vzoru přístupu k účtu Azure Cosmos DB. Někdo k tomuto účtu přistupoval z neznámé IP adresy v porovnání s poslední aktivitou. K účtu byl přidaný útočník nebo oprávněný uživatel k němu přistupoval z nového a neobvyklého zeměpisného umístění. Příkladem druhé z nich je Vzdálená údržba z nové aplikace nebo vývojáře.|
|**Neobvyklé množství dat extrahovaných z Cosmos DB účtu**|Indikuje, že došlo ke změně vzoru extrakce dat z Azure Cosmos DB účtu. Někdo v porovnání s poslední aktivitou extrahovali neobvyklý objem dat. Útočník mohl extrahovat velké množství dat z databáze Azure Cosmos DB (například exfiltrace nebo únik dat nebo neoprávněný přenos dat). Nebo legitimní uživatel nebo aplikace mohl extrahovat neobvyklé množství dat z kontejneru (například pro aktivitu zálohování údržby).|

Další informace najdete v tématu [Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB](../cosmos-db/cosmos-db-advanced-threat-protection.md).
