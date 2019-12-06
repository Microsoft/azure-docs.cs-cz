---
title: 'Rychlý start: Samoobslužné resetování hesla Azure AD'
description: V tomto rychlém startu rychle nakonfigurujete samoobslužné resetování hesla Azure AD, aby mohli uživatelé resetovat vlastní hesla
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 582a6a32aa4c34b2e6fef37f3de5b5414de16cf3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846653"
---
# <a name="quickstart-self-service-password-reset"></a>Rychlý start: Samoobslužné resetování hesla

Tento rychlý start vás provede konfigurací samoobslužného resetování hesla, které nabízí správcům IT jednoduchý způsob, jak umožnit uživatelům resetovat svá hesla nebo odemknout účty.

## <a name="prerequisites"></a>Předpoklady

* Funkční tenant Azure AD, který má přiřazenou alespoň zkušební licenci.
* Účet s oprávněními globálního správce
* Testovací účet uživatele bez oprávnění správce s heslem, které znáte. Pokud potřebujete účet uživatele vytvořit, podívejte se do článku [Rychlý start: Přidání nových uživatelů do služby Azure AD](../add-users-azure-active-directory.md).
* Pilotní skupina pro testování, ve které je testovací uživatel bez oprávnění správce členem. Pokud potřebujete skupinu vytvořit, podívejte se do článku [Vytvoření skupiny a přidání členů ve službě Azure AD](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

[Zobrazit tento proces jako video na YouTube](https://youtu.be/Pa0eyqjEjvQ)

1. V existujícím tenantovi Azure AD vyberte v nabídce Azure Portal nebo na **domovské** stránce možnost **Azure Active Directory**. Pak vyberte **resetování hesla**.

2. Na stránce **Vlastnosti** v části **Samoobslužné resetování hesla povoleno** zvolte **Vybráno**.
    * U položky **Vybrat skupinu** vyberte pilotní skupinu, kterou jste vytvořili v rámci požadavků v tomto článku.
    * Klikněte na **Uložit**.

3. Na stránce **Metody ověřování** proveďte následující volby:
   * Počet metod nutných pro resetování: **1**
   * Metody dostupné pro uživatele:
      * **E-mail**
      * **Kód mobilní aplikace (Preview)**
   * Klikněte na **Uložit**.

     ![Výběr metod ověřování pro SSPR][Authentication]

4. Na stránce **Registrace** proveďte následující volby:
   * Při přihlášení vyžadovat registraci uživatelů: **Ano**
   * Nastavit počet dnů před vyzváním uživatelů k potvrzení ověřovacích údajů: **365**

## <a name="test-self-service-password-reset"></a>Testování samoobslužného resetování hesla

Teď můžete otestovat konfiguraci samoobslužného resetování hesla prostřednictvím testovacího uživatele. Vzhledem k tomu, že Microsoft vynucuje pro účty správců Azure požadavky na silné ověřování, může testování s použitím účtu správce změnit výsledek testu. Další informace týkající se zásad hesel správců najdete v našem článku o [zásadách hesel](concept-sspr-policy.md).

1. Otevřete nové okno prohlížeče v režimu InPrivate nebo anonymním režimu a přejděte na adresu [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Přihlaste se pomocí testovacího uživatele bez oprávnění správce a zaregistrujte svůj telefon pro ověření.
3. Po dokončení klikněte na tlačítko **Vypadá to dobře** a zavřete okno prohlížeče.
4. Otevřete nové okno prohlížeče v režimu InPrivate nebo anonymním režimu a přejděte na adresu [https://aka.ms/sspr](https://aka.ms/sspr).
5. Zadejte ID testovacího uživatele bez oprávnění správce a znaky z testu CAPTCHA a potom klikněte na **Další**.
6. Postupujte podle kroků k resetování hesla.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Zakázání samoobslužného resetování hesla je snadné. Otevřete svého tenanta Azure AD, v části **vlastnosti** > **resetování hesla**a potom v části **Samoobslužné resetování hesla povoleno**vyberte **žádné** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak pro uživatele, kteří existují jenom v cloudu, rychle nakonfigurovat samoobslužné resetování hesla. Pokud se chcete dozvědět, jak provést podrobnější zavedení, pokračujte na našeho průvodce zavedením.

> [!div class="nextstepaction"]
> [Zavedení samoobslužného resetování hesla](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Dostupné metody ověřování Azure AD a požadované množství"
