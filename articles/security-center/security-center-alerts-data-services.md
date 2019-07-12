---
title: Pro datové služby ve službě Azure Security Center detekce hrozeb | Dokumentace Microsoftu
description: Toto téma představuje datových služeb výstrah k dispozici ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 87cfd2769e473d26c2dcae1b7b418f6fb1739915
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626295"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Detekce hrozeb pro datové služby ve službě Azure Security Center

 Security Center analyzuje protokoly služby úložiště dat a aktivují upozornění, když zjistí hrozbu pro vaše datové prostředky. Toto téma obsahuje seznam výstrah, které Security Center generuje následující služby:

* [Azure SQL Database a SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)

## Azure SQL Database a SQL Data Warehouse <a name="data-sql"></a>

Detekuje neobvyklé aktivity a potenciálně nebezpečné detekce hrozeb SQL pokusí o přístup k databázím nebo jejich zneužití. Security Center analyzuje protokoly auditu SQL a běží nativně v modulu SQL.

|Výstrahy|Popis|
|---|---|
|**Zranitelnost vůči útoku prostřednictvím injektáže SQL**|Aplikace v databázi vygeneruje Chybný příkaz SQL. Může to znamenat možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Existují dva možné důvody vygenerování chybného příkazu: Chyba v kódu aplikace, sestavit chybného příkazu SQL. Nebo kód aplikace nebo uložených procedur neměli neupravují uživatelský vstup při sestavování chybného příkazu SQL, který může být zneužit pro injektáž SQL.|
|**Potenciální útok prostřednictvím injektáže SQL**|Došlo k výskytu aktivního zneužití na zjištěné aplikace zranitelné vůči útoku prostřednictvím injektáže SQL. To znamená, že útočník pokouší vložit škodlivé příkazy SQL s použitím zranitelného kódu aplikace nebo uložených procedur komponentami TableAdapter.|
|**Přístup z neobvyklého umístění**|Došlo ke změně vzoru přístupu k systému SQL server, když někdo přihlásil k SQL serveru z neobvyklé geografické lokality. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).|
|**Přístup z neznámého objektu zabezpečení**|Došlo ke změně vzoru přístupu k systému SQL server – někdo přihlásil k SQL serveru pomocí neobvyklého objektu zabezpečení (uživatel SQL). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).|
|**Přístup z potenciálně škodlivé aplikace**|Pro přístup do databáze byla použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.|
|**Přihlašovací údaje SQL útok hrubou silou**|Došlo k neobvykle vysoký počet neúspěšných přihlášení s jinými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.|

Další informace o SQL hrozeb detekce výstrahy naleznete v tématu[detekce hrozeb Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)a projděte si část výstrahy detekce hrozeb. Viz také [jak Azure Security Center pomáhá odhalit Cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) Chcete-li zobrazit příklad použití škodlivých aktivit zjišťování SQL ke zjištění útoku v Security Center.

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Rozšířená ochrana před internetovými útoky pro Azure Storage je pouze aktuálně k dispozici pro úložiště objektů Blob. 

Advanced Threat Protection pro Azure Storage poskytuje další vrstvu analytických nástrojů zabezpečení, která detekuje neobvyklé a potenciálně škodlivé pokusy o přístup nebo využití účtů úložiště. Tato úroveň ochrany lze řešení ohrožení bez nutnosti odborné zabezpečení a systémy pro monitorování zabezpečení.

Security Center analyzuje diagnostické protokoly pro čtení, zápisu a žádosti o odstranění do úložiště objektů Blob ke zjištění hrozeb a aktivují upozornění, když dojde k anomálie v aktivitě. Další informace najdete v tématu [konfigurace protokolování Storage Analytics](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) Další informace.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Neobvyklé umístění přístup anomálií**|Analýza provozu vzorky sítě zjistila neobvyklou odchozí komunikaci protokolu RDP (Remote Desktop) pocházející z prostředků ve vašem nasazení. Tato aktivita se považuje za neobvyklé pro toto prostředí a může naznačovat, že došlo k napadení vašeho prostředku, který se teď používá k útoku hrubou silou externí koncový bod protokolu RDP. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.|
|**Aplikace access anomálií**|Označuje, že neobvyklé aplikace přístup k tomuto účtu úložiště. Možnou příčinou je, že útočník má získat přístup k účtu úložiště pomocí nové aplikace.|
|**Anonymní přístup anomálií**|Označuje, že dojde ke změně vzoru přístupu k účtu úložiště. Například účet byl otevřen anonymně (bez ověřování), což neočekávaný porovnává se vzorem poslední přístup na tento účet. Možnou příčinou je, že útočník zneužije veřejné oprávnění ke čtení pro kontejner, že obsahuje několika úložiště.|
|**Data průsak ven anomálií**|Označuje, že má byly extrahovány neobvykle velký objem dat v porovnání s poslední aktivitu na tento kontejner úložiště. Možnou příčinou je, že útočník má extrahovat velké množství dat z kontejneru, že obsahuje několika úložiště.|
|**Neočekávané odstranit anomálií**|Označuje, že jednu nebo více operací neočekávané odstranit došlo v účtu úložiště, ve srovnání s poslední aktivitu na tento účet. Možnou příčinou je, že útočník odstranila data z vašeho účtu úložiště.|
|**Nahrát balíček cloudové služby Azure**|Označuje, že k Azure Cloud Service balíček (.cspkg soubor) se odeslal do účtu úložiště neobvyklým způsobem, ve srovnání s poslední aktivitu na tento účet. Možnou příčinou je, že útočník byla Příprava k nasazení škodlivý kód z vašeho účtu úložiště do cloudové služby Azure.|
|**Oprávnění přístupu anomálií**|Označuje, že oprávnění tohoto kontejneru úložiště se změnily neobvyklým způsobem. Možnou příčinou je, že se změnila útočníka kontejneru oprávnění oslabit stav zabezpečení a získáte trvalosti.|
|**Kontroly přístupu anomálií**|Označuje, že oprávnění přístupu k účtu úložiště byly podrobeny neobvyklým způsobem, ve srovnání s poslední aktivitu na tento účet. Možnou příčinou je, že útočník provedl rekognoskace pro budoucí útoku.|
|**Anomálií zkoumání dat**|Označuje, že objekty BLOB nebo kontejnery v účtu úložiště byly uvedené neobvyklým způsobem, ve srovnání s poslední aktivitu na tento účet. Možnou příčinou je, že útočník provedl rekognoskace pro budoucí útoku.|

>[!NOTE]
>Rozšířená ochrana před internetovými útoky pro Azure Storage není aktuálně dostupná v oblastech suverénních cloudů a Azure government.

Další informace o výstrahách pro úložiště najdete v tématu [rozšířené ochrany před internetovými útoky pro Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection) článku a projděte si část pojednávající o výstrahách ochrany.
