---
title: Souhlas správce pro připojení k účtu LinkedIn – Azure AD | Dokumenty společnosti Microsoft
description: Vysvětluje, jak povolit nebo zakázat připojení účtů integrace LinkedIn v aplikacích Microsoftu ve službě Azure Active Directory.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bf65f69d9dcaf6de2236c98b56b58ec7e021099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025399"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrace připojení účtů LinkedIn ve službě Azure Active Directory

Uživatelům ve vaší organizaci můžete povolit přístup k jejich připojením LinkedIn v některých aplikacích Microsoftu. Žádná data nejsou sdílena, dokud uživatelé neodsouhlasí připojení svých účtů. Svou organizaci můžete integrovat v [Centru pro správu](https://aad.portal.azure.com)Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Nastavení připojení účtu LinkedIn se aktuálně zavádí do organizací Azure AD. Když je zavedena do vaší organizace, je povolena ve výchozím nastavení.
> 
> Výjimky:
> * Toto nastavení není dostupné pro zákazníky, kteří používají Microsoft Cloud pro vládu USA, Microsoft Cloud Germany nebo Azure a Office 365 provozované společností 21Vianet v Číně.
> * Nastavení je ve výchozím nastavení vypnuto pro klienty zřízené v Německu. Všimněte si, že toto nastavení není k dispozici pro zákazníky, kteří používají Microsoft Cloud Německo.
> * Nastavení je ve výchozím nastavení vypnuto pro klienty zřízené ve Francii.
>
> Jakmile jsou pro vaši organizaci povolena připojení k účtu LinkedIn, připojení k účtu fungují poté, co uživatelé souhlasí s tím, aby aplikace přistupovaly k firemním datům jejich jménem. Informace o nastavení souhlasu uživatele naleznete [v tématu Jak odebrat uživateli přístup k aplikaci](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Povolení připojení účtu LinkedIn na webu Azure Portal

Připojení účtů LinkedIn můžete povolit pouze pro uživatele, ke kterým chcete mít přístup, od celé organizace až po vybrané uživatele ve vaší organizaci.

1. Přihlaste se k [Centru pro správu Azure AD](https://aad.portal.azure.com/) pomocí účtu, který je globálním správcem organizace Azure AD.
1. Vyberte **Uživatelé**.
1. V okně **Uživatelé** vyberte **Uživatelská nastavení**.
1. V části **Připojení účtů LinkedIn**umožněte uživatelům připojit své účty pro přístup k připojením LinkedIn v některých aplikacích Microsoftu. Žádná data nejsou sdílena, dokud uživatelé neodsouhlasí připojení svých účtů.

    * Výběrem **možnosti Ano** povolíte službu pro všechny uživatele ve vaší organizaci.
    * Výběrem **možnosti Vybraná skupina** povolíte službu pouze pro skupinu vybraných uživatelů ve vaší organizaci.
    * Výběrem **možnosti Ne,** chcete-li odvolat souhlas všech uživatelů ve vaší organizaci.

    ![Integrace připojení účtů LinkedIn v organizaci](./media/linkedin-integration/linkedin-integration.png)

1. Až budete hotovi, vyberte **Uložit,** chcete-li uložit nastavení.

> [!Important]
> Integrace LinkedIn není pro vaše uživatele plně povolena, dokud neuspějí s připojením svých účtů. Pokud uživatelům povolíte připojení k účtu, nebudou sdílena žádná data.

### <a name="assign-selected-users-with-a-group"></a>Přiřazení vybraných uživatelů ke skupině
Nahradili jsme možnost "Vybrané", která určuje seznam uživatelů s možností vybrat skupinu uživatelů, abyste mohli povolit možnost připojit účty LinkedIn a Microsoft pro jednu skupinu namísto mnoha jednotlivých uživatelů. Pokud nemáte pro vybrané jednotlivé uživatele povolena připojení k účtu LinkedIn, nemusíte nic dělat. Pokud jste dříve povolili připojení účtů LinkedIn pro vybrané jednotlivé uživatele, měli byste:

1. Získání aktuálního seznamu jednotlivých uživatelů
1. Přesunutí aktuálně povolených jednotlivých uživatelů do skupiny
1. Použijte skupinu z předchozí skupiny jako vybranou skupinu v nastavení připojení účtu LinkedIn v Centru pro správu Azure AD.

> [!NOTE]
> I když aktuálně vybrané jednotlivé uživatele do skupiny nepřesunete, můžou v aplikacích Microsoftu stále vidět informace na LinkedInu.

### <a name="get-the-current-list-of-selected-users"></a>Získání aktuálního seznamu vybraných uživatelů

1. Přihlaste se k Microsoft u 365 pomocí účtu správce.
1. Přejděte do části https://linkedinselectedusermigration.azurewebsites.net/ (Soubor > Nový > Jiné). Zobrazí se seznam uživatelů, kteří jsou vybráni pro připojení účtů LinkedIn.
1. Exportujte seznam do souboru CSV.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Přesunutí aktuálně vybraných jednotlivých uživatelů do skupiny

1. Spuštění PowerShellu
1. Instalace modulu Azure AD spuštěním`Install-Module AzureAD`
1. Spusťte tento skript:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Pokud chcete skupinu použít z kroku 2 jako vybranou skupinu v nastavení připojení účtu LinkedIn v Centru pro správu Azure AD, přečtěte si část [Povolení připojení účtů LinkedIn na webu Azure Portal](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Povolení připojení účtů LinkedIn pomocí zásad skupiny

1. Stažení [souborů šablon pro správu Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extrahujte soubory **ADMX** a zkopírujte je do centrálního úložiště.
1. Otevřete Správu zásad skupiny.
1. Vytvořte objekt zásad skupiny s následujícím nastavením:**Šablony** > pro **správu konfigurace** > uživatele**Microsoft Office 2016** > **Různé** > **zobrazovat funkce LinkedIn v aplikacích Office**.
1. Vyberte **Možnost Povoleno** nebo **Zakázáno**.
  
   Stav | Účinek
   ------ | ------
   **Enabled** (Povoleno) | **Nastavení Zobrazit LinkedIn v aplikacích Office** v office 2016 Možnosti je povolená. Uživatelé ve vaší organizaci můžou ve svých aplikacích Office 2016 používat funkce LinkedInu.
   **Disabled** (Zakázáno) | Nastavení **Zobrazit LinkedIn v aplikacích Office** v možnostech Office 2016 je zakázané a koncoví uživatelé toto nastavení nemůžou změnit. Uživatelé ve vaší organizaci nemůžou ve svých aplikacích Office 2016 používat funkce LinkedInu.

Tyto zásady skupiny se týkají jenom aplikací Office 2016 pro místní počítač. Pokud uživatelé zakáží LinkedIn ve svých aplikacích Office 2016, můžou pořád vidět funkce LinkedInu v Office 365.

## <a name="next-steps"></a>Další kroky

* [Souhlas uživatele a sdílení dat pro LinkedIn](linkedin-user-consent.md)

* [Informace a funkce LinkedIn ve vašich aplikacích Microsoftu](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum nápovědy LinkedIn](https://www.linkedin.com/help/linkedin)

* [Zobrazení aktuálního nastavení integrace LinkedIn na webu Azure Portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
