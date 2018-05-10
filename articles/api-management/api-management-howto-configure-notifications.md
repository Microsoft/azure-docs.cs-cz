---
title: Konfigurace oznámení a e-mailových šablon ve službě Azure API Management | Microsoft Docs
description: Zjistěte, jak konfigurace oznámení a e-mailových šablon ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: apimpm
ms.openlocfilehash: 60788f76dac58ead10e43e892d587a86bdd3fcad
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Konfigurace oznámení a e-mailových šablon ve službě Azure API Management
API Management nabízí možnost konfigurace oznámení pro konkrétní události a nakonfigurovat e-mailových šablon, které se používají ke komunikaci s správci a vývojáři instance služby API Management. Tento článek ukazuje, jak nakonfigurovat oznámení události, k dispozici a obsahuje základní informace o konfiguraci e-mailových šablon pro tyto události.

## <a name="prerequisites"></a>Požadavky

Pokud jste instanci služby API Management, proveďte následující rychlý start: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).

## <a name="publisher-notifications"> </a>Konfigurace oznámení

1. Vyberte vaše **API MANAGEMENT** instance.
2. Klikněte na tlačítko **oznámení** Chcete-li zobrazit dostupné oznámení.

    ![Vydavatele oznámení][api-management-publisher-notifications]

    Následující seznam událostí může být nakonfigurována pro oznámení.

    * **Žádostí o odběr (které vyžadují schválení)** -příjemců zadané e-mailu a uživatelé dostanou e-mailová oznámení o odběru požadavky pro rozhraní API produkty, které vyžadují schválení.
    * **Nová předplatná** -příjemců zadané e-mailu a uživatelé dostanou e-mailová oznámení o nových předplatných rozhraní API produktu.
    * **Žádosti o aplikace Galerie** -příjemců zadané e-mailu a uživatelé obdrží e-mailová oznámení při nové aplikace se odešlou do Galerie aplikací.
    * **SKRYTÁ** -příjemců zadané e-mailu a uživatelé budou obdrží e-mailu skrytá kopii všechny e-maily odesílané pro vývojáře.
    * **Nový problém nebo komentář** – příjemců zadané e-mailu a uživatelé dostanou e-mailová oznámení, když nový problém nebo komentář je odesíláno na portál pro vývojáře.
    * **Zprávu zavřete účtu** -příjemců zadané e-mailu a uživatelé dostanou e-mailová oznámení po uzavření účtu.
    * **Blížící limit kvóty předplatného** -následující příjemců e-mailu a uživatelé obdrží e-mailová oznámení při použití předplatné získá brzy bude dosaženo kvóty využití.

    U každé události lze zadat příjemců e-mailu pomocí e-mailovou adresu textového pole nebo můžete vybrat uživatele ze seznamu.

3. Pokud chcete zadat e-mailové adresy, které mají být informování, zadejte je do textového pole e-mailovou adresu. Pokud máte více e-mailové adresy, oddělte je čárkami.

    ![Příjemců oznámení][api-management-email-addresses]
4. Stiskněte **Přidat**.

## <a name="email-templates"> </a>Konfigurace šablon oznámení
API Management poskytuje šablony oznámení pro e-mailové zprávy, které se odesílají při správě a používání služby. Následující e-mailové šablony jsou k dispozici.

* Odeslání aplikace Galerie schválení
* Písmeno farewell vývojáře
* Limit kvóty vývojáře blíží oznámení
* Pozvat uživatele
* Nový komentář přidat problému
* Nový problém přijat
* Nové předplatné aktivované
* Potvrzení odběru obnovit
* Odmítne požadavek na odběr
* Přišel požadavek na předplatné

Tyto šablony se dá změnit podle potřeby.

Chcete-li zobrazit a konfigurovat e-mailových šablon pro vaše instance služby API Management, klikněte na tlačítko **šablony oznámení**.

![Šablony e-mailů][api-management-email-templates]

Každý e-mailové šablony má předmět ve formátu prostého textu a definice text ve formátu HTML. Každá položka se dají přizpůsobit podle potřeby.

![Editor šablony e-mailu][api-management-email-template]

**Parametry** seznam obsahuje seznam parametrů, které v případě vložit do předmětu nebo textu, bude nahrazen určenou hodnotu, při odeslání e-mailu. Pokud chcete vložit parametr, umístěte kurzor, kde chcete parametru přejděte a klikněte na šipku nalevo od názvu parametru.

> [!NOTE] 
> Parametry nejsou nahrazené skutečnými hodnotami při zobrazení náhledu nebo odeslání testu.

Chcete-li uložit změny do šablony e-mailu, klikněte na tlačítko **Uložit**, nebo zrušit změny, klikněte na tlačítko **zahodit**.
 

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
