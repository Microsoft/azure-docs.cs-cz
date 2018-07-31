---
title: Správa hesel aplikací v Azure Active Directory | Dokumentace Microsoftu
description: Tato stránka vám pomůže uživatelům pomoct pochopit, co jsou hesla aplikací a jaké se používají s ohledem na dvoustupňové ověřování.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: c6340133971a226002ce11ae1521bdc88e3e7975
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343284"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Správa hesel aplikací pro dvoustupňové ověřování.

Některé aplikace nezaložené na prohlížeči, jako je například Outlook 2010, nepodporuje dvoustupňové ověřování. Tato nedostatečná podpora znamená, že pokud používáte dvoustupňové ověřování, aplikace nebude fungovat. Chcete-li tento problém vyřešit, můžete vytvořit automaticky vytvořené heslo pro použití s každou aplikaci nezaložené na prohlížeči, nezávisle na normální heslo.

Při používání hesel aplikací, je důležité pamatovat:

- Hesla aplikací jsou automaticky generované a pouze jednou zadali jednu aplikaci.

- Platí omezení 40 hesel na uživatele. Pokud se pokusíte vytvořit po tohoto limitu, budete vyzváni k odstranění stávajícího hesla před nebudou moct vytvořit nový.

- Používejte jedno heslo aplikace na zařízení, ne podle aplikací. Například vytvořte jedno heslo pro všechny aplikace na svém přenosném počítači a pak opět jedno heslo pro všechny aplikace na ploše.

    >[!Note]
    >Klienti Office 2013 (včetně Outlooku) podporují nové protokoly pro ověřování a je možné s dvoustupňovým ověřováním. Tato podpora znamená, že po zapnutí dvoustupňového ověřování, budete už nepotřebujete hesla aplikací pro klienty Office 2013. Další informace najdete v tématu [jak moderní ověřování funguje pro klientské aplikace Office 2013 a Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) článku.

## <a name="where-to-create-and-delete-your-app-passwords"></a>Kam chcete vytvářet a odstraňovat hesel aplikací

Heslo aplikace už zadaný při počátečním dvoustupňovém ověření registrace. Pokud potřebujete více než jednoho hesla, můžete vytvořit další hesla podle způsobu, jakým používáte dvoustupňové ověřování:

- **Použít dvoustupňové ověření pomocí účtu Microsoft Azure.** Vytvářet a odstraňovat pomocí hesla aplikace [webu Azure portal](https://portal.azure.com). Další informace najdete v tématu [hesla aplikací a dvoustupňové ověřování](https://support.microsoft.com/en-us/help/12409/microsoft-account-app-passwords-two-step-verification) článku.

- **Použít dvoustupňové ověření pomocí osobního účtu Microsoft.** Vytvářet a odstraňovat pomocí hesla aplikace [Základy zabezpečení](https://account.microsoft.com/account/) stránku s vaším účtem Microsoft. Další informace najdete v tématu [hesla aplikací a dvoustupňové ověřování](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) článku.

- **Dvoustupňové ověřování pomocí svůj pracovní nebo školní účet a aplikace Office 365.** Vytvoření a odstranění hesla aplikace, postupujte podle pokynů v [vytvoření a odstranění hesla aplikace pomocí portálu služeb Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) části tohoto článku.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Vytvoření a odstranění hesla aplikace pomocí portálu služeb Office 365

Pokud používáte dvoustupňové ověřování s svůj pracovní nebo školní účet a aplikace Office 365, můžete vytvořit a odstranit hesel aplikací pomocí portálu služeb Office 365. Kdykoli můžete mít maximálně 40 hesel aplikací. Pokud budete potřebovat jiné heslo aplikace po tohoto limitu, budete mít k odstranění jednoho ze stávajících hesel aplikací.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Chcete-li vytvořit hesla aplikací pomocí portálu služeb Office 365

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu.

2. Přejděte na https://portal.office.com, vyberte **nastavení** ikonu v pravém horním rohu **portál služeb Office 365** stránce a potom rozbalte **dalšího ověření zabezpečení**.

    ![Zobrazení portálu Office rozbalená oblast další bezpečnostní ověření](media/security-info/security-info-o365password.png)

3. Vyberte text, který říká, **vytvořit a spravovat hesla aplikací** otevřít **hesla aplikací** stránky.

4. Vyberte **vytvořit**, zadejte popisný název aplikace, které musí heslo aplikace a pak vyberte **Další**.

5. Vyberte **zkopírovat heslo do schránky**a pak vyberte **Zavřít**.

6. Heslo zkopírované aplikace používáte k přihlášení do aplikace nezaložené na prohlížeči. Stačí zadat toto heslo jednou a je uloží do budoucna.

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Odstranění hesla aplikace pomocí portálu služeb Office 365

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu.

2. Přejděte na https://portal.office.com, vyberte **nastavení** ikonu v pravém horním rohu **portál služeb Office 365** stránce a pak vyberte **dalšího ověření zabezpečení**.

3. Vyberte text, který říká, **vytvořit a spravovat hesla aplikací** otevřít **hesla aplikací** stránky.

4. Vyberte **odstranit** heslo aplikace odstranit, vyberte **Ano** v potvrzovacím okně a pak vyberte **Zavřít**.

    Heslo aplikace se úspěšně odstranil.

5. Postupujte podle kroků pro vytvoření heslo aplikace. Chcete-li vytvořit nové heslo aplikace.

## <a name="manage-app-passwords-in-the-azure-portal"></a>Správa hesel aplikací na webu Azure Portal

Pokud používáte dvoustupňové ověřování s Azure, budete chtít vytvořit hesla aplikací na webu Azure portal.

## <a name="manage-app-passwords-with-the-myapps-portal"></a>Správa hesel aplikací pomocí portálu MyApps

Můžete také vytvářet a odstraňovat hesla aplikací na portálu Moje aplikace.

### <a name="to-create-an-app-password-using-the-my-apps-portal"></a>Chcete-li vytvořit heslo aplikace na portálu Moje aplikace

1. Přihlaste se k [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

2. Vyberte své jméno v pravém horním rohu a vyberte **profilu**.

3. Vyberte **dalšího ověření zabezpečení**.

   ![Vyberte další bezpečnostní ověření – snímek obrazovky](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Vyberte **hesla aplikací**.

   ![Vyberte hesel aplikací – snímek obrazovky](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Klikněte na možnost **Vytvořit**.

6. Zadejte název hesla aplikace a pak vyberte **Další**.

7. Kopírovat heslo aplikace do schránky a vložte ho do vaší aplikace.
   
    ![Vytvořit heslo aplikace](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-my-apps-portal"></a>Chcete-li odstranit heslo aplikace na portálu Moje aplikace

1. Přejděte na svůj profil a potom vyberte **dalšího ověření zabezpečení**.

2. Vyberte **hesla aplikací**a pak vyberte **odstranit** vedle heslo aplikace, kterou chcete odstranit.

   ![Odstranit heslo aplikace.](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

3. Vyberte **Ano** pro potvrzení, kterou chcete odstranit heslo a pak vyberte **Zavřít**.

## <a name="if-your-app-passwords-arent-working-properly"></a>Pokud vaše hesla aplikací nejsou správně funguje.

Ujistěte se, že jste správně zadali heslo. Pokud si nejste jisti, že jste správně zadali heslo, můžete zkusit znovu přihlásit a vytvořit nové heslo aplikace. Pokud žádná z těchto možností problém vyřešit, obraťte na podporu společnosti tak, můžete odstranit stávající hesla aplikací umožňují vytvářet zcela nové značky. 

## <a name="next-steps"></a>Další postup

- [Správa nastavení dvoustupňové ověření](multi-factor-authentication-end-user-manage-settings.md)

- Vyzkoušejte si [aplikaci Microsoft Authenticator](microsoft-authenticator-app-how-to.md) ověření přihlášení s oznámení aplikací, místo příjem textů nebo volání.
