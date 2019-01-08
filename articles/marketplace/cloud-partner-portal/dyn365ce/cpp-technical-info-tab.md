---
title: Dynamics 365 pro technické informace o zapojení zákazníků karta – Azure Marketplace | Dokumentace Microsoftu
description: Jak určit technické informace pro Dynamics 365 Customer Engagement aplikace na webu AppSource Marketplace.
services: Dynamics 365 for Customer Engagement Offer, Azure, Marketplace, Cloud Partner Portal, AppSource
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
ms.date: 01/02/2019
ms.author: pbutlerm
ms.openlocfilehash: 214abd64232130dd3fd5fdde510f7545732ac82e
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082639"
---
# <a name="dynamics-365-for-customer-engagement-technical-info-tab"></a>Dynamics 365 pro kartu technické informace o zapojení zákazníků

**Technické informace o** karty **nová nabídka** stránky můžete zadat podrobné informace o vaší Dynamics 365 Customer Engagement aplikace včetně CRM balíček a marketing logo prostředků.  Na této kartě je rozdělen do čtyř oddílů: **Informace o aplikaci**, **CRM balíček**, **Dostupnost balíčku CRM**, a **marketingové artefakty**. Připojený hvězdička (*) na název pole označuje, že je povinný. 


## <a name="application-info-section"></a>Oddíl informací o aplikaci

Bude poskytovat podrobnosti o vaší aplikaci Dynamics 365 v této části.

![Oddíl informací o aplikaci na kartě technické informace](./media/dynce-technical-info-tab1.png)

Následující tabulka popisuje tato pole.

|      Pole                    |    Popis                  |
|    ---------                  |  ---------------                |
|   Základní licenční model          |  Licenční model Určuje, jak zákazníci jsou přiřazeny vaši aplikaci v Centru pro správu Dynamics 365. **Prostředek** licencování je založený na instancích, zatímco **uživatele** jeden do každého tenanta přiřazení licencí.  |
|  Odchozí S2S a CRM Secure Store přístup |  Umožňuje konfigurovat CRM Secure Store nebo odchozí přístup k serveru (S2S). *Tato funkce vyžaduje specializované pozornost od týmu služby Dynamics 365 během fáze certifikace.* Microsoft bude kontaktovat, abyste mohli provést další kroky pro tuto funkci podporovat.  |
| Přihlášení k odběru událostí životního cyklu aplikace CRM | Integrace s událostmi životního cyklu Dynamics 365 vyžaduje poskytnutí o vyhrazenou službu, který je registrovaný prostřednictvím metody speciální smlouvy s Microsoftem. *Tato funkce vyžaduje specializované pozornost od týmu služby Dynamics 365 během fáze certifikace.* Vás bude kontaktovat na dokončení další kroky pro podporu této možnosti.  |
| Adresa Url konfigurace aplikace | Adresa URL webové stránky, která umožňuje uživateli konfigurace aplikace |
| Podporovaných produktů Dynamics 365  | Vyberte produkty Dynamics 365, které tato nabídka platí pro. V rámci této nabídky se zobrazí v rámci vybrané produkty v AppSource.  |
| Marketingové pouze změny         | Nastavení této možnosti Ano znamená, že byly provedeny pouze marketing/popisný změny do existující nabídky.  Tyto změny umožní nabídka obejít certifikace a fáze zřizování.  |
|  |  |


## <a name="crm-package-section"></a>Části balíčku aplikace CRM

Bude poskytovat podrobnosti o souboru balíčku AppSource v této části.  Tato informace se použijí podle týmů ověření a certifikace Dynamics 365.

![Balíček CRM část technické informace o kartě](./media/dynce-technical-info-tab2.png)

Následující tabulka popisuje tato pole.

|      Pole                    |    Popis                  |
|    ---------                  |  ---------------                |
|  Název souboru balíčku     |  Název souboru balíčku (.zip).  Tento název se *není* veřejné a se používá interně týmem certifikační Dynamics 365.  |
|  URL                          |  Adresa URL účtu Azure Storage, která obsahuje soubor nahraného balíčku. Tato adresa URL by měla obsahovat jen pro čtení klíč SAS pro povolení náš tým ke sbírání vašeho balíčku pro ověření.  |
| Více než jeden balíček crm     | Vyberte Ano pouze pokud podporujete více verzí aplikace crm pomocí různých balíčků.  Každá verze bude mít odpovídající vazby soubor balíčku, které je třeba vytvořit samostatně.  |
| Scénář a použití případu asset   | Umožňuje nahrát dokument funkční specifikace pro vaši aplikaci pro použití týmem ověření Dynamics 365.  Upřednostňované formát tato specifikace je [E2E uživatelské scénáře šablony](http://download.microsoft.com/download/5/1/8/51812AC9-BCD8-489F-937C-5D439C494EC1/E2E%20User%20Scenario%20Template.docx).  |
|  |  |


## <a name="crm-package-availability-section"></a>Oddíl Dostupnost balíčku CRM

V této části vyberte, které geografické oblasti vaše aplikace bude dostupná pro zákazníky.  Nasazení do těchto suverénních oblastech *vyžadují zvláštní oprávnění a ověření* během certifikačního procesu: [Německo](https://docs.microsoft.com/azure/germany/), [cloudu pro státní správu USA](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)a TIP.


## <a name="marketing-artifacts-section"></a>Část artefakty marketing

Tato část vyžaduje, abyste pro nahrání logo aplikace, který se použije k reprezentaci balíčku na webu Marketplace AppSource.  Obrázek loga musí být ve formátu PNG a je 255 x 115 velikost v pixelech.


## <a name="next-steps"></a>Další postup

Doporučujeme nabízet ukázku aplikace vyplněním [kartu testovací verze](./cpp-testdrive-tab.md)
