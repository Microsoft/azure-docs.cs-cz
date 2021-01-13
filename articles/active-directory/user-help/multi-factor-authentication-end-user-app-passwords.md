---
title: Správa hesel aplikací – Azure Active Directory | Microsoft Docs
description: Přečtěte si o heslech aplikací a o tom, k čemu se používají, s ohledem na dvoustupňové ověřování.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 07303a0b0b3007ade9adb90af7397855a5014cc0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179418"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Správa hesel aplikací pro dvoustupňové ověřování

> [!Important]
>Správce vám možná neumožní používat hesla aplikací. Pokud nevidíte jako možnost **hesla aplikací** , nejsou ve vaší organizaci k dispozici.

Při používání hesel aplikací je důležité pamatovat:

- Hesla aplikací se generují automaticky a je třeba je vytvořit a zadat jednou pro každou aplikaci.

- Pro každého uživatele je povolený limit 40 hesel. Pokud se po uplynutí tohoto limitu pokusíte jeden vytvořit, budete vyzváni k odstranění existujícího hesla, než bude povoleno vytvoření nového.

    >[!Note]
    >Klienti Office 2013 (včetně Outlooku) podporují nové ověřovací protokoly a dají se použít se dvěma kroky ověřování. Tato podpora znamená, že po zapnutí dvoustupňové ověřování už nebudete potřebovat hesla aplikací pro klienty Office 2013. Další informace najdete v článku [jak moderní ověřování funguje pro klientské aplikace office 2013 a office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) .

## <a name="create-new-app-passwords"></a>Vytvoření nových hesel aplikací

Během vašeho počátečního procesu registrace dvou faktorů jste zadali jedno heslo aplikace. Pokud požadujete více než jednu, budete je muset vytvořit sami. Můžete vytvářet hesla aplikací z více oblastí v závislosti na tom, jak se ve vaší organizaci nastavuje dvojúrovňové ověřování. Další informace o registraci pro použití dvojúrovňového ověřování u svého pracovního nebo školního účtu najdete v tématu [Přehled dvojúrovňového ověřování a pracovního nebo školního účtu](multi-factor-authentication-end-user-first-time.md) a související články.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Kde vytvořit a odstranit hesla aplikací

Můžete vytvářet a odstraňovat hesla aplikací podle toho, jak se používá dvojúrovňové ověřování:

- **Vaše organizace používá dvojúrovňové ověřování a stránku další ověření zabezpečení.** Pokud používáte svůj pracovní nebo školní účet (například, alain@contoso.com ) se dvojúrovňovém ověřováním ve vaší organizaci, můžete heslo aplikace spravovat na [stránce další ověření zabezpečení](https://account.activedirectory.windowsazure.com/Proofup.aspx). Podrobné pokyny najdete v tématu [vytváření a odstraňování hesel aplikací pomocí stránky další ověření zabezpečení](#create-and-delete-app-passwords-from-the-additional-security-verification-page) v tomto článku.

- **Vaše organizace používá dvojúrovňové ověřování a portál Office 365.** Pokud používáte svůj pracovní nebo školní účet (například, alain@contoso.com ), dvojúrovňové ověřování a Microsoft 365 aplikace ve vaší organizaci, můžete spravovat hesla aplikací ze [stránky portálu Office 365](https://www.office.com). Podrobné pokyny najdete v tématu [vytváření a odstraňování hesel aplikací pomocí portálu Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) v tomto článku.

- **Používáte dvojúrovňové ověřování s osobní účet Microsoft.** Pokud používáte osobní účet Microsoft (například, alain@outlook.com ) se dvojúrovňovém ověřováním, můžete na [stránce základy zabezpečení](https://account.microsoft.com/security/)spravovat hesla aplikací. Podrobné pokyny najdete v tématu [používání hesel aplikací s aplikacemi, které nepodporují dvoustupňové ověřování](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Vytváření a odstraňování hesel aplikací ze stránky další ověření zabezpečení

Hesla aplikací můžete vytvořit a odstranit na stránce **Další ověření zabezpečení** pro svůj pracovní nebo školní účet.

1. Přihlaste se na [stránku další ověření zabezpečení](https://account.activedirectory.windowsazure.com/Proofup.aspx)a pak vyberte **hesla aplikace**.

    ![Stránka hesla aplikací – zvýrazněná karta hesla aplikace](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Vyberte **vytvořit**, zadejte název aplikace, která vyžaduje heslo aplikace, a pak vyberte **Další**.

    ![Stránka vytvořit hesla aplikací s názvem aplikace, která potřebuje heslo](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Zkopírujte heslo na stránce **heslo aplikace** a pak vyberte **Zavřít**.

    ![Stránka hesla aplikace s heslem pro zadanou aplikaci](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Na stránce **hesla aplikací** se ujistěte, že je vaše aplikace uvedená.

    ![Stránka hesla aplikací s novou aplikací zobrazenou v seznamu](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Otevřete aplikaci, pro kterou jste vytvořili heslo aplikace (třeba Outlook 2010), a po zobrazení výzvy vložte heslo aplikace. Tento postup byste měli udělat jenom jednou pro každou aplikaci.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Odstranění hesla aplikace pomocí stránky hesla aplikace

1. Na stránce **hesla aplikací** vyberte **Odstranit** vedle hesla aplikace, které chcete odstranit.

   ![Snímek obrazovky, který ukazuje odstranění hesla aplikace na stránce hesla aplikací](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Výběrem **Ano** potvrďte, že chcete heslo odstranit, a pak vyberte **Zavřít**.

    Heslo aplikace se úspěšně odstranilo.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Vytváření a odstraňování hesel aplikací pomocí portálu Office 365

Pokud ke svému pracovnímu nebo školnímu účtu a vašim Microsoft 365m aplikacím používáte dvoustupňové ověřování, můžete hesla aplikací vytvořit a odstranit pomocí portálu Office 365.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Vytvoření hesel aplikací pomocí portálu Office 365

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu, na [stránce Můj účet](https://myaccount.microsoft.com)a vyberte **informace o zabezpečení**.

    ![Portál Office zobrazující kartu informace o zabezpečení](media/multi-factor-authentication-end-user-app-passwords/mfa-security-info.png)

2. Vyberte **Přidat metodu**, v rozevíracím seznamu zvolte **heslo aplikace** a pak klikněte na **Přidat**.

    ![Stránka informace o zabezpečení se seznamem přidat metodu drowpdown](media/multi-factor-authentication-end-user-app-passwords/mfa-add-method.png)

3. Zadejte název hesla aplikace a pak vyberte **Další**.

    ![Stránka pro vytvoření hesel aplikací s názvem hesla aplikace](media/multi-factor-authentication-end-user-app-passwords/mfa-enter-app-password-name.png)

4. Zkopírujte heslo na stránce **heslo aplikace** a potom vyberte **Hotovo**.

    ![Stránka heslo aplikace s nově vytvořeným heslem aplikace](media/multi-factor-authentication-end-user-app-passwords/mfa-copy-app-password.png)

5. Na stránce **bezpečnostní údaje** se ujistěte, že je uvedené heslo aplikace.

    ![Stránka informace o zabezpečení s novým heslem aplikace zobrazeným v seznamu](media/multi-factor-authentication-end-user-app-passwords/mfa-verify-app-password.png)  

6. Otevřete aplikaci, pro kterou jste vytvořili heslo aplikace (třeba Outlook 2016), a po zobrazení výzvy vložte heslo aplikace. Tento postup byste měli udělat jenom jednou pro každou aplikaci.

### <a name="to-delete-app-passwords-using-the-security-info-page"></a>Odstranění hesel aplikací pomocí stránky informace o zabezpečení

1. Na stránce **bezpečnostní údaje** vyberte **Odstranit** vedle hesla aplikace, které chcete odstranit.

   ![Snímek obrazovky, který ukazuje odstranění hesla aplikace na stránce informace o zabezpečení](media/multi-factor-authentication-end-user-app-passwords/mfa-delete-app-password.png)

2. V potvrzovacím poli vyberte **OK** .

    Heslo aplikace se úspěšně odstranilo.

## <a name="if-your-app-passwords-arent-working-properly"></a>Pokud hesla aplikací nefungují správně

Ujistěte se, že jste správně zadali heslo. Pokud jste si jisti, že jste zadali heslo správně, můžete se pokusit o opětovné přihlášení a vytvořit nové heslo aplikace. Pokud ani jedna z těchto možností problém nevyřeší, obraťte se na oddělení technické podpory vaší organizace, aby mohla odstranit stávající hesla aplikací, a umožní vám vytvořit novou značku.

## <a name="next-steps"></a>Další kroky

- [Správa nastavení dvou kroků ověřování](multi-factor-authentication-end-user-manage-settings.md)

- Vyzkoušejte si [aplikaci Microsoft Authenticator](user-help-auth-app-download-install.md) , abyste ověřili přihlášení pomocí oznámení aplikací namísto přijímání textů nebo volání.
