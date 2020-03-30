---
title: Konfigurace oznámení a šablon e-mailů
titleSuffix: Azure API Management
description: Zjistěte, jak nakonfigurovat oznámení a šablony e-mailů ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 786a9e26003a7afb98307e0bd7fae94c42a2f00d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244066"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Konfigurace oznámení a e-mailových šablon ve službě Azure API Management

Správa rozhraní API umožňuje konfigurovat oznámení pro konkrétní události a konfigurovat šablony e-mailů, které se používají ke komunikaci se správci a vývojáři instance api Management. Tento článek ukazuje, jak nakonfigurovat oznámení pro dostupné události a poskytuje přehled konfigurace e-mailových šablon používaných pro tyto události.

## <a name="prerequisites"></a>Požadavky

Pokud nemáte instanci služby Api Management, proveďte následující rychlý start: [Vytvoření instance Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-notifications"></a><a name="publisher-notifications"> </a>Konfigurace oznámení

1.  Vyberte instanci **API MANAGEMENT.**
2.  Kliknutím na **Oznámení** zobrazíte dostupná oznámení.

    ![Oznámení pro vydavatele][api-management-publisher-notifications]

    Následující seznam událostí lze nakonfigurovat pro oznámení.

    -   **Žádosti o předplatné (vyžadující schválení)** – zadali příjemci e-mailu a uživatelé obdrží e-mailová oznámení o žádostech o předplatné pro produkty rozhraní API vyžadující schválení.
    -   **Nová předplatná** – zadali příjemci e-mailu a uživatelé obdrží e-mailová oznámení o nových předplatných produktů rozhraní API.
    -   **Žádosti o galerii aplikací** – Zadali příjemci e-mailu a uživatelé obdrží e-mailová oznámení při odeslání nových žádostí do galerie aplikací.
    -   **BCC** - Zadali příjemci e-mailu a uživatelé obdrží e-mail emblém ové kopie všech e-mailů odeslaných vývojářům.
    -   **Nový problém nebo komentář** – Zadali příjemci e-mailu a uživatelé obdrží e-mailová oznámení při odeslání nového problému nebo komentáře na portál pro vývojáře.
    -   **Zpráva o účtu -** Zadali příjemci e-mailu a uživatelé obdrží e-mailová oznámení při uzavření účtu.
    -   **Blížící se limit kvóty předplatného** – následující příjemci e-mailu a uživatelé obdrží e-mailová oznámení, když se využití předplatného přiblíží kvótě využití.

        > [!NOTE]
        > Oznámení se aktivují kvótou pouze podle zásad [předplatného.](api-management-access-restriction-policies.md#SetUsageQuota) [Kvóta podle zásad klíče](api-management-access-restriction-policies.md#SetUsageQuotaByKey) negeneruje oznámení.

    Pro každou událost můžete určit příjemce e-mailu pomocí textového pole e-mailové adresy nebo můžete vybrat uživatele ze seznamu.

3.  Chcete-li zadat e-mailové adresy, které mají být oznámeny, zadejte je do textového pole e-mailové adresy. Pokud máte více e-mailových adres, oddělte je pomocí čárek.

    ![Příjemci oznámení][api-management-email-addresses]

4.  Stiskněte **Přidat**.

## <a name="configure-notification-templates"></a><a name="email-templates"> </a>Konfigurace šablon oznámení

Správa rozhraní API poskytuje šablony oznámení pro e-mailové zprávy, které jsou odesílány v průběhu správy a používání služby. K dispozici jsou následující šablony e-mailů.

-   Odeslání galerie žádostí schváleno
-   Developer rozloučení dopis
-   Limit kvóty pro vývojáře blížící se oznámení
-   Pozvat uživatele
-   K problému byl přidán nový komentář
-   Bylo přijato nové číslo
-   Nové předplatné aktivováno
-   Potvrzení obnoveného předplatného
-   Žádost o předplatné se odmítne.
-   Přijatý požadavek na předplatné

Tyto šablony lze upravit podle potřeby.

Pokud chcete zobrazit a nakonfigurovat šablony e-mailů pro instanci Správy rozhraní API, klikněte na **Šablony oznámení**.

![Šablony e-mailů][api-management-email-templates]

Každá šablona e-mailu má předmět ve formátu prostého textu a definici těla ve formátu HTML. Každá položka může být přizpůsobena podle potřeby.

![Editor šablon e-mailu][api-management-email-template]

Seznam **Parametry** obsahuje seznam parametrů, které budou po vložení do předmětu nebo těla při odeslání e-mailu nahrazeny určenou hodnotou. Chcete-li vložit parametr, umístěte kurzor na místo, kam chcete parametr přejít, a klepněte na šipku vlevo od názvu parametru.

> [!NOTE]
> Parametry nejsou nahrazeny skutečnými hodnotami při náhledu nebo odeslání testu.

Chcete-li změny uložit do šablony e-mailu, klepněte na tlačítko **Uložit**nebo zrušit změny, klepněte na **tlačítko Zahodit**.

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
[configure publisher notifications]: #publisher-notifications
[configure email templates]: #email-templates
[how to create and use groups]: api-management-howto-create-groups.md
[how to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[get started with azure api management]: get-started-create-service-instance.md
[create an api management service instance]: get-started-create-service-instance.md
