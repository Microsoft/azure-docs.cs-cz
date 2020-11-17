---
title: Souhlas správce pro připojení účtu LinkedIn – Azure AD | Microsoft Docs
description: Vysvětluje, jak povolit nebo zakázat připojení k účtu pro integraci LinkedInu v aplikacích Microsoftu v Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d70676e117a71fbd194e921b4755ba5d196871c7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650304"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrace připojení k účtu LinkedIn v Azure Active Directory

Uživatelům ve vaší organizaci můžete dovolit přístup k jejich připojením LinkedIn v některých aplikacích Microsoftu. Žádná data nejsou sdílená, dokud uživatel nesouhlasí připojit své účty. Organizaci můžete integrovat do [centra pro správu](https://aad.portal.azure.com)Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Nastavení připojení účtu LinkedIn se v současnosti navádějí do organizací Azure AD. Pokud je zavedená do vaší organizace, je ve výchozím nastavení povolená.
>
> Výjimky:
>
> * Toto nastavení není k dispozici pro zákazníky, kteří používají Microsoft Cloud pro státní správu USA, Microsoft Cloud Německo nebo Azure a Microsoft 365 provozované společností 21Vianet v Číně.
> * Toto nastavení je ve výchozím nastavení vypnuté pro organizace Azure AD zřízené v Německu. Všimněte si, že toto nastavení není k dispozici pro zákazníky, kteří používají Microsoft Cloud Německo.
> * Toto nastavení je ve výchozím nastavení vypnuté pro organizace zřízené ve Francii.
>
> Jakmile budou připojení k účtu LinkedInu pro vaši organizaci povolená, připojení k účtům budou fungovat až po jejich souhlasu s aplikacemi, které přistupují k firemním datům. Informace o nastavení souhlasu uživatele najdete v tématu [Postup odebrání přístupu uživatele k aplikaci](../manage-apps/methods-for-removing-user-access.md).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Povolit připojení k účtu LinkedIn v Azure Portal

Připojení k účtu LinkedIn můžete povolit jenom pro uživatele, kterým chcete mít přístup, od celé organizace až po vybrané uživatele ve vaší organizaci.

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com/) pomocí účtu, který je globálním správcem pro organizaci Azure AD.
1. Vyberte **Uživatelé**.
1. Na stránce **Uživatelé** vyberte **nastavení uživatele**.
1. V části **připojení k účtu LinkedInu** Umožněte uživatelům připojit své účty pro přístup k jejich připojením LinkedIn v některých aplikacích Microsoftu. Žádná data nejsou sdílená, dokud uživatel nesouhlasí připojit své účty.

    * Vyberte **Ano** , pokud chcete službu povolit pro všechny uživatele ve vaší organizaci.
    * Výběrem **vybrané skupiny** povolíte službu jenom pro skupinu vybraných uživatelů ve vaší organizaci.
    * Vyberte **ne** , pokud chcete odvolat souhlas všech uživatelů ve vaší organizaci.

    ![Integrace připojení k účtu LinkedIn v organizaci](./media/linkedin-integration/linkedin-integration.png)

1. Až budete hotovi, vyberte **Uložit** a uložte nastavení.

> [!Important]
> Integrace LinkedInu není pro vaše uživatele plně povolena, dokud nesouhlasí připojit své účty. Žádná data nejsou sdílena, pokud pro uživatele povolíte připojení k účtu.

### <a name="assign-selected-users-with-a-group"></a>Přiřazení vybraných uživatelů ke skupině

Nahradili jsme možnost Selected, která určuje seznam uživatelů s možností výběru skupiny uživatelů, aby bylo možné povolit možnost připojit LinkedIn a účty Microsoft pro jednu skupinu, a ne mnoho jednotlivých uživatelů. Pokud nemáte pro vybrané jednotlivé uživatele povolená připojení k účtu LinkedIn, nemusíte nic dělat. Pokud jste dříve povolili připojení k účtu LinkedIn pro vybrané jednotlivé uživatele, měli byste:

1. Získat aktuální seznam jednotlivých uživatelů
1. Přesun aktuálně povolených individuálních uživatelů do skupiny
1. V nastavení připojení k účtu LinkedIn v centru pro správu Azure AD použijte skupinu z předchozí jako vybranou skupinu.

> [!NOTE]
> I když nepřesunete aktuálně vybrané uživatele do skupiny, můžou se v aplikacích Microsoftu zobrazovat informace o LinkedInu.

### <a name="move-currently-selected-users-to-a-group"></a>Přesunout aktuálně vybrané uživatele do skupiny

1. Vytvořte soubor CSV pro uživatele, kteří jsou vybráni pro připojení k účtu LinkedIn.
1. Přihlaste se Microsoft 365 pomocí účtu správce.
1. Spusťte PowerShell.
1. Nainstalujte modul Azure AD spuštěním `Install-Module AzureAD`
1. Spusťte tento skript:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Pokud chcete použít skupinu z kroku 2 jako vybranou skupinu v nastavení připojení k účtu LinkedIn v centru pro správu Azure AD, přečtěte si téma [Povolení připojení k účtu LinkedIn v Azure Portal](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Povolení připojení k účtu LinkedIn pomocí Zásady skupiny

1. Stažení [souborů šablon pro správu Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extrahujte soubory **ADMX** a zkopírujte je do svého centrálního úložiště.
1. Otevřete Správu zásad skupiny.
1. Vytvořte objekt Zásady skupiny s následujícím nastavením: **Konfigurace uživatele**  >  **šablony pro správu**  >  **systém Microsoft Office 2016**  >  **různé**  >  **Zobrazit funkce LinkedInu v aplikacích Office**.
1. Vyberte možnost **povoleno** nebo **zakázáno**.
  
   Stav | Účinek
   ------ | ------
   **Povoleno** | Nastavení **Zobrazit funkce LinkedInu v aplikacích Office** v možnostech Office 2016 je povolené. Uživatelé ve vaší organizaci můžou používat funkce LinkedInu v aplikacích Office 2016.
   **Zakázáno** | Nastavení **Zobrazit funkce LinkedInu v aplikacích Office** v možnostech Office 2016 je zakázané a koncoví uživatelé toto nastavení nemůžou změnit. Uživatelé ve vaší organizaci nemůžou ve svých aplikacích Office 2016 používat funkce LinkedIn.

Tato zásada skupiny ovlivňuje jenom aplikace Office 2016 pro místní počítač. Pokud uživatelé vypnou LinkedIn ve svých aplikacích Office 2016, můžou pořád zobrazit funkce LinkedInu v Microsoft 365.

## <a name="next-steps"></a>Další kroky

* [Souhlas uživatele a sdílení dat pro LinkedIn](linkedin-user-consent.md)

* [Informace a funkce LinkedInu v aplikacích Microsoftu](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum helpdesku pro LinkedIn](https://www.linkedin.com/help/linkedin)

* [Zobrazit aktuální nastavení integrace LinkedInu v Azure Portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)