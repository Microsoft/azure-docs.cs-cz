---
title: Souhlas s služby LinkedIn ve vaší organizaci – Azure Active Directory | Dokumentace Microsoftu
description: Vysvětluje, jak povolit nebo zakázat LinkedIn integrace pro aplikace Microsoftu ve službě Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: abcb1696efe44293d01153aa37a9835ba5f43370
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199694"
---
# <a name="consent-to-linkedin-integration-for-your-azure-active-directory-organization"></a>Souhlas s integrace LinkedIn ve vaší organizaci Azure Active Directory

V tomto článku se dozvíte, jak povolit nebo zakázat integrace LinkedIn ve vaší organizaci, který v Centru pro správu Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Nastavení integrace LinkedIn se aktuálně zavádí do Azure AD organizace. Když zprovoznily pro vaši organizaci, je standardně povolená.
> 
> Výjimky:
> * Toto nastavení není k dispozici pro zákazníky, kteří používají Microsoft Cloud pro státní správu USA, Microsoft Cloud Germany nebo Azure a Office 365, kterou provozuje společnost 21Vianet v Číně.
> * Toto nastavení je vypnuto ve výchozím nastavení pro klienty, které jsou zřízené v Německu. Všimněte si, že toto nastavení není k dispozici pro zákazníky, kteří používají Microsoft Cloud Germany.
> * Toto nastavení je vypnuto ve výchozím nastavení pro klienty zřízené ve Francii.
>
> Integrace funguje pouze v případě, že máte povolené *a* po uživatelům udělit souhlas s aplikacemi, které přistupují k firemním datům jejich jménem. Informace o nastavení souhlasu uživatele najdete v tématu [odebrání přístupu uživatele k aplikaci](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>Povolit nebo zakázat integrace LinkedIn pro vaše uživatele na webu Azure Portal

Můžete povolit nebo zakázat integrace LinkedIn pro celého tenanta nebo pouze pro vybrané uživatele ve vašem tenantovi.

1. Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com/) pomocí účtu, který má oprávnění globálního správce pro tenanta Azure AD.
2. Vyberte **uživatelé**.
3. Na **uživatelé** okně vyberte **uživatelská nastavení**.
4. V části **s účtem LinkedIn**:

   * Vyberte **Ano** souhlas pro všechny uživatele v organizaci připojit své účty k získání přístupu k jejich LinkedIn kontakty v rámci některé aplikace Microsoftu.
   * Vyberte **vybrané** souhlas pro jenom vybraným uživatelům v organizaci připojit své účty, chcete-li získat přístup ke kontaktům jejich Linkedinu v rámci některé aplikace Microsoftu.
   * Vyberte **ne** chcete odvolat svůj souhlas pro uživatele ve vaší organizaci připojit své účty k získání přístupu k jejich LinkedIn kontakty v rámci některé aplikace Microsoftu.
    ![Povolení integrace LinkedIn v organizaci](./media/linkedin-integration/linkedin-integration.png)
5. Až to uděláte tak, že vyberete uložit nastavení **Uložit**.

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-group-policy"></a>Povolit nebo zakázat integrace LinkedIn pro uživatele v zásadách skupiny

1. Stáhněte si [souborů šablon pro správu Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrahovat **ADMX** soubory a zkopírujte je do centrálního úložiště.
3. Otevřete správu zásad skupiny.
4. Vytvoření objektu zásad skupiny s následujícím nastavením: **Konfigurace uživatele** > **šablony pro správu** > **Microsoft Office 2016** > **různé**  >  **Funkcí služby LinkedIn zobrazit v aplikacích Office**.
5. Vyberte **povolené** nebo **zakázané**.
  
   Stav | Efekt
   ------ | ------
   **Enabled** (Povoleno) | **Funkcí služby LinkedIn zobrazit v aplikacích Office** je povolené nastavení v možnostech Office 2016. Uživatelé ve vaší organizaci můžou používat funkce Linkedinu v aplikacích Office.
   **Disabled** (Zakázáno) | **Funkcí služby LinkedIn zobrazit v aplikacích Office** zakázáno v možnostech Office 2016 a toto nastavení nejde změnit koncovým uživatelům. Uživatelé ve vaší organizaci nemůžou používat funkce Linkedinu ve svých aplikacích Office 2016.

Tyto zásady skupiny ovlivní pouze aplikace Office 2016 pro místní počítač. Uživatelé mohou vidět funkce Linkedinu v profilové karty v rámci Office 365, i když se zakážou LinkedIn ve svých aplikacích Office 2016.

## <a name="learn-more"></a>Další informace

* [Integrace Linkedinu ve vaší organizaci](linkedin-user-consent.md)

* [LinkedIn informace a funkce v aplikacích Microsoftu](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum pro nápovědu LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Další postup

Pomocí následujícího odkazu zobrazíte aktuální nastavení integrace LinkedIn na webu Azure Portal:

[Zobrazit aktuální nastavení integrace LinkedIn na webu Azure Portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
