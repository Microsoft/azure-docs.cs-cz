---
title: Konfigurace oznámení a e-mailových šablon
titleSuffix: Azure API Management
description: Naučte se konfigurovat oznámení a e-mailové šablony v Azure API Management.
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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84690298"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Konfigurace oznámení a e-mailových šablon ve službě Azure API Management

API Management poskytuje možnost konfigurovat oznámení pro konkrétní události a konfigurovat e-mailové šablony, které se používají ke komunikaci se správci a vývojáři instance API Management. Tento článek ukazuje, jak nakonfigurovat oznámení pro dostupné události a poskytuje přehled konfigurace e-mailových šablon používaných pro tyto události.

## <a name="prerequisites"></a>Předpoklady

Pokud nemáte instanci služby API Management, dokončete následující rychlý Start: [vytvoření instance Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-notifications"></a><a name="publisher-notifications"> </a>Konfigurace oznámení

1.  Vyberte instanci služby **API Management** .
2.  Kliknutím na **oznámení** zobrazíte dostupná oznámení.

    ![Oznámení vydavatelů][api-management-publisher-notifications]

    Následující seznam událostí lze nakonfigurovat pro oznámení.

    -   **Žádosti o předplatné (vyžaduje schválení)** – zadaní příjemci e-mailu a uživatelé budou dostávat e-mailová oznámení o žádostech o odběr pro produkty API vyžadující schválení.
    -   **Nové předplatné** – zadaní příjemci e-mailu a uživatelé budou dostávat e-mailová oznámení o nových předplatných produktu API.
    -   **Požadavky na galerii aplikací** – zadané příjemce e-mailu a uživatelé budou dostávat e-mailová oznámení, když se do Galerie aplikací odešlou nové aplikace.
    -   **Skrytá** – zadané příjemce e-mailu a uživatelé obdrží e-mail s nezávisle kopiemi e-mailů odeslaných vývojářům.
    -   **Nový problém nebo komentář** – zadané příjemce e-mailu a uživatelé budou dostávat e-mailová oznámení při odeslání nového problému nebo komentáře na portálu pro vývojáře.
    -   **Zavřít zprávu účtu** – zadané příjemce e-mailu a uživatelé budou dostávat e-mailová oznámení v případě, že je účet uzavřený.
    -   **Blíží se kvóta předplatného** – následující příjemci e-mailu a uživatelé budou dostávat e-mailová oznámení, když se použití předplatného blíží kvótě využití.

        > [!NOTE]
        > Oznámení se aktivují jenom pomocí zásad [předplatného](api-management-access-restriction-policies.md#SetUsageQuota) . [Kvóta podle klíčových](api-management-access-restriction-policies.md#SetUsageQuotaByKey) zásad negeneruje oznámení.

    Pro každou událost můžete zadat příjemce e-mailu pomocí textového pole e-mailová adresa nebo můžete vybrat uživatele ze seznamu.

3.  Pokud chcete zadat e-mailové adresy, které chcete oznámit, zadejte je do textového pole e-mailová adresa. Pokud máte více e-mailových adres, oddělte je čárkami.

    ![Příjemci oznámení][api-management-email-addresses]

4.  Stiskněte **Přidat**.

## <a name="configure-notification-templates"></a><a name="email-templates"> </a>Konfigurace šablon oznámení

API Management poskytuje šablony oznámení pro e-mailové zprávy, které se odesílají v průběhu správy a používání služby. K dispozici jsou následující e-mailové šablony.

-   Schválená odeslání Galerie aplikací
-   Farewell – písmeno pro vývojáře
-   Oznámení o přístupu k omezení kvóty pro vývojáře
-   Pozvat uživatele
-   Byl přidán nový komentář k problému.
-   Byl přijat nový problém.
-   Aktivované nové předplatné
-   Potvrzení obnovení předplatného
-   Žádosti o předplatné se odmítnou.
-   Žádost o předplatné přijata

Tyto šablony lze upravit podle potřeby.

Chcete-li zobrazit a nakonfigurovat e-mailové šablony pro instanci API Management, klikněte na možnost **šablony oznámení**.

![Šablony e-mailů][api-management-email-templates]

Každá šablona e-mailu má předmět v prostém textu a definici těla ve formátu HTML. Každou položku lze podle potřeby přizpůsobit.

![Editor šablon e-mailu][api-management-email-template]

Seznam **parametrů** obsahuje seznam parametrů, které při vložení do předmětu nebo textu nahradí určenou hodnotu při odeslání e-mailu. Chcete-li vložit parametr, umístěte kurzor na místo, kam chcete použít parametr, a klikněte na šipku nalevo od názvu parametru.

> [!NOTE]
> Při náhledu nebo odeslání testu nejsou parametry nahrazeny skutečnými hodnotami.

Pokud chcete změny uložit do e-mailové šablony, klikněte na **Uložit** nebo změny zrušte kliknutím na **Zahodit**.

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
