---
title: Publikovat nabídku aplikace Power BI – Azure Marketplace | Dokumentace Microsoftu
description: Publikujte nabídku aplikace Power BI na webu marketplace Microsoft AppSource.
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
ms.openlocfilehash: 0e82041dab1932ed7c129b598740b2441aa59315
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822748"
---
# <a name="publish-a-power-bi-app-offer"></a>Publikovat nabídku aplikace Power BI

Posledním krokem po definované v rámci nabídky v portál partnerů cloudu a vytvoří přidružené technické prostředky je odeslat nabídku k publikování. Chcete-li spustit tento proces v levém podokně **nová nabídka** okně **publikovat**. Další informace najdete v tématu [publikování Azure Marketplace a AppSource nabídky](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Postup publikování

Toto jsou hlavní kroky v procesu publikování:

![Publikování kroky procesu pro aplikace Power BI nabízejí](./media/publishing-process-steps.png)

Tato tabulka popisuje jednotlivé kroky a zajišťuje jeho odhadovaný čas dokončení:

|   Publikování kroku            |   Čas     |   Popis                                                                  |
| --------------------         |------------| ----------------                                                               |
| Ověřit požadované součásti       | 15 minut     | Poskytuje informace a nabídku, nastavení se ověří.                            |
| Certifikace                | 1-7 dní   | Tým certifikace Power BI analyzuje vaši nabídku. Tým spustí aplikaci Power BI prostřednictvím testu ruční ověření nainstalováním aplikace prostřednictvím adresy URL zadané instalace. Primární ověření jsou prováděny v rámci procesu certifikace aplikací (popsáno dále v tomto dokumentu).         |
| Balení                    | \< 1 hodina  | Technické prostředky nabídky jsou zabaleny pro používání zákazníka.                        |
| Generování zájemců registrace | \< 1 hodina  | Vedoucí systémy jsou konfigurovány a nasazeny.                                      |
| Vydavatel schvalování            | \-         | Než uvedete nabídky dokončení konečnou kontrolu a potvrzení. Také teď budete mít odkaz na náhled vaší nabídky. Jakmile budete spokojeni s vzhled ve verzi preview, vyberte **aktivace** na **stav** kartu. Tím se odešle požadavek na týmem registrace na uvedení vaší aplikace na AppSource.    |
| Živé                         | \< 3 hodiny | Nabídky je nyní veřejně uvádějí ("živé") na AppSource a Zákazníci můžete zobrazit vaši aplikaci a nasadit ho do svých předplatných Power BI. Také obdržíte e-mail s potvrzením. V pravém sloupci na **všechny nabídky** kartu, můžete zobrazit stav vaší nabídky. Na **stav** kartu, můžete zobrazit podrobný stav toku publikování vaší nabídky. |
|   |   |

Povolit až osm dní pro dokončení tohoto procesu. Po projít postup publikování vaší nabídky aplikace Power BI nebude uvedené [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) oddílu aplikace Power BI.


### <a name="app-certification-process"></a>Proces certifikace aplikací

Tým připojování Microsoftu používá tento proces se ověřit váš příspěvek nabídky aplikace Power BI:

1. Projděte si právní dokumenty a odkazy.
2. Ověřte kontaktní informace na podporu.
3. Ověření správné instalace pomocí adresy URL instalační služby.
4. Kontrola přítomnosti malwaru a dalšího škodlivého obsahu aplikace.
5. Zkontrolujte, zda zobrazený obsah odpovídá popis aplikace.
6. Ověřte, že operace související s aplikací fungovat podle očekávání v Power BI. Tým sestavy a řídicí panely se otevře s ukázkovými daty, připojí se k vlastním zdrojům dat, aktualizuje data a tak dále.

Tým certifikace poskytuje zpětnou vazbu, pokud se vyhledejte všechny problémy.  Další informace o požadavcích aplikace Power BI, najdete v článku [dokumentace k aplikaci Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Další postup

Doporučujeme pravidelně sledovat vaše aplikace v [AppSource marketplace](https://appsource.microsoft.com).  Také byste měli použít [prodejce Insights](../../cloud-partner-portal-orig/si-getting-started.md) funkce [portál partnerů cloudu](https://cloudpartner.azure.com/#insights) získat přehled o zákaznících marketplace a využití aplikací. Nakonec můžete [aktualizovat vaši nabídku](./cpp-update-existing-offer.md).
