---
title: Azure Media Services kódování s vysokou dostupností
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
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78934190"
---
# <a name="media-services-high-availability-encoding"></a>Media Services kódování s vysokou dostupností 

Služba Azure Media Services Encoding je místní platforma pro zpracování dávek a aktuálně není určená pro vysokou dostupnost v rámci jedné oblasti. Služba kódování aktuálně neposkytuje okamžité převzetí služeb při selhání, pokud dojde k výpadku místního datového centra nebo selhání základní komponenty nebo závislých služeb (například úložiště, SQL). Tento článek vysvětluje, jak nasadit Media Services pro udržení architektury s vysokou dostupností s převzetím služeb při selhání a zajištění optimální dostupnosti pro vaše aplikace.

Podle pokynů a osvědčených postupů popsaných v článku snížíte riziko chyb při kódování, zpoždění a minimalizaci doby obnovení, pokud dojde k výpadku v jedné oblasti.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Jak sestavit systém kódování v různých oblastech

* [Vytvořte](create-account-cli-how-to.md) dva (nebo více) Azure Media Services účty.

    Tyto dva účty se musí nacházet v různých oblastech. Další informace najdete v tématu [oblasti, ve kterých je nainstalovaná služba Azure Media Services](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Nahrajte médium do stejné oblasti, ze které plánujete úlohu odeslat. Další informace o tom, jak spustit kódování, najdete v tématu [Vytvoření vstupu úlohy z adresy URL https](job-input-from-http-how-to.md) nebo [Vytvoření vstupu úlohy z místního souboru](job-input-from-local-file-how-to.md).

    Pokud budete později potřebovat [úlohu](transforms-jobs-concept.md) znovu odeslat do jiné oblasti, můžete pomocí JobInputHttp nebo pomocí funkce [copy-BLOB](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) zkopírovat data z kontejneru zdrojových prostředků do kontejneru assetů v alternativní oblasti.
* Přihlaste se k odběru zpráv JobStateChange v každém účtu prostřednictvím Azure Event Grid. Další informace naleznete v tématu:

    * [Ukázka zvukové analýzy](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) , která ukazuje, jak monitorovat úlohu pomocí Azure Event Grid, včetně přidání náhradního zařízení pro případ, že Azure Event Grid zprávy z nějakého důvodu jsou zpožděny.
    * [Azure Event Grid schémat pro události Media Services](media-services-event-schemas.md)
    * [Zaregistrujte se na události prostřednictvím Azure Portal nebo rozhraní](reacting-to-media-services-events.md) příkazového řádku (můžete to provést také pomocí sady EventGrid Management SDK).
    * [Microsoft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (podporující události Media Services nativně).

    Můžete také spotřebovávat Event Grid události prostřednictvím Azure Functions.
* Při vytváření [úlohy](transforms-jobs-concept.md):

    * Náhodně vyberte účet ze seznamu aktuálně používaných účtů (Tento seznam bude obvykle obsahovat oba účty, ale pokud se zjistí problémy, může obsahovat jenom jeden účet). Pokud je seznam prázdný, vyvolejte výstrahu, aby mohl operátor prozkoumat.
    * Obecné pokyny potřebujete jednu [rezervovanou jednotku médií](media-reserved-units-cli-how-to.md) na [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (Pokud nepoužíváte [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) , kde se doporučuje 3 rezervované jednotky médií na JobOutput).
    * Získejte počet rezervovaných jednotek médií (MRUs) pro vybraný účet. Pokud aktuální počet **rezervovaných jednotek médií** již není na maximální hodnotě, přidejte počet MRUs potřebných pro úlohu a aktualizujte službu. Pokud je rychlost odeslání úlohy vysoká a často se dotazuje na MRUs, abyste zjistili, že jste na maximum, použijte distribuovanou mezipaměť pro hodnotu s přiměřeným časovým limitem.
    * Udržujte si počet neletových úloh.

* Když vaše obslužná rutina JobStateChange získá oznámení, že úloha dosáhla naplánovaného stavu, zaznamenejte čas, který vstoupí do stavu plánu, a na použitou oblast nebo účet.
* Když vaše obslužná rutina JobStateChange dostane oznámení, že úloha dosáhla stavu zpracování, označte záznam pro úlohu jako zpracování.
* Když vaše obslužná rutina JobStateChange dostane oznámení, že úloha dosáhla stavu dokončeno/chyba, označit záznam pro úlohu jako konečný a snížit počet úloh inletu. Získejte počet rezervovaných jednotek médií pro vybraný účet a porovnejte aktuální číslo MRU s počtem úloh inletu. Pokud je počet vašich letů nižší než počet naposledy použitých položek, požádejte ho a aktualizujte službu.
* Mít samostatný proces, který pravidelně prohledává vaše záznamy o úlohách.
    
    * Pokud máte úlohy v plánovaném stavu, které nepokročily do stavu zpracování v přiměřené době pro danou oblast, odeberte tuto oblast ze seznamu aktuálně používaných účtů.  V závislosti na vašich obchodních požadavcích se můžete rozhodnout tyto úlohy okamžitě zrušit a znovu je odeslat do jiné oblasti. Nebo můžete jiným uživatelům poskytnout další čas na přechod k dalšímu stavu.
    * V závislosti na počtu rezervovaných jednotek médií nakonfigurovaných na účtu a rychlosti odesílání můžou být ve stavu ve frontě také úlohy, které systém ještě nevybral ke zpracování.  Pokud se seznam úloh ve frontě přesáhne přijatelnému limitu v oblasti, můžete tyto úlohy zrušit a odeslat do jiné oblasti.  Může se ale jednat o příznak, který nemá na účtu pro aktuální zatížení nakonfigurovaný dostatek rezervovaných jednotek médií.  V případě potřeby můžete požádat o vyšší kvótu rezervované jednotky médií prostřednictvím podpory Azure.
    * Pokud byla oblast odebrána ze seznamu účtů, před přidáním zpět do seznamu ji monitorujte pro obnovení.  Regionální stav se dá monitorovat prostřednictvím stávajících úloh v oblasti (Pokud se nezrušily a znovu neodeslaly), a to přidáním účtu zpátky do seznamu po určitou dobu a pomocí operátorů sledujících komunikaci Azure o výpadkech, které by mohly ovlivnit Azure Media Services.
    
Pokud zjistíte, že počet MRU je thrashing nahoru a dolů, přesuňte logiku snížení na pravidelný úkol. Aby bylo možné zjistit, jestli je potřeba aktualizovat MRUs, je třeba, aby logika předběžných úloh porovnala počet letů do aktuálního počtu.

## <a name="next-steps"></a>Další kroky

* [Vytváření streamování pro různé oblasti videa na vyžádání](media-services-high-availability-streaming.md)
* Podívejte se na [ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
