---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: 5bc00f4de95d22eec71f9b1b2504b00f506232dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99213926"
---
### <a name="to-sign-up-for-a-sendgrid-account"></a>Registrace účtu SendGrid
1. Přihlaste se na [Azure Portal][Azure portal].
2. V nabídce Azure Portal nebo na domovské stránce vyberte **vytvořit prostředek**.

    ![Snímek obrazovky Azure Portal nabídky s vybranou možností vytvořit prostředek][command-bar-new]
3. Vyhledejte a vyberte **SendGrid**.

    ![Snímek obrazovky Azure Portal Marketplace zobrazující ve vyhledávacím poli "SendGr" a ve výsledcích hledání SendGrid vybrané.][sendgrid-store]
4. Vyplňte registrační formulář a vyberte **Vytvořit**.

    ![Snímek obrazovky dialogového okna vytvořit nový účet SendGrid s vyplněnými poli název, heslo, předplatné a skupina prostředků][sendgrid-create]
5. Zadejte **Název** pro identifikaci služby SendGrid v nastavení Azure. Název musí mít délku 1 až 100 znaků a musí obsahovat pouze alfanumerické znaky, pomlčky, tečky a podtržítka. Název musí být v seznamu předplacených položek Azure Storu jedinečný.
6. Zadejte a potvrďte **Heslo**.
7. Vyberte své **předplatné**.
8. Vytvořte novou **Skupinu prostředků**, nebo použijte existující.
9. V části **Cenová úroveň** vyperte plán SendGrid, který si chcete zaregistrovat.

    ![Snímek obrazovky s dialogovým oknem vytvořit nový účet SendGrid s otevřeným oddílem zvolit cenovou úroveň][sendgrid-pricing]
10. Zadejte **Propagační kód**, pokud nějaký máte.
11. Zadejte **kontaktní informace**.
12. Přečtěte si a přijměte **Právní podmínky**.
13. Po potvrzení nákupu se zobrazí automaticky otevírané okno pro **úspěšné nasazení** a zobrazí se Váš účet.

    ![Snímek obrazovky se stránkou s účty SendGrid, na kterých je zobrazený nový ContosoSendGrid účtu.][all-resources]

    Jakmile dokončíte nákup a kliknutím na tlačítko **Manage** (Spravovat) zahájíte proces ověření e-mailu, přijde vám od SendGridu e-mail s výzvou k ověření účtu. Pokud tento e-mail neobdržíte nebo máte problémy s ověřením účtu, přečtěte si naše Nejčastější dotazy.

    ![Snímek stránky ContosoSendGrid účtu s zvýrazněným tlačítkem spravovat][manage]

    **Do ověření účtu můžete odesílat maximálně pouze 100 e-mailů za den.**

    Pokud chcete upravit plán předplatného nebo zobrazit nastavení kontaktů SendGridu, kliknutím na název vaší služby SendGrid otevřete řídicí panel SendGrid na Marketplace.

    ![Snímek obrazovky zobrazující, že stránka nastavení pro účet ContosoSendGrid je otevřená, výběrem možnosti všechna nastavení na stránce účtu ContosoSendGrid.][settings]

    Abyste mohli odesílat e-maily pomocí SendGridu, musíte zadat klíč rozhraní API.

### <a name="to-find-your-sendgrid-api-key"></a>Vyhledání klíče rozhraní API pro SendGrid
1. Klikněte na **Manage** (Spravovat).

    ![Snímek stránky ContosoSendGrid účtu s zvýrazněným tlačítkem spravovat][manage]
2. Na řídicím panelu SendGrid vyberte **Settings** (Nastavení) a potom **API Keys** (Klíče rozhraní API) v nabídce na levé straně.

    ![Snímek obrazovky řídicího panelu SendGrid se zvoleným rozevíracím seznamem nastavení a vybranými klíči rozhraní API][api-keys]

3. Klikněte na tlačítko **vytvořit klíč rozhraní API**.

    ![Snímek obrazovky s klíči rozhraní API s vybraným tlačítkem vytvořit klíč rozhraní API][general-api-key]
4. Zadejte minimálně hodnotu **Name of this key** (Název tohoto klíče) a umožněte úplný přístup k funkci **Mail Send** (Odesílání e-mailů) a vyberte **Save** (Uložit).

    ![Snímek obrazovky Přidat nový klíč Obecné rozhraní API s možností odeslat poštu nastavit na úplný přístup, naplánované odesílání se nastaví na bez přístupu a zvýrazněné tlačítko Uložit.][access]
5. V tomto okamžiku se jednou zobrazí váš klíč rozhraní API. Bezpečně si jej uložte.

### <a name="to-find-your-sendgrid-credentials"></a>Vyhledání přihlašovacích údajů SendGrid
1. **Uživatelské jméno** najdete kliknutím na ikonu klíče.

    ![Snímek stránky ContosoSendGrid účtu se zvýrazněnou ikonou klíče][key]
2. Heslo jste si zvolili během nastavení. Pokud chcete provést změny, můžete vybrat **Change password** (Změnit heslo) nebo **Reset password** (Resetovat heslo).

Pokud chcete spravovat nastavení doručitelnosti e-mailů, klikněte na **tlačítko Manage** (Spravovat). Tím se přesměruje na řídicí panel SendGrid.

![Snímek stránky ContosoSendGrid účtu s zvýrazněným tlačítkem spravovat][manage]

Další informace o posílání e-mailů prostřednictvím SendGrid najdete v článku [Přehled rozhraní API pro e-maily][Email API Overview].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
