---
title: Nejčastější dotazy souvisí s využitím rozhraní API | Dokumentace Microsoftu
description: Seznam měřiče služby Azure Stack, porovnání, které se využití Azure API, čas využití a hlásí čas kódy chyb.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 11/08/2018
ms.openlocfilehash: 92774592f86a71a8482fd3d44eca404fcf2d4e6e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429549"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Nejčastější dotazy v rozhraní API pro využití služby Azure Stack

Tento článek obsahuje odpovědi na některé nejčastější dotazy o rozhraní API využití služby Azure Stack.

## <a name="what-meter-ids-can-i-see"></a>Jaké měření ID mohou zobrazit?
Využití hlášené pro následující poskytovatele prostředků:

### <a name="network"></a>Síť
  
**ID měřiče**: F271A8A388C44D93956A063E1D2FA80B  
**Název měřiče**: Použití statické IP adresy  
**Jednotka**: IP adresy  
**Poznámky k**: Počet IP adres používat. Při volání rozhraní API využití s denní členitosti, vrátí měřič počtem hodin IP adresu.  
  
**ID měřiče**: 9E2739BA86744796B465F64674B822BA  
**Název měřiče**: Použití dynamické IP adresy  
**Jednotka**: IP adresy  
**Poznámky k**: Počet IP adres používat. Při volání rozhraní API využití s denní členitosti, vrátí měřič počtem hodin IP adresu.  
  
### <a name="storage"></a>Storage
  
**ID měřiče**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Název měřiče**: TableCapacity  
**Jednotka**: GB\*hodin  
**Poznámky k**: Celková kapacita spotřebovaná tabulky.  
  
**ID měřiče**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Název měřiče**: PageBlobCapacity  
**Jednotka**: GB\*hodin  
**Poznámky k**: Celková kapacita spotřebovaná objekty BLOB stránky.  
  
**ID měřiče**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Název měřiče**: QueueCapacity  
**Jednotka**: GB\*hodin  
**Poznámky k**: Celková kapacita spotřebovaná fronty.  
  
**ID měřiče**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Název měřiče**: BlockBlobCapacity  
**Jednotka**: GB\*hodin  
**Poznámky k**: Celková kapacita spotřebovaná objekty BLOB bloku.  
  
**ID měřiče**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Název měřiče**: TableTransactions  
**Jednotka**: Počet požadavků na 10 000's  
**Poznámky k**: Tabulka žádostí o služby (v 10,000s).  
  
**ID měřiče**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Název měřiče**: TableDataTransIn  
**Jednotka**: Příchozí přenos dat v GB  
**Poznámky k**: Příchozí data služby tabulky v GB.  
  
**ID měřiče**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Název měřiče**: TableDataTransOut  
**Jednotka**: Výstup v GB  
**Poznámky k**: Tabulka služby sazbách za odchozí data v GB  
  
**ID měřiče**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Název měřiče**: BlobTransactions  
**Jednotka**: Počet požadavků za 10, 000's  
**Poznámky k**: Žádosti o služby objektů BLOB (v 10,000s).  
  
**ID měřiče**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Název měřiče**: BlobDataTransIn  
**Jednotka**: Příchozí přenos dat v GB  
**Poznámky k**: BLOB service příchozího přenosu dat v GB.  
  
**ID měřiče**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Název měřiče**: BlobDataTransOut  
**Jednotka**: Výstup v GB  
**Poznámky k**: BLOB service sazbách za odchozí data v GB.  
  
**ID měřiče**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Název měřiče**: QueueTransactions  
**Jednotka**: Počet požadavků za 10, 000's  
**Poznámky k**: Fronty žádostí o služby (v 10,000s).  
  
**ID měřiče**: E518E809-E369-4A45-9274-2017B29FFF25  
**Název měřiče**: QueueDataTransIn  
**Jednotka**: Příchozí přenos dat v GB  
**Poznámky k**: Fronty služby příchozí přenosy v GB.  
  
**ID měřiče**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Název měřiče**: QueueDataTransOut  
**Jednotka**: Výstup v GB  
**Poznámky k**: Fronty služby sazbách za odchozí data v GB  

### <a name="compute"></a>Compute 
  
**ID měřiče**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Název měřiče**: Hodiny pro velikosti základní virtuální počítač  
**Jednotka**: Virtuální základní hodiny  
**Poznámky k**: Počet virtuálních jader vynásobené hodin, po které byl virtuální počítač spuštěn.  
  
**ID měřiče**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Název měřiče**: Hodiny velikost virtuálního počítače Windows  
**Jednotka**: Virtuální základní hodiny  
**Poznámky k**: Počet virtuálních jader vynásobené hodin, po které byl virtuální počítač spuštěn.  
  
**ID měřiče**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Název měřiče**: Hodiny pro velikosti virtuální počítač  
**Jednotka**: Hodiny pro virtuální počítač  
**Poznámky k**: Zaznamená virtuálního počítače základní a Windows. Neupravuje jádra.  
  
### <a name="managed-disks"></a>Managed Disks

**ID měřiče**: 380874f9-300c-48e0-95a0-d2d9a21ade8f   
**Název měřiče**: S4   
**Jednotka**: Počet disků\*měsíc   
**Poznámky k**: Standardní spravovaný Disk – 32 GB 

**ID měřiče**: 1b77d90f-427b-4435-b4f1-d78adec53222   
**Název měřiče**: S6   
**Jednotka**: Počet disků\*měsíc   
**Poznámky k**: Standardní spravovaný Disk – 64 GB 

**Meter ID**: d5f7731b-f639-404a-89d0-e46186e22c8d   
**Název měřiče**: S10   
**Jednotka**: Počet disků\*měsíc   
**Poznámky k**: Standardní spravovaný Disk – 128 GB 

**ID měřiče**: ff85ef31-da5b-4eac-95dd-a69d6f97b18a   
**Název měřiče**: S15   
**Jednotka**: Počet disků\*měsíc   
**Poznámky k**: Standardní spravovaný Disk – 256 GB 

**ID měřiče**: 88ea9228-457a-4091-adc9-ad5194f30b6e   
**Název měřiče**: S20   
**Jednotka**: Počet disků\*měsíc      
**Poznámky k**: Standardní spravovaný Disk – 512 GB 

**ID měřiče**: 5b1db88a-8596-4002-8052-347947c26940   
**Název měřiče**: S30   
**Jednotka**: Počet disků\*měsíc   
**Poznámky k**: Standardní spravovaný Disk – 1 024 GB 

**ID měřiče**: 7660b45b-b29d-49cb-b816-59f30fbab011   
**Název měřiče**: P4   
**Jednotka**: Počet disků\*měsíc   
**Poznámky k**: Premium Managed Disk – 32 GB 

**ID měřiče**: 817007fd-a077-477f-bc01-b876f27205fd   
**Název měřiče**: P6   
**Jednotka**: Počet disků\*měsíc   
**Poznámky k**: Premium spravovaný Disk – 64 GB 

**Meter ID**: e554b6bc-96cd-4938-a5b5-0da990278519   
**Název měřiče**: P10   
**Jednotka**: Počet disků\*měsíc   
**Poznámky k**: Premium Managed Disk – 128 GB  

**ID měřiče**: cdc0f53a-62a9-4472-a06c-e99a23b02907   
**Název měřiče**: P15  
**Jednotka**: Počet disků\*měsíc   
**Poznámky k**: Disk – 256 GB spravované úrovně Premium 

**Meter ID**: b9cb2d1a-84c2-4275-aa8b-70d2145d59aa   
**Název měřiče**: P20   
**Jednotka**: Počet disků\*měsíc   
**Poznámky k**: Premium Managed Disk – 512 GB 

**ID měřiče**: 06bde724-9f94-43c0-84c3-d0fc54538369   
**Název měřiče**: P30   
**Jednotka**: Počet disků\*měsíc   
**Poznámky k**: Premium Managed Disk – 1 024 GB 

**ID měřiče**: 7ba084ec-ef9c-4d64-a179-7732c6cb5e28   
**Název měřiče**: ActualStandardDiskSize   
**Jednotka**: GB\*měsíc      
**Poznámky k**: Skutečná velikost na disku spravovaných disků úrovně standard  

**ID měřiče**: daef389a-06e5-4684-a7f7-8813d9f792d5  
**Název měřiče**: ActualPremiumDiskSize   
**Jednotka**: GB\*měsíc      
**Poznámky k**: Skutečná velikost na disku premium spravovaný disk 

**ID měřiče**: 75d4b707-1027-4403-9986-6ec7c05579c8  
**Název měřiče**: ActualStandardSnapshotSize   
**Jednotka**: GB\*měsíc   
**Poznámky k**: Skutečná velikost na disku standardní spravovaný snímek.  

**ID měřiče**: 5ca1cbb9-6f14-4e76-8be8-1ca91547965e   
**Název měřiče**: ActualPremiumSnapshotSize   
**Jednotka**: GB\*měsíc   
**Poznámky k**: Skutečná velikost na disku, spravované premium.   

**ID měřiče**: 5d76e09f-4567-452a-94cc-7d1f097761f0   
**Název měřiče**: S4   
**Jednotka**: Počet disků\*hodin   
**Poznámky k**: Standardní spravovaný Disk – 32 GB (zastaralé) 

**ID měřiče**: dc9fc6a9-0782-432a-b8dc-978130457494   
**Název měřiče**: S6   
**Jednotka**: Počet disků\*hodin   
**Poznámky k**: Standardní spravovaný Disk – 64 GB (zastaralé) 

**ID měřiče**: e5572fce-9f58-49d7-840c-b168c0f01fff   
**Název měřiče**: S10   
**Jednotka**: Počet disků\*hodin   
**Poznámky k**: Standardní spravovaný Disk – 128 GB (zastaralé) 

**ID měřiče**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**Název měřiče**: S15   
**Jednotka**: Počet disků\*hodin   
**Poznámky k**: Standardní spravovaný Disk – 256 GB (zastaralé) 

**ID měřiče**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**Název měřiče**: S20   
**Jednotka**: Počet disků\*hodin      
**Poznámky k**: Standardní spravovaný Disk – 512 GB (zastaralé) 

**ID měřiče**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**Název měřiče**: S30   
**Jednotka**: Počet disků\*hodin   
**Poznámky k**: Standardní spravovaný Disk – 1 024 GB (zastaralé) 

**ID měřiče**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**Název měřiče**: P4   
**Jednotka**: Počet disků\*hodin   
**Poznámky k**: Premium Managed Disk – 32 GB (zastaralé) 

**ID měřiče**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**Název měřiče**: P6   
**Jednotka**: Počet disků\*hodin   
**Poznámky k**: Premium spravovaný Disk – 64 GB (zastaralé) 

**ID měřiče**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**Název měřiče**: P10   
**Jednotka**: Počet disků\*hodin   
**Poznámky k**: Premium Managed Disk – 128 GB (zastaralé)  

**ID měřiče**: 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**Název měřiče**: P15  
**Jednotka**: Počet disků\*hodin   
**Poznámky k**: Premium Managed Disk – 256 GB (zastaralé) 

**ID měřiče**: c7e7839c-293b-4761-ae4c-848eda91130b   
**Název měřiče**: P20   
**Jednotka**: Počet disků\*hodin   
**Poznámky k**: Premium Managed Disk – 512 GB (zastaralé) 

**ID měřiče**: 9f502103-adf4-4488-b494-456c95d23a9f   
**Název měřiče**: P30   
**Jednotka**: Počet disků\*hodin   
**Poznámky k**: Premium Managed Disk – 1 024 GB (zastaralé) 

**ID měřiče**: 8a409390-1913-40ae-917b-08d0f16f3c38   
**Název měřiče**: ActualStandardDiskSize   
**Jednotka**: Bajtů\*hodin      
**Poznámky k**: Skutečná velikost na disku spravovaných disků úrovně standard (zastaralé)  

**ID měřiče**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Název měřiče**: ActualPremiumDiskSize   
**Jednotka**: Bajtů\*hodin      
**Poznámky k**: Skutečná velikost na disku premium spravovaný disk (zastaralé) 

**ID měřiče**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Název měřiče**: ActualStandardSnapshotSize   
**Jednotka**: Bajtů\*hodin   
**Poznámky k**: Skutečná velikost na disku standardní spravovaný snímek (zastaralé) 

**ID měřiče**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**Název měřiče**: ActualPremiumSnapshotSize   
**Jednotka**: Bajtů\*hodin   
**Poznámky k**: Skutečná velikost na disku, spravované premium (zastaralé) 

### <a name="sql-rp"></a>Poskytovatele prostředků SQL
  
**ID měřiče**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Název měřiče**: DatabaseSizeHourSqlMeter  
**Jednotka**: MB\*hodin  
**Poznámky k**: Celkové kapacity databáze při vytváření. Při volání rozhraní API využití s denní členitosti, vrátí měřič MB počtem hodin.  
  
### <a name="mysql-rp"></a>Poskytovatele prostředků MySql   
  
**ID měřiče**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Název měřiče**: DatabaseSizeHourMySqlMeter  
**Jednotka**: MB\*hodin  
**Poznámky k**: Celkové kapacity databáze při vytváření. Při volání rozhraní API využití s denní členitosti, vrátí měřič MB počtem hodin.    
### <a name="key-vault"></a>Key Vault   
  
**ID měřiče**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Název měřiče**: Transakce služby Key Vault  
**Jednotka**: Počet požadavků na 10 000's  
**Poznámky k**: Počet požadavků rozhraní API REST přijme rovina dat trezoru klíčů.  
  
**ID měřiče**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Název měřiče**: Pokročilé klíče transakce  
**Jednotka**:  10 tisíc transakcí  
**Poznámky k**: Klíč RSA 3K/4 kB, ECC transakce. (preview).  
  
### <a name="app-service"></a>App Service   
  
**ID měřiče**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Název měřiče**: App Service  
**Jednotka**: Virtuální základní hodiny  
**Poznámky k**: Počet virtuálních jader, které se používají ke spouštění služby app service. Poznámka: Společnost Microsoft používá tento měřič platby prostřednictvím služby App Service ve službě Azure Stack. Poskytovatelé cloudových služeb můžete použít jiné služby App Service měřiče (dole) k výpočtu využití pro své klienty.  
  
**ID měřiče**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Název měřiče**: Funkce požadavků  
**Jednotka**: 10 požadavků  
**Poznámky k**: Celkový počet vyžádaných spuštění (za 10 spuštění). Spuštění se počítají pokaždé, když funkce běží v reakci na událost nebo vazbou aktivovaném.  
  
**ID měřiče**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Název měřiče**: Functions - Compute  
**Jednotka**:  GB-s  
**Poznámky k**:  Spotřeby prostředků měřené v gigabajtsekundách (GB/s). **Zjištěnými spotřeba prostředků** se počítá vynásobením průměrné měsíční velikosti paměti v Gigabajtech čas v milisekundách ke spuštění funkce. Paměť, kterou funkce využije měří se zaokrouhlením nejbližší 128 MB až do maximální velikosti paměti 1 536 MB, doba spuštění se počítá se zaokrouhlením na nejbližší 1 ms. Minimální doba spuštění a paměť pro spuštění jedné funkce je 100 ms a 128 mb.  
  
**ID měřiče**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Název měřiče**: Hodiny sdílené služby App Service  
**Jednotka**: 1 hodina  
**Poznámky k**: Za hodinu využívání horizontálních oddílů Plán služby App Service. Plány se měří na základě aplikací.  
  
**ID měřiče**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Název měřiče**: Hodiny Free služby App Service  
**Jednotka**: 1 hodina  
**Poznámky k**: Za hodinu použití bezplatný plán služby App Service. Plány se měří na základě aplikací.  
  
**ID měřiče**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Název měřiče**: Hodiny krátkodobého používání aplikační služby Standard  
**Jednotka**: 1 hodina  
**Poznámky k**: Vypočítá na základě velikosti a počtu instancí.  
  
**ID měřiče**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Název měřiče**: Hodiny střednědobého používání služby standardní aplikace  
**Jednotka**: 1 hodina  
**Poznámky k**: Vypočítá na základě velikosti a počtu instancí.  
  
**ID měřiče**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Název měřiče**: Hodiny velké standardní služby App Service  
**Jednotka**: 1 hodina  
**Poznámky k**: Vypočítá na základě velikosti a počtu instancí.  
  
### <a name="custom-worker-tiers"></a>Vlastní vrstvy pracovních procesů   
  
**ID měřiče**: *Vlastní vrstvy pracovních procesů*  
**Název měřiče**: Vlastní vrstvy pracovních procesů  
**Jednotka**: Hodiny  
**Poznámky k**: ID měřiče deterministické je vytvořen na základě skladové položky a název vrstvy pracovního procesu vlastní. Toto ID měřiče je jedinečný pro každou vrstvu pracovního procesu vlastní.  
  
**ID měřiče**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Název měřiče**: SNI SSL  
**Jednotka**: Per SNI SSL Binding  
**Poznámky k**: App Service podporuje dva typy připojení SSL: Připojení SSL pomocí indikace (SNI) název Server a připojení SSL na IP adresy. Připojení SSL typu SNI funguje v moderních prohlížečích, připojení SSL na základě IP adresy lze pak použít ve všech.  
  
**ID měřiče**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Název měřiče**: IP SSL  
**Jednotka**: Na IP adresu, na základě vazby SSL  
**Poznámky k**: App Service podporuje dva typy připojení SSL: Připojení SSL pomocí indikace (SNI) název Server a připojení SSL na IP adresy. Připojení SSL typu SNI funguje v moderních prohlížečích, připojení SSL na základě IP adresy lze pak použít ve všech.  
  
**ID měřiče**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Název měřiče**:  Webový proces  
**Jednotka**:  
**Poznámky k**: Počítá za aktivní lokality za hodinu.  
  
**ID měřiče**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Název měřiče**: Externí odchozí šířka pásma  
**Jednotka**: GB  
**Poznámky k**: Celkový počet příchozích bajty odpovědi na žádost a celkový počet odchozího požadavku bajtů + celkový počet příchozích FTP vyžádání bajty odpovědi a celkový počet příchozích webové nasazení bajty odpovědi na žádost.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsdocsmicrosoftcomazurebillingbilling-usage-rate-card-overviewazure-resource-usage-api-preview-currently-in-public-preview"></a>Jak Azure Stack využití rozhraní API pro porovnání [využití rozhraní API Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (aktuálně ve verzi public preview)?
* Rozhraní API využití Tenanta je konzistentní s Azure API s jednou výjimkou: *showDetails* příznak není aktuálně podporován ve službě Azure Stack.
* Rozhraní API využití zprostředkovatele platí jenom pro Azure Stack.
* V současné době [RateCard API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) , který je k dispozici v Azure, která není k dispozici ve službě Azure Stack.

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
