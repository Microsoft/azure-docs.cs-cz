---
title: Nejčastější dotazy souvisí s využitím rozhraní API | Dokumentace Microsoftu
description: Seznam měřiče služby Azure Stack, porovnání, které se využití Azure API, čas využití a hlásí čas kódy chyb.
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
ms.date: 09/06/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 76953f14744196306d0274882e8ec3b454fa99e3
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44051183"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Nejčastější dotazy v rozhraní API pro využití služby Azure Stack

Tento článek obsahuje odpovědi na některé nejčastější dotazy o rozhraní API využití služby Azure Stack.

## <a name="what-meter-ids-can-i-see"></a>Jaké měření ID mohou zobrazit?
Využití hlášené pro následující poskytovatele prostředků:

### <a name="network"></a>Síť
  
**ID měřiče**: F271A8A388C44D93956A063E1D2FA80B  
**Název měřiče**: použití statické IP adresy  
**Jednotka**: IP adresy  
**Poznámky k**: počet IP adres používat. Při volání rozhraní API využití s denní členitosti, vrátí měřič počtem hodin IP adresu.  
  
**ID měřiče**: 9E2739BA86744796B465F64674B822BA  
**Název měřiče**: použití dynamické IP adresy  
**Jednotka**: IP adresy  
**Poznámky k**: počet IP adres používat. Při volání rozhraní API využití s denní členitosti, vrátí měřič počtem hodin IP adresu.  
  
### <a name="storage"></a>Úložiště
  
**ID měřiče**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Název měřiče**: TableCapacity  
**Jednotka**: GB\*hodin  
**Poznámky k**: celková kapacita spotřebovaná tabulky.  
  
**ID měřiče**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Název měřiče**: PageBlobCapacity  
**Jednotka**: GB\*hodin  
**Poznámky k**: celková kapacita spotřebovaná objekty BLOB stránky.  
  
**ID měřiče**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Název měřiče**: QueueCapacity  
**Jednotka**: GB\*hodin  
**Poznámky k**: celková kapacita spotřebovaná fronty.  
  
**ID měřiče**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Název měřiče**: BlockBlobCapacity  
**Jednotka**: GB\*hodin  
**Poznámky k**: celková kapacita spotřebovaná objekty BLOB bloku.  
  
**ID měřiče**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Název měřiče**: TableTransactions  
**Jednotka**: počet v 10 požadavků 000's  
**Poznámky k**: tabulku žádosti o služby (v 10,000s).  
  
**ID měřiče**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Název měřiče**: TableDataTransIn  
**Jednotka**: příchozí přenos dat v GB  
**Poznámky k**: Table service příchozího přenosu dat v GB.  
  
**ID měřiče**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Název měřiče**: TableDataTransOut  
**Jednotka**: výstup v GB  
**Poznámky k**: Table service odchozí přenos dat v GB  
  
**ID měřiče**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Název měřiče**: BlobTransactions  
**Jednotka**: počet požadavků za 10, 000's  
**Poznámky k**: Blob žádosti o služby (v 10,000s).  
  
**ID měřiče**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Název měřiče**: BlobDataTransIn  
**Jednotka**: příchozí přenos dat v GB  
**Poznámky k**: služby Blob service příchozího přenosu dat v GB.  
  
**ID měřiče**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Název měřiče**: BlobDataTransOut  
**Jednotka**: výstup v GB  
**Poznámky k**: služby Blob service odchozí přenos dat v GB.  
  
**ID měřiče**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Název měřiče**: QueueTransactions  
**Jednotka**: počet požadavků za 10, 000's  
**Poznámky k**: fronty žádostí o služby (v 10,000s).  
  
**ID měřiče**: E518E809-E369-4A45-9274-2017B29FFF25  
**Název měřiče**: QueueDataTransIn  
**Jednotka**: příchozí přenos dat v GB  
**Poznámky k**: fronty service příchozího přenosu dat v GB.  
  
**ID měřiče**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Název měřiče**: QueueDataTransOut  
**Jednotka**: výstup v GB  
**Poznámky k**: fronty service odchozí přenos dat v GB  

### <a name="compute"></a>Compute 
  
**ID měřiče**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Název měřiče**: základní hodiny velikost virtuálního počítače  
**Jednotka**: virtuální základní hodiny  
**Poznámky k**: počet virtuálních jader vynásobené hodiny virtuálního počítače.  
  
**ID měřiče**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Název měřiče**: hodin velikost virtuálního počítače Windows  
**Jednotka**: virtuální základní hodiny  
**Poznámky k**: počet virtuálních jader vynásobené hodiny virtuálního počítače.  
  
**ID měřiče**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Název měřiče**: hodin velikost virtuálního počítače  
**Jednotka**: hodiny pro virtuální počítač  
**Poznámky k**: zachytí základní a Windows virtuální počítač. Neupravuje jádra.  
  
### <a name="managed-disks"></a>Managed Disks

**ID měřiče**: 5d76e09f-4567-452a-94cc-7d1f097761f0   
**Název měřiče**: S4   
**Jednotka**: počet disků   
**Poznámky k**: standardní spravovaný Disk – 32 GB 

**ID měřiče**: dc9fc6a9-0782-432a-b8dc-978130457494   
**Název měřiče**: S6   
**Jednotka**: počet disků   
**Poznámky k**: standardní spravovaný Disk – 64 GB 

**ID měřiče**: e5572fce-9f58-49d7-840c-b168c0f01fff   
**Název měřiče**: S10   
**Jednotka**: počet disků   
**Poznámky k**: standardní spravovaný Disk – 128 GB 

**ID měřiče**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**Název měřiče**: s.15   
**Jednotka**: počet disků   
**Poznámky k**: standardní spravovaný Disk – 256 GB 

**ID měřiče**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**Název měřiče**: S20   
**Jednotka**: počet disků      
**Poznámky k**: standardní spravovaný Disk – 512 GB 

**ID měřiče**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**Název měřiče**: S30   
**Jednotka**: počet disků   
**Poznámky k**: standardní spravovaný Disk – 1 024 GB 

**ID měřiče**: d9aac1eb-a5d1-42f2-b617-9e3ea94fed88   
**Název měřiče**: S40   
**Jednotka**: počet disků   
**Poznámky k**: standardní spravovaný Disk – 2 048 GB 

**ID měřiče**: a54899dd-458e-4a40-9abd-f57cafd936a7   
**Název měřiče**: S50   
**Jednotka**: počet disků   
**Poznámky k**: standardní spravovaný Disk – 4096 GB 

**ID měřiče**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**Název měřiče**: P4   
**Jednotka**: počet disků   
**Poznámky k**: Premium spravovaný Disk – 32 GB 

**ID měřiče**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**Název měřiče**: P6   
**Jednotka**: počet disků   
**Poznámky k**: Premium spravovaný Disk – 64 GB 

**ID měřiče**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**Název měřiče**: P10   
**Jednotka**: počet disků   
**Poznámky k**: Premium spravovaný Disk – 128 GB  

**ID měřiče**: 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**Název měřiče**: P15  
**Jednotka**: počet disků   
**Poznámky k**: Disk – 256 GB spravované úrovně Premium 

**ID měřiče**: c7e7839c-293b-4761-ae4c-848eda91130b   
**Název měřiče**: P20   
**Jednotka**: počet disků   
**Poznámky k**: Premium spravovaný Disk – 512 GB 

**ID měřiče**: 9f502103-adf4-4488-b494-456c95d23a9f   
**Název měřiče**: P30   
**Jednotka**: počet disků   
**Poznámky k**: Premium spravovaný Disk – 1 024 GB 

**ID měřiče**: 043757fc-049f-4e8b-8379-45bb203c36b1   
**Název měřiče**: P40   
**Jednotka**: počet disků    
**Poznámky k**: Premium spravovaný Disk – 2 048 GB 

**ID měřiče**: c0342c6f-810b-4942-85d3-6eaa561b6570   
**Název měřiče**: P50   
**Jednotka**: počet disků   
**Poznámky k**: Premium spravovaný Disk – 4096 GB 

**ID měřiče**: 8a409390-1913-40ae-917b-08d0f16f3c38   
**Název měřiče**: ActualStandardDiskSize   
**Jednotka**: bajtů      
**Poznámky k**: Skutečná velikost na disku spravovaných disků úrovně standard  

**ID měřiče**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Název měřiče**: ActualPremiumDiskSize   
**Jednotka**: bajtů      
**Poznámky k**: Skutečná velikost na disku premium spravovaný disk 

**ID měřiče**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Název měřiče**: ActualStandardSnapshotSize   
**Jednotka**: bajtů   
**Poznámky k**: Skutečná velikost na disku standardní spravovaný snímek.  

**ID měřiče**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**Název měřiče**: ActualPremiumSnapshotSize   
**Jednotka**: bajtů   
**Poznámky k**: Skutečná velikost na disku, spravované premium.   

### <a name="sql-rp"></a>Poskytovatele prostředků SQL
  
**ID měřiče**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Název měřiče**: DatabaseSizeHourSqlMeter  
**Jednotka**: MB\*hodin  
**Poznámky k**: Celkový počet DB kapacitu při vytvoření. Při volání rozhraní API využití s denní členitosti, vrátí měřič MB počtem hodin.  
  
### <a name="mysql-rp"></a>Poskytovatele prostředků MySql   
  
**ID měřiče**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Název měřiče**: DatabaseSizeHourMySqlMeter  
**Jednotka**: MB\*hodin  
**Poznámky k**: Celkový počet DB kapacitu při vytvoření. Při volání rozhraní API využití s denní členitosti, vrátí měřič MB počtem hodin.    
### <a name="key-vault"></a>Key Vault   
  
**ID měřiče**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Název měřiče**: transakce služby Key Vault  
**Jednotka**: počet v 10 požadavků 000's  
**Poznámky k**: číslo rozhraní REST API požadavků přijatých službou rovina dat trezoru klíčů.  
  
**ID měřiče**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Název měřiče**: pokročilé klíče transakce  
**Jednotka**: 10 tisíc transakcí  
**Poznámky k**: RSA 3 K/4 kB, transakce klíče ECC. (preview).  
  
### <a name="app-service"></a>App Service   
  
**ID měřiče**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Název měřiče**: služby App Service  
**Jednotka**: virtuální základní hodiny  
**Poznámky k**: počet virtuálních jader, které se používají ke spouštění služby app service. Poznámka: Společnost Microsoft používá tento měřič platby prostřednictvím služby App Service ve službě Azure Stack. Poskytovatelé cloudových služeb můžete použít jiné služby App Service měřiče (dole) k výpočtu využití pro své klienty.  
  
**ID měřiče**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Název měřiče**: žádosti o funkce  
**Jednotka**: 10 požadavků  
**Poznámky k**: Celkový počet vyžádaných spuštění (za 10 spuštění). Spuštění se počítají pokaždé, když funkce běží v reakci na událost nebo vazbou aktivovaném.  
  
**ID měřiče**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Název měřiče**: funkce - Compute  
**Jednotka**: GB-s  
**Poznámky k**: spotřeby prostředků měřené v gigabajtsekundách (GB/s). **Zjištěnými spotřeba prostředků** se počítá vynásobením průměrné měsíční velikosti paměti v Gigabajtech čas v milisekundách ke spuštění funkce. Paměť, kterou funkce využije měří se zaokrouhlením nejbližší 128 MB až do maximální velikosti paměti 1 536 MB, doba spuštění se počítá se zaokrouhlením na nejbližší 1 ms. Minimální doba spuštění a paměť pro spuštění jedné funkce je 100 ms a 128 mb.  
  
**ID měřiče**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Název měřiče**: sdílené hodin App Service  
**Jednotka**: 1 hodina  
**Poznámky k**: za hodinu použití horizontálních oddílů Plán služby App Service. Plány se měří na základě aplikací.  
  
**ID měřiče**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Název měřiče**: bezplatný hodiny služby aplikace  
**Jednotka**: 1 hodina  
**Poznámky k**: za hodinu použití bezplatný plán služby App Service. Plány se měří na základě aplikací.  
  
**ID měřiče**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Název měřiče**: malé standardní hodiny App Service  
**Jednotka**: 1 hodina  
**Poznámky k**: počítá na základě velikosti a počtu instancí.  
  
**ID měřiče**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Název měřiče**: střední standardní App Service hodiny  
**Jednotka**: 1 hodina  
**Poznámky k**: počítá na základě velikosti a počtu instancí.  
  
**ID měřiče**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Název měřiče**: velké standardní hodiny App Service  
**Jednotka**: 1 hodina  
**Poznámky k**: počítá na základě velikosti a počtu instancí.  
  
### <a name="custom-worker-tiers"></a>Vlastní vrstvy pracovních procesů   
  
**ID měřiče**: *vlastní vrstvy pracovních procesů*  
**Název měřiče**: vlastní vrstvy pracovních procesů  
**Jednotka**: hodiny  
**Poznámky k**: ID měřiče Deterministické je vytvořen na základě skladové položky a název vrstvy pracovního procesu vlastní. Toto ID měřiče je jedinečný pro každou vrstvu pracovního procesu vlastní.  
  
**ID měřiče**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Název měřiče**: SNI SSL  
**Jednotka**: na vazbu SNI SSL  
**Poznámky k**: služby App Service podporuje dva typy připojení SSL: připojení SSL pomocí indikace názvu serveru (SNI) a IP adresy. Připojení SSL typu SNI funguje v moderních prohlížečích, připojení SSL na základě IP adresy lze pak použít ve všech.  
  
**ID měřiče**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Název měřiče**: IP SSL.  
**Jednotka**: na IP adresu, na základě vazby SSL  
**Poznámky k**: služby App Service podporuje dva typy připojení SSL: připojení SSL pomocí indikace názvu serveru (SNI) a IP adresy. Připojení SSL typu SNI funguje v moderních prohlížečích, připojení SSL na základě IP adresy lze pak použít ve všech.  
  
**ID měřiče**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Název měřiče**: webový proces  
**Jednotka**:  
**Poznámky k**: počítá za aktivní lokality za hodinu.  
  
**ID měřiče**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Název měřiče**: externí šířky pásma pro výchozí přenos dat  
**Jednotka**: GB  
**Poznámky k**: Celkový počet příchozího požadavku bajty odpovědi a celkový počet odchozího požadavku bajtů + FTP celkový počet příchozích žádostí bajty odpovědi a celkový počet příchozích webové nasazení bajty odpovědi na žádost.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Jak Azure Stack využití rozhraní API pro porovnání [využití rozhraní API Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (aktuálně ve verzi public preview)?
* Rozhraní API využití Tenanta je konzistentní s Azure API s jednou výjimkou: *showDetails* příznak není aktuálně podporován ve službě Azure Stack.
* Rozhraní API využití zprostředkovatele platí jenom pro Azure Stack.
* V současné době [RateCard API](https://msdn.microsoft.com/library/azure/mt219004.aspx) , který je k dispozici v Azure, která není k dispozici ve službě Azure Stack.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Jaký je rozdíl mezi časem využití a ohlášených?
Sestavy využití dat mají dva hlavní časové hodnoty:

* **Hlášená čas**. Čas, kdy události využití zadáno využití systému
* **Čas použití**. Pokud byl prostředek služby Azure Stack uplynulý čas

Může se zobrazit rozdíl v hodnotách pro využití a čas hlášené pro konkrétní použití událostí. Zpoždění může být až několik hodin v jakémkoli prostředí.

V současné době můžete dát dotaz na pouze *hlášené čas*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Co znamenají tyto kódy chyb rozhraní API využití?
| **Stavový kód HTTP** | **Kód chyby:** | **Popis** |
| --- | --- | --- |
| 400/Chybný požadavek |*NoApiVersion* |*Verze api-version* chybí parametr dotazu. |
| 400/Chybný požadavek |*InvalidProperty* |Vlastnost chybí nebo má neplatnou hodnotu. Zpráva, kód chyby v textu odpovědi identifikuje chybějící vlastnosti. |
| 400/Chybný požadavek |*RequestEndTimeIsInFuture* |Hodnota pro *ReportedEndTime* je v budoucnosti. Pro tento argument nejsou povoleny hodnoty v budoucnu. |
| 400/Chybný požadavek |*SubscriberIdIsNotDirectTenant* |ID předplatného, který není platný tenanta volajícího používala volání rozhraní API poskytovatele. |
| 400/Chybný požadavek |*SubscriptionIdMissingInRequest* |Chybí ID předplatného volajícího. |
| 400/Chybný požadavek |*InvalidAggregationGranularity* |Byla vyžádána neplatnou agregace členitosti. Platné hodnoty jsou denně a po hodinách. |
| 503 |*ServiceUnavailable* |Vzhledem k tomu, služba je zaneprázdněná nebo volání je omezené došlo k opakovatelné chybě. |

## <a name="next-steps"></a>Další kroky
[Fakturace zákazníkům a vrácení peněz ve službě Azure Stack](azure-stack-billing-and-chargeback.md)

[Rozhraní API využití prostředků poskytovatele](azure-stack-provider-resource-api.md)

[Rozhraní API využití prostředků tenanta](azure-stack-tenant-resource-usage-api.md)
