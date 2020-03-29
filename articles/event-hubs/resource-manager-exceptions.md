---
title: Azure Event Hubs – výjimky správce prostředků | Dokumenty společnosti Microsoft
description: Seznam výjimek Azure Event Hubs, které se objevily ve Správci prostředků Azure a navrhovaných akcích.
services: service-bus-messaging
documentationcenter: na
author: spelluru
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2019
ms.author: spelluru
ms.openlocfilehash: e6ee1137fce97cbe5a64aa5287223f6ba09dcf47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74936084"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure Event Hubs – výjimky správce prostředků
Tento článek uvádí výjimky generované při interakci s Azure Event Hubs pomocí Azure Resource Manager – prostřednictvím šablon nebo přímých volání.

> [!IMPORTANT]
> Tento dokument je často aktualizován. Zkontrolujte, zda neobsahuje aktualizace.

Následující části obsahují různé výjimky nebo chyby, které se objevují prostřednictvím Správce prostředků Azure.

## <a name="error-code-conflict"></a>Kód chyby: Konflikt

| Kód chyby | Přivrácený kód došlo k chybě. | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Konflikt | 40300 | Maximální počet prostředků typu EventHub bylo dosaženo nebo překročeno. Aktuální: #, Max povoleno: # | Obor názvů dosáhl [své kvóty](event-hubs-quotas.md) pro počet center událostí, které může obsahovat. | Odstraňte všechna nepoužívaná nebo nadřazená centra událostí z oboru názvů nebo zvažte upgrade na [vyhrazený cluster](event-hubs-dedicated-overview.md). |
| Konflikt | Žádná | Konfiguraci zotavení po havárii (DR) nelze odstranit, protože probíhá replikace. Převzetí služeb při selhání nebo přerušení párování před pokusem o odstranění konfigurace zotavení po Havárii. | [Probíhá geografická replikace,](event-hubs-geo-dr.md) takže konfiguraci nelze v tuto chvíli odstranit. | Chcete-li odblokovat odstranění konfigurace, počkejte, dokud nebude replikace dokončena, aktivujte převzetí služeb při selhání nebo přerušte párování geodr. |
| Konflikt | Žádná | Aktualizace oboru názvů se nezdařila s konfliktem v back-endu. | V tomto oboru názvů se právě provádí jiná operace. | Počkejte, až se aktuální operace dokončí, a potom akci opakujte. |

## <a name="error-code-429"></a>Kód chyby: 429

| Kód chyby | Přivrácený kód došlo k chybě. | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | Žádná | Zřizování oboru názvů v přechodu | V tomto oboru názvů se právě provádí jiná operace. | Počkejte, až se aktuální operace dokončí, a potom akci opakujte. |
| 429 | Žádná | Probíhá operace zotavení po havárii. | V tomto oboru názvů nebo párování se právě provádí operace [GeoDR.](event-hubs-geo-dr.md) | Počkejte na dokončení aktuální operace GeoDR a akci opakujte. |

## <a name="error-code-badrequest"></a>Kód chyby: BadRequest

| Kód chyby | Přivrácený kód došlo k chybě. | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Chybný požadavek | 40000 | PartitionCount nelze změnit pro centrum událostí. | Základní nebo standardní úroveň Azure Event Hubs nepodporuje změnu oddílů. | Vytvořte nové centrum událostí s hledaným počtem oddílů v základním nebo standardním oboru názvů vrstvy. Horizontální navýšení kapacity oddílů je podporováno pro [vyhrazené clustery](event-hubs-dedicated-overview.md). |
| Chybný požadavek | 40000 | Hodnota #pro MessageRetentionInDays není platná pro úroveň Basic. hodnota nesmí překročit "1" den (dny). | Obory názvů Centra událostí základní vrstvy podporují pouze uchovávání zpráv až 1 den. | Pokud je žádoucí více než jeden den uchovávání zpráv, [vytvořte standardní obor názvů Event Hubs](event-hubs-create.md). | 
| Chybný požadavek | Žádná | Zadaný název není k dispozici. | Názvy oborů názvů musí být jedinečné a zadaný název je již přijat. | Pokud jste vlastníkem existujícího oboru názvů se zadaným názvem, můžete jej odstranit, což způsobí ztrátu dat. Potom akci opakujte se stejným názvem. Pokud není bezpečné obor názvů odstranit (nebo nejste vlastníkem), zvolte jiný název oboru názvů. |
| Chybný požadavek | Žádná | Zadané předplatné dosáhlo své kvóty oborů názvů. | Vaše předplatné dosáhlo [kvóty](event-hubs-quotas.md) pro počet oborů názvů, které může obsahovat. | Zvažte odstranění nepoužívaných oborů názvů v tomto předplatném, vytvoření jiného předplatného nebo upgrade na [vyhrazený cluster](event-hubs-dedicated-overview.md). |
| Chybný požadavek | Žádná | Nelze aktualizovat obor názvů, který je sekundární | Obor názvů nelze aktualizovat, protože se jedná o sekundární obor názvů v [geodr párování](event-hubs-geo-dr.md). | V případě potřeby proveďte změnu primárního oboru názvů v tomto párování. V opačném případě přerušíte párování GeoDR, abyste změnu vytvořili. |
| Chybný požadavek | Žádná | Nelze nastavit automatické nafukování v základní skladové jednotce | Automatické nafouknutí nelze povolit v oborech názvů centra událostí základní vrstvy. | Chcete-li [povolit automatické nafukování](event-hubs-auto-inflate.md) v oboru názvů, ujistěte se, že je standardní vrstvy. |
| Chybný požadavek | Žádná | K vytvoření oboru názvů není dostatečná kapacita. Obraťte se na správce centra událostí. | Vybraná oblast je na kapacitě a nelze vytvořit více oborů názvů. | Vyberte jinou oblast, ve které se bude ubytovávat obor názvů. |
| Chybný požadavek | Žádná | Operaci nelze provést u typu entity ConsumerGroup, protože obor názvů "název oboru názvů" používá úroveň Basic.  | Obory názvů Centra událostí základní vrstvy mají [quota]((event-hubs-quotas.md#event-hubs-basic-and-standard---quotas-and-limits) jedné skupiny spotřebitelů (výchozí). Vytváření dalších skupin spotřebitelů není podporováno. | Pokračujte v používání výchozí skupiny spotřebitelů ($Default) nebo pokud je potřeba další, zvažte místo toho použití oboru názvů Centra událostí úrovně standardu. | 
| Chybný požadavek | Žádná | Název oboru názvů neexistuje. | Zadaný obor názvů nebyl nalezen. | Zkontrolujte, zda je název oboru názvů správný a zda je k dispozici ve vašem předplatném. Pokud tomu tak není, [vytvořte obor názvů Event Hubs](event-hubs-create.md). | 
| Chybný požadavek | Žádná | Vlastnost umístění prostředku neodpovídá jeho obsahující obor názvů. | Vytvoření centra událostí v určité oblasti se nezdařilo, protože neodpovídá oblasti oboru názvů. | Zkuste vytvořit centrum událostí ve stejné oblasti jako obor názvů. | 

## <a name="error-code-internal-server-error"></a>Kód chyby: Vnitřní chyba serveru

| Kód chyby | Přivrácený kód došlo k chybě. | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Vnitřní chyba serveru | Žádná | Vnitřní chyba serveru. | Služba Event Hubs měla vnitřní chybu. | Opakujte selhání operace. Pokud operace nadále selhat, obraťte se na podporu. |