---
title: Přihlášení k Azure Notebooks
description: Rychle se přihlaste do notebooků Azure a nastavte ID uživatele, které vám umožní přístup k uloženým projektům a sdílení poznámkových bloků s ostatními.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: fb8c94b1-6d0a-4b77-8d14-ae6efcdd99f4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: kraigb
ms.openlocfilehash: 0c12c8673ff61dcca62c72addcbf0d9b33c2c252
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973062"
---
# <a name="quickstart-sign-in-and-set-a-user-id"></a>Rychlý Start: přihlášení a nastavení ID uživatele

I když můžete Azure Notebooks vždy zobrazovat bez přihlašování, musíte se přihlásit, abyste mohli spustit poznámkové bloky, přistupovat k uloženým projektům a poznámkovým blokům a sdílet poznámkové bloky s ostatními.

## <a name="sign-in"></a>Přihlásit se

1. V pravém horním rohu [Notebooks.Azure.com](https://notebooks.azure.com/)vyberte **Přihlásit** .

    ![Umístění příkazu pro přihlášení na Azure Notebooks](media/accounts/sign-in-command.png)

1. Po zobrazení výzvy zadejte e-mailovou adresu účtu Microsoft nebo pracovní nebo školní účet a vyberte **Další**. Typy účtů jsou popsány v [uživatelském účtu pro Azure Notebooks](azure-notebooks-user-account.md). Pokud nemáte účet Microsoft nebo ho chcete použít přímo s Azure Notebooks, vyberte **vytvořit**:

    ![Vytvořit nový příkaz účet Microsoft v příkazovém řádku pro přihlášení](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Pokud se pokusíte vytvořit nový účet s e-mailovou adresou, na které už je přidružený účet, může se vám zobrazit zpráva "nemůžete se zaregistrovat tady pomocí pracovní nebo školní e-mailové adresy. Použijte osobní e-mail, jako je například Gmail nebo Yahoo!, nebo Získejte nový Outlookový e-mail. V takovém případě se zkuste přihlásit pomocí pracovní e-mailové adresy, aniž byste museli vytvořit nový účet.

1. Po zobrazení výzvy zadejte své heslo.

1. Pokud se přihlašujete poprvé, Azure Notebooks požádá o oprávnění k přístupu k vašemu účtu. Pokračujte výběrem **Ano** :

    ![Výzva k zadání oprávnění účtu](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Nastavení ID uživatele

1. Po prvním přihlášení se vám přiřadí dočasné ID uživatele, jako je například "anon-idrca3". Pokaždé, když máte ID uživatele, které začíná na "anon-", Azure Notebooks vás vyzve k vytvoření vlastního ID. Vaše ID uživatele se používá v jakékoli adrese URL, kterou získáte pro sdílení vašich projektů a poznámkových bloků, takže vyberte něco, co je pro vás jedinečné a smysluplné.

    ![Výzva k zadání ID uživatele pro Azure Notebooks](media/accounts/create-user-id.png)

    Pokud vyberete **ne**, Azure Notebooks se při každém přihlášení nadále zobrazovat výzva k zadání ID uživatele. Vaše ID uživatele můžete také nastavit kdykoli v [profilu uživatele](azure-notebooks-user-profile.md).

1. Po úspěšném přihlášení Azure Notebooks přejít na stránku veřejného profilu, na které můžete vybrat **Upravit informace o profilu** a vyplnit zbývající informace. (Další informace najdete v [profilu a ID uživatele](azure-notebooks-user-profile.md)):

    ![Počáteční zobrazení stránky profilu Azure Notebooks](media/accounts/profile-page-new.png)

> [!NOTE]
> Pokud se zobrazí zpráva "ID uživatele se už používá", zkuste jiné ID. ID uživatelů jsou v rámci všech účtů Azure Notebooks jedinečné a Azure Notebooks také rezervují určitá ID uživatelů, například názvy značek společnosti Microsoft.

## <a name="sign-out"></a>Odhlásit se

Pokud se chcete odhlásit, vyberte své uživatelské jméno v pravém horním rohu stránky a pak vyberte **Odhlásit**:

![Umístění příkazu k odhlášení na Azure Notebooks](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rychlý Start: vytvoření a sdílení poznámkového bloku](quickstart-create-share-jupyter-notebook.md)
