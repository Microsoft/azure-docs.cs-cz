---
title: Souhlas správce s účtem LinkedIn – Azure Active Directory | Dokumentace Microsoftu
description: Vysvětluje, jak povolit nebo zakázat integraci s účtem LinkedIn v aplikacích Microsoftu v Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e07c53192ea2c8b792256af944c81c9c909dc55
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368114"
---
# <a name="consent-to-linkedin-account-connections-for-an-azure-active-directory-organization"></a>Souhlas s účtem LinkedIn organizace Azure Active Directory

Povolit uživatelům ve vaší organizaci pro přístup k jejich připojením Linkedinu v rámci některé aplikace Microsoftu. Žádná data se sdílí, dokud uživatelé udělit souhlas s připojit své účty. Vaše organizace v Azure Active Directory (Azure AD) můžete integrovat [centra pro správu](https://aad.portal.azure.com).

> [!IMPORTANT]
> S účtem LinkedIn nastavení je aktuálně nasazuje pro Azure AD organizace. Když zprovoznily pro vaši organizaci, je standardně povolená.
> 
> Výjimky:
> * Toto nastavení není k dispozici pro zákazníky, kteří používají Microsoft Cloud pro státní správu USA, Microsoft Cloud Germany nebo Azure a Office 365, kterou provozuje společnost 21Vianet v Číně.
> * Toto nastavení je vypnuto ve výchozím nastavení pro klienty, které jsou zřízené v Německu. Všimněte si, že toto nastavení není k dispozici pro zákazníky, kteří používají Microsoft Cloud Germany.
> * Toto nastavení je vypnuto ve výchozím nastavení pro klienty zřízené ve Francii.
>
> Jakmile s účtem LinkedIn, budou povolené pro vaši organizaci, účet připojení fungovat po uživatelům udělit souhlas s aplikacemi, které přistupují k firemním datům jejich jménem. Informace o nastavení souhlasu uživatele najdete v tématu [odebrání přístupu uživatele k aplikaci](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="use-the-azure-portal-to-enable-linkedin-account-connections"></a>Pomocí webu Azure portal povolit s účtem LinkedIn

Můžete povolit s účtem LinkedIn pouze uživatelé mají mít přístup z celé organizace jenom vybraným uživatelům ve vaší organizaci.

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com/) pomocí účtu, který má oprávnění globálního správce organizace služby Azure AD.
2. Vyberte **uživatelé**.
3. Na **uživatelé** okně vyberte **uživatelská nastavení**.
4. V části **s účtem LinkedIn**, umožňuje uživatelům připojit své účty pro přístup k jejich připojením Linkedinu v rámci některé aplikace Microsoftu. Žádná data se sdílí, dokud uživatelé udělit souhlas s připojit své účty.

  * Vyberte **Ano** vyjádřit souhlas se službou pro všechny uživatele v organizaci
  * Vyberte **vybrané** souhlas pouze pro vybrané uživatele v organizaci
  * Vyberte **ne** chcete odvolat svůj souhlas pro uživatele ve vaší organizaci

    ![Integrace s účtem LinkedIn v organizaci](./media/linkedin-integration/linkedin-integration.png)

5. Jakmile budete hotovi, vyberte **Uložit** uložte nastavení.
     
## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Pomocí zásad skupiny umožňující s účtem LinkedIn

1. Stáhněte si [souborů šablon pro správu Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrahovat **ADMX** soubory a zkopírujte je do centrálního úložiště.
3. Otevřete správu zásad skupiny.
4. Vytvoření objektu zásad skupiny s následujícím nastavením: **Konfigurace uživatele** > **šablony pro správu** > **Microsoft Office 2016** > **různé**  >  **Funkcí služby LinkedIn zobrazit v aplikacích Office**.
5. Vyberte **povolené** nebo **zakázané**.
  
   Stav | Efekt
   ------ | ------
   **Enabled** (Povoleno) | **Funkcí služby LinkedIn zobrazit v aplikacích Office** je povolené nastavení v možnostech Office 2016. Uživatelé ve vaší organizaci můžou používat funkce Linkedinu v aplikacích Office 2016.
   **Disabled** (Zakázáno) | **Funkcí služby LinkedIn zobrazit v aplikacích Office** zakázáno v možnostech Office 2016 a toto nastavení nejde změnit koncovým uživatelům. Uživatelé ve vaší organizaci nemůžou používat funkce Linkedinu ve svých aplikacích Office 2016.

Tyto zásady skupiny ovlivní pouze aplikace Office 2016 pro místní počítač. Pokud uživatelům zakázat LinkedIn ve svých aplikacích Office 2016, se stále zobrazuje funkce Linkedinu v Office 365.

## <a name="next-steps"></a>Další postup

* [Souhlas uživatele a sdílení na LinkedIn dat](linkedin-user-consent.md)

* [LinkedIn informace a funkce v aplikacích Microsoftu](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum pro nápovědu LinkedIn](https://www.linkedin.com/help/linkedin)

* [Zobrazit aktuální nastavení integrace LinkedIn na webu Azure Portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
