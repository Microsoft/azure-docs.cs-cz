---
title: Nabídka pro publikování Power BIch aplikací | Azure Marketplace
description: Na webu Microsoft AppSource Marketplace publikujete nabídku aplikace Power BI.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: eb31520c81a4064edbe54a0256b694c4ad88fb49
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141702"
---
# <a name="publish-a-power-bi-app-offer"></a>Publikování nabídky aplikace v Power BI

>[!Important]
>Od 13. dubna 2020 začneme přesouvat správu nabídek Power BI aplikací do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Při správě migrovaných nabídek postupujte podle pokynů v tématu [Přehled vytvoření aplikace Power BI](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) .

Poslední krok, po definování nabídky v portál partnerů cloudu a vytvoření přidružených technických prostředků, je odeslání nabídky k publikování. Chcete-li spustit tento proces, vyberte v levém podokně okna **Nová nabídka** možnost **publikovat**. Další informace najdete v tématu [publikování Azure Marketplace a AppSource nabídek](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Kroky publikování

Toto jsou hlavní kroky procesu publikování:

![Publikování kroků procesu pro Power BI nabídku aplikace](./media/publishing-process-steps.png)

Tato tabulka popisuje jednotlivé kroky a poskytuje odhadovaný čas dokončení:

|   Krok publikování            |   Time     |   Popis                                                                  |
| --------------------         |------------| ----------------                                                               |
| Ověřit požadavky       | 15 minut     | Budou ověřeny informace nabídky a nastavení nabídky.                            |
| Certifikace                | 1-7 dní   | Power BI Certifikační tým analyzuje vaši nabídku. Tým spustí vaši aplikaci v Power BI pomocí testu ručního ověření instalací aplikace prostřednictvím zadané instalační adresy URL. Primární ověřování se provádí jako součást procesu certifikace aplikace (popsaných dále v tomto dokumentu).         |
| Balení                    | \<1 hodina  | Technické prostředky nabídky jsou zabaleny pro zákaznické použití.                        |
| Registrace generování potenciálních zákazníků | \<1 hodina  | Nakonfigurují a nasazují se systémy potenciálních zákazníků.                                      |
| Schvalování vydavatele            | \-         | Dokončili jste závěrečnou kontrolu a potvrzení před tím, než bude nabídka živá. Teď budete mít odkaz na náhled vaší nabídky. Až budete spokojeni s tím, jak náhled vypadá, vyberte na kartě **stav** možnost **Přejít na aktivní** . Tím se pošle žádost týmu registrace k vypsání vaší aplikace na AppSource.    |
| Živé                         | \<3 hodiny | Vaše nabídka je teď veřejně uvedená ("Live") na AppSource a zákazníci si můžou aplikaci zobrazit a nasadit ji v předplatných Power BI. Obdržíte také potvrzovací e-mail. V pravém sloupci na kartě **všechny nabídky** uvidíte stav všech nabídek. Na kartě **stav** můžete zobrazit podrobný stav postupu publikování pro vaši nabídku. |
|   |   |

Umožněte dokončení tohoto procesu až osm dní. Až provedete tyto kroky publikování, vaše nabídka aplikace Power BI se zobrazí v části Power BI aplikace v [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) .


### <a name="app-certification-process"></a>Proces certifikace aplikace

Tým Microsoft připojování používá tento postup k ověření vašeho odeslání nabídky Power BI aplikací:

1. Projděte si právní dokumenty a odkazy na technickou podporu.
2. Ověřte kontaktní informace podpory.
3. K ověření správné instalace použijte adresu URL instalačního programu.
4. Naskenujte aplikaci pro malware a další škodlivý obsah.
5. Ověřte, že zobrazený obsah odpovídá popisu aplikace.
6. Ověřte, že operace související s aplikacemi fungují podle očekávání v Power BI. Tým otevře sestavy a řídicí panely s ukázkovými daty, připojí se k vlastním zdrojům dat, aktualizuje data a tak dále.

Certifikační tým poskytuje zpětnou vazbu, pokud nalezne nějaké problémy.  Další informace o Power BI požadavků aplikací najdete v dokumentaci k [aplikaci Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Další kroky

Doporučujeme pravidelně monitorovat aplikaci na [webu AppSource Marketplace](https://appsource.microsoft.com).  K získání přehledu o vašich zákaznících a využití aplikací byste měli použít také funkci [prodejce Insights](../../cloud-partner-portal-orig/si-getting-started.md) v [portál partnerů cloudu](https://cloudpartner.azure.com/#insights) . Nakonec můžete [nabídku aktualizovat](./cpp-update-existing-offer.md).
