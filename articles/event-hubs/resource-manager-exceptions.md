---
title: Výjimky pro Azure Event Hubs – Správce prostředků | Microsoft Docs
description: Seznam výjimek Event Hubs Azure, které jsou Surface Azure Resource Manager a navrhovaných akcí
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: cec24696d0d49ba408860f6562c34dd14876c311
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334204"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Výjimky pro Azure Event Hubs – Správce prostředků
Tento článek uvádí výjimky vygenerované při interakci s Azure Event Hubs s využitím šablon nebo přímých volání prostřednictvím šablon Azure Resource Manager.

> [!IMPORTANT]
> Tento dokument se často aktualizuje. Vraťte se prosím na aktualizace.

V následujících oddílech jsou uvedeny různé výjimky a chyby, které jsou surfované prostřednictvím Azure Resource Manager.

## <a name="error-code-conflict"></a>Kód chyby: konflikt

| Kód chyby | Chybový kód | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Konflikt | 40300 | Byl dosažen nebo překročen maximální počet prostředků typu EventHub. Skutečný: #, max. povoleno: # | Obor názvů dosáhl své [kvóty](event-hubs-quotas.md) pro počet Event Hubs, které může obsahovat. | Odstraňte z oboru názvů všechna nepoužívaná nebo nadbytečná centra událostí nebo zvažte upgrade na [vyhrazený cluster](event-hubs-dedicated-overview.md). |
| Konflikt | žádné | Konfiguraci zotavení po havárii (DR) nelze odstranit, protože probíhá replikace. Před pokusem o odstranění konfigurace DR došlo k převzetí služeb při selhání nebo přerušení. | Probíhá [replikace GeoDR](event-hubs-geo-dr.md) , takže tuto konfiguraci nejde v tuto chvíli odstranit. | Chcete-li odblokovat odstranění konfigurace, buď počkejte na dokončení replikace, aktivujte převzetí služeb při selhání nebo přerušit párování GeoDR. |
| Konflikt | žádné | Aktualizace oboru názvů se nezdařila kvůli konfliktu v back-endu. | V tomto oboru názvů se právě provádí jiná operace. | Počkejte, než se aktuální operace dokončí, a pak to zkuste znovu. |

## <a name="error-code-429"></a>Kód chyby: 429

| Kód chyby | Chybový kód | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | žádné | Zřizování oboru názvů v přechodu | V tomto oboru názvů se právě provádí jiná operace. | Počkejte, než se aktuální operace dokončí, a pak to zkuste znovu. |
| 429 | žádné | Probíhá operace zotavení po havárii. | V tomto oboru názvů nebo párování se právě provádí operace [GeoDR](event-hubs-geo-dr.md) . | Počkejte, dokud se aktuální operace GeoDR nedokončí, a pak to zkuste znovu. |

## <a name="error-code-badrequest"></a>Kód chyby: důvodu chybného požadavku

| Kód chyby | Chybový kód | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Důvodu chybného požadavku | 40000 | PartitionCount se nedá změnit v centru událostí. | Úroveň Basic nebo Standard služby Azure Event Hubs nepodporuje změnu oddílů. | Vytvořte nové centrum událostí s požadovaným počtem oddílů v oboru názvů úrovně Basic nebo Standard. Škálování oddílu na více instancí se podporuje u [vyhrazených clusterů](event-hubs-dedicated-overview.md). |
| Důvodu chybného požadavku | 40000 | Hodnota ' # ' pro MessageRetentionInDays není platná pro úroveň Basic. hodnota nemůže být delší než 1 den. | Obory názvů úrovně Basic Event Hubs podporují pouze uchovávání zpráv po dobu až 1 dne. | Pokud je žádoucí více než jeden den uchovávání zpráv, [vytvořte obor názvů standard Event Hubs](event-hubs-create.md). | 
| Důvodu chybného požadavku | žádné | Zadaný název není k dispozici. | Názvy oborů názvů musí být jedinečné a zadaný název je již obsazen. | Pokud jste vlastníkem stávajícího oboru názvů se zadaným názvem, můžete ho odstranit, což způsobí ztrátu dat. Pak to zkuste znovu se stejným názvem. Pokud obor názvů není bezpečné odstranit (nebo nejste vlastníkem), vyberte jiný název oboru názvů. |
| Důvodu chybného požadavku | žádné | Zadané předplatné dosáhlo kvóty oborů názvů. | Vaše předplatné dosáhlo [kvóty](event-hubs-quotas.md) pro počet oborů názvů, které může uchovávat. | Zvažte možnost Odstranit nepoužívané obory názvů v tomto předplatném, vytvořit jiné předplatné nebo upgradovat na [vyhrazený cluster](event-hubs-dedicated-overview.md). |
| Důvodu chybného požadavku | žádné | Nejde aktualizovat obor názvů, který je sekundární. | Obor názvů se nedá aktualizovat, protože se jedná o sekundární obor názvů ve [párování GeoDR](event-hubs-geo-dr.md). | V případě potřeby proveďte změnu na primární obor názvů v tomto párování. Jinak přerušte párování GeoDR, aby se změna provedla. |
| Důvodu chybného požadavku | žádné | Nejde nastavit automatické rozploché v základní skladové jednotce (SKU). | Automatické vystavení nelze povolit na úrovni Basic Event Hubs obory názvů. | Pokud chcete [Povolit automatické rozplochení](event-hubs-auto-inflate.md) v oboru názvů, ujistěte se, že je na úrovni Standard. |
| Důvodu chybného požadavku | žádné | Pro vytvoření oboru názvů není k dispozici dostatek kapacity. Obraťte se na správce Event Hubs. | Vybraná oblast má kapacitu a další obory názvů nelze vytvořit. | Vyberte jinou oblast, do které se má obor názvů zahouse. |
| Důvodu chybného požadavku | žádné | Operaci nejde u typu entity ' Consumer ' provést, protože obor názvů ' název oboru názvů ' používá ' základní ' úroveň.  | Obory názvů úrovně Basic Event Hubs mají kvótu (Event-hub-quotas.md) jedné skupiny příjemců (výchozí nastavení). Vytváření více skupin uživatelů se nepodporuje. | Pokračujte v používání výchozí skupiny uživatelů ($Default), nebo pokud je potřeba víc, zvažte místo toho použití Event Hubs oboru názvů úrovně Standard. | 
| Důvodu chybného požadavku | žádné | Obor názvů "název oboru názvů" neexistuje. | Zadaný obor názvů nebyl nalezen. | Dvakrát ověřte, že je název oboru názvů správný a najdete ho v předplatném. Pokud ne, [vytvořte obor názvů Event Hubs](event-hubs-create.md). | 
| Důvodu chybného požadavku | žádné | Vlastnost Location prostředku se neshoduje s oborem názvů, který ho obsahuje. | Vytvoření centra událostí v konkrétní oblasti se nezdařilo, protože se neshodovalo s oblastí oboru názvů. | Zkuste vytvořit centrum událostí ve stejné oblasti jako obor názvů. | 

## <a name="error-code-internal-server-error"></a>Kód chyby: interní chyba serveru

| Kód chyby | Chybový kód | Chybová zpráva | Popis | Doporučení |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Vnitřní chyba serveru | žádné | Došlo k vnitřní chybě serveru. | Došlo k vnitřní chybě služby Event Hubs. | Opakujte operaci, která selhala. Pokud se operace stále nedaří, obraťte se na podporu. |