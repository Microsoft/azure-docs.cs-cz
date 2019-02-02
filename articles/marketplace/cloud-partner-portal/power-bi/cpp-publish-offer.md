---
title: Publikovat nabídku aplikace Power BI – Azure Marketplace | Dokumentace Microsoftu
description: Publikujte nabídku aplikace Power BI na webu Microsoft Marketplace AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 2b3783060cf5502076ce3bc98cf07f005366a9e2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666713"
---
# <a name="publish-power-bi-app-offer"></a>Publikovat nabídku aplikace Power BI

Posledním krokem po nabídky definované na portálu a vytvoří přidružené technické prostředky je odeslat nabídku k publikování.  Chcete-li spustit tento proces, klikněte na tlačítko **publikovat** tlačítko nabídky svislou v **nová nabídka** okno.  Další informace najdete v tématu [publikování Azure Marketplace a AppSource nabídky](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Postup publikování

Následující diagram znázorňuje hlavní kroky v procesu publikování "vysílat živě".

![Publikování kroky procesu pro aplikace Power BI](./media/publishing-process-steps.png)

Následující tabulka popisuje tyto kroky a poskytuje odhad maximální doba pro jejich dokončení:

|   Publikování kroku            |   Čas     |   Popis                                                                  |
| --------------------         |------------| ----------------                                                               |
| Ověřit požadované součásti       | 15 min     | Poskytuje informace a nabídku, nastavení se ověří.                            |
| Certifikace                | 1-7 dní   | Tým certifikace Power BI analyzuje vaši nabídku. Zajišťuje každodenní provoz vaší aplikace Power BI prostřednictvím testu ruční ověření nainstalováním aplikace pomocí adresy URL zadané instalace. Hlavní ověření probíhá v rámci procesu certifikace aplikací. Níže jsou uvedeny.         |
| Balení                    | \< 1 hodina  | Technické prostředky nabídky jsou zabaleny pro používání zákazníka.                        |
| Generování zájemců registrace | \< 1 hodina  | Vedoucí systémy jsou konfigurovány a nasazeny.                                      |
| Vydavatel schvalování            | \-         | Zkontrolujte poslední vydavatele a potvrzení před uvedete nabídky. Také teď bude mít odkaz na náhled vaší nabídky. Jakmile budete spokojeni s vzhled ve verzi preview, klikněte na tlačítko **aktivace** tlačítko **stav** kartu. Tato akce odešle žádost na týmem registrace na uvedení vaší aplikace na AppSource.    |
| Živé                         | \< 3 hodiny | Nabídky je nyní veřejně uvádějí ("živé") na AppSource a zákazníci budou moci zobrazit a nasazení vaší aplikace v Power BI předplatná. Také obdržíte e-mail s potvrzením. V libovolném okamžiku můžete kliknout na **všechny nabídky** kartu a zjistit stav pro všechny vaše nabídky uvedený v pravém sloupci. Můžete kliknout na **stav** karty můžete zobrazit stav publikování tok v souvislosti s vaší nabídky. |
|   |   |

Umožňuje až osm dní pro dokončení tohoto procesu. Po projít postup publikování vaší nabídky aplikace Power BI nebude uvedené [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) části aplikace Power BI.


### <a name="app-certification-process"></a>Proces certifikace aplikací

Tým připojování Microsoftu používá následující proces pro ověření vašeho příspěvku nabídka Power BI:

1. Právní dokumenty a odkazy jsou kontrolovány.
2. Kontaktní informace podpory se ověří.
3. Adresa URL instalačního programu se používá k ověření správné instalace. 
4. Aplikace je vyhledán malware a další škodlivý obsah. 
5. Ověření se provádí tento obsah zobrazit odpovídá popis aplikace.
6. Operace související s aplikací fungovat podle očekávání v Power BI: otevření sestavy a řídicí panely s ukázkovými daty, připojte se k vlastním zdrojům dat, aktualizace atd.

Tým certifikace poskytuje zpětnou vazbu, pokud se vyhledejte všechny problémy.  Další informace o požadavcích aplikace Power BI, najdete v článku [dokumentace k aplikaci Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Další postup

Doporučujeme pravidelně sledovat vaše aplikace v [AppSource Marketplace](https://appsource.microsoft.com).  Kromě toho byste měli použít [prodejce Insights](../../cloud-partner-portal-orig/si-getting-started.md) funkce [portál partnerů cloudu](https://cloudpartner.azure.com/#insights) poskytnout přehledné informace o marketplace zákazníků a využití.  Můžete také provádět určité [aktualizace pro vaši nabídku](./cpp-update-existing-offer.md).
