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
ms.date: 04/29/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1130885cc3168582935264ffaad9fd7a8ba3c60b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64920235"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrace s účtem LinkedIn ve službě Azure Active Directory

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

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Povolit s účtem LinkedIn na webu Azure Portal

Můžete povolit s účtem LinkedIn pouze uživatelé mají mít přístup z celé organizace jenom vybraným uživatelům ve vaší organizaci.

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com/) pomocí účtu, který má oprávnění globálního správce organizace služby Azure AD.
1. Vyberte **uživatelé**.
1. Na **uživatelé** okně vyberte **uživatelská nastavení**.
1. V části **s účtem LinkedIn**, umožňuje uživatelům připojit své účty pro přístup k jejich připojením Linkedinu v rámci některé aplikace Microsoftu. Žádná data se sdílí, dokud uživatelé udělit souhlas s připojit své účty.

    * Vyberte **Ano** povolit službu pro všechny uživatele ve vaší organizaci
    * Vyberte **vybrané** povolit pouze skupinu vybraných uživatelů ve vaší organizaci
    * Vyberte **ne** chcete odvolat svůj souhlas všech uživatelů ve vaší organizaci

    ![Integrace s účtem LinkedIn v organizaci](./media/linkedin-integration/linkedin-integration.png)

1. Jakmile budete hotovi, vyberte **Uložit** uložte nastavení.

> [!Important]
> Integrace Linkedinu nebylo úplně povoleno pro uživatele, dokud se vyjádřit souhlas připojit své účty. Žádná data se sdílí povolit účet připojení pro vaše uživatele.

### <a name="assign-selected-users-with-a-group"></a>Přiřadit vybraným uživatelům se skupinou
Jsme nahradili možnost "Vybrané", který určuje seznam uživatelů s možností vyberte skupinu uživatelů, tak, že povolíte možnost propojení účtů LinkedIn a Microsoft pro jednu skupinu namísto mnoho jednotlivých uživatelů. Pokud nemáte s účtem LinkedIn povolená pro jednotlivé vybrané uživatele, není nutné nic dělat. Pokud jste dříve povolili s účtem LinkedIn pro jednotlivé vybrané uživatele, měli byste:

1. Získat aktuální seznam jednotlivých uživatelů
1. Přesunutí aktuálně zapnuté jednotlivé uživatele do skupiny
1. Pomocí skupiny z předchozí jako vybrané skupiny v s účtem LinkedIn nastavení v Centru pro správu Azure AD.

> [!NOTE]
> I v případě, že vaše aktuálně vybrané jednotlivé uživatele nelze přesunout do skupiny, stále vidí LinkedIn informace v aplikacích Microsoftu.

### <a name="get-the-current-list-of-selected-users"></a>Získat aktuální seznam vybraných uživatelů

1. Microsoft 365 se přihlaste pomocí účtu správce.
1. Přejděte do části https://linkedinselectedusermigration.azurewebsites.net/ (Soubor > Nový > Jiné). Zobrazí se seznam uživatelů, kteří jsou vybrány pro s účtem LinkedIn.
1. Seznam exportujte do souboru CSV.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Přesunout vybrané jednotlivé uživatele do skupiny

1. Spuštění PowerShellu
1. Spuštěním instalace modulu Azure AD `Install-Module AzureAD`
1. Spusťte tento skript:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Na použití skupiny z kroku 2 jako vybrané skupiny v s účtem LinkedIn nastavení v Centru pro správu Azure AD, najdete v článku [povolit s účtem LinkedIn na webu Azure Portal](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Pomocí zásad skupiny umožňující s účtem LinkedIn

1. Stáhněte si [souborů šablon pro správu Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extrahovat **ADMX** soubory a zkopírujte je do centrálního úložiště.
1. Otevřete správu zásad skupiny.
1. Vytvoření objektu zásad skupiny s následujícím nastavením: **Konfigurace uživatele** > **šablony pro správu** > **Microsoft Office 2016** > **různé**  >  **Funkcí služby LinkedIn zobrazit v aplikacích Office**.
1. Vyberte **povolené** nebo **zakázané**.
  
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
