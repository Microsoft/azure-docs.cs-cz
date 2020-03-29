---
title: Kódování s vysokou dostupností služby Azure Media Services
description: Zjistěte, jak převzetí služeb při selhání sekundární účet Služby Media Services, pokud dojde k výpadku nebo selhání místního datového centra.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78934190"
---
# <a name="media-services-high-availability-encoding"></a>Kódování s vysokou dostupností služeb Media Services 

Služba kódování Azure Media Services je regionální platforma pro dávkové zpracování, která není aktuálně navržená pro vysokou dostupnost v rámci jedné oblasti. Služba kódování aktuálně neposkytuje okamžité převzetí služeb při selhání služby, pokud dojde k výpadku regionálního datového centra nebo selhání podkladové součásti nebo závislých služeb (například úložiště, SQL). Tento článek vysvětluje, jak nasadit služby Media Services k udržení architektury s vysokou dostupností s převzetím služeb při selhání a zajištění optimální dostupnosti pro vaše aplikace.

Dodržováním pokynů a osvědčených postupů popsaných v článku snížíte riziko selhání kódování, zpoždění a minimalizujete dobu obnovení, pokud dojde k výpadku v jedné oblasti.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Jak vytvořit meziregionální kódovací systém

* [Vytvořte](create-account-cli-how-to.md) dva (nebo více) účtů Azure Media Services.

    Tyto dva účty musí být v různých oblastech. Další informace najdete [v tématu oblasti, ve kterých se nasazuje služba Azure Media Services](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Nahrajte média do stejné oblasti, ze které plánujete úlohu odeslat. Další informace o tom, jak začít s kódováním, najdete [v tématu Vytvoření vstupu úlohy z adresy URL protokolu HTTPS](job-input-from-http-how-to.md) nebo Vytvoření vstupu [úlohy z místního souboru](job-input-from-local-file-how-to.md).

    Pokud pak potřebujete znovu odeslat [úlohu](transforms-jobs-concept.md) do jiné oblasti, můžete použít JobInputHttp nebo použít [Copy-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) ke kopírování dat ze zdrojového kontejneru datových zdrojů do kontejneru datových zdrojů v alternativní oblasti.
* Přihlásit se k odběru jobstatechange zprávy v každém účtu prostřednictvím Služby Azure Event Grid. Další informace naleznete v tématu:

    * [Ukázka audio analýzy,](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) která ukazuje, jak monitorovat úlohu pomocí Azure Event Grid, včetně přidání záložního řešení pro případ, že by se zprávy Azure Event Grid z nějakého důvodu zpozdily.
    * [Události schémat azure event grid pro mediální služby](media-services-event-schemas.md)
    * [Zaregistrujte se na události prostřednictvím portálu Azure nebo příkazového příkazu k obsluze](reacting-to-media-services-events.md) (můžete to udělat také pomocí sady EventGrid Management SDK)
    * [Sada Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (která nativně podporuje události mediálních služeb).

    Můžete také využívat události Event Grid prostřednictvím funkce Azure.
* Při vytváření [úlohy](transforms-jobs-concept.md):

    * Náhodně vyberte účet ze seznamu aktuálně používaných účtů (tento seznam obvykle obsahuje oba účty, ale pokud jsou zjištěny problémy, může obsahovat pouze jeden účet). Pokud je seznam prázdný, vyzvěte výstrahu, aby ho operátor mohl prozkoumat.
    * Obecné pokyny je, že potřebujete [jednu rezervovanou jednotku média](media-reserved-units-cli-how-to.md) na [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (pokud nepoužíváte [VideoAnalyzerPreset,](analyzing-video-audio-files-concept.md) kde se doporučuje 3 jednotky vyhrazených médií na JobOutput).
    * Získejte počet rezervovaných jednotek médií (MRU) pro vybraný účet. Pokud počet **aktuálních rezervovaných jednotek média** ještě není na maximální hodnotě, přidejte číslo jednotek MRU, které úloha potřebuje, a aktualizujte službu. Pokud je míra odesílání úlohy vysoká a často se dotazujete na mru, abyste zjistili, že jste na maximu, použijte distribuovanou mezipaměť pro hodnotu s přiměřeným časovým limitem.
    * Udržujte počet pracovních míst za letu.

* Když obslužná rutina JobStateChange dostane oznámení, že úloha dosáhla naplánovaného stavu, zaznamenejte čas, kdy vstoupí do stavu plánu a použité oblasti/účtu.
* Když vaše obslužná rutina JobStateChange získá oznámení, že úloha dosáhla stavu zpracování, označte záznam úlohy jako zpracování.
* Když obslužná rutina JobStateChange získá oznámení, že úloha dosáhla stavu Dokončeno/Chybová/Zrušeno, označte záznam úlohy jako konečný a snížípočet úloh za letu. Získejte počet rezervovaných jednotek médií pro vybraný účet a porovnejte aktuální číslo MRU s počtem pracovních míst v letadle. Pokud je počet zaletu menší než počet MRU, pak jej zmenšete a aktualizujte službu.
* Mít samostatný proces, který pravidelně sleduje vaše záznamy o úlohách
    
    * Pokud máte úlohy v naplánovaném stavu, které nebyly převedeny do stavu zpracování v přiměřeném množství času pro danou oblast, odeberte tuto oblast ze seznamu aktuálně používaných účtů.  V závislosti na vašich obchodních požadavcích se můžete rozhodnout tyto úlohy okamžitě zrušit a znovu je odeslat do jiné oblasti. Nebo bys jim mohl dát víc času, aby se přesunuli do dalšího státu.
    * V závislosti na počtu rezervovaných jednotek médií nakonfigurovaných na účtu a rychlosti odeslání mohou být ve stavu ve frontě úlohy, které systém ještě nevyzvedl ke zpracování.  Pokud seznam úloh ve stavu ve frontě roste nad přijatelný limit v oblasti, tyto úlohy mohou být zrušeny a odeslány do jiné oblasti.  To však může být příznakem nemají dostatek rezervovaných jednotek médií nakonfigurován na účtu pro aktuální zatížení.  V případě potřeby můžete prostřednictvím podpory Azure požádat o vyšší kvótu rezervované jednotky médií.
    * Pokud byla oblast odebrána ze seznamu účtů, před přidáním zpět do seznamu ji sledujte, zda se neobnovuje.  Regionální stav lze sledovat prostřednictvím existujících úloh v regionu (pokud nebyly zrušeny a znovu odeslány), přidáním účtu zpět do seznamu po určité době a operátory, kteří sledují komunikaci Azure o výpadcích, které mohou mít vliv na výpadky, které mohou mít vliv na výpadky, které mohou mít vliv na Mediální služby Azure.
    
Pokud zjistíte, že počet MRU je mlácení nahoru a dolů hodně, přesuňte logiku snížení periodické úlohy. Mají logiku před úlohou porovnat počet zaletu na aktuální počet MRU a zjistěte, zda je třeba aktualizovat MRU.

## <a name="next-steps"></a>Další kroky

* [Vytváření streamování videa na vyžádání napříč oblastmi](media-services-high-availability-streaming.md)
* Rezervovat [ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
