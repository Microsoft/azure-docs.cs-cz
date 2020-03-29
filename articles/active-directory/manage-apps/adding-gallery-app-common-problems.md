---
title: Problém s přidáním aplikace Azure AD Gallery | Dokumenty společnosti Microsoft
description: Seznamte se s běžnými problémy, kterým lidé čelí při přidávání aplikací Azure AD Gallery, a na tom, co můžete udělat pro jejich vyřešení
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2b42880f99f3e87d75854166047896860f9eb14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784427"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Problém s přidáním aplikace Azure AD Gallery

Tento článek vám pomůže pochopit běžné problémy, kterým lidé čelí při přidávání aplikací Azure AD Gallery a co můžete udělat pro jejich vyřešení.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Kliknul jsem na tlačítko "Přidat" a aplikace trvala dlouho, než se objevila

Za určitých okolností může trvat 1-2 minuty (a někdy i déle), než se aplikace zobrazí po přidání do adresáře. I když to není normální očekávaný výkon, můžete vidět přidání aplikace probíhá kliknutím na ikonu **Oznámení** (zvonek) v pravém horním části [portálu Azure](https://portal.azure.com/) a hledá **inprogress** nebo **dokončené** oznámení označené **Přidání aplikace.**

Pokud vaše aplikace není nikdy přidána nebo se při kliknutí na tlačítko **Přidat** vyskytne chyba, zobrazí se **oznámení** ve stavu **Chybová.** Pokud chcete další podrobnosti o chybě získat další informace nebo sdílet s pracovníkem podpory, můžete zobrazit další informace o chybě podle pokynů v části Jak zobrazit podrobnosti o [portálu oznámení.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Po kliknutí na tlačítko Přidat se aplikace neobjevila

Někdy se z důvodu přechodných problémů, problémů se sítí nebo chyby přidání aplikace nezdaří. Můžete říct, že k tomu dojde, když kliknete na ikonu **Oznámení** (zvonek) v pravém horním části portálu Azure a zobrazí se červená ikona (!) vedle oznámení **o přidání aplikace.** To znamená, že při vytváření aplikace došlo k chybě.

Pokud při klepnutí na tlačítko **Přidat** narazíte na chybu, zobrazí se **oznámení** v **chybovém** stavu. Pokud chcete další podrobnosti o chybě získat další informace nebo sdílet s pracovníkem podpory, můžete zobrazit další informace o chybě podle pokynů v části Jak zobrazit podrobnosti o [portálu oznámení.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Nevím, jak nastavit přihlášku, jakmile ji přidám

Pokud potřebujete pomoc se učením o aplikacích, je dobrým začátkem [článku Seznam výukových lekcí o integraci aplikací SaaS s Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

Kromě toho [knihovna dokumentů aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) vám pomůže získat další informace o jednotném přihlašování pomocí Azure AD a o tom, jak funguje.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Jak zobrazit podrobnosti oznámení portálu

Podrobnosti o každém oznámení portálu můžete zobrazit podle následujících kroků:

1.  Vyberte ikonu **Oznámení** (zvonek) v pravém horním maši portálu Azure.

2.  Vyberte libovolné oznámení v **chybovém** stavu (ty s červenou (!) vedle nich).

    >[!NOTE]
    >Nelze klepnout na oznámení ve stavu **Úspěšné** nebo **Probíhá.**
    >
    >

4.  Další podrobnosti o problému naleznete v části **Podrobnosti o oznámení.**

5.  Pokud stále potřebujete pomoc, můžete tyto informace sdílet také s pracovníkem podpory nebo skupinou produktů a získat pomoc s problémem.

6.  Kliknutím na **ikonu** **kopírování** vpravo od textového pole **chyby Kopírovat** zkopírujte všechny podrobnosti oznámení, které chcete sdílet s pracovníkem podpory nebo pracovníkem skupiny produktů.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Jak získat pomoc odesláním podrobností oznámení pracovníkovi podpory

Je velmi důležité, abyste sdíleli **všechny níže uvedené údaje** s pracovníkem podpory, pokud potřebujete pomoc, aby vám mohli rychle pomoci. Můžete to udělat snadno **pořízením snímku obrazovky** nebo kliknutím na **ikonu chyby Kopírovat**, která se nachází vpravo od textového pole **chyby kopírovat.**

## <a name="notification-details-explained"></a>Podrobnosti oznámení vysvětleny

Další podrobnosti o oznámeních naleznete v následujících popisech.

### <a name="essential-notification-items"></a>Základní položky oznámení

- **Název** – popisný název oznámení

  * Příklad – **Nastavení proxy serveru aplikace**

- **Popis** – popis toho, co se stalo v důsledku operace

  -   Příklad – **Zadaná interní adresa URL je již používána jinou aplikací.**

- **ID oznámení** – jedinečné ID oznámení

  -   Příklad – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **ID žádosti o klienta** – konkrétní ID žádosti provedené vaším prohlížečem

  -   Příklad – **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Časové razítko UTC** – časové razítko, během kterého došlo k oznámení, v UTC

  -   Příklad – **2017-03-23T19:50:43.7583681Z**

- **Interní ID transakce** – interní ID, které můžeme použít k vyhlédnutí chyby v našich systémech

  -   Příklad – **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **HLAVNÍ uživatel** – uživatel, který operaci provedl

  -   Příklad – **tperkins\@f128.info**

- **ID klienta** – jedinečné ID klienta, který byl uživatel, který operaci provedl, byl členem

  -   Příklad – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

- **ID objektu uživatele** – jedinečné ID uživatele, který operaci provedl

  -   Příklad – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Podrobné položky oznámení

-   **Zobrazovaný název** – **(může být prázdný)** podrobnější zobrazovaný název chyby

    -   Příklad – **Nastavení proxy serveru aplikace**

-   **Status** – specifický stav oznámení

    -   Příklad – **nezdařilo se**

-   **ID objektu** – **(může být prázdný)** ID objektu, proti kterému byla operace provedena

    -   Příklad – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Podrobnosti** – podrobný popis toho, co se stalo v důsledku operace

    -   Příklad – **Interní adresa URL `https://bing.com/` je neplatná, protože je již používána.**

-   **Chyba kopírování** – Kliknutím na **ikonu kopie** vpravo od textového pole **Chyby kopírování** zkopírujete všechny podrobnosti oznámení, které chcete sdílet s podporou nebo skupinou produktů. 
-   engineer (technik)

    -   Příklad```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

