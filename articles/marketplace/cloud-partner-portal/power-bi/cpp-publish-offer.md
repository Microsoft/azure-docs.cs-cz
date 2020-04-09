---
title: Publikovat nabídku aplikace Power BI | Azure Marketplace
description: Publikujte nabídku aplikace Power BI na tržišti Microsoft AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: a5eb5b652f7a419c6c2d1b3e6880c3c3003fb679
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985520"
---
# <a name="publish-a-power-bi-app-offer"></a>Publikování nabídky aplikace Power BI

>[!Important]
>dubna 2020 začneme přesouvat správu nabídek aplikací Power BI do Centra partnerů. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Podle pokynů v [přehledu vytváření aplikací Power BI](https://aka.ms/AzureCreatePBIServiceApp) spravujte migrované nabídky.

Posledním krokem po definování nabídky na portálu partnerů cloudu a vytvoření souvisejících technických prostředků je odeslání nabídky k publikování. Chcete-li tento proces spustit, vyberte v levém podokně okna **Nová nabídka** **možnost Publikovat**. Další informace najdete [v tématu publikování nabídek Azure Marketplace a AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Kroky publikování

Toto jsou hlavní kroky v procesu publikování:

![Kroky procesu publikování pro nabídku aplikací Power BI](./media/publishing-process-steps.png)

Tato tabulka popisuje každý krok a poskytuje jeho odhadovaný čas dokončení:

|   Krok publikování            |   Time     |   Popis                                                                  |
| --------------------         |------------| ----------------                                                               |
| Ověřit požadavky       | 15 minut     | Informace o nabídce a nastavení nabídky jsou ověřeny.                            |
| Certifikace                | 1-7 dní   | Certifikační tým Power BI analyzuje vaši nabídku. Tým spustí vaši aplikaci Power BI prostřednictvím ručního ověřovacího testu instalací aplikace přes zadanou adresu URL instalace. Primární ověření se provádějí jako součást procesu certifikace aplikace (popsaného dále v tomto dokumentu).         |
| Balení                    | \<1 hodina  | Technická aktiva nabídky jsou balena pro použití zákazníkem.                        |
| Registrace generování potenciálních zákazníků | \<1 hodina  | Systémy potenciálních zákazníků jsou konfigurovány a nasazeny.                                      |
| Odhlášení vydavatele            | \-         | Před udumí na živo dokončíte závěrečnou recenzi a potvrzení. Nyní budete mít také odkaz na náhled vaší nabídky. Až budete spokojeni s tím, jak bude náhled vypadat, na kartě **Stav** vyberte **Přejít živě.** Tím odešlete žádost onboarding týmu seznam vaší aplikace na AppSource.    |
| Živé                         | \<3 hodiny | Vaše nabídka je teď veřejně uvedená ("live") na AppSource a zákazníci si můžou vaši aplikaci zobrazit a nasadit ve svých předplatných Power BI. Obdržíte také potvrzovací e-mail. V pravém sloupci na kartě **Všechny nabídky** můžete zobrazit stav všech nabídek. Na kartě **Stav** se zobrazí podrobný stav toku publikování pro vaši nabídku. |
|   |   |

Vyčkejte až osm dní, než bude tento proces dokončen. Po procházení těchto kroků publikování bude nabídka aplikace Power BI uvedená v části Aplikace [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI.


### <a name="app-certification-process"></a>Proces certifikace aplikací

Tým společnosti Microsoft používá tento proces k ověření odeslání nabídky aplikace Power BI:

1. Prohlédněte si právní dokumenty a odkazy na nápovědu.
2. Ověřte kontaktní informace podpory.
3. Správná instalace slouží k ověření správné instalace pomocí adresy URL instalačního programu.
4. Vyhledání malwaru a dalšího škodlivého obsahu v aplikaci.
5. Ověřte, zda zobrazený obsah odpovídá popisu aplikace.
6. Ověřte, že operace související s aplikací fungují podle očekávání v Power BI. Tým otevře sestavy a řídicí panely s ukázkovými daty, připojí se k vlastním zdrojům dat, aktualizuje data a tak dále.

Certifikační tým poskytuje zpětnou vazbu, pokud zjistí nějaké problémy.  Další informace o požadavcích na aplikace Power BI najdete v [dokumentaci k aplikacím Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Další kroky

Doporučujeme pravidelně sledovat aplikaci na [trhu AppSource](https://appsource.microsoft.com).  Měli byste také použít funkci [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) na [portálu cloudových partnerů,](https://cloudpartner.azure.com/#insights) abyste získali přehled o zákaznících na marketplace a využití aplikací. Nakonec můžete [aktualizovat svou nabídku](./cpp-update-existing-offer.md).
