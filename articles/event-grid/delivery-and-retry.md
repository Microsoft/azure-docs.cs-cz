---
title: Doručování a opakování sítě Událostí Azure
description: Popisuje, jak Azure Event Grid doručuje události a jak zpracovává nedoručené zprávy.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: spelluru
ms.openlocfilehash: dda2fd98c4c0d330059156a5ec00baa97ffaf627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921058"
---
# <a name="event-grid-message-delivery-and-retry"></a>Doručení a opakování zprávy v gridu událostí

Tento článek popisuje, jak Azure Event Grid zpracovává události, když není potvrzeno doručení.

Event Grid poskytuje trvalé doručení. Doručuje každou zprávu alespoň jednou pro každé předplatné. Události jsou odesílány do registrovaného koncového bodu každého předplatného okamžitě. Pokud koncový bod nepotvrdí přijetí události, Event Grid opakuje doručení události.

## <a name="batched-event-delivery"></a>Dávková dodávka událostí

Event Grid ve výchozím nastavení odesílá každou událost jednotlivě odběratelům. Odběratel obdrží pole s jednou událostí. Mřížku událostí můžete nakonfigurovat tak, aby dávkové události pro doručování pro lepší výkon PROTOKOLU HTTP ve scénářích s vysokou propustností.

Dávková dodávka má dvě nastavení:

* **Maximální počet událostí na dávku** – maximální počet událostí, které bude Event Grid doručován na dávku. Toto číslo nebude nikdy překročeno, ale může být doručeno méně událostí, pokud v době publikování nejsou k dispozici žádné jiné události. Event Grid nezpozdí události, aby bylo možné vytvořit dávku, pokud je k dispozici méně událostí. Musí být mezi 1 a 5000.
* **Upřednostňovaná velikost dávky v kilobajtech** – cílový strop pro velikost dávky v kilobajtech. Podobně jako maximální počet událostí může být velikost dávky menší, pokud v době publikování není k dispozici více událostí. Je možné, že dávka je větší než upřednostňovaná velikost *dávky, pokud* je jedna událost větší než upřednostňovaná velikost. Například pokud upřednostňovaná velikost je 4 KB a událost 10 KB je posunuta do mřížky událostí, událost 10 KB bude stále doručena ve vlastní dávce, nikoli vynechána.

Dávkové doručení v nakonfigurované na základě předplatného na základě událostí prostřednictvím portálu, cli, PowerShell nebo Sady SDK.

### <a name="azure-portal"></a>Portál Azure: 
![Nastavení dávkového doručení](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
Při vytváření odběru událostí použijte následující parametry: 

- **maximální počet událostí na dávku** - maximální počet událostí v dávce. Musí být číslo mezi 1 a 5000.
- **upřednostňovaná velikost dávky v kilobajtech** – upřednostňovaná velikost dávky v kilobajtech. Musí být číslo mezi 1 a 1024.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Další informace o používání Azure CLI s Event Grid najdete v [tématu Směrování událostí úložiště do koncového bodu webu pomocí Azure CLI](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Plán opakování a doba trvání

Event Grid čeká 30 sekund na odpověď po doručení zprávy. Po 30 sekundách, pokud koncový bod neodpověděl, zpráva je zařazena do fronty pro opakování. Event Grid používá exponenciální zásady opakování backoff pro doručení událostí. Event Grid opakuje doručení na následující plán na základě nejlepší úsilí:

- 10 sekund
- 30 sekund
- 1 minuta
- 5 minut
- 10 minut
- 30 minut
- 1 hodina
- Každou hodinu po dobu až 24 hodin

Pokud koncový bod odpoví do 3 minut, Event Grid se pokusí odebrat událost z fronty opakování na základě nejlepší úsilí, ale duplicity mohou být stále přijata.

Event Grid přidá malou randomizaci do všech kroků opakování a může oportunisticky přeskočit některé opakování, pokud koncový bod je trvale není v pořádku, dolů na dlouhou dobu nebo se zdá být zahlceni.

Pro deterministické chování nastavte čas události na živé a maximální počet pokusů o doručení v [zásadách opakování předplatného](manage-event-delivery.md).

Ve výchozím nastavení vyprší platnost aplikace Event Grid všechny události, které nejsou doručeny do 24 hodin. [Zásady opakování](manage-event-delivery.md) můžete přizpůsobit při vytváření odběru událostí. Zadáte maximální počet pokusů o doručení (výchozí je 30) a čas události k životu (výchozí hodnota je 1440 minut).

## <a name="delayed-delivery"></a>Zpožděné doručení

Jako koncový bod dojde selhání doručení, Event Grid začne zpozdit doručení a opakování událostí do tohoto koncového bodu. Například pokud prvních 10 událostí publikovaných do koncového bodu nezdaří, Event Grid bude předpokládat, že koncový bod dochází k problémům a zpozdí všechny následné opakování *a nové* dodávky na nějakou dobu – v některých případech až několik hodin.

Funkční účel zpožděné doručení je chránit nefunkční koncové body, stejně jako systém Event Grid. Bez back-off a zpoždění doručení do nefunkčních koncových bodů, Event Grid zásady opakování a funkce svazku můžete snadno zahltit systém.

## <a name="dead-letter-events"></a>Události nedoručených písmen

Když Event Grid nemůže doručit událost, může odeslat nedoručenou událost do účtu úložiště. Tento proces se označuje jako nedoručené. Ve výchozím nastavení se mřížka událostí nezapne nedoručené. Chcete-li povolit, musíte zadat účet úložiště pro uložení nedoručených událostí při vytváření předplatného událostí. Můžete vyžádat události z tohoto účtu úložiště k vyřešení dodávek.

Event Grid odešle událost do umístění nedoručených písmen, když vyzkoušela všechny pokusy o opakování. Pokud Event Grid obdrží 400 (Bad Request) nebo 413 (Požadavek entity příliš velké) kód odpovědi, okamžitě odešle událost do koncového bodu nedoručené písmeno. Tyto kódy odpovědí označují, že doručení události nebude nikdy úspěšné.

Mezi posledním pokusem o doručení události a doručením do umístění nedoručených písmen je pět minut. Toto zpoždění je určen ke snížení počtu operací úložiště objektů blob. Pokud umístění nedoručených adres není k dispozici po dobu čtyř hodin, událost je vynechána.

Před nastavením umístění nedoručených písmen musíte mít účet úložiště s kontejnerem. Koncový bod pro tento kontejner při vytváření předplatného událostí. Koncový bod je ve formátu:`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Můžete chtít být upozorněni, když událost byla odeslána do umístění nedoručeného dopisu. Chcete-li použít Event Grid reagovat na nedoručené události, [vytvořte odběr událostí](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) pro úložiště objektů blob nedoručených písmen. Pokaždé, když vaše úložiště objektů blob nedoručených písmen obdrží nedoručenou událost, Event Grid upozorní obslužnou rutinu. Obslužná rutina odpoví akce, které chcete provést pro vyrovnání nedoručených událostí.

Příklad nastavení umístění nedoručených adres naleznete v tématu [Zásady nedoručených a opakování .](manage-event-delivery.md)

## <a name="message-delivery-status"></a>Stav doručení zprávy

Event Grid používá kódy odpovědí HTTP k potvrzení příjmu událostí. 

### <a name="success-codes"></a>Kódy úspěšnosti

Event Grid považuje za úspěšné dodávky **pouze** následující kódy odpovědí HTTP. Všechny ostatní stavové kódy jsou považovány za neúspěšné dodávky a budou opakovány nebo nedoručeny podle potřeby. Po obdržení úspěšného stavového kódu událost grid považuje doručení za dokončené.

- 200 OK
- 201 Vytvořeno
- 202 Přijato
- 203 Neautoritativní informace
- 204 Žádný obsah

### <a name="failure-codes"></a>Chybové kódy

Všechny ostatní kódy, které nejsou ve výše uvedené sadě (200-204), jsou považovány za chyby a budou opakovány. Některé mají specifické zásady opakování s vázané na ně uvedené níže, všechny ostatní postupujte podle standardníexponenciální back-off model. Je důležité mít na paměti, že vzhledem k vysoce paralelizované povaze architektury Event Grid je chování opakování nedeterministické. 

| Kód stavu | Opakování chování |
| ------------|----------------|
| 400 Chybný požadavek | Opakujte po 5 minutách nebo více (Deadletter okamžitě, pokud deadletter nastavení) |
| 401 Neautorizováno | Opakujte akci po 5 minutách nebo více |
| 403 Zakázáno | Opakujte akci po 5 minutách nebo více |
| 404 Nenalezeno | Opakujte akci po 5 minutách nebo více |
| 408 – Časový limit žádosti | Opakujte akci po 2 minutách nebo více |
| 413 Entita požadavku je příliš velká. | Opakujte po 10 sekundách nebo více (Deadletter okamžitě, pokud deadletter nastavení) |
| 503 – Nedostupná služba | Opakování po 30 sekundách nebo více |
| Všechny ostatní | Opakujte akci po 10 sekundách nebo více |


## <a name="next-steps"></a>Další kroky

* Stav dodávek událostí najdete v [tématu Sledování doručování zpráv v programu Event Grid](monitor-event-delivery.md).
* Možnosti doručování událostí najdete v tématu [Zásady nedoručených a opakování](manage-event-delivery.md).
