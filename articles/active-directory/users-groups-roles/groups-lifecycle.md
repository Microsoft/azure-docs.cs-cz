---
title: Nastavení vypršení platnosti pro skupiny Office 365 – Azure Active Directory | Microsoft Docs
description: Jak nastavit vypršení platnosti pro skupiny Office 365 v Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 074b9ec06818363a97253a587ac451a38999832f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837938"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Konfigurace zásad vypršení platnosti pro skupiny Office 365

V tomto článku se dozvíte, jak spravovat životní cyklus skupin Office 365 nastavením zásad vypršení platnosti. Zásady vypršení platnosti můžete nastavit jenom pro skupiny Office 365 v Azure Active Directory (Azure AD).

Jakmile nastavíte vypršení platnosti skupiny:

- Vlastníkům skupiny se oznámí, že obnoví skupinu jako vypršení platnosti v blízkosti
- Odstraní se libovolná skupina, která není obnovená.
- Všechny odstraněné skupiny Office 365 je možné obnovit do 30 dnů vlastníky skupiny nebo správcem.

V současné době se pro skupiny Office 365 v rámci jednoho tenanta dá nakonfigurovat jenom jedna zásada vypršení platnosti.

> [!NOTE]
> Konfigurace a použití zásad vypršení platnosti pro skupiny Office 365 vyžaduje, abyste měli licence Azure AD Premium pro členy všech skupin, na které se zásady vypršení platnosti používají.

Informace o tom, jak stáhnout a nainstalovat rutiny Azure AD PowerShellu, najdete v tématu [Azure Active Directory PowerShellu pro graf 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Role a oprávnění

Níže jsou uvedené role, které můžou nakonfigurovat a používat vypršení platnosti pro skupiny Office 365 ve službě Azure AD.

Role | Oprávnění
-------- | --------
Globální správce nebo Správce uživatelů | Může vytvořit, číst, aktualizovat nebo odstranit nastavení zásad vypršení platnosti skupin Office 365.<br>Může obnovit libovolnou skupinu Office 365.
Uživatel | Může obnovit skupinu Office 365, kterou vlastní.<br>Může obnovit skupinu Office 365, kterou vlastní.<br>Může číst nastavení zásad vypršení platnosti.

Další informace o oprávněních k obnovení odstraněné skupiny najdete [v tématu Obnovení odstraněné skupiny Office 365 v Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Nastavení vypršení platnosti skupiny

1. Otevřete [Centrum pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem vaší organizace Azure AD.

2. Vyberte **skupiny**a pak vyberte **vypršení platnosti** a otevřete nastavení vypršení platnosti.
  
   ![Nastavení vypršení platnosti pro skupiny](./media/groups-lifecycle/expiration-settings.png)

3. Na stránce **vypršení platnosti** můžete:

  - Nastavte dobu života skupiny ve dnech. Můžete vybrat jednu z přednastavených hodnot nebo vlastní hodnotu (měla by být 31 dní nebo více).
  - Zadejte e-mailovou adresu, na kterou by se měla odeslat oznámení o prodloužení a vypršení platnosti, když skupina nemá žádného vlastníka.
  - Vyberte, které skupiny Office 365 vyprší. Můžete nastavit vypršení platnosti pro:
    - **Vše** Skupiny Office 365
    - Seznam **vybraných** skupin Office 365
    - **Žádné** pro omezení vypršení platnosti pro všechny skupiny
  - Až budete hotovi, uložte nastavení kliknutím na **Uložit**.

> [!NOTE]
> Když nastavíte vypršení platnosti, všechny skupiny, které jsou starší než interval vypršení platnosti, se nastaví na 30 dní do vypršení platnosti. První e-mail s oznámením o obnovení se pošle během dne. Například skupina A byla vytvořena před 400 dny a interval vypršení platnosti je nastaven na 180 dnů. Když použijete zásady vypršení platnosti, Skupina A bude mít 30 dní, než se odstraní, pokud je vlastník neobnoví.
> Když se dynamická skupina odstraní a obnoví, zobrazuje se jako nová skupina a znovu se vyplní podle pravidla. Tento proces může trvat až 24 hodin.

## <a name="email-notifications"></a>E-mailová oznámení

E-mailová oznámení, jako je tato, se odešlou vlastníkům skupiny Office 365 30 dní, 15 dnů a 1 den před vypršením platnosti skupiny. Jazyk e-mailu Určuje preferovaný jazyk vlastníka skupin nebo nastavení jazyka Azure AD. Pokud vlastník skupiny definoval preferovaný jazyk nebo má více vlastníků stejný preferovaný jazyk, použije se tento jazyk. Ve všech ostatních případech se používá nastavení jazyka Azure AD.

![E-mailová oznámení vypršení platnosti](./media/groups-lifecycle/expiration-notification.png)

Z e-mailu s oznámením **skupiny** můžou vlastníci skupiny přímo přistupovat na stránku podrobností skupiny na přístupovém panelu. V takovém případě mohou uživatelé získat další informace o skupině, jako je její popis, čas jejich poslední obnovy, čas vypršení platnosti a také možnost obnovit skupinu. Stránka Podrobnosti skupiny teď obsahuje taky odkazy na prostředky skupiny Office 365, aby vlastník skupiny mohl pohodlně zobrazit obsah a aktivitu ve své skupině.

Po vypršení platnosti skupiny se skupina odstraní o jeden den po datu vypršení platnosti. E-mailová oznámení, jako je tato zpráva, se odešlou vlastníkům skupiny Office 365, které o vypršení platnosti a následném odstranění skupiny sady Office 365 informují.

![E-mailová oznámení o odstranění skupiny](./media/groups-lifecycle/deletion-notification.png)

Skupinu je možné obnovit do 30 dnů od jejího odstranění výběrem možnosti **obnovit skupinu** nebo pomocí rutin prostředí PowerShell, jak je popsáno v tématu [Obnovení odstraněné skupiny Office 365 v Azure Active Directory](groups-restore-deleted.md). Počítejte s tím, že doba obnovení skupiny není přizpůsobená na 30 dní.

Pokud skupina, kterou obnovujete, obsahuje dokumenty, weby SharePointu nebo jiné trvalé objekty, může trvat až 24 hodin, než se úplně obnoví skupina a její obsah.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Jak načíst datum vypršení platnosti skupiny Office 365
Kromě přístupového panelu, kde uživatelé mohou zobrazit podrobnosti o skupině včetně data vypršení platnosti a data posledního obnovení, může být datum vypršení platnosti skupiny sady Office 365 z Microsoft Graph REST API beta. v Microsoft Graph Beta byla povolena vlastnost expirationDateTime jako skupina. Dá se načíst pomocí žádosti o získání. Další podrobnosti najdete v [tomto příkladu](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Aby bylo možné spravovat členství ve skupinách na přístupovém panelu, musí být v obecném nastavení skupiny Azure Active Directory nastavení omezit přístup ke skupinám v přístupovém panelu.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Jak funguje vyprší platnost skupiny Office 365 s poštovní schránkou na základě právního blokování
Po vypršení platnosti skupiny a jejím odstranění se po uplynutí 30 dnů od odstranění dat skupiny z aplikací, jako je Planner, weby nebo týmy, trvale odstraní, ale poštovní schránka s právním blokováním se zachová a není trvale Odstraněná. Správce může pomocí rutin systému Exchange obnovit poštovní schránku, aby data načetla. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Jak funguje vypršení platnosti skupiny Office 365 se zásadami uchovávání informací
Zásady uchovávání informací jsou nakonfigurovány pomocí centra zabezpečení a dodržování předpisů. Pokud jste pro skupiny Office 365 nastavili zásady uchovávání informací, když vyprší a odstraníte skupinu, konverzace skupiny v poštovní schránce skupiny a souborech v lokalitě skupiny se uchovávají v kontejneru uchovávání informací o konkrétním počtu dní definovaných v uchování. politických. Uživatelé neuvidí po vypršení platnosti skupinu ani její obsah, ale mohou obnovit lokalitu a data poštovní schránky prostřednictvím e-Discovery.

## <a name="powershell-examples"></a>Příklady prostředí PowerShell
Tady jsou příklady, jak můžete pomocí rutin PowerShellu nakonfigurovat nastavení vypršení platnosti pro skupiny Office 365 ve vaší organizaci Azure AD:

1. Nainstalujte modul PowerShellu v 2.0 a přihlaste se na příkazovém řádku PowerShellu:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Nakonfigurujte nastavení vypršení platnosti pomocí rutiny New-AzureADMSGroupLifecyclePolicy a nastavte dobu života všech skupin Office 365 v organizaci Azure AD na 365 dnů. Oznámení o obnovení pro skupiny Office 365 bez vlastníků se odešlouemailaddress@contoso.comdo:
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Načtěte existující zásady Get-AzureADMSGroupLifecyclePolicy: Tato rutina načte aktuální nastavení vypršení platnosti skupiny Office 365, která jsou nakonfigurovaná. V tomto příkladu vidíte:

   - ID zásady
   - Doba života všech skupin Office 365 v organizaci Azure AD je nastavená na 365 dnů.
   - Oznámení o obnovení pro skupiny Office 365 bez vlastníků se odešlouemailaddress@contoso.comdo.
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Aktualizujte stávající sadu zásad – AzureADMSGroupLifecyclePolicy: Tato rutina se používá k aktualizaci existujících zásad. V následujícím příkladu se doba života skupiny v existující zásadě změní z 365 dnů na 180 dnů.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Přidejte do zásady Add-AzureADMSLifecyclePolicyGroup konkrétní skupiny: Tato rutina přidá skupinu do zásad životního cyklu. Jako příklad:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Odeberte existující zásady Remove-AzureADMSGroupLifecyclePolicy: Tato rutina odstraní nastavení vypršení platnosti skupiny Office 365, ale vyžaduje ID zásad. Tím se zakáže vypršení platnosti pro skupiny Office 365.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Následující rutiny se dají použít ke konfiguraci zásad podrobněji. Další informace najdete v [dokumentaci k prostředí PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Další postup

Tyto články poskytují další informace o skupinách Azure AD.

- [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Správa členů skupiny](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
