---
title: Nastavte hesla aplikace na stránce informace o zabezpečení (Preview) – Azure Active Directory | Microsoft Docs
description: Nastavte automaticky generovaná hesla (hesla aplikací), která se použijí v libovolné aplikaci bez prohlížeče, nebo v jakékoli aplikaci, která nepodporuje dvojúrovňové ověřování ve vaší organizaci. Heslo této aplikace je oddělené od normálního hesla a lze je nastavit na stránce informace o zabezpečení.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: d599607da6653d088f53490ea4d4b5f7f6d0f236
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382927"
---
# <a name="manage-app-passwords-from-your-security-info-preview-page"></a>Správa hesel aplikací ze stránky informace o zabezpečení (Preview)

Některé aplikace, jako je například Outlook 2010, nepodporují dvoustupňové ověřování. Tato nedostatečná podpora znamená, že pokud ve vaší organizaci používáte dvoustupňové ověřování, aplikace nebude fungovat. Pokud se chcete tomuto problému vyhnout, můžete vytvořit automaticky generované heslo, které se použije u každé neprohlížečové aplikace, a to odděleně od normálního hesla.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Správce vám možná neumožní používat hesla aplikací. Pokud nevidíte jako možnost **hesla aplikací** , nejsou ve vaší organizaci k dispozici.

Při používání hesel aplikací je důležité pamatovat:

- Hesla aplikací se generují automaticky a pro každou aplikaci se zadávají jenom jednou.

- Pro každého uživatele je povolený limit 40 hesel. Pokud se po uplynutí tohoto limitu pokusíte jeden vytvořit, budete vyzváni k odstranění existujícího hesla, než bude povoleno vytvoření nového.

- Použijte jedno heslo aplikace na zařízení, ne na aplikaci. Můžete například vytvořit jedno heslo pro všechny aplikace na přenosném počítači a další jedno heslo pro všechny aplikace na ploše.

    >[!Note]
    >Klienti Office 2013 (včetně Outlooku) podporují nové ověřovací protokoly a dají se použít se dvěma kroky ověřování. Tato podpora znamená, že po zapnutí dvoustupňové ověřování už nebudete potřebovat hesla aplikací pro klienty Office 2013. Další informace najdete v článku [jak moderní ověřování funguje pro klientské aplikace office 2013 a office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) .

## <a name="create-new-app-passwords"></a>Vytvoření nových hesel aplikací

Pokud používáte dvoustupňové ověřování u svého pracovního nebo školního účtu a váš správce zapnul prostředí s informacemi o zabezpečení, můžete vytvořit a odstranit hesla aplikací pomocí stránky **bezpečnostní údaje** .

>[!Note]
>Pokud správce nezapnul prostředí informací o zabezpečení, je nutné postupovat podle pokynů a informací v části [Správa hesel aplikací pro dvoustupňové ověřování](multi-factor-authentication-end-user-app-passwords.md) .

### <a name="to-create-a-new-app-password"></a>Vytvoření nového hesla aplikace

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a https://myprofile.microsoft.com/ pak přejít na stránku.

    ![Stránka můj profil zobrazující odkazy na zvýrazněné informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. V levém navigačním podokně nebo na odkaz v bloku **informací o** zabezpečení vyberte možnost **informace o zabezpečení** a pak vyberte **Přidat metodu** ze stránky **bezpečnostní údaje** .

    ![Stránka informace o zabezpečení se zvýrazněnou možností přidat metodu](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stránce **Přidat metodu** vyberte v rozevíracím seznamu **heslo aplikace** a pak vyberte **Přidat**.

    ![Pole přidat metodu s vybraným heslem aplikace](media/security-info/securityinfo-myprofile-addpassword.png)

4. Zadejte název aplikace, která vyžaduje heslo aplikace, a pak vyberte **Další**.

    ![Stránka s heslem aplikace s názvem aplikace](media/security-info/securityinfo-myprofile-password-appname.png)

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

- Další informace o stránce **informace o zabezpečení** a o tom, jak je nastavit, najdete v tématu [Přehled informací o zabezpečení](user-help-security-info-overview.md) .
