---
title: Definovat nastavení nabídky pro nabídku poradenské služby | Azure Marketplace
description: Nastavení nabídky definujte v nabídce poradenské služby Azure nebo Dynamics 365 na portálu cloudových partnerů pro Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 6a3c168d0bd13e7c335841ac4016f18464cd50d5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985112"
---
# <a name="offer-settings-tab"></a>Karta nastavení nabídky

>[!Important]
>dubna 2020 začneme přesouvat správu vašich poradenských nabídek služeb do Partnerského centra. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Podle pokynů v [přehledu pro vytvoření služby](https://aka.ms/AzureCreateConsultingService) postupujte podle pokynů pro správu migrovaných nabídek.

Na obrazovce **Nová nabídka** je prvním krokem vytvoření identity nabídky. Identita nabídky se skládá ze tří částí: **ID nabídky**, **ID vydavatele**a **název**. Každá z těchto částí je popsána v následujících částech.

![Vytvořit novou nabídku konzultačních služeb – karta Nastavení nabídky](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>ID nabídky*

Tento identifikátor je jedinečný název, který vytvoříte při prvním odeslání nabídky. Musí se skládat pouze z nižších alfanumerických znaků, pomlček nebo podtržítek. **ID nabídky** je viditelné v adrese URL a ovlivňuje výsledky vyhledávače. Příkladem je *yourcompanyname_exampleservice*.

Jak je znázorněno v příkladu, **ID nabídky** je připojenk id vydavatele vytvořit jedinečný identifikátor. Tento jedinečný identifikátor je vystaven jako trvalý odkaz, který lze rezervovat a je indexován vyhledávači.

>[!Note]
>Po aktivní nabídce nelze její identifikátor aktualizovat.


### <a name="publisher-id"></a>ID vydavatele*

Tento identifikátor souvisí s vaším účtem. Po přihlášení pomocí účtu organizace se vaše **ID aplikace Publisher** zobrazí v rozevírací nabídce.


### <a name="name"></a>Jméno*

Tento řetězec se zobrazí jako název nabídky na AppSource nebo na Azure Marketplace. Pole **Název** je omezeno na 50 znaků. Recenzent možná bude muset upravit váš název, aby k názvu nabídky připojoval dobu trvání a typ nabídky.

Následující příklad ukazuje, jak je sestaven název nabídky. 

![Vytvoření nové nabídky konzultačních služeb](media/cppsampleconsultingoffer.png)

Název nabídky se skládá ze čtyř částí:

-   **Doba trvání:** Definováno na kartě **Podrobnosti ve výkladu** editoru. Doba trvání může být vyjádřena v hodinách, dnech nebo týdnech.
-   **Druh služby:** Definováno na kartě **Podrobnosti ve výkladu** editoru. Typy služeb `Assessment`jsou `Briefing` `Implementation`, `Proof of concept`, `Workshop`, a .
-   **Předložka:** Vloženo recenzentem.
-   **Název:** Definováno na stránce **Nastavení nabídky.**

>[!Note]
>Pole **Název** je omezeno na 50 znaků. Recenzent možná bude muset upravit váš název, aby k názvu nabídky připojoval dobu trvání a typ nabídky.

Následující seznam obsahuje několik dobře pojmenovaných názvů nabídek:

-   Základy pro profesionální služby: 1-ho-Briefing
-   Platforma pro migraci do cloudu: 1-hod briefing
-   PowerApps a Microsoft Flow: Jednodenní workshop
-   Azure Machine Learning: 3-Wk PoC
-   Cihla a klikněte na maloobchodní řešení: 1-hr Briefing
-   Přineste si vlastní data: 1-Wk Workshop
-   CloudAnalytics: Třídenní workshop
-   Školení Power BI: Třídenní workshop
-   Řešení správy prodeje: 1týdenní implementace
-   Crm Rychlý start: 1-denní workshop
-   Dynamics 365 pro prodej: Dvoudenní hodnocení

Po vyplnění karty **Nastavení nabídky** uložte příspěvek. Název nabídky se nyní zobrazí nad editorem a najdete jej ve **všech nabídkách**.

## <a name="next-steps"></a>Další kroky

Teď můžete zadat [podrobnosti o výloze a určit, jestli se má publikovat na Azure Marketplace nebo appsource](./cpp-consulting-service-storefront-details.md).
