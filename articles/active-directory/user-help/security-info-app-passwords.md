---
title: Vytvoření hesel aplikací ze stránky Security Info (Preview) – Azure AD
description: Vytvářejte automaticky generovaná hesla (hesla aplikací), která se použijí v libovolné aplikaci bez prohlížeče, nebo v jakékoli aplikaci, která nepodporuje dvojúrovňové ověřování ve vaší organizaci. Heslo této aplikace je oddělené od normálního hesla a lze je nastavit na stránce informace o zabezpečení.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 84588130788e9be8d3be52a8ea0f3988dce7b952
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91537000"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>Vytváření hesel aplikací na stránce Bezpečnostní údaje (Preview)

Některé aplikace, jako je například Outlook 2010, nepodporují dvoustupňové ověřování. Tato nedostatečná podpora znamená, že pokud ve vaší organizaci používáte dvoustupňové ověřování, aplikace nebude fungovat. Pokud se chcete tomuto problému vyhnout, můžete vytvořit automaticky generované heslo, které se použije u každé neprohlížečové aplikace, a to odděleně od normálního hesla.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Správce vám možná neumožní používat hesla aplikací. Pokud nevidíte jako možnost **hesla aplikací** , nejsou ve vaší organizaci k dispozici.

Při používání hesel aplikací je důležité pamatovat:

- Hesla aplikací se generují automaticky a je třeba je vytvořit a zadat jednou pro každou aplikaci.

- Pro každého uživatele je povolený limit 40 hesel. Pokud se po uplynutí tohoto limitu pokusíte jeden vytvořit, budete vyzváni k odstranění existujícího hesla, než bude povoleno vytvoření nového.

    >[!Note]
    >Klienti Office 2013 (včetně Outlooku) podporují nové ověřovací protokoly a dají se použít se dvěma kroky ověřování. Tato podpora znamená, že po zapnutí dvoustupňové ověřování už nebudete potřebovat hesla aplikací pro klienty Office 2013. Další informace najdete v článku [jak moderní ověřování funguje pro klientské aplikace office 2013 a office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) .

## <a name="create-new-app-passwords"></a>Vytvoření nových hesel aplikací

Pokud používáte dvoustupňové ověřování u svého pracovního nebo školního účtu a váš správce zapnul prostředí s informacemi o zabezpečení, můžete vytvořit a odstranit hesla aplikací pomocí stránky **bezpečnostní údaje** .

>[!Note]
>Pokud správce nezapnul prostředí informací o zabezpečení, je nutné postupovat podle pokynů a informací v části [Správa hesel aplikací pro dvoustupňové ověřování](multi-factor-authentication-end-user-app-passwords.md) .

### <a name="to-create-a-new-app-password"></a>Vytvoření nového hesla aplikace

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a pak přejít na https://myaccount.microsoft.com/ stránku.

    ![Stránka můj profil zobrazující odkazy na zvýrazněné informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. V levém navigačním podokně nebo na odkaz v bloku **informací o** zabezpečení vyberte možnost **informace o zabezpečení** a pak vyberte **Přidat metodu** ze stránky **bezpečnostní údaje** .

    ![Stránka informace o zabezpečení se zvýrazněnou možností přidat metodu](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stránce **Přidat metodu** vyberte v rozevíracím seznamu **heslo aplikace** a pak vyberte **Přidat**.

    ![Pole přidat metodu s vybraným heslem aplikace](media/security-info/securityinfo-myprofile-addpassword.png)

4. Zadejte název aplikace, která vyžaduje heslo aplikace, a pak vyberte **Další**.

    ![Snímek obrazovky zobrazující stránku heslo aplikace s názvem zadané aplikace](media/security-info/securityinfo-myprofile-password-appname.png)

5. Zkopírujte text z pole **heslo** , vložte ho do oblasti hesla aplikace (v tomto příkladu je to pro Outlook 2010) a potom vyberte **Hotovo**.

    ![Stránka s heslem aplikace s názvem aplikace](media/security-info/securityinfo-myprofile-password-copytext.png)

    Heslo se přidá a Vy se můžete úspěšně přihlásit do své aplikace.

## <a name="delete-your-app-passwords"></a>Odstranění hesel aplikací

Pokud už nepotřebujete používat aplikaci, která vyžaduje heslo aplikace, můžete odstranit příslušné heslo aplikace. Při odstranění hesla aplikace se uvolní jedna z dostupných bodů hesla aplikace, která se použijí v budoucnu.

>[!Important]
>Pokud omylem odstraníte heslo aplikace, neexistuje žádný způsob, jak ho vrátit zpátky. Budete muset vytvořit nové heslo aplikace a znovu ho zadat do aplikace podle kroků v části [vytvoření nových hesel aplikací](#create-new-app-passwords) v tomto článku.

### <a name="to-delete-an-app-password"></a>Odstranění hesla aplikace

1. Na stránce **informace o zabezpečení** vyberte odkaz **Odstranit** vedle možnosti **heslo aplikace** pro konkrétní aplikaci.

    ![Odkaz pro odstranění metody hesla aplikace z informací o zabezpečení](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. V potvrzovacím poli vyberte **Ano** , pokud chcete odstranit **heslo aplikace**. Po odstranění hesla aplikace se z bezpečnostních údajů odstraní a zmizí ze stránky **bezpečnostní údaje** .

## <a name="for-more-information"></a>Další informace

- Další informace o stránce **informace o zabezpečení** a o tom, jak je nastavit, najdete v tématu [Přehled informací o zabezpečení](./security-info-setup-signin.md) .