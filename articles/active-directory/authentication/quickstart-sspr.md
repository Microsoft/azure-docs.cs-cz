---
title: Rychlý Start – Samoobslužné resetování hesla služby Azure AD
description: V tomto rychlém startu se dozvíte, jak nakonfigurovat Samoobslužné resetování hesla služby Azure AD, aby uživatelé mohli resetovat svoje vlastní hesla a snížit režijní náklady na IT oddělení.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 12/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd178f52665c77f03a48d87a9e73c9019390bb21
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154852"
---
# <a name="quickstart-configure-azure-active-directory-self-service-password-reset"></a>Rychlý Start: Konfigurace samoobslužného resetování hesla Azure Active Directory

V tomto rychlém startu nakonfigurujete Samoobslužné resetování hesla Azure Active Directory (AD), aby uživatelé mohli resetovat hesla nebo odemknout své účty. Pomocí SSPR můžou uživatelé resetovat svoje vlastní přihlašovací údaje bez helpdesku nebo pomoci správce. Tato možnost umožní uživatelům znovu získat přístup k účtu, aniž by čekali na další podporu.

> [!IMPORTANT]
> V tomto rychlém startu se zobrazí správce, jak povolit samoobslužné resetování hesla. Pokud už jste koncoví uživatelé zaregistrovali pro Samoobslužné resetování hesla a potřebujete se zpátky do svého účtu, přečtěte si https://aka.ms/sspr.
>
> Pokud váš IT tým nepovolil možnost resetovat si vlastní heslo, obraťte se na helpdesk a získáte další pomoc.

## <a name="prerequisites"></a>Požadavky

* Funkční tenant Azure AD, který má přiřazenou alespoň zkušební licenci.
    * V případě potřeby [ho vytvořte zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Účet s oprávněními globálního správce.
* Testovací uživatel bez oprávnění správce s heslem, které znáte, jako je například *testuser*.
    * Pokud potřebujete vytvořit uživatele, přečtěte si téma [rychlý Start: přidání nových uživatelů do Azure Active Directory](../add-users-azure-active-directory.md).
* Pilotní skupina, která se má testovat s tím, že uživatel bez oprávnění správce je členem, jako je například *SSPR-test-Group*.
    * Pokud potřebujete vytvořit skupinu, přečtěte si téma Postup [Vytvoření skupiny a přidání členů v Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

[Zobrazit tento proces jako video na YouTube](https://youtu.be/Pa0eyqjEjvQ)

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **Azure Active Directory** a pak zvolte **resetování hesla**.

1. Na stránce **vlastnosti** v části možnost pro **Samoobslužné resetování hesla povoleno**vyberte možnost **vybrané**.
1. Zvolte **Vybrat skupinu**a potom vyberte svou pilotní skupinu vytvořenou jako součást části požadavky tohoto článku, například *SSPR-test-Group*. Až budete připraveni, vyberte **Uložit**.
1. Na stránce **metody ověřování** proveďte následující volby a potom zvolte **Uložit**:
    * Počet metod nutných pro resetování: **1**
    * Metody dostupné pro uživatele:
        * **Kód mobilní aplikace**
        * **E-mail**

    > [!div class="mx-imgBorder"]
    > ![Výběr metod ověřování pro SSPR][Authentication]

4. Na stránce **registrace** proveďte následující volby a potom zvolte **Uložit**:
   * Při přihlášení vyžadovat registraci uživatelů: **Ano**
   * Nastavit počet dnů před vyzváním uživatelů k potvrzení ověřovacích údajů: **365**

## <a name="test-self-service-password-reset"></a>Testování samoobslužného resetování hesla

Teď umožňuje testovat konfiguraci SSPR pomocí testovacího uživatele, který je součástí skupiny, kterou jste vybrali v předchozí části, jako je například *testuser*. Vzhledem k tomu, že Microsoft vynucuje pro účty správců Azure požadavky na silné ověřování, může testování s použitím účtu správce změnit výsledek testu. Další informace týkající se zásad hesel správců najdete v našem článku o [zásadách hesel](concept-sspr-policy.md).

1. Otevřete nové okno prohlížeče v režimu InPrivate nebo anonymním režimu a přejděte na adresu [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Přihlaste se pomocí testovacího uživatele bez oprávnění správce, jako je například *testuser*, a zaregistrujte si telefon pro ověřování.
3. Po dokončení vyberte tlačítko s označením **vypadá dobře** a zavřete okno prohlížeče.
4. Otevřete nové okno prohlížeče v režimu InPrivate nebo anonymním režimu a přejděte na adresu [https://aka.ms/sspr](https://aka.ms/sspr).
5. Zadejte ID uživatele testovacích uživatelů bez oprávnění správce, jako je například *testuser*, znaky z CAPTCHA a pak vyberte **Další**.
6. Postupujte podle pokynů k resetování hesla.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete zakázat Samoobslužné resetování hesla, vyhledejte a vyberte **Azure Active Directory** v Azure Portal. Vyberte **resetování hesla**a potom v části **Samoobslužné resetování hesla povoleno**vyberte **žádné** . Až budete připraveni, vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nakonfigurovat Samoobslužné resetování hesla jenom pro cloudové uživatele. Pokud se chcete dozvědět, jak provést podrobnější zavedení, pokračujte na našeho průvodce zavedením.

> [!div class="nextstepaction"]
> [Zavedení samoobslužného resetování hesla](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Dostupné metody ověřování Azure AD a požadované množství"

<!-- INTERNAL LINKS -->
[register-sspr]: ../user-help/active-directory-passwords-reset-register.md
[reset-password]: ../user-help/active-directory-passwords-update-your-own-password.md
