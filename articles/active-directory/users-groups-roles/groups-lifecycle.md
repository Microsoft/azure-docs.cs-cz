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
ms.topic: how-to
ms.date: 03/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 182f51a150c2ef944b0104b73c63028e915c1a4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84728329"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Konfigurace zásad vypršení platnosti pro skupiny Office 365

V tomto článku se dozvíte, jak spravovat životní cyklus skupin Office 365 nastavením zásad vypršení platnosti. Zásady vypršení platnosti můžete nastavit jenom pro skupiny Office 365 v Azure Active Directory (Azure AD).

Jakmile nastavíte vypršení platnosti skupiny:

- Skupiny s aktivitami uživatele se automaticky Obnovují jako vypršení platnosti v blízkosti.
- Vlastníci skupiny jsou upozorněni na obnovení skupiny, pokud se skupina automaticky neobnovuje.
- Odstraní se všechny skupiny, které nejsou obnoveny.
- Všechny odstraněné skupiny Office 365 je možné obnovit do 30 dnů vlastníky skupiny nebo správcem.

V současné době je možné nakonfigurovat pro všechny skupiny Office 365 v organizaci Azure AD jenom jednu zásadu vypršení platnosti.

> [!NOTE]
> Konfigurace a použití zásad vypršení platnosti pro skupiny Office 365 vyžaduje, abyste měli k disAzure AD Premium licence pro členy všech skupin, na které se zásady vypršení platnosti aplikují, ale ne nutně.

Informace o tom, jak stáhnout a nainstalovat rutiny Azure AD PowerShellu, najdete v tématu [Azure Active Directory PowerShellu pro graf 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Automatické obnovení na základě aktivity

Pomocí služby Azure AD Intelligence se teď skupiny automaticky Obnovují na základě toho, jestli se nedávno používaly. Tato funkce eliminuje nutnost ručních akcí podle vlastníků skupin, protože je založená na aktivitě uživatelů ve skupinách napříč službami Office 365, jako je Outlook, SharePoint nebo Teams. Pokud například vlastník nebo člen skupiny nahraje dokument do SharePointu, navštíví kanál týmů nebo pošle e-mailovou zprávu do skupiny v Outlooku, skupina se automaticky obnoví a vlastník nezíská žádná oznámení o obnovení.

### <a name="activities-that-automatically-renew-group-expiration"></a>Aktivity, které automaticky Obnovují vypršení platnosti skupiny

Následující akce uživatele způsobují automatické obnovení skupin:

- SharePoint: zobrazení, úpravy, stažení, přesunutí, sdílení nebo nahrání souborů
- Outlook: připojit se ke skupině, číst/zapisovat zprávy ze skupinového prostoru, jako je například zpráva (v aplikaci Outlook Web Access)
- Týmy: navštivte kanál týmů

### <a name="auditing-and-reporting"></a>Auditování a vytváření sestav

Správci můžou získat seznam automaticky obnovených skupin z protokolů auditu aktivit ve službě Azure AD.

![Automatické obnovování skupin na základě aktivity](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Role a oprávnění

Níže jsou uvedené role, které můžou nakonfigurovat a používat vypršení platnosti pro skupiny Office 365 ve službě Azure AD.

Role | Oprávnění
-------- | --------
Globální správce, správce skupiny nebo Správce uživatelů | Může vytvořit, číst, aktualizovat nebo odstranit nastavení zásad vypršení platnosti skupin Office 365.<br>Může obnovit libovolnou skupinu Office 365.
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
> - Když nastavíte vypršení platnosti, všechny skupiny, které jsou starší než interval vypršení platnosti, se nastaví na 35 dní do vypršení platnosti, pokud se skupina automaticky neobnoví nebo ji vlastník obnoví.
> - Když se dynamická skupina odstraní a obnoví, zobrazuje se jako nová skupina a znovu se vyplní podle pravidla. Tento proces může trvat až 24 hodin.
> - Oznámení o vypršení platnosti skupin používaných v týmech se zobrazí v informačním kanálu vlastník týmů.

## <a name="email-notifications"></a>E-mailová oznámení

Pokud se skupiny automaticky neobnovily, e-mailová oznámení, jako je tato, se odešlou vlastníkům skupiny Office 365 30 dní, 15 dnů a 1 den před vypršením platnosti skupiny. Jazyk e-mailu Určuje preferovaný jazyk vlastníka skupin nebo nastavení jazyka Azure AD. Pokud vlastník skupiny definoval preferovaný jazyk nebo má více vlastníků stejný preferovaný jazyk, použije se tento jazyk. Ve všech ostatních případech se používá nastavení jazyka Azure AD.

![E-mailová oznámení vypršení platnosti](./media/groups-lifecycle/expiration-notification.png)

Z e-mailu s oznámením **skupiny** můžou vlastníci skupiny přímo přistupovat na stránku podrobností skupiny na [přístupovém panelu](https://account.activedirectory.windowsazure.com/r#/applications). V takovém případě mohou uživatelé získat další informace o skupině, jako je její popis, čas jejich poslední obnovy, čas vypršení platnosti a také možnost obnovit skupinu. Stránka Podrobnosti skupiny teď obsahuje taky odkazy na prostředky skupiny Office 365, aby vlastník skupiny mohl pohodlně zobrazit obsah a aktivitu ve své skupině.

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

Zásady uchovávání informací jsou nakonfigurovány pomocí centra zabezpečení a dodržování předpisů. Pokud jste pro skupiny Office 365 nastavili zásady uchovávání informací, když vyprší a odstraníte skupinu, konverzace skupiny v poštovní schránce skupiny a souborech v lokalitě skupiny se v kontejneru uchovávání uchovávají v případě určitého počtu dní definovaného v zásadách uchovávání informací. Uživatelé neuvidí po vypršení platnosti skupinu ani její obsah, ale mohou obnovit lokalitu a data poštovní schránky prostřednictvím e-Discovery.

## <a name="powershell-examples"></a>Příklady prostředí PowerShell

Tady jsou příklady, jak můžete pomocí rutin PowerShellu nakonfigurovat nastavení vypršení platnosti pro skupiny Office 365 ve vaší organizaci Azure AD:

1. Nainstalujte modul PowerShellu v 2.0 a přihlaste se na příkazovém řádku PowerShellu:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Nakonfigurujte nastavení vypršení platnosti pomocí rutiny New-AzureADMSGroupLifecyclePolicy a nastavte dobu života všech skupin Office 365 v organizaci Azure AD na 365 dnů. Oznámení o obnovení pro skupiny Office 365 bez vlastníků se odešlou emailaddress@contoso.com do:
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Načtení existující zásady Get-AzureADMSGroupLifecyclePolicy: Tato rutina načte aktuální nastavení vypršení platnosti skupiny Office 365, která jsou nakonfigurovaná. V tomto příkladu vidíte:

   - ID zásady
   - Doba života všech skupin Office 365 v organizaci Azure AD je nastavená na 365 dnů.
   - Oznámení o obnovení pro skupiny Office 365 bez vlastníků se odešlou do emailaddress@contoso.com .
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Aktualizace existující sady zásad – AzureADMSGroupLifecyclePolicy: Tato rutina se používá k aktualizaci existujících zásad. V následujícím příkladu se doba života skupiny v existující zásadě změní z 365 dnů na 180 dnů.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Přidání specifických skupin do zásad Add-AzureADMSLifecyclePolicyGroup: Tato rutina přidá skupinu do zásad životního cyklu. Příklad:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Odeberte existující zásady Remove-AzureADMSGroupLifecyclePolicy: Tato rutina odstraní nastavení vypršení platnosti skupiny Office 365, ale vyžaduje ID zásad. Tato rutina zakáže vypršení platnosti pro skupiny Office 365.
  
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
- Reset – AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Další kroky

Tyto články poskytují další informace o skupinách Azure AD.

- [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Správa členů skupiny](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
