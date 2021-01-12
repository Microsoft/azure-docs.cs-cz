---
title: Řešení běžných potíží při přidávání nebo odebírání aplikace k Azure Active Directory
description: Řešení běžných problémů, které lidé při přidávání nebo odebírání aplikace do Azure Active Directory čelí
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/11/2018
ms.author: kenwith
ms.openlocfilehash: f873608d07785e6b99bd8e0fcdadf64a8899d490
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108411"
---
# <a name="troubleshoot-common-problem-adding-or-removing-an-application-to-azure-active-directory"></a>Řešení běžných potíží při přidávání nebo odebírání aplikace k Azure Active Directory
Tento článek vám pomůže pochopit běžné problémy, které lidé čelí při přidávání nebo odebírání aplikace do Azure Active Directory.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Kliknuli jsem na tlačítko Přidat a aplikace trvala dlouhou dobu.
Za určitých okolností může trvat 1-2 minut (a někdy delší), než se aplikace zobrazí po přidání do adresáře. I když se nejedná o normální očekávaný výkon, můžete zobrazit Přidání aplikace kliknutím na ikonu **oznámení** (zvonek) v pravém horním rohu [Azure Portal](https://portal.azure.com/) a vyhledáním **probíhajícího** nebo **dokončeného** oznámení s popiskem **Přidat aplikaci.**

Pokud se vaše aplikace nikdy nepřidala nebo při kliknutí na tlačítko **Přidat** dojde k chybě, zobrazí se **oznámení** v **chybovém** stavu. Pokud chcete získat další informace o chybě, abyste se seznámili s pracovníkem technické podpory nebo je mohli sdílet, můžete zobrazit další informace o chybě pomocí postupu uvedeného v části [jak zobrazit podrobnosti o oznámení na portálu](#how-to-see-the-details-of-a-portal-notification) .

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Klikli na tlačítko Přidat a moje aplikace se nezobrazila
Kvůli přechodným problémům, potížím se sítí nebo chybě se ale přidání aplikace nepodaří. K tomu dojde, když kliknete na ikonu **oznámení** (zvonek) v pravém horním rohu Azure Portal a zobrazí se vedle oznámení o **Přidání aplikace** červená ikona (!). To znamená, že při vytváření aplikace došlo k chybě.

Pokud při kliknutí na tlačítko **Přidat** dojde k chybě, zobrazí se **oznámení** v **chybovém** stavu. Pokud chcete získat další informace o chybě, abyste se seznámili s pracovníkem technické podpory nebo je mohli sdílet, můžete zobrazit další informace o chybě pomocí postupu uvedeného v části [jak zobrazit podrobnosti o oznámení na portálu](#how-to-see-the-details-of-a-portal-notification) .

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Nevím, jak nastavit aplikaci, když ji přidáte
Pokud potřebujete pomáhat se seznámení s aplikacemi, [Seznam kurzů, jak integrovat aplikace SaaS s Azure Active Directorym](../saas-apps/tutorial-list.md) článkem, je dobrým místem, kde začít.

Kromě toho [Knihovna dokumentů aplikací Azure AD](./what-is-application-management.md) pomáhá získat další informace o jednotném přihlašování pomocí služby Azure AD a o tom, jak to funguje.

## <a name="i-want-to-delete-an-application-but-the-delete-button-is-disabled"></a>Chci odstranit aplikaci, ale tlačítko Odstranit je zakázané

Tlačítko Odstranit bude zakázáno v následujících scénářích:

- Pro aplikace v rámci podnikové aplikace, pokud nemáte jednu z následujících rolí: globální správce, správce cloudové aplikace, Správce aplikací nebo vlastník instančního objektu.

- V případě aplikace Microsoftu je nebudete moct odstranit z uživatelského rozhraní bez ohledu na vaši roli.

- Pro servicePrincipals, které odpovídají spravované identitě. Objekty služby spravované identity se v okně podnikové aplikace nedají odstranit. Abyste ho mohli spravovat, musíte přejít do prostředku Azure. Další informace o [spravované identitě](../managed-identities-azure-resources/overview.md)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Jak zobrazit podrobnosti oznámení na portálu
Podrobnosti o jakémkoli oznámení na portálu můžete zobrazit podle následujících kroků:
1.  V pravém horním rohu Azure Portal vyberte ikonu **oznámení** (zvonek).
2.  Vyberte jakékoli oznámení v **chybovém** stavu (vedle sebe se červenou (!)).
    >[!NOTE]
    >Oznámení nemůžete kliknout v **úspěšném** **nebo** probíhajícím stavu.
4.  Použijte informace v části **Podrobnosti oznámení** , abyste porozuměli dalším podrobnostem o problému.
5.  Pokud stále potřebujete pomoc, můžete tyto informace také sdílet s pracovníkem podpory nebo produktovou skupinou, abyste získali pomoc s vaším problémem.
6.  Kliknutím na **ikonu Kopírovat** napravo od textového pole **Kopírovat chybu** zkopírujte všechny podrobnosti oznámení, které chcete sdílet s pracovníkem podpory nebo produktovou skupinou.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Jak získat pomoc odesláním podrobností oznámení pracovníkovi podpory
Pokud potřebujete pomoc, je důležité, abyste nasdíleli **všechny níže uvedené podrobnosti** s pracovníkem podpory, aby vám mohli rychle pomoci. **Poznamenejte si snímek obrazovky** nebo vyberte **ikonu Chyba kopírování**, která se nachází na pravé straně textového pole **Chyba kopírování** .

## <a name="notification-details-explained"></a>Vysvětlení podrobností oznámení
Další podrobnosti o oznámeních najdete v následujících popisech.

### <a name="essential-notification-items"></a>Základní položky oznámení
- **Title** – popisný název oznámení
  * Příklad – **nastavení proxy aplikace**
- **Popis** – popis toho, co se stalo v důsledku operace
  -   Příklad – **zadaná interní adresa URL se už používá v jiné aplikaci** .
- **ID oznámení** – jedinečné ID oznámení
  -   Příklad – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**
- **ID žádosti klienta** – konkrétní ID žádosti provedené prohlížečem
  -   Příklad – **302fd775-3329-4670-a9f3-bea37004f0bc**
- **Časové razítko UTC** – časové razítko, během kterého došlo k oznámení, v UTC
  -   Příklad – **2017-03-23T19:50:43.7583681 z**
- **ID interní transakce** – interní ID, které můžeme použít k vyhledání chyby v našich systémech
  -   Příklad – **71a2f329-ca29-402F-aa72-bc00a7aca603**
- **UPN** – uživatel, který operaci provedl
  -   Příklad – **tperkins \@ f128.info**
- **ID tenanta** – jedinečné ID tenanta, kterého byl uživatel, který provedl operaci, členem
  -   Příklad – **7918d4b5-0442-4a97-be2d-36f9f9962ece**
- **ID objektu uživatele** – jedinečné ID uživatele, který operaci provedl.
  -   Příklad – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Podrobné položky oznámení
-   **Zobrazované jméno** – **(může být prázdné)** podrobnější zobrazované jméno pro chybu
    -   Příklad – **nastavení proxy aplikace**
-   **Stav** – konkrétní stav oznámení
    -   Příklad – **selhání**
-   **ID objektu** – **(může být prázdné)** ID objektu, proti kterému se operace prováděla.
    -   Příklad – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**
-   **Podrobnosti** – podrobný popis toho, co se stalo v důsledku operace
    -   Příklad – **interní adresa URL `https://bing.com/` je neplatná, protože se už používá** .
-   **Chyba kopírování** – kliknutím na **ikonu kopírování** napravo od textového pole **Kopírovat chybu** zkopírujte všechny podrobnosti oznámení, které chcete sdílet s podporou nebo produktovou skupinou. 
-   engineer (technik)
    -   Případě ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```
