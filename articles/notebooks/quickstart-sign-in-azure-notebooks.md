---
title: Přihlášení k poznámkovým blokům Azure Preview
description: Rychle se přihlaste k náhledu poznámkových bloků Azure a nastavte ID uživatele, které vám umožní přistupovat k uloženým projektům a sdílet poznámkové bloky s ostatními.
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: b6572a7c0b965b2b72916db577b47eff4f1921c0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75647012"
---
# <a name="quickstart-sign-in-and-set-a-user-id-for-azure-notebooks-preview"></a>Úvodní příručka: Přihlášení a nastavení ID uživatele pro azure notebooky Preview

I když můžete vždy zobrazit poznámkové bloky Azure bez přihlášení, musíte se přihlásit, abyste mohli spouštět poznámkové bloky, přistupovat k uloženým projektům a poznámkovým blokům a sdílet poznámkové bloky s ostatními.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="sign-in"></a>Přihlášení

1. Vyberte **Přihlásit se v** pravém horním rohu [notebooks.azure.com](https://notebooks.azure.com/).

    ![Umístění příkazu pro přihlášení v poznámkových blocích Azure](media/accounts/sign-in-command.png)

1. Po zobrazení výzvy zadejte e-mailovou adresu účtu Microsoft nebo pracovního nebo školního účtu a vyberte **Další**. Typy účtů jsou popsány v [uživatelském účtu pro poznámkové bloky Azure](azure-notebooks-user-account.md). Pokud účet Microsoft nemáte nebo ho chcete vytvořit speciálně pro poznámkové bloky Azure, vyberte **Vytvořit jeden:**

    ![Vytvoření nového příkazu účtu Microsoft v přihlašovacím řádku](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Pokud se pokusíte vytvořit nový účet s e-mailovou adresou, ke které je již přidružen účet, může se zobrazit zpráva "Nemůžete se sem zaregistrovat pomocí pracovní nebo školní e-mailové adresy. Použijte osobní e-mail, například Gmail nebo Yahoo!, nebo si pořažte nový e-mail outlooku." V takovém případě zkuste přihlásit pomocí pracovní e-mailové adresy bez vytvoření nového účtu.

1. Po zobrazení výzvy zadejte své heslo.

1. Pokud se přihlašujete poprvé, poznámkové bloky Azure vás požádá o oprávnění k přístupu k vašemu účtu. Chcete-li pokračovat, vyberte **možnost Ano:**

    ![Výzva k oprávnění účtu](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Nastavení ID uživatele

1. Při prvním přihlášení vám bude přiřazeno dočasné ID uživatele, například "anon-idrca3". Kdykoli máte ID uživatele, které začíná na "anon-", poznámkové bloky Azure vás vyzve k vytvoření vlastního ID. Vaše ID uživatele se používá v libovolné adrese URL, kterou získáte ke sdílení projektů a poznámkových bloků, takže si vyberte něco, co je pro vás jedinečné a smysluplné.

    ![Výzva k zadání ID uživatele pro poznámkové bloky Azure](media/accounts/create-user-id.png)

    Pokud vyberete **Ne díky**, poznámkové bloky Azure nadále vás vyzve k id uživatele při každém přihlášení. Id uživatele lze také kdykoli nastavit ve vašem [uživatelském profilu](azure-notebooks-user-profile.md).

1. Po úspěšném přihlášení se poznámkové bloky Azure přepnou na stránku vašeho veřejného profilu, na které můžete vybrat **Upravit informace o profilu** a vyplnit zbytek informací (další informace najdete v [tématu Váš profil a ID uživatele](azure-notebooks-user-profile.md)):

    ![Počáteční zobrazení stránky profilu poznámkových bloků Azure](media/accounts/profile-page-new.png)

> [!NOTE]
> Pokud se zobrazí zpráva "ID uživatele je již používáno", zkuste jiné ID. ID uživatelů jsou jedinečná ve všech účtech poznámkových bloků Azure a poznámkové bloky Azure si také rezervují určitá ID uživatelů, jako jsou značky Microsoftu.

## <a name="sign-out"></a>Odhlásit se

Pokud se chcete odhlásit, vyberte své uživatelské jméno v pravém horním setu stránky a pak vyberte **Odhlásit se**:

![Umístění příkazu pro odhlášení v poznámkových blocích Azure](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Úvodní příručka: Vytvoření a sdílení poznámkového bloku](quickstart-create-share-jupyter-notebook.md)
