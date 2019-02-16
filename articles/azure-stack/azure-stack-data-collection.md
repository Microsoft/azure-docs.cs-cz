---
title: Zpracování dat a protokolů Azure Stack | Dokumentace Microsoftu
description: Další informace o tom, jak Azure Stack shromažďuje informace.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2019
ms.author: PatAltimore
ms.reviewer: chengwei
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 7beb9dc31e8987d69c2cf9cf64be36caf0e0e308
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56318921"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack protokolu a zákazník zpracování dat 
*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*  

Microsoft je v rozsahu procesoru nebo subprocessor osobních údajů v souvislosti s Azure Stack, společnost Microsoft neposkytuje na všechny zákazníky, efektivní 25 květen 2018, závazků v (a) v "zpracování osobních údajů; Zřízení GDPR"v oddílu"Podmínky ochrany dat" [podmínky Online služeb](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551078818&sdata=cpWsfZTBHpqEFr50DWQOryq342U8shgeFgMXVPQz5ug%3D&reserved=0) a (b) v Evropské unie obecné Data Protection nařízení podmínky v 4 přílohy [podmínky Online služeb](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551088813&sdata=bv1CBiaCnYmjiv6S0dFCbWEd4fNCkPBjBwgylNa%2FNt0%3D&reserved=0). 

Jak Azure Stack se nachází ve svých datacentrech, společnost Microsoft se kontroler Data pouze data, která se sdílí s Microsoftem prostřednictvím [diagnostiky](azure-stack-diagnostics.md), [Telemetrie](azure-stack-telemetry.md), a [fakturace](azure-stack-usage-reporting.md).  

## <a name="data-access-controls"></a>Řízení přístupu k datům 
Zaměstnanci Microsoftu, kteří jsou přiřazeni k prozkoumání případ podpory, bude udělen přístup jen pro čtení k šifrovaným datům. Zaměstnanci Microsoftu také mají přístup k nástrojům, které slouží k odstranění dat v případě potřeby. Veškerý přístup k zákaznickým datům je auditovány a protokolovány.  

Řízení přístupu k datům:
1.  Data se uchovávají pouze maximálně 90 dnů po zavření případ.
2.  Zákazník má vždy možnost, které mají data odebrat kdykoli během tohoto 90denního období.
3.  Zaměstnanci Microsoftu jsou udělen přístup k datům v případ od případu a pouze v případě potřeby umožňující podporu problém vyřešit. 
4.  V případě kde musí společnost Microsoft sdílet s partnery pro výrobce OEM, zákaznická data souhlasu zákazníka je povinný.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>Jaké žádosti subjektu údajů (PSÚ) ovládacích prvků zákazníci mají?
Jak už bylo zmíněno dříve, společnost Microsoft podporuje odstranění dat na vyžádání za žádost zákazníka. Zákazníci můžou požádat o, že naše pracovník podpory odstranili všechny jejich protokoly pro daný proces kdykoli výběru zákazníka, než data trvale smazána.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Microsoft upozorní zákazníky data se odstraní?
Pro akci odstranění automatizovaných datových (90 dnů po zavření případ) jsme není aktivně oslovení zákazníků a oznámení o odstranění. 

Pracovníka podpory společnosti Microsoft pro akci odstranění dat na vyžádání, má přístup k nástroji, kde se můžete iniciovat odstranění dat na vyžádání a můžete poskytují potvrzení na telefonu se zákazníkem po dokončení.

## <a name="diagnostic-data"></a>Diagnostická data
Jako součást procesu podpory, můžou operátoři Azure stacku [sdílet diagnostické protokoly](azure-stack-diagnostics.md) s týmy podpory a engineering Azure Stack pro usnadnění odstraňování potíží.

Skript pro zákazníky, které chcete shromáždit a nahrát požadované soubory protokolů diagnostiky a společnost Microsoft poskytuje nástroj. Po shromáždění, soubory protokolu se přenášejí přes HTTPS chráněné šifrované připojení k Microsoftu. Protože HTTPS zajišťuje šifrování přenosu, není žádné heslo potřebné pro šifrování během přenosu. Po jejich přijetí, protokoly jsou zašifrované a uložené, dokud se automaticky odstraní 90 dnů po uzavření případu podpory.

## <a name="telemetry-data"></a>Telemetrická data
[Azure Stack telemetrie](azure-stack-telemetry.md) automaticky nahrává data systému společnosti Microsoft prostřednictvím uživatelského rozhraní připojené. Operátoři Azure stacku mají ovládací prvky pro přizpůsobení telemetrických dat funkce a nastavení ochrany osobních údajů v každém okamžiku.

Microsoft nebude v úmyslu shromažďovat citlivá data, třeba čísla kreditních karet, uživatelská jména a hesla, e-mailové adresy nebo podobné citlivé informace. Pokud vyhodnotíme neúmyslně přijatých citlivé informace, budeme ji odstranit. 

## <a name="billing-data"></a>Fakturační údaje
[Fakturace Azure Stack](azure-stack-usage-reporting.md) využívá globální Azure fakturaci a využití kanálů a proto je v souladu s pokyny pro dodržování předpisů Microsoft.

Operátoři Azure stacku můžete nakonfigurovat služby Azure Stack k předávání informací o využití do Azure pro účely fakturace. Toto je nezbytné pro Azure Stack Několikauzlovými zákazníky, kteří zvolte model fakturace platit jako využití. Generování sestav o využívání je řízen nezávisle z telemetrických dat a nevyžaduje pro zákazníky s několika uzly, kteří se rozhodnou kapacitního modelu, nebo pro uživatele Azure Stack Development Kit. Pro tyto scénáře vytváření sestav využití můžete vypnout pomocí [registrační skript](azure-stack-usage-reporting.md).


## <a name="next-steps"></a>Další postup 
[Další informace o zabezpečení Azure stacku](azure-stack-security-foundations.md) 
