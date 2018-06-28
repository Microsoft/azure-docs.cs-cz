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
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: ac22ba34bff1d5321c05bc0a0a1b14ca742079a7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051490"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Nejčastější dotazy v zásobníku Azure využití rozhraní API

Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se rozhraní API služby Azure zásobníku využití.

## <a name="what-meter-ids-can-i-see"></a>Jaké měření ID můžete zobrazit?
Využití je hlášené pro následující zprostředkovatele prostředků:

**Síť**  
  
**Měřicí ID**: F271A8A388C44D93956A063E1D2FA80B  
**Název měřidla**: použití statických adres IP  
**Jednotka**: IP adresy  
**Poznámky k**: počet IP adres používá. Při volání využití rozhraní API s dnech, měřidlo vrátí IP adresu násobí hodnotou počet hodin.  
  
**Měřicí ID**: 9E2739BA86744796B465F64674B822BA  
**Název měřidla**: použití dynamické adres IP  
**Jednotka**: IP adresy  
**Poznámky k**: počet IP adres používá. Při volání využití rozhraní API s dnech, měřidlo vrátí IP adresu násobí hodnotou počet hodin.  
  
**Úložiště**  
  
**Měřicí ID**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Název měřidla**: TableCapacity  
**Jednotka**: GB\*hodin  
**Poznámky k**: celková kapacita spotřebovávají tabulky.  
  
**Měřicí ID**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Název měřidla**: PageBlobCapacity  
**Jednotka**: GB\*hodin  
**Poznámky k**: celková kapacita spotřebovávají objekty BLOB stránky.  
  
**Měřicí ID**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Název měřidla**: QueueCapacity  
**Jednotka**: GB\*hodin  
**Poznámky k**: celková kapacita spotřebovávají fronty.  
  
**Měřicí ID**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Název měřidla**: BlockBlobCapacity  
**Jednotka**: GB\*hodin  
**Poznámky k**: celková kapacita spotřebovávají objekty BLOB bloku.  
  
**Měřicí ID**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Název měřidla**: TableTransactions  
**Jednotka**: požadavku počet v 10 000's  
**Poznámky k**: Tabulka žádosti o služby (v 10,000s).  
  
**Měřicí ID**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Název měřidla**: TableDataTransIn  
**Jednotka**: vstupní data v GB  
**Poznámky k**: Tabulka příchozí data služby v GB.  
  
**Měřicí ID**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Název měřidla**: TableDataTransOut  
**Jednotka**: odchozí v GB  
**Poznámky k**: Tabulka sazbách za odchozí data služby v GB  
  
**Měřicí ID**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Název měřidla**: BlobTransactions  
**Jednotka**: počet požadavků za 10 000's  
**Poznámky k**: Blob žádosti o služby (v 10,000s).  
  
**Měřicí ID**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Název měřidla**: BlobDataTransIn  
**Jednotka**: vstupní data v GB  
**Poznámky k**: Blob příchozí data služby v GB.  
  
**Měřicí ID**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Název měřidla**: BlobDataTransOut  
**Jednotka**: odchozí v GB  
**Poznámky k**: Blob sazbách za odchozí data služby v GB.  
  
**Měřicí ID**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Název měřidla**: QueueTransactions  
**Jednotka**: počet požadavků za 10 000's  
**Poznámky k**: fronty žádosti o služby (v 10,000s).  
  
**Měřicí ID**: E518E809-E369-4A45-9274-2017B29FFF25  
**Název měřidla**: QueueDataTransIn  
**Jednotka**: vstupní data v GB  
**Poznámky k**: fronty příchozí data služby v GB.  
  
**Měřicí ID**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Název měřidla**: QueueDataTransOut  
**Jednotka**: odchozí v GB  
**Poznámky k**: fronty služby odchozí data v GB  
  
**SQL RP**  
  
**Měřicí ID**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Název měřidla**: DatabaseSizeHourSqlMeter  
**Jednotka**: MB\*hodin  
**Poznámky k**: Celkový počet DB kapacity při vytváření. Při volání využití rozhraní API s dnech, měřidlo vrátí MB násobí hodnotou počet hodin.  
  
**MySql RP**  
  
**Měřicí ID**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Název měřidla**: DatabaseSizeHourMySqlMeter  
**Jednotka**: MB\*hodin  
**Poznámky k**: Celkový počet DB kapacity při vytváření. Při volání využití rozhraní API s dnech, měřidlo vrátí MB násobí hodnotou počet hodin.  
  
**Compute**  
  
**Měřicí ID**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Název měřidla**: základní virtuální počítač velikost hodiny  
**Jednotka**: virtuální základní hodiny  
**Poznámky k**: počet jader virtuální násobí hodnotou hodin virtuální počítač spustil.  
  
**Měřicí ID**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Název měřidla**: velikost virtuálního počítače s Windows – hodiny  
**Jednotka**: virtuální základní hodiny  
**Poznámky k**: počet jader virtuální násobí hodnotou hodin virtuální počítač spustil.  
  
**Měřicí ID**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Název měřidla**: čas velikost virtuálního počítače  
**Jednotka**: čas virtuálních počítačů  
**Poznámky k**: zaznamená základní a virtuální počítač s Windows. Nepřizpůsobí pro počet jader.  
  
**Key Vault**  
  
**Měřicí ID**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Název měřidla**: Key Vault transakce  
**Jednotka**: požadavku počet v 10 000's  
**Poznámky k**: číslo REST API požadavků přijatých službou roviny data Key Vault.  
  
**Měřicí ID**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Název měřidla**: Advanced klíče transakce  
**Jednotka**: 10 tisíc transakcí  
**Poznámky k**: RSA 3 K nebo 4 kB, ECC klíče transakce. (preview).  
  
*Aplikační služby**  
  
**Měřicí ID**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Název měřidla**: služby App Service  
**Jednotka**: virtuální základní hodiny  
**Poznámky k**: počet virtuální jader používá ke spouštění služby app service. Poznámka: Společnost Microsoft používá toto měření k účtují v zásobníku Azure App Service. Poskytovatele cloudových služeb můžete použít jiné aplikace služby měřidla (dole) k výpočtu využití pro své klienty.  
  
**Měřicí ID**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Název měřidla**: požadavky funkce  
**Jednotka**: 10 požadavků  
**Poznámky k**: Celkový počet požadovaný spuštění (podle 10 spuštěních). Spuštění, se počítají pokaždé, když funkce běží v reakci na událost, nebo se aktivuje vazbu.  
  
**Měřicí ID**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Název měřidla**: funkce – výpočetní  
**Jednotka**: GB-s  
**Poznámky k**: spotřeby prostředků měří v sekundách gigabajt (GB/s). **Zjištěnými spotřeby prostředků** se počítá vynásobením čas v milisekundách, jak dlouho trvá ke spuštění funkce paměti Průměrná velikost v GB. Množství paměti používané funkce se měří zaokrouhlení až nejbližší 128 MB, a to až do velikosti 1 536 MB, maximální velikost paměti s časem provedení vypočítána zaokrouhlení až nejbližší 1 ms. Minimální doba provádění a paměť pro spuštění jedné funkce je 100 ms a 128 mb.  
  
**Měřicí ID**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Název měřidla**: sdílené hodiny služby na úrovni aplikace  
**Jednotka**: 1 hodina  
**Poznámky k**: za hodinu využití horizontálního oddílu plán služby App Service. Na aplikace na základě měření podle objemu plány.  
  
**Měřicí ID**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Název měřidla**: hodiny bezplatnou aplikaci služby na úrovni  
**Jednotka**: 1 hodina  
**Poznámky k**: za hodinu využití volného plán služby App Service. Na aplikace na základě měření podle objemu plány.  
  
**Měřicí ID**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Název měřidla**: malé hodiny Standard služby aplikace  
**Jednotka**: 1 hodina  
**Poznámky k**: počítá na základě velikosti a počtu instancí.  
  
**Měřicí ID**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Název měřidla**: střední standardní aplikace služby hodin  
**Jednotka**: 1 hodina  
**Poznámky k**: počítá na základě velikosti a počtu instancí.  
  
**Měřicí ID**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Název měřidla**: velké hodiny Standard služby aplikace  
**Jednotka**: 1 hodina  
**Poznámky k**: počítá na základě velikosti a počtu instancí.  
  
**Vlastní pracovní vrstev**  
  
**Měřicí ID**: *vlastní pracovní vrstev*  
**Název měřidla**: vlastní pracovní vrstev  
**Jednotka**: čas  
**Poznámky k**: Deterministickou měření ID je vytvořeno na základě SKU a vlastní pracovní název vrstvy. Toto měření ID je jedinečný pro každou vrstvu vlastní pracovního procesu.  
  
**Měřicí ID**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Název měřidla**: SNI SSL  
**Jednotka**: za vazbu SNI SSL  
**Poznámky k**: služby App Service podporuje dva typy připojení SSL: připojení SSL indikace názvu serveru (SNI) a připojení SSL IP adres. Připojení SSL typu SNI funguje v moderních prohlížečích, připojení SSL na základě IP adresy lze pak použít ve všech.  
  
**Měřicí ID**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Název měřidla**: IP SSL  
**Jednotka**: za IP na základě vazbu SSL  
**Poznámky k**: služby App Service podporuje dva typy připojení SSL: připojení SSL indikace názvu serveru (SNI) a připojení SSL IP adres. Připojení SSL typu SNI funguje v moderních prohlížečích, připojení SSL na základě IP adresy lze pak použít ve všech.  
  
**Měřicí ID**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Název měřidla**: webové procesu  
**Jednotka**:  
**Poznámky k**: počítá se na aktivní lokality za hodinu.  
  
**Měřicí ID**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Název měřidla**: externí odchozí šířky pásma  
**Jednotka**: GB  
**Poznámky k**: Celkový počet příchozího požadavku odpovědi bajtů + celkový počet odchozích požadavek bajtů + celkový FTP příchozí žádosti o odpovědi bajtů + celkový příchozí webové nasazení bajtů odezvy požadavku.  
  

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
