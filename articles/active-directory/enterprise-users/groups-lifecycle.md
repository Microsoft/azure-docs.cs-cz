---
title: Nastavit vypršení platnosti pro Microsoft 365 skupiny – Azure Active Directory | Microsoft Docs
description: Jak nastavit vypršení platnosti pro skupiny Microsoft 365 v Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a4ba5a9211cdb89f6ac5a92a315378e58f5d08a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709510"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Konfigurace zásad vypršení platnosti pro skupiny Microsoft 365

V tomto článku se dozvíte, jak spravovat životní cyklus Microsoft 365 skupin nastavením zásad vypršení platnosti. Zásady vypršení platnosti můžete nastavit jenom pro Microsoft 365 skupiny v Azure Active Directory (Azure AD).

Jakmile nastavíte vypršení platnosti skupiny:

- Skupiny s aktivitami uživatele se automaticky Obnovují jako vypršení platnosti v blízkosti.
- Vlastníci skupiny jsou upozorněni na obnovení skupiny, pokud se skupina automaticky neobnovuje.
- Odstraní se všechny skupiny, které nejsou obnoveny.
- Odstraněné skupiny Microsoft 365 mohou být obnoveny do 30 dnů vlastníky skupiny nebo správcem.

V současné době je možné nakonfigurovat pro všechny Microsoft 365 skupiny v organizaci Azure AD jenom jednu zásadu vypršení platnosti.

> [!NOTE]
> Konfigurace a použití zásad vypršení platnosti pro Microsoft 365 skupiny vyžaduje, abyste si je nastavili, ale ne nutně přiřazovat licence Azure AD Premium pro členy všech skupin, na které se zásady vypršení platnosti používají.

Informace o tom, jak stáhnout a nainstalovat rutiny Azure AD PowerShellu, najdete v tématu [Azure Active Directory PowerShellu pro graf 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Automatické obnovení na základě aktivity

Pomocí služby Azure AD Intelligence se teď skupiny automaticky Obnovují na základě toho, jestli se nedávno používaly. Tato funkce eliminuje nutnost ručních akcí podle vlastníků skupin, protože je založená na aktivitě uživatelů ve skupinách napříč Microsoft 365 službami, jako je Outlook, SharePoint nebo Teams. Pokud například vlastník nebo člen skupiny nahraje dokument do SharePointu, navštíví kanál týmů nebo pošle e-mail do skupiny v Outlooku, skupina se automaticky prodlouží přibližně 35 dní před vypršením platnosti skupiny a vlastník nezíská žádná oznámení o obnovení.

### <a name="activities-that-automatically-renew-group-expiration"></a>Aktivity, které automaticky Obnovují vypršení platnosti skupiny

Následující akce uživatele způsobují automatické obnovení skupin:

- SharePoint: zobrazení, úpravy, stažení, přesunutí, sdílení nebo nahrání souborů
- Outlook: připojit se ke skupině, číst/zapisovat zprávy ze skupinového prostoru, jako je například zpráva (v aplikaci Outlook Web Access)
- Týmy: navštivte kanál týmů

### <a name="auditing-and-reporting"></a>Auditování a vytváření sestav

Správci můžou získat seznam automaticky obnovených skupin z protokolů auditu aktivit ve službě Azure AD.

![Automatické obnovování skupin na základě aktivity](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Role a oprávnění

Níže jsou uvedené role, které můžou nakonfigurovat a používat vypršení platnosti pro Microsoft 365 skupiny ve službě Azure AD.

Role | Oprávnění
-------- | --------
Globální správce, správce skupiny nebo Správce uživatelů | Může vytvořit, číst, aktualizovat nebo odstranit nastavení zásad vypršení platnosti skupin Microsoft 365.<br>Může obnovit libovolnou skupinu Microsoft 365.
User | Může obnovit Microsoft 365 skupinu, kterou vlastní.<br>Může obnovit Microsoft 365 skupinu, kterou vlastní.<br>Může číst nastavení zásad vypršení platnosti.

Další informace o oprávněních k obnovení odstraněné skupiny najdete [v tématu Obnovení odstraněné Microsoft 365 skupiny v Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Nastavení vypršení platnosti skupiny

1. Otevřete [Centrum pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem vaší organizace Azure AD.

2. Vyberte **skupiny** a pak vyberte **vypršení platnosti** a otevřete nastavení vypršení platnosti.
  
   ![Nastavení vypršení platnosti pro skupiny](./media/groups-lifecycle/expiration-settings.png)

3. Na stránce **vypršení platnosti** můžete:

    - Nastavte dobu života skupiny ve dnech. Můžete vybrat jednu z přednastavených hodnot nebo vlastní hodnotu (měla by být 30 dní nebo více).
    - Zadejte e-mailovou adresu, na kterou by se měla odeslat oznámení o prodloužení a vypršení platnosti, když skupina nemá žádného vlastníka.
    - Vyberte, které Microsoft 365 skupiny vyprší. Můžete nastavit vypršení platnosti pro:
      - **Vše** Skupiny Microsoft 365
      - Seznam **vybraných** Microsoft 365 skupin
      - **Žádné** pro omezení vypršení platnosti pro všechny skupiny
    - Až budete hotovi, uložte nastavení kliknutím na **Uložit**.

> [!NOTE]
> - Když nastavíte vypršení platnosti, všechny skupiny, které jsou starší než interval vypršení platnosti, se nastaví na 35 dní do vypršení platnosti, pokud se skupina automaticky neobnoví nebo ji vlastník obnoví.
> - Když se dynamická skupina odstraní a obnoví, zobrazuje se jako nová skupina a znovu se vyplní podle pravidla. Tento proces může trvat až 24 hodin.
> - Oznámení o vypršení platnosti skupin používaných v týmech se zobrazí v informačním kanálu vlastník týmů.

## <a name="email-notifications"></a>E-mailová oznámení

Pokud se skupiny automaticky neobnovily, e-mailová oznámení, jako je tato, se odešlou do Microsoft 365 vlastníkům skupiny 30 dní, 15 dnů a 1 den před vypršením platnosti skupiny. Jazyk e-mailu Určuje preferovaný jazyk vlastníka skupin nebo nastavení jazyka Azure AD. Pokud vlastník skupiny definoval preferovaný jazyk nebo má více vlastníků stejný preferovaný jazyk, použije se tento jazyk. Ve všech ostatních případech se používá nastavení jazyka Azure AD.

![E-mailová oznámení vypršení platnosti](./media/groups-lifecycle/expiration-notification.png)

Z e-mailu s oznámením **skupiny** můžou vlastníci skupiny přímo přistupovat na stránku podrobností skupiny na [přístupovém panelu](https://account.activedirectory.windowsazure.com/r#/applications). V takovém případě mohou uživatelé získat další informace o skupině, jako je její popis, čas jejich poslední obnovy, čas vypršení platnosti a také možnost obnovit skupinu. Stránka Podrobnosti skupiny teď také obsahuje odkazy na prostředky skupiny Microsoft 365, aby vlastník skupiny mohl pohodlně zobrazit obsah a aktivitu ve své skupině.

Po vypršení platnosti skupiny se skupina odstraní o jeden den po datu vypršení platnosti. E-mailová oznámení, jako je tato zpráva, se odešlou vlastníkovi skupiny Microsoft 365 o vypršení platnosti a následném odstranění jejich Microsoft 365 skupiny.

![E-mailová oznámení o odstranění skupiny](./media/groups-lifecycle/deletion-notification.png)

Skupinu je možné obnovit do 30 dnů od jejího odstranění výběrem možnosti **obnovit skupinu** nebo pomocí rutin prostředí PowerShell, jak je popsáno v tématu [Obnovení odstraněné Microsoft 365 skupiny v Azure Active Directory](groups-restore-deleted.md). Počítejte s tím, že doba obnovení skupiny není přizpůsobená na 30 dní.

Pokud skupina, kterou obnovujete, obsahuje dokumenty, weby SharePointu nebo jiné trvalé objekty, může trvat až 24 hodin, než se úplně obnoví skupina a její obsah.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Jak načíst datum vypršení platnosti skupiny Microsoft 365

Kromě přístupového panelu, kde mohou uživatelé zobrazit podrobnosti o skupině včetně data vypršení platnosti a data posledního obnovení, může být datum vypršení Microsoft 365 skupiny načteno z Microsoft Graph REST API beta. v Microsoft Graph Beta byla povolena vlastnost expirationDateTime jako skupina. Dá se načíst pomocí žádosti o získání. Další podrobnosti najdete v [tomto příkladu](/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Aby bylo možné spravovat členství ve skupinách na přístupovém panelu, musí být v obecném nastavení skupiny Azure Active Directory nastavení omezit přístup ke skupinám v přístupovém panelu.

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Jak Microsoft 365 vypršení platnosti skupiny funguje s poštovní schránkou na základě právního blokování

Po vypršení platnosti skupiny a jejím odstranění se po uplynutí 30 dnů od odstranění dat skupiny z aplikací, jako je Planner, weby nebo týmy, trvale odstraní, ale poštovní schránka s právním blokováním se zachová a není trvale Odstraněná. Správce může pomocí rutin systému Exchange obnovit poštovní schránku, aby data načetla.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Jak funguje Microsoft 365 vypršení platnosti skupiny se zásadami uchovávání informací

Zásady uchovávání informací jsou nakonfigurovány pomocí centra zabezpečení a dodržování předpisů. Pokud jste nastavili zásady uchovávání informací pro Microsoft 365 skupiny, když vyprší a odstraníte skupinu, konverzace skupiny v poštovní schránce skupiny a v souborech v lokalitě skupiny se v kontejneru uchovávání uchovávají v případě určitého počtu dní definovaného v zásadách uchovávání informací. Uživatelé neuvidí po vypršení platnosti skupinu ani její obsah, ale mohou obnovit lokalitu a data poštovní schránky prostřednictvím e-Discovery.

## <a name="powershell-examples"></a>Příklady prostředí PowerShell

Tady jsou příklady, jak můžete pomocí rutin PowerShellu nakonfigurovat nastavení vypršení platnosti pro Microsoft 365 skupiny ve vaší organizaci Azure AD:

1. Nainstalujte modul PowerShellu v 2.0 a přihlaste se na příkazovém řádku PowerShellu:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Nakonfigurujte nastavení vypršení platnosti pomocí rutiny New-AzureADMSGroupLifecyclePolicy a nastavte dobu života pro všechny skupiny Microsoft 365 v organizaci Azure AD na 365 dní. Oznámení o obnovení pro skupiny Microsoft 365 bez vlastníků se odešlou emailaddress@contoso.com do.
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Načtení existující zásady Get-AzureADMSGroupLifecyclePolicy: Tato rutina načte aktuální nastavení vypršení platnosti skupiny Microsoft 365. V tomto příkladu vidíte:

   - ID zásady
   - Doba života všech skupin Microsoft 365 v organizaci Azure AD je nastavená na 365 dní.
   - Oznámení o obnovení pro skupiny Microsoft 365 bez vlastníků se odešlou do emailaddress@contoso.com .
  
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
  
1. Odeberte existující zásady Remove-AzureADMSGroupLifecyclePolicy: Tato rutina odstraní nastavení vypršení platnosti skupiny Microsoft 365, ale vyžaduje ID zásad. Tato rutina zakáže vypršení platnosti pro Microsoft 365 skupiny.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Následující rutiny se dají použít ke konfiguraci zásad podrobněji. Další informace najdete v [dokumentaci k prostředí PowerShell](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Další kroky

Tyto články poskytují další informace o skupinách Azure AD.

- [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Správa členů skupiny](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
