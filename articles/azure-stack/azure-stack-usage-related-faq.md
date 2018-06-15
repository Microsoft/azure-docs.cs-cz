---
title: Nejčastější dotazy souvisí s využitím rozhraní API | Microsoft Docs
description: Seznam zásobník Azure měřidla, porovnání Azure využití rozhraní API, doba využití a hlášené čas kódy chyb.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: f0df9b02e7672faec3a1d94997c9b27ffca275e5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159614"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Nejčastější dotazy v zásobníku Azure využití rozhraní API
Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se rozhraní API služby Azure zásobníku využití.

## <a name="what-meter-ids-can-i-see"></a>Jaké měření ID můžete zobrazit?
Využití je hlášené pro následující zprostředkovatele prostředků:

| **Poskytovatel prostředků** | **ID měření** | **Název měřidla** | **jednotka** | **Další informace** |
| --- | --- | --- | --- | --- |
| **Síť** |F271A8A388C44D93956A063E1D2FA80B |Použití statických IP adres |IP adresy| Počet IP adres používá. Při volání využití rozhraní API s dnech, měřidlo vrátí IP adresu násobí hodnotou počet hodin. |
| |9E2739BA86744796B465F64674B822BA |Použití dynamických IP adres |IP adresy| Počet IP adres používá. Při volání využití rozhraní API s dnech, měřidlo vrátí IP adresu násobí hodnotou počet hodin. |
| **Úložiště** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*hodin |Celková kapacita spotřebovávají tabulky. |
| |B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*hodin |Celková kapacita spotřebovávají objekty BLOB stránky. |
| |B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*hodin |Celková kapacita spotřebovávají fronty. |
| |09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*hodin |Celková kapacita spotřebovávají objekty BLOB bloku. |
| |B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |Počet požadavku v 10 000's |Tabulka žádosti o služby (v 10,000s). |
| |50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |Příchozí přenos dat v GB |Tabulka příchozí data služby v GB. |
| |1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |Odchozí v GB |Odchozí data služby Table v GB |
| |43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |Počet požadavků za 10 000's |Objekt BLOB žádosti o služby (v 10,000s). |
| |9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |Příchozí přenos dat v GB |Příchozí data služby objektů BLOB v GB. |
| |3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |Odchozí v GB |Odchozí data služby objektů BLOB v GB. |
| |EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |Počet požadavků za 10 000's |Fronty žádostí o služby (v 10,000s). |
| |E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |Příchozí přenos dat v GB |Příchozí data služby fronty v GB. |
| |DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |Odchozí v GB |Odchozí data fronty služby v GB |
| **SQL RP**            | CBCFEF9A-B91F-4597-A4D3-01FE334BED82 | DatabaseSizeHourSqlMeter   | MB\*hodin   | Celkové kapacity databáze při vytváření. Při volání využití rozhraní API s dnech, měřidlo vrátí MB násobí hodnotou počet hodin. |
| **MySql RP**          | E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3 | DatabaseSizeHourMySqlMeter | MB\*hodin    | Celkové kapacity databáze při vytváření. Při volání využití rozhraní API s dnech, měřidlo vrátí MB násobí hodnotou počet hodin. |
| **Compute** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |Základní virtuální počítač velikost hodiny |Virtuální základní hodiny | Počet jader virtuální násobí hodnotou hodiny, které virtuální počítač spustil. |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Velikost virtuálního počítače s Windows – hodiny |Virtuální základní hodiny | Počet jader virtuální násobí hodnotou hodin, po které byl virtuální počítač spuštěn. |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |Virtuální počítač velikost hodiny |Virtuální počítač hodiny |Zaznamená základní a systému Windows virtuálního počítače. Nepřizpůsobí pro počet jader. |
| **Key Vault** |EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Transakce Key Vault | Počet požadavku v 10 000's| Počet přijatých Key Vault datové roviny požadavky REST API. |
| |2C354225-B2FE-42E5-AD89-14F0EA302C87 |Pokročilé klíče transakce | 10 tisíc transakcí|     RSA 3K nebo 4 kB, ECC klíče transakce. (preview). |
| **Aplikační služby** | 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA | App Service | Virtuální základní hodiny | Počet virtuálních jader používá ke spouštění služby app service. Poznámka: Společnost Microsoft používá toto měření k účtují v zásobníku Azure App Service. Poskytovatele cloudových služeb můžete použít jiné aplikace služby měřidla (dole) k výpočtu využití pro své klienty. |
|  | 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE | Požadavky funkce | 10 požadavků | Celkový počet požadovaný spuštěních (podle 10 spuštěních). Spuštění, se počítají pokaždé, když funkce běží v reakci na událost, nebo se aktivuje vazbu. |
|  | D1D04836-075C-4F27-BF65-0A1130EC60ED | Funkce – výpočetní | GB-s | Spotřeba prostředků se měří v sekundách gigabajt (GB/s). **Zjištěnými spotřeby prostředků** se počítá vynásobením čas v milisekundách, jak dlouho trvá ke spuštění funkce paměti Průměrná velikost v GB. Množství paměti používané funkce se měří zaokrouhlení až nejbližší 128 MB, a to až do velikosti 1 536 MB, maximální velikost paměti s časem provedení vypočítána zaokrouhlení až nejbližší 1 ms. Minimální doba provádění a paměť pro spuštění jedné funkce je 100 ms a 128 mb. |
|  | 957E9F36-2C14-45A1-B6A1-1723EF71A01D | Hodiny sdílené aplikační služby | 1 hodina | Za hodinu využití horizontálního oddílu plán služby App Service. Na aplikace na základě měření podle objemu plány. |
|  | 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9 | Hodiny aplikační služby volné | 1 hodina | Za hodinu využití volného plán služby App Service. Na aplikace na základě měření podle objemu plány. |
|  | 88039D51-A206-3A89-E9DE-C5117E2D10A6 | Hodiny krátkodobého používání aplikační služby standardní | 1 hodina | Počítá na základě velikosti a počtu instancí. |
|  | 83A2A13E-4788-78DD-5D55-2831B68ED825 | Hodiny střednědobého používání aplikační služby Standard | 1 hodina | Počítá na základě velikosti a počtu instancí. |
|  | 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6 | Hodiny velké standardní aplikační služby | 1 hodina | Počítá na základě velikosti a počtu instancí. |
|  | *Vlastní pracovní vrstev* | Vlastní pracovní vrstev | Hodiny | Deterministické měření ID je vytvořeno na základě SKU a vlastní pracovní název vrstvy. Toto měření ID je jedinečný pro každou vrstvu vlastní pracovního procesu. |
|  | 264ACB47-AD38-47F8-ADD3-47F01DC4F473 | SNI SSL | Za vazbu SNI SSL | Služby App Service podporuje dva typy připojení SSL: připojení SSL indikace názvu serveru (SNI) a připojení SSL IP adres. Připojení SSL typu SNI funguje v moderních prohlížečích, připojení SSL na základě IP adresy lze pak použít ve všech. |
|  | 60B42D72-DC1C-472C-9895-6C516277EDB4 | IP SSL | Za IP na základě vazbu SSL | Služby App Service podporuje dva typy připojení SSL: připojení SSL indikace názvu serveru (SNI) a připojení SSL IP adres. Připojení SSL typu SNI funguje v moderních prohlížečích, připojení SSL na základě IP adresy lze pak použít ve všech. |
|  | 73215A6C-FA54-4284-B9C1-7E8EC871CC5B | Webový proces |  | Vypočítat za aktivní lokality za hodinu. |
|  | 5887D39B-0253-4E12-83C7-03E1A93DFFD9 | Externí odchozí šířky pásma | GB | Celkový počet bajtů příchozí žádosti o odpovědi + celkový počet odchozích požadavku, že bajtů + celkový FTP příchozí žádosti o odpovědi bajtů + celkový příchozí webové nasadit bajty odpovědi. |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Jak Azure zásobníku využití rozhraní API porovnání [rozhraní API pro Azure využití](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (momentálně ve verzi public preview)?
* Rozhraní API klienta využití je konzistentní s Azure API, s jednou výjimkou: *showDetails* příznak aktuálně není podporována v zásobníku Azure.
* Rozhraní API poskytovatele využití se vztahuje pouze na Azure zásobníku.
* V současné době [RateCard API](https://msdn.microsoft.com/library/azure/mt219004.aspx) který je k dispozici v Azure, která není k dispozici v zásobníku Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Jaký je rozdíl mezi časem využití a vykazovaného časového?
Sestavy využití dat mají dva hlavní časové hodnoty:

* **Hlášený čas**. Čas, kdy k události využití zadáno systému využití
* **Doba využití**. Pokud byl prostředek Azure zásobníku uplynulý čas

Může se zobrazit nesoulad hodnoty pro využití čas a čas hlášené pro konkrétní využití událost. Zpoždění může být stejně dlouho jako několik hodin v jakémkoli prostředí.

V současné době můžete dotazovat pouze systémem *hlášené čas*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Co znamenají tyto kódy chyb využití rozhraní API?
| **Stavový kód protokolu HTTP** | **Kód chyby** | **Popis** |
| --- | --- | --- |
| / 400 – Chybný požadavek |*NoApiVersion* |*Verze rozhraní api* chybí parametr dotazu. |
| / 400 – Chybný požadavek |*InvalidProperty* |Vlastnost nebyla nalezena nebo má neplatnou hodnotu. Zprávy v kód chyby v textu odpovědi identifikuje chybí vlastnost. |
| / 400 – Chybný požadavek |*RequestEndTimeIsInFuture* |Hodnota *ReportedEndTime* je v budoucnu. Hodnoty v budoucnu nejsou povolené pro tento argument. |
| / 400 – Chybný požadavek |*SubscriberIdIsNotDirectTenant* |Volání API poskytovatele použila ID odběru, který není platný volajícího klienta. |
| / 400 – Chybný požadavek |*SubscriptionIdMissingInRequest* |Chybí ID předplatného volajícího. |
| / 400 – Chybný požadavek |*InvalidAggregationGranularity* |Bylo vyžadováno agregaci neplatný členitosti. Platné hodnoty jsou denní a po hodinách. |
| 503 |*ServiceUnavailable* |Opakovatelná chyba došlo k chybě, protože služba je zaneprázdněná nebo volání je omezené. |

## <a name="next-steps"></a>Další kroky
[Zákazník fakturace a vrácení peněz v Azure zásobníku](azure-stack-billing-and-chargeback.md)

[Využití prostředků poskytovatele rozhraní API](azure-stack-provider-resource-api.md)

[Využití prostředků rozhraní API klienta](azure-stack-tenant-resource-usage-api.md)
