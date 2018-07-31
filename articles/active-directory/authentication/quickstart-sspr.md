---
title: 'Rychlý start: Samoobslužné resetování hesla Azure AD'
description: V tomto rychlém startu rychle nakonfigurujete samoobslužné resetování hesla Azure AD, aby mohli uživatelé resetovat vlastní hesla
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: c40cb3192d514d990ea2a5d66e1484ff204e9b10
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223553"
---
# <a name="quickstart-self-service-password-reset"></a>Rychlý start: Samoobslužné resetování hesla

Tento rychlý start vás provede konfigurací samoobslužného resetování hesla, které nabízí správcům IT jednoduchý způsob, jak umožnit uživatelům resetovat svá hesla nebo odemknout účty.

## <a name="prerequisites"></a>Požadavky

* Funkční tenant Azure AD, který má přiřazenou alespoň zkušební licenci.
* Účet s oprávněními globálního správce.
* Testovací účet uživatele bez oprávnění správce s heslem, které znáte. Pokud potřebujete účet uživatele vytvořit, podívejte se do článku [Rychlý start: Přidání nových uživatelů do služby Azure AD](../add-users-azure-active-directory.md).
* Pilotní skupina pro testování, ve které je testovací uživatel bez oprávnění správce členem. Pokud potřebujete skupinu vytvořit, podívejte se do článku [Vytvoření skupiny a přidání členů ve službě Azure AD](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

> [!VIDEO https://www.youtube.com/embed/Pa0eyqjEjvQ]

1. Ve svém stávajícím tenantovi Azure AD na webu **Azure Portal** vyberte v části **Azure Active Directory** volbu **Resetování hesla**.

2. Na stránce **Vlastnosti** v části **Samoobslužné resetování hesla povoleno** zvolte **Vybráno**.
    * U položky **Vybrat skupinu** vyberte pilotní skupinu, kterou jste vytvořili v rámci požadavků v tomto článku.
    * Klikněte na **Uložit**.

3. Na stránce **Metody ověřování** proveďte následující volby:
   * Počet metod nutných pro resetování: **1**
   * Metody dostupné pro uživatele:
      * **Mobilní telefon**
      * **Telefon do kanceláře**
   * Klikněte na **Uložit**.

    ![Ověřování][Authentication]

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

Zakázání samoobslužného resetování hesla je snadné. Otevřete svého tenanta Azure AD, přejděte do **Resetování hesla** > **Vlastnosti** a pak v části **Samoobslužné resetování hesla povoleno** vyberte **Nikdo**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak pro uživatele, kteří existují jenom v cloudu, rychle nakonfigurovat samoobslužné resetování hesla. Pokud se chcete dozvědět, jak provést podrobnější zavedení, pokračujte na našeho průvodce zavedením.

> [!div class="nextstepaction"]
> [Zavedení samoobslužného resetování hesla](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Dostupné metody ověřování Azure AD a požadované množství"
