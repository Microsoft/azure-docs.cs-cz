---
title: Detekce hrozeb pro datové služby v Azure Security Center | Microsoft Docs
description: Toto téma představuje výstrahy datových služeb dostupné v Azure Security Center.
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
ms.author: v-mohabe
ms.openlocfilehash: 1cafd8a3c766e57aed67634d7da8498c9a6ee120
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295822"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Detekce hrozeb pro datové služby v Azure Security Center

 Security Center analyzuje protokoly služeb úložiště dat a aktivuje výstrahy, když zjistí hrozbu pro vaše datové prostředky. V tomto tématu jsou uvedeny výstrahy, které Security Center generuje pro následující služby:

* [Azure SQL Database a SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)

## Azure SQL Database a SQL Data Warehouse<a name="data-sql"></a>

Detekce hrozeb SQL detekuje aktivity neobvyklé, které ukazují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Security Center analyzuje protokoly auditu SQL a spustí se nativně v modulu SQL.

|Výstrahy|Popis|
|---|---|
|**Zranitelnost vůči injektáže SQL**|Aplikace vygenerovala v databázi chybný příkaz SQL. To může znamenat možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Existují dva možné důvody vygenerování chybného příkazu: Buď závada v kódu aplikace vytvořila chybný příkaz SQL. Nebo, kód aplikace nebo uložené procedury neopravily uživatelský vstup při vytváření chybného příkazu SQL, který může být zneužit pro vkládání SQL.|
|**Potenciální injektáže SQL**|K aktivnímu zneužití došlo v případě, že je zjištěná aplikace zranitelná pomocí injektáže SQL. To znamená, že se útočník pokouší vložit škodlivé příkazy SQL pomocí ohroženého kódu aplikace nebo uložených procedur.|
|**Přístup z neobvyklého umístění**|Došlo ke změně vzoru přístupu k serveru SQL Server, pokud se někdo přihlásil k systému SQL Server z neobvyklého zeměpisného umístění. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).|
|**Přístup z neznámého objektu zabezpečení**|Došlo ke změně vzoru přístupu k serveru SQL Server – někdo se přihlásil k systému SQL Server pomocí neobvyklého objektu zabezpečení (uživatel SQL). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).|
|**Přístup z potenciálně škodlivé aplikace**|Pro přístup k databázi byla použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.|
|**Pověření hrubou silou SQL**|Došlo k neobvyklému vysokému počtu neúspěšných přihlášení s různými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.|

Další informace o výstrahách detekce hrozeb SQL najdete v tématu[Azure SQL Database detekci hrozeb](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)a v části výstrahy detekce hrozeb. Podívejte se také na to, [jak Azure Security Center pomáhá odhalit cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) a zobrazit příklad toho, jak Security Center při zjišťování útoku používali škodlivou detekci aktivit SQL.

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Rozšířená ochrana před internetovými útoky pro Azure Storage je aktuálně dostupná jenom pro Blob Storage. 

Advanced Threat Protection pro Azure Storage poskytuje další vrstvu analytických nástrojů zabezpečení, která detekuje neobvyklé a potenciálně škodlivé pokusy o přístup nebo využití účtů úložiště. Tato vrstva ochrany umožňuje řešit hrozby, aniž byste museli být odborníkem na zabezpečení, a spravovat systémy monitorování zabezpečení.

Security Center analyzuje diagnostické protokoly žádostí o čtení, zápis a odstranění do úložiště objektů blob za účelem detekce hrozeb a aktivuje výstrahy při výskytu anomálií v aktivitě. Další informace najdete v tématu [Konfigurace protokolování analýza úložiště](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) pro další informace.

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Neobvyklá anomálie přístupu k poloze**|Byla zjištěna analýza síťového provozu neobvyklé odchozího přenosu protokol RDP (Remote Desktop Protocol) (RDP) pocházející z prostředku ve vašem nasazení. Tato aktivita se považuje za neobvyklou pro toto prostředí a může znamenat, že došlo k ohrožení zabezpečení prostředku a že se teď používá pro externí koncový bod protokolu RDP hrubou silou. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.|
|**Anomálie přístupu k aplikacím**|Indikuje, že se k tomuto účtu úložiště přistupovala neobvyklá aplikace. Možnou příčinou je, že útočník získal k účtu úložiště pomocí nové aplikace.|
|**Anomálie anonymního přístupu**|Indikuje, že došlo ke změně vzoru přístupu k účtu úložiště. K účtu se například přistupuje anonymně (bez ověřování), které se ve srovnání s nedávným vzorem přístupu na tomto účtu neočekávalo. Možnou příčinou je, že útočník zneužije veřejný přístup pro čtení kontejneru, který obsahuje úložiště objektů BLOB (s).|
|**Anomálie exfiltrace dat**|Indikuje, že se v porovnání s poslední aktivitou tohoto kontejneru úložiště extrahuje neobvykle velké množství dat. Možnou příčinou je, že útočník extrahuje velké množství dat z kontejneru, který obsahuje úložiště objektů BLOB (s).|
|**Neočekávaná anomálie odstranění**|Označuje, že v účtu úložiště se vyskytla jedna nebo víc neočekávaných operací odstranění v porovnání s poslední aktivitou tohoto účtu. Možnou příčinou je, že útočník odstranil data z vašeho účtu úložiště.|
|**Nahrání balíčku cloudové služby Azure**|Indikuje, že balíček Azure Cloud Service (soubor. cspkg) se neobvyklým způsobem nahrál do účtu úložiště, a to v porovnání s poslední aktivitou na tomto účtu. Možnou příčinou je to, že se útočník připravuje na nasazení škodlivého kódu z vašeho účtu úložiště do cloudové služby Azure.|
|**Anomálie přístupu k oprávněním**|Indikuje, že oprávnění k přístupu tohoto kontejneru úložiště se změnila neobvyklým způsobem. Možnou příčinou je to, že útočník změnil oprávnění kontejneru pro oslabení stav zabezpečení nebo získání trvalosti.|
|**Anomálie přístupu ke kontrole**|Označuje, že přístupová oprávnění účtu úložiště byla v porovnání s poslední aktivitou tohoto účtu prověřena neobvyklým způsobem. Možnou příčinou je, že útočník provedl rekognoskace k budoucímu útoku.|
|**Anomálie průzkumu dat**|Označuje, že objekty blob nebo kontejnery v účtu úložiště jsou v porovnání s poslední aktivitou tohoto účtu vyhodnoceny neobvyklým způsobem. Možnou příčinou je, že útočník provedl rekognoskace k budoucímu útoku.|

>[!NOTE]
>Rozšířená ochrana před internetovými útoky pro Azure Storage není v současnosti dostupná v oblastech cloudu Azure pro státní správu a svrchované oblasti.

Další informace o výstrahách pro úložiště najdete v článku [Rozšířená ochrana před internetovými útoky pro Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection) a přečtěte si část výstrahy ochrany.
