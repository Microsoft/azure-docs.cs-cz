---
title: Scheduler vysokou dostupnost a spolehlivost
description: Scheduler vysokou dostupnost a spolehlivost
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2016
ms.author: deli
ms.openlocfilehash: 7e7fe49de7814b6058468d630f8638720e5864f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866090"
---
# <a name="scheduler-high-availability-and-reliability"></a>Scheduler vysokou dostupnost a spolehlivost
## <a name="azure-scheduler-high-availability"></a>Vysoká dostupnost Azure Scheduler
Jako základní platformu Azure služby Azure Scheduler je vysoce dostupný a funkce nasazení geograficky redundantní služby a úlohy místní geografické replikace.

### <a name="geo-redundant-service-deployment"></a>Nasazení služby geograficky redundantní
Azure Scheduler je k dispozici prostřednictvím uživatelského rozhraní v téměř každé geografické oblasti, která je v Azure ještě dnes. Seznam oblastí, které jsou k dispozici ve službě Azure Scheduler je [tady](https://azure.microsoft.com/regions/#services). Pokud datového centra v hostované oblasti vykresleno není k dispozici, jsou možnosti převzetí služeb při selhání Azure Scheduler tak, že služba je k dispozici z jiného datového centra.

### <a name="geo-regional-job-replication"></a>Geograficky místní úlohy replikace
Ne jenom je Azure Scheduler front-end pro požadavky na správu, ale vlastní úlohu k dispozici je také geograficky replikované. Po výpadku v jedné oblasti Azure Scheduler převezme a zajistí, že se úlohu spustit z jiného datového centra ve spárované zeměpisné oblasti.

Například pokud jste vytvořili úlohu v jihu USA, Azure Scheduler automaticky replikuje této úlohy v Severní jihu USA. Když dojde k selhání v jihu USA, Azure Scheduler zajistí úlohy je z Sever střední USA. 

![][1]

V důsledku toho Azure Scheduler zajistí, že vaše data zůstává v rámci stejné zeměpisné oblasti širší v případě selhání Azure. V důsledku toho nemusí duplicitní úlohu jenom k přidání vysoká dostupnost – Azure Scheduler automaticky poskytuje vysokou dostupnost funkcí pro úlohy.

## <a name="azure-scheduler-reliability"></a>Spolehlivost Azure Scheduler
Azure Scheduler zaručuje vlastní vysokou dostupnost a přistupují k úlohy vytvořené uživatelem. Například vaší práce může vyvolat koncový bod protokolu HTTP, který je k dispozici. Azure Scheduler přesto pokusí provést úlohu úspěšně, tím, že jste alternativní možnosti, jak nakládat s chybou. Azure Scheduler k tomu dvěma způsoby:

### <a name="configurable-retry-policy-via-retrypolicy"></a>Konfigurovat zásady opakujte prostřednictvím "retryPolicy"
Azure Scheduler umožňuje konfigurovat zásady opakování. Ve výchozím nastavení pokud úloha selže, Scheduler pokusí úlohu znovu čtyři vícekrát v intervalech 30 sekund. Můžete znovu nakonfigurovat tyto zásady opakování být agresivnější (například desetkrát, v intervalech 30 sekund) nebo volnější (například dvakrát, v denních intervalech.)

Jako příklad když to může pomoct můžete vytvořit úlohu, která spouští jednou za týden a vyvolá koncový bod HTTP. Pokud koncový bod HTTP je vypnutý po několik hodin, když vaše úloha běží, nemusí chcete čekat jeden další týden pro úlohu znovu spustit, protože i výchozí zásady opakovaných pokusů se nezdaří. V takových případech může znovu nakonfigurovat zásady opakování standardní opakovat každé tři hodiny, (například) namísto každých 30 sekund.

Zjistěte, jak nakonfigurovat zásady opakovaných pokusů, najdete v tématu [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Alternativní koncový bod možnosti konfigurace: prostřednictvím "errorAction"
Pokud cílový koncový bod pro úlohu Azure Scheduler zůstane nedostupný, Azure Scheduler spadne zpět na alternativní koncový bod zpracování chyb po následující jeho zásady opakování. Pokud je nakonfigurovaný koncový bod alternativní zpracování chyb, Azure Scheduler jej spustí. Alternativní koncový bod jsou vysoce dostupné při krátkodobém selhání vlastní úlohy.

Jako příklad na obrázku níže Azure Scheduler odpovídá jeho zásady opakovaných pokusů a stiskněte tlačítko New Yorku webové služby. Po opakované pokusy selžou, zkontroluje, jestli je alternativním. Potom přejde dopředu a spustí zasílání požadavků na alternativní s stejné zásady opakování.

![][2]

Všimněte si, že platí stejné zásady opakovaných pokusů pro původní akce a akce alternativní chyby. Je také možné, že typ akce akce alternativní chyby se liší od hlavní akce typ akce. Například při hlavní akce může být vyvolání koncový bod protokolu HTTP, Chyba akce místo pravděpodobně fronty úložiště, fronty service bus nebo akce témata sběrnice služby, která provádí protokolování chyb.

Naučte se konfigurovat alternativní koncový bod, najdete v tématu [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Viz také
 [Co je Scheduler?](scheduler-intro.md)

 [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)

 [Úvod do používání Scheduleru na portálu Azure Portal](scheduler-get-started-portal.md)

 [Plány a fakturace v Azure Scheduleru](scheduler-plans-billing.md)

 [Sestavení komplexních plánů a pokročilé opakování v Azure Scheduleru](scheduler-advanced-complexity.md)

 [REST API Azure Scheduleru – referenční informace](https://msdn.microsoft.com/library/mt629143)

 [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)

 [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)

 [Odchozí ověření Azure Scheduleru](scheduler-outbound-authentication.md)

[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
