---
title: Konfigurace oznámení a e-mailových šablon ve službě Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat oznámení a e-mailových šablon ve službě Azure API Management.
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
ms.openlocfilehash: 16beaadae36dfc7445a88875d36786bd97889599
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445069"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Konfigurace oznámení a e-mailových šablon ve službě Azure API Management
API Management nabízí možnost nakonfigurovat oznámení pro konkrétní události a nakonfigurovat e-mailové šablony, které se používají ke komunikaci s správci a vývojáři instance služby API Management. Tento článek ukazuje, jak nakonfigurovat oznámení dostupná událostem a poskytuje přehled o konfiguraci e-mailové šablony pro tyto události.

## <a name="prerequisites"></a>Požadavky

Pokud jste instanci služby API Management, projděte si následující rychlý start: [vytvoření instance Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="publisher-notifications"> </a>Konfigurace oznámení

1. Vyberte vaše **API MANAGEMENT** instance.
2. Klikněte na tlačítko **oznámení** Chcete-li zobrazit dostupné oznámení.

    ![Vydavatele oznámení][api-management-publisher-notifications]

    Následující seznam událostí může být nakonfigurována pro oznámení.

    * **Žádosti o předplatné (které vyžadují schválení)** -zadané e-mailové příjemce a uživatelé obdrží e-mailová oznámení o žádosti rozhraní API produkty, které vyžadují schválení.
    * **Nová předplatná** -zadané e-mailové příjemce a uživatelé obdrží e-mailová oznámení o nových předplatných produktu API.
    * **Žádosti o aplikace Galerie** -zadané e-mailové příjemce a uživatelé obdrží e-mailová oznámení při odeslání nové aplikace v galerii aplikací.
    * **SKRYTÁ** -zadané e-mailové příjemce a uživatelé obdrží e-mailu nevidomí kopii všechny e-maily odeslané na vývojáře.
    * **Nový problém nebo komentář** – zadané e-mailové příjemce a uživatelé obdrží e-mailová oznámení, když se vytvoří nový problém nebo komentář je odesíláno na portálu pro vývojáře.
    * **Zavřít účet zpráva** -zadané e-mailové příjemce a uživatelé obdrží e-mailová oznámení po uzavření účtu.
    * **Blížící se omezení kvóty předplatného** -následující příjemců e-mailu a uživatelé obdrží e-mailová oznámení při využití předplatného blíží kvótu využití.

    Pro každou událost můžete zadat příjemců e-mailu pomocí e-mailová adresa textového pole nebo uživatelům můžete vybrat ze seznamu.

3. Zadejte e-mailové adresy, abyste dostávali oznámení, zadejte je v textovém poli e-mailovou adresu. Pokud máte více e-mailové adresy, oddělte je čárkami.

    ![Příjemce oznámení][api-management-email-addresses]
4. Stiskněte **Přidat**.

## <a name="email-templates"> </a>Konfigurace šablon oznámení
API Management nabízí šablony oznámení pro e-mailové zprávy, které jsou odeslány při správě a používání služby. Jsou k dispozici následující e-mailové šablony.

* Odeslání aplikace Galerie schválení
* Písmeno farewell pro vývojáře
* Maximální kvóta vývojář blíží oznámení
* Pozvat uživatele
* Nový komentář přidán k problému
* Nový problém přijetí
* Aktivovat nové předplatné
* Potvrzení odběru obnovit
* Odmítne žádost o odběr
* Byla přijata žádost o předplatné

Tyto šablony lze upravit podle potřeby.

Chcete-li zobrazit a konfigurovat e-mailové šablony pro vaše instance služby API Management, klikněte na tlačítko **šablon oznámení**.

![Šablony e-mailů][api-management-email-templates]

Každou e-mailová šablona má subjektu ve formátu prostého textu a definice text ve formátu HTML. Každá položka je přizpůsobit podle potřeby.

![Editor šablon e-mailu][api-management-email-template]

**Parametry** seznam obsahuje seznam parametrů, který se při vložit do předmětu nebo obsahu, budou nahrazeny určenou hodnotu při odeslání e-mailu. Vložit parametr, umístěte kurzor, kde chcete, aby parametr přejděte a klikněte na šipku nalevo od názvu parametru.

> [!NOTE] 
> Parametry nejsou nahradit skutečnými hodnotami při zobrazení náhledu nebo odeslání testu.

Chcete-li uložit změny do e-mailovou šablonu, klikněte na tlačítko **Uložit**, nebo zrušit změny, klikněte na tlačítko **zahodit**.
 

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
