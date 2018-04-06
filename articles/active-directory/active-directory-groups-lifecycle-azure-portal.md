---
title: Vypršení platnosti pro Office 365 skupin v Azure Active Directory | Microsoft Docs
description: Jak nastavit vypršení platnosti pro skupiny Office 365 ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: aa8551db8c3982fa61b3d4731a8aac3e2832f1b1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Konfigurace zásad vypršení platnosti pro skupiny Office 365

Teď můžete spravovat životní cyklus skupiny Office 365 pomocí nastavení zásad vypršení platnosti pro ně. Pro pouze skupiny Office 365 ve službě Azure Active Directory (Azure AD) můžete nastavit zásady vypršení platnosti. 

Jakmile jednou nastavíte skupinu vyprší:
-   Vlastníci skupiny jsou upozorněni obnovit skupinu jako doba vypršení platnosti přiblíží
-   Je-li odstranit všechny skupiny, který není obnoven
-   Všechny skupiny Office 365, který je odstraněn může být obnovena do 30 dní, podle vlastníků skupiny nebo správce

> [!NOTE]
> Konfiguraci a použití zásad vypršení platnosti pro skupiny Office 365 vyžaduje, abyste měli na straně Azure AD Premium licence pro všechny členy skupiny, pro které platí zásady vypršení platnosti.

Informace o tom, jak stáhnout a nainstalovat rutiny Azure AD PowerShell najdete v tématu [Azure Active Directory PowerShell pro graf 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Role a oprávnění
Níže jsou role, které můžete konfigurovat a používat vypršení platnosti pro skupiny Office 365 ve službě Azure AD.

Role | Oprávnění
-------- | --------
Globální správce nebo správce účtu uživatele | Můžete vytvořit, číst, aktualizovat nebo odstranit nastavení zásad vypršení platnosti skupiny Office 365<br>Můžete obnovit všechny skupiny Office 365
Uživatel | Můžete obnovit skupinu služeb Office 365, který vlastní<br>Můžete obnovit skupinu služeb Office 365, který vlastní<br>Může číst vypršení nastavení zásad

Další informace o oprávnění k obnovení odstraněné skupině, najdete v části [obnovit odstraněné skupiny Office 365 ve službě Azure Active Directory](active-directory-groups-restore-azure-portal.md).

## <a name="set-group-expiration"></a>Sada skupiny vypršení platnosti

1. Otevřete [centra pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem v klientovi služby Azure AD.

2. Vyberte **skupiny**, pak vyberte **vypršení platnosti** otevře se nastavení vypršení platnosti.
  
  ![Okno vypršení platnosti](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. Na **vypršení platnosti** okno, můžete:

  * Nastavení doby platnosti skupiny ve dnech. Můžete třeba vybrat jednu z přednastavení hodnoty, nebo vlastní hodnota (by měl být 31 dnů nebo déle). 
  * Zadejte e-mailovou adresu, kde odeslat oznámení vypršení platnosti a obnovení v případě, že skupina má bez vlastníka. 
  * Vyberte skupiny Office 365, které vyprší. Můžete povolit vypršení platnosti pro **všechny** skupiny Office 365, můžete povolit pouze **vybrané** skupiny Office 365, nebo můžete vybrat **žádné** zakázat vypršení platnosti pro všechny skupiny .
  * Uložit nastavení, když jste hotovi výběrem **Uložit**.


E-mailová oznámení, jako je tato jsou odesílány vlastníků skupiny Office 365 30 dní, 15 dní a 1 den před vypršením platnosti skupiny.

![Vypršení platnosti e-mailových oznámení](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Z **obnovit skupinu** oznámení e-mailu, skupině mohou vlastníci přímo přístup stránce s podrobnostmi o skupiny na přístupovém panelu. Uživatelé existuje, můžete získat další informace o skupině jako jeho popis, když ho byl naposledy obnovil, když vyprší platnost a také možnost obnovit skupinu. Stránce s podrobnostmi o skupiny teď taky obsahuje odkazy na skupiny prostředků Office 365, tak, aby vlastník skupiny můžete pohodlně zobrazit obsah a aktivity v jejich skupiny.

Když vyprší platnost skupinu, je skupinu odstranit jeden den po datu vypršení platnosti. E-mailové oznámení, jako je tato posílá informace o vypršení platnosti a následné mazání jejich skupiny Office 365 vlastníci skupiny Office 365.

![Skupiny odstranění e-mailových oznámení](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

Skupinu lze obnovit do 30 dní od jeho odstranění výběrem **obnovení skupiny** nebo pomocí rutin prostředí PowerShell, jak je popsáno v [obnovit odstraněné skupiny Office 365 ve službě Azure Active Directory](active-directory-groups-restore-azure-portal.md).
    
Pokud na skupinu, kterou jste obnovení obsahuje dokumenty, weby Sharepointu nebo jiné trvalé objekty, může trvat až 24 hodin plně obnovit skupiny a její obsah.

> [!NOTE]
> * Při prvním nastavení vypršení platnosti všech skupin, které jsou starší než interval vypršení platnosti jsou nastaveny na 30 dní do vypršení platnosti. První e-mail s oznámením obnovení odeslání během dne. 
>   Například skupiny A byl vytvořen před 400 dny a interval vypršení platnosti je nastaven na 180 dní. Při aplikování nastavení vypršení platnosti, skupiny A má 30 dní, než je odstraní, pokud vlastník obnovuje ho.
> * Aktuálně je možné nakonfigurovat jednu zásadu vypršení platnosti pro skupiny Office 365 na klienta.
> * Když dynamická skupina je odstranit a obnovit, je považovat za novou skupinu a znovu vyplněna podle pravidla. Tento proces může trvat až 24 hodin.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Jak funguje vypršení platnosti skupiny Office 365 s poštovní schránku soudního sporu
Když skupina vyprší a je odstraněna, pak 30 dnů po jejím odstranění skupiny dat z aplikace jako Planner, lokality, nebo týmy je trvale odstraněn, ale schránku skupiny, která je soudního sporu se uchovávají a není trvale odstraní. Správce může použít rutin systému Exchange k obnovení poštovní schránky se načíst data. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Jak funguje vypršení platnosti skupiny Office 365 s zásady uchovávání informací
Zásady uchovávání informací je nakonfigurované prostřednictvím centru dodržování předpisů a zabezpečení. Pokud jste nastavili zásady uchovávání informací pro skupiny Office 365, když skupina vyprší a je odstraněn, skupina konverzace v poštovní schránce skupiny a soubory na serveru skupiny jsou uchovány v kontejneru uchování pro určitý počet dní, které jsou definované v uchovávání zásady. Uživatelé neuvidí skupiny nebo jeho obsahu po vypršení platnosti, ale můžete obnovit data lokality a poštovní schránky prostřednictvím elektronického zjišťování.

## <a name="powershell-examples"></a>Příklady prostředí PowerShell
Zde jsou příklady jak konfigurovat nastavení vypršení platnosti pro skupiny Office 365 ve vašem klientovi pomocí rutin prostředí PowerShell:

1. Nainstalujte modul prostředí PowerShell v2.0 Preview (2.0.0.137) a přihlaste se na příkazovém řádku prostředí PowerShell:
  ````
  Install-Module -Name AzureADPreview
  connect-azuread 
  ````
2. Konfiguruje nastavení vypršení platnosti New-AzureADMSGroupLifecyclePolicy: Tato rutina nastaví dobu pro všechny skupiny Office 365 v klientovi do 365 dní. Obnovení oznámení pro Office 365 skupiny bez odeslána vlastníky 'emailaddress@contoso.com.
  
  ````
  New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
  ````
3. Načíst existující zásady Get-AzureADMSGroupLifecyclePolicy: Tato rutina načte aktuální nastavení vypršení platnosti skupiny Office 365, která byla nakonfigurována. V tomto příkladu se zobrazí:
  * ID zásady 
  * Doba platnosti pro všechny skupiny Office 365 v klientovi je nastavena na 365 dnů
  * Obnovení oznámení pro Office 365 skupiny bez odeslána vlastníky 'emailaddress@contoso.com. "
  
  ````
  Get-AzureADMSGroupLifecyclePolicy
  
  ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
  --                                    ------------------- ----------------- ---------------------------
  26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
  ```` 
   
4. Aktualizovat existující zásady Set-AzureADMSGroupLifecyclePolicy: Tato rutina se používá k aktualizaci existující zásady. V následujícím příkladu je doba platnosti skupiny v existující zásady změnit z 365 dnů na 180 dní. 
  
  ````
  Set-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”   -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
  ````
  
5. Přidání konkrétních skupin do zásad přidat AzureADMSLifecyclePolicyGroup: Tato rutina přidá skupinu zásadám životního cyklu. Jako příklad: 
  
  ````
  Add-AzureADMSLifecyclePolicyGroup -Id “26fcc232-d1c3-4375-b68d-15c296f1f077” -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
  ````
  
6. Odeberte existující zásady Remove-AzureADMSGroupLifecyclePolicy: Tato rutina odstraní nastavení vypršení platnosti skupiny Office 365, ale vyžaduje ID zásad. Tato akce zakáže vypršení platnosti pro skupiny Office 365. 
  
  ````
  Remove-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”
  ````
  
Následující rutiny můžete použít konfiguraci zásad podrobněji. Další informace najdete v části [prostředí PowerShell dokumentaci](https://docs.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Další postup
Tyto články poskytují další informace o skupinách Azure AD.

* [Zobrazení existujících skupin](active-directory-groups-view-azure-portal.md)
* [Spravovat nastavení skupiny](active-directory-groups-settings-azure-portal.md)
* [Správa členů skupiny](active-directory-groups-members-azure-portal.md)
* [Správa členství ve skupině](active-directory-groups-membership-azure-portal.md)
* [Dynamická pravidla pro uživatele ve skupině pro správu](active-directory-groups-dynamic-membership-azure-portal.md)
