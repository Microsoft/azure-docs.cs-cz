---
title: Azure Media Services vysoká dostupnost
description: Převzetí služeb při selhání pro sekundární Media Services účet, pokud dojde k výpadku nebo selhání regionálního datacentra.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: e2212c2ad5a0ab1899d281e99a89b33a2fc0298f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202283"
---
# <a name="azure-media-services-high-availability-guidance"></a>Doprovodné materiály k Azure Media Services pro vysokou dostupnost

Služba Azure Media Services Encoding je místní platforma pro zpracování dávek a aktuálně není určená pro vysokou dostupnost v rámci jedné oblasti. Služba kódování aktuálně neposkytuje okamžité převzetí služeb při selhání, pokud dojde k výpadku místního datového centra nebo selhání základní součásti nebo závislých služeb (například úložiště, SQL atd.).  Tento článek vysvětluje, jak nasadit Media Services pro udržení architektury s vysokou dostupností s převzetím služeb při selhání a zajištění optimální dostupnosti pro vaše aplikace. 

Podle pokynů a osvědčených postupů popsaných v článku snížíte riziko chyb při kódování, zpoždění a minimalizaci doby obnovení, pokud dojde k výpadku v jedné oblasti.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Jak sestavit systém kódování v různých oblastech

* [Vytvořte](create-account-cli-how-to.md) dva (nebo více) Azure Media Services účty.
* Přihlaste se k odběru zpráv **JobStateChange** v každém účtu.

    * V Media Services V3 se provádí prostřednictvím Azure Event Grid. Další informace naleznete v tématu:
    
       * [Příklady Event Grid](../../event-grid/receive-events.md) 
       * [Azure Event Grid schémat pro Media Services události](media-services-event-schemas.md), 
       * [Zaregistrujte se na události prostřednictvím Azure Portal nebo rozhraní](reacting-to-media-services-events.md) příkazového řádku (můžete to provést také pomocí sady EventGrid Management SDK).
       * [Microsoft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (podporující události Media Services nativně). 
       
        Můžete také spotřebovávat Event Grid události prostřednictvím Azure Functions.
    * V Media Services V2 se to provádí přes [NotificationEndpoints](../previous/media-services-dotnet-check-job-progress-with-webhooks.md).
* Při [vytváření úlohy](transforms-jobs-concept.md):

    * Náhodně vyberte účet ze seznamu aktuálně používaných účtů (Tento seznam bude obvykle obsahovat oba účty, ale pokud se zjistí problémy, může obsahovat jenom jeden účet). Pokud je seznam prázdný, vyvolejte výstrahu, aby mohl operátor prozkoumat.
    * Obecné pokyny potřebujete jednu [rezervovanou jednotku médií](media-reserved-units-cli-how-to.md) na úlohu nebo [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (Pokud nepoužíváte [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) v V3).
    * Získejte počet [rezervovaných jednotek médií](media-reserved-units-cli-how-to.md) (MRUs) pro vybraný účet. Pokud aktuální počet **rezervovaných jednotek médií** již není na maximální hodnotě, přidejte počet MRUs potřebných pro úlohu a aktualizujte službu. Pokud je rychlost odeslání úlohy vysoká a často se dotazuje na MRUs, abyste zjistili, že jste na maximum, použijte distribuovanou mezipaměť pro hodnotu s přiměřeným časovým limitem.
    * Udržujte si počet neletových úloh.
* Když vaše obslužná rutina JobStateChange získá oznámení, že úloha dosáhla naplánovaného stavu, zaznamenejte čas, který vstoupí do stavu plánu, a na použitou oblast nebo účet.    
* Když vaše obslužná rutina JobStateChange dostane oznámení, že úloha dosáhla stavu zpracování, označte záznam pro úlohu jako zpracování.
* Když vaše obslužná rutina JobStateChange dostane oznámení, že úloha dosáhla stavu dokončeno/chyba, označit záznam pro úlohu jako konečný a snížit počet úloh inletu. Získejte počet rezervovaných jednotek médií pro vybraný účet a porovnejte aktuální číslo MRU s počtem úloh inletu. Pokud je počet vašich letů nižší než počet naposledy použitých položek, požádejte ho a aktualizujte službu.
* Mít samostatný proces, který pravidelně vyhledává vaše záznamy o úlohách. Pokud máte úlohy v plánovaném stavu, které nepokročily do stavu zpracování v přiměřené době pro danou oblast, odeberte tuto oblast ze seznamu aktuálně používaných účtů.

    * V závislosti na vašich obchodních požadavcích se můžete rozhodnout tyto úlohy okamžitě zrušit a znovu je odeslat na jiný účet. Nebo můžete jiným uživatelům poskytnout další čas na přechod k dalšímu stavu.   
    * Po určité době přidejte účet zpátky do aktuálně používaného seznamu (s předpokladem, že se oblast obnovila).
    
Pokud zjistíte, že počet MRU je thrashing nahoru a dolů, přesuňte logiku snížení na pravidelný úkol. Aby bylo možné zjistit, jestli je potřeba aktualizovat MRUs, je třeba, aby logika předběžných úloh porovnala počet letů do aktuálního počtu.

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Jak vytvořit streamování mezi oblastmi videa na vyžádání 

* Streamování mezi jednotlivými oblastmi videa na vyžádání zahrnuje duplikaci [prostředků](assets-concept.md), [zásady klíčů obsahu](content-key-policy-concept.md) (Pokud se používají), [zásady streamování](streaming-policy-concept.md)a [Lokátory streamování](streaming-locators-concept.md). 
* V obou oblastech budete muset vytvořit zásady a udržovat je aktuální. 
* Když vytváříte Lokátory streamování, budete chtít použít stejnou hodnotu ID lokátoru, hodnotu ID ContentKey a hodnotu ContentKey.  
* Pokud obsah kódujete, doporučuje se kódovat obsah v oblasti A a publikovat ho, pak zkopírovat kódovaný obsah do oblasti B a publikovat ho pomocí stejných hodnot jako z oblasti A.
* Traffic Manager můžete používat na názvech hostitelů pro zdroj a službu pro doručování klíčů (v Media Services konfigurace bude vypadat jako adresa URL vlastního klíčového serveru).

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu](create-account-cli-how-to.md)
* Podívejte se na [ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
