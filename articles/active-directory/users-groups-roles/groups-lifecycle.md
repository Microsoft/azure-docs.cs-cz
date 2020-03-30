---
title: Nastavení vypršení platnosti pro skupiny Office 365 – Azure Active Directory | Dokumenty společnosti Microsoft
description: Jak nastavit vypršení platnosti pro skupiny Office 365 ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b551bc8a46a45135bf6a9a8e328b4b0e74f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048249"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Konfigurace zásad vypršení platnosti pro skupiny Office 365

V tomto článku se dozvíte, jak spravovat životní cyklus skupin Office 365 nastavením zásad vypršení platnosti. Zásady vypršení platnosti můžete nastavit jenom pro skupiny Office 365 ve službě Azure Active Directory (Azure AD).

Jakmile nastavíte vypršení platnosti skupiny:

- Skupiny s aktivitami uživatelů se automaticky obnovují s blížícím se vypršením platnosti.
- Vlastníci skupiny jsou upozorněni na obnovení skupiny, pokud skupina není automaticky obnovena.
- Všechny skupiny, která není obnovena, bude odstraněna.
- Všechny odstraněné skupiny Office 365 můžou do 30 dnů obnovit vlastníci skupiny nebo správce.

V současné době lze nakonfigurovat jenom jednu zásadu vypršení platnosti pro všechny skupiny Office 365 v organizaci Azure AD.

> [!NOTE]
> Konfigurace a použití zásad vypršení platnosti pro skupiny Office 365 vyžaduje, abyste měli, ale ne nutně přiřadit licence Azure AD Premium pro členy všech skupin, na které se zásady vypršení platnosti vztahují.

Informace o tom, jak stáhnout a nainstalovat rutiny prostředí Azure AD PowerShell, najdete [v tématu Azure Active Directory PowerShell pro graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Automatické obnovení na základě aktivity

S Azure AD intelligence skupiny se teď automaticky obnovují na základě toho, jestli byly nedávno použity. Tato funkce eliminuje potřebu ruční akce ze strany vlastníků skupin, protože je založena na aktivitě uživatelů ve skupinách ve službách Office 365, jako je Outlook, SharePoint nebo Teams. Pokud například vlastník nebo člen skupiny nahraje dokument na SharePointu, navštíví kanál Teams nebo pošle e-mail skupině v Outlooku, skupina se automaticky obnoví a vlastník nezískají žádná oznámení o obnovení.

### <a name="activities-that-automatically-renew-group-expiration"></a>Aktivity, které automaticky obnovují vypršení platnosti skupiny

Následující akce uživatele způsobují automatické obnovení skupiny:

- SharePoint: Zobrazení, úpravy, stahování, přesouvání, sdílení nebo nahrávání souborů
- Outlook: Připojení ke skupině, čtení a zápis skupinové zprávy ze skupinového prostoru, jako zpráva (v Outlook Web Accessu)
- Týmy: Navštivte kanál Teams

### <a name="auditing-and-reporting"></a>Audit a podávání zpráv

Správci můžou získat seznam automaticky obnovených skupin z protokolů auditu aktivit ve službě Azure AD.

![Automatické obnovení skupin na základě aktivity](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Role a oprávnění

Následují role, které můžou ve službě Azure AD nakonfigurovat a používat vypršení platnosti pro skupiny Office 365.

Role | Oprávnění
-------- | --------
Globální správce, správce skupiny nebo správce uživatelů | Můžete vytvořit, číst, aktualizovat nebo odstranit nastavení zásad vypršení platnosti skupin Office 365<br>Může obnovit libovolnou skupinu Office 365
Uživatel | Může obnovit skupinu Office 365, kterou vlastní<br>Může obnovit skupinu Office 365, kterou vlastní.<br>Umí číst nastavení zásad vypršení platnosti.

Další informace o oprávněních k obnovení odstraněné skupiny najdete [v tématu Obnovení odstraněné skupiny Office 365 ve službě Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Nastavení vypršení platnosti skupiny

1. Otevřete [Centrum pro správu Azure AD](https://aad.portal.azure.com) s účtem, který je globálním správcem ve vaší organizaci Azure AD.

2. Vyberte **Skupiny**, pak vyberte **Vypršení platnosti,** chcete-li otevřít nastavení vypršení platnosti.
  
   ![Nastavení vypršení platnosti pro skupiny](./media/groups-lifecycle/expiration-settings.png)

3. Na stránce **Vypršení platnosti** můžete:

    - Nastavte životnost skupiny ve dnech. Můžete vybrat jednu z přednastavených hodnot nebo vlastní hodnotu (měla by být 31 dní nebo více).
    - Zadejte e-mailovou adresu, na kterou mají být oznámení o obnovení a vypršení platnosti odeslána, pokud skupina nemá vlastníka.
    - Vyberte, jejichž platnost vyprší. Vypršení platnosti můžete nastavit pro:
      - **Všechny** Skupiny Office 365
      - Seznam **vybraných** skupin Office 365
      - **Žádné** omezení vypršení platnosti pro všechny skupiny
    - Po dokončení nastavení uložte možnost **Uložit**.

> [!NOTE]
> - Při prvním nastavení vypršení platnosti jsou všechny skupiny, které jsou starší než interval vypršení platnosti, nastaveny na 35 dní do vypršení platnosti, pokud není skupina automaticky obnovena nebo ji vlastník neobnoví.
> - Když je dynamická skupina odstraněna a obnovena, je považována za novou skupinu a znovu vyplněna podle pravidla. Tento proces může trvat až 24 hodin.
> - Oznámení o vypršení platnosti pro skupiny používané v Teams se zobrazují ve zdroji Vlastníků týmů.

## <a name="email-notifications"></a>E-mailová oznámení

Pokud se skupiny neobnovují automaticky, e-mailová oznámení, jako je tato, se odešlou vlastníkům skupin Office 365 30 dní, 15 dní a 1 den před vypršením platnosti skupiny. Jazyk e-mailu je určen upřednostňovaným jazykem vlastníka skupin nebo nastavením jazyka Azure AD. Pokud vlastník skupiny definoval upřednostňovaný jazyk nebo více vlastníků má stejný upřednostňovaný jazyk, použije se tento jazyk. Pro všechny ostatní případy se používá nastavení jazyka Azure AD.

![E-mailová oznámení vypršení platnosti](./media/groups-lifecycle/expiration-notification.png)

V e-mailu s oznámením **obnovit skupinu** mohou vlastníci skupiny přímo přistupovat ke stránce s podrobnostmi o skupině na [přístupovém panelu](https://account.activedirectory.windowsazure.com/r#/applications). Zde mohou uživatelé získat další informace o skupině, jako je její popis, kdy byla naposledy obnovena, kdy vyprší její platnost, a také možnost obnovit skupinu. Stránka s podrobnostmi o skupině teď obsahuje taky odkazy na prostředky skupiny Office 365, takže vlastník skupiny může pohodlně zobrazit obsah a aktivitu ve své skupině.

Po vypršení platnosti skupiny je skupina odstraněna jeden den po datu vypršení platnosti. E-mailové oznámení, jako je toto, se odešle vlastníkům skupin office 365, které je informuje o vypršení platnosti a následném odstranění jejich skupiny Office 365.

![E-mailová oznámení o odstranění skupiny](./media/groups-lifecycle/deletion-notification.png)

Skupinu lze obnovit do 30 dnů od jejího odstranění výběrem **skupiny Obnovení** nebo pomocí rutin prostředí PowerShell, jak je popsáno v části [Obnovení odstraněné skupiny Office 365 ve službě Azure Active Directory](groups-restore-deleted.md). Vezměte prosím na vědomí, že 30denní období obnovení skupiny nelze přizpůsobit.

Pokud obnovující skupina obsahuje dokumenty, sharepointové weby nebo jiné trvalé objekty, může úplné obnovení skupiny a jejího obsahu trvat až 24 hodin.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Jak načíst datum vypršení platnosti skupiny Office 365

Kromě přístupového panelu, kde mohou uživatelé zobrazit podrobnosti o skupině včetně data vypršení platnosti a data posledního obnovení, lze datum vypršení platnosti skupiny Office 365 načíst z beta verze rozhraní MICROSOFT Graph REST API. expirationDateTime jako vlastnost skupiny byla povolena v aplikaci Microsoft Graph Beta. Lze načíst s požadavkem GET. Další podrobnosti naleznete v [tomto příkladu](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Chcete-li spravovat členství ve skupinách na přístupovém panelu, je třeba ve všeobecném nastavení skupin služby Azure Active Directory nastavit možnost Omezit přístup ke skupinám na přístupovém panelu na "Ne".

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Jak funguje vypršení platnosti skupiny Office 365 s poštovní schránkou v blokování

Když vyprší platnost skupiny a je odstraněna, pak 30 dní po odstranění dat skupiny z aplikací, jako je Planner, Weby nebo Týmy, je trvale odstraněna, ale poštovní schránka skupiny, která je v právním stavu, se zachová a není trvale odstraněna. Správce může pomocí rutin serveru Exchange obnovit poštovní schránku pro načtení dat.

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Jak funguje vypršení platnosti skupiny Office 365 se zásadami uchovávání informací

Zásady uchovávání informací jsou konfigurovány prostřednictvím Centra zabezpečení a dodržování předpisů. Pokud jste nastavili zásady uchovávání informací pro skupiny Office 365, když vyprší platnost skupiny a je odstraněna, konverzace skupiny v poštovní schránce skupiny a soubory na webu skupiny se uchovají v kontejneru pro uchovávání informací po určitý počet dní definovaný v uchovávání informací Zásad. Uživatelé neuvidí skupinu ani její obsah po vypršení platnosti, ale mohou obnovit data webu a poštovní schránky prostřednictvím elektronického zjišťování.

## <a name="powershell-examples"></a>Příklady prostředí PowerShell

Tady jsou příklady, jak pomocí rutin Prostředí PowerShell nakonfigurovat nastavení vypršení platnosti pro skupiny Office 365 ve vaší organizaci Azure AD:

1. Nainstalujte modul PowerShellu v2.0 a přihlaste se na výzvu PowerShellu:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Konfigurace nastavení vypršení platnosti Pomocí rutiny New-AzureADMSGroupLifecyclePolicy nastavte životnost pro všechny skupiny Office 365 v organizaci Azure AD na 365 dní. Oznámení o obnovení pro skupiny Office 365emailaddress@contoso.combez vlastníků budou odeslána do aplikace "
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Načíst existující zásady Get-AzureADMSGroupLifecyclePolicy: Tato rutina načte aktuální nastavení vypršení platnosti skupiny Office 365, které byly nakonfigurovány. V tomto příkladu můžete vidět:

   - ID zásady
   - Životnost pro všechny skupiny Office 365 v organizaci Azure AD je nastavená na 365 dní
   - Oznámení o obnovení pro skupiny Office 365emailaddress@contoso.combez vlastníků budou odeslána na " ..
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Aktualizujte existující zásady Set-AzureADMSGroupLifecyclePolicy: Tato rutina se používá k aktualizaci existující zásady. V níže uvedeném příkladu se životnost skupiny v existující chodu změní z 365 dnů na 180 dní.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Přidejte konkrétní skupiny do zásady Add-AzureADMSLifecyclePolicyGroup: Tato rutina přidá skupinu do zásad životního cyklu. Příklad:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Odeberte existující zásady Remove-AzureADMSGroupLifecyclePolicy: Tato rutina odstraní nastavení vypršení platnosti skupiny Office 365, ale vyžaduje ID zásady. Tato rutina zakáže vypršení platnosti pro skupiny Office 365.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Následující rutiny lze použít ke konfiguraci zásad podrobněji. Další informace naleznete v [dokumentaci k prostředí PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

- Zásady životního cyklu get-AzureADMSGroup
- New-AzureADMSGroupZásady životního cyklu
- Zásady životního cyklu get-AzureADMSGroup
- Zásady životního cyklu set-AzureADMSGroup
- Odebrat AzureADMSGroupZásadživotního cyklu
- Add-AzureADMSSkupina zásad životního cyklu
- Odebrat AzureADMSSkupina zásad životního cyklu
- Skupina obnovení životního cyklu AzureaDMS
- Get-AzureADMSSkupina zásad životního cyklu

## <a name="next-steps"></a>Další kroky

Tyto články poskytují další informace o skupinách Azure AD.

- [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Správa členů skupiny](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
