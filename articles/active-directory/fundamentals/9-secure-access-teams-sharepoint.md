---
title: Zabezpečení externího přístupu k Microsoft teams, SharePointu a OneDrivu pomocí Azure Active Directory
description: Zabezpečený přístup k Microsoft 365 službám jako součást celkového zabezpečení externího přístupu.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f373c849cd542769fec7a10f20dad04e742c9591
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565134"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>Zabezpečení externího přístupu k Microsoft teams, SharePointu a OneDrivu pro firmy 

Microsoft teams, SharePoint a OneDrive pro firmy jsou tři z nejčastěji používaných způsobů spolupráce a sdílení obsahu s externími uživateli. Pokud jsou "schválené" metody příliš omezující, uživatelé přejdou mimo schválené metody odesláním obsahu e-mailem nebo nastavením nezabezpečených externích procesů a aplikací, jako je osobní DropBox nebo OneDrive. Vaším cílem je vyrovnávat požadavky na zabezpečení díky snadné spolupráci.

Tento článek vás seznámí s určením a konfigurací externí spolupráce, která bude vyhovovat vašim obchodním cílům, pomocí Microsoft teams a SharePointu.

## <a name="governance-begins-in-azure-active-directory"></a>Zásady správného řízení začínají v Azure Active Directory

Sdílení v Microsoft 365 je součástí, které se řídí [externími identitami | Nastavení externí spolupráce](https://aad.portal.azure.com/) v Azure Active Directory (Azure AD). Pokud je v Azure AD zakázané nebo omezené externí sdílení, přepíše se všechna nastavení sdílení nakonfigurovaná v Microsoft 365. Výjimkou je to, že pokud není integrace služby Azure AD B2B povolená, SharePoint a OneDrive je možné nakonfigurovat tak, aby podporovaly sdílení ad-hoc pomocí jednorázových hesel (jednorázového hesla).

![Snímek obrazovky s externím nastavením spolupráce](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>Přístup uživatelů typu host

Existují tři možnosti přístupu uživatele typu Host, které určují, co můžou uživatelé typu Host vidět po jeho pozvání. 

Chcete-li uživatelům typu Host zabránit v zobrazení podrobností o dalších uživatelích typu Host a k zobrazení výčtu členství ve skupině, vyberte možnost Uživatelé typu Host mají omezený přístup k vlastnostem a členstvím objektů adresáře.

### <a name="guest-invite-settings"></a>Nastavení pozvánky hosta

Tato nastavení určují, kdo může pozvat hosty a jak se tito hosté můžou pozvat. Tato nastavení jsou povolená jenom v případě, že je povolená integrace s B2B.

Doporučujeme povolit správcům a uživatelům v roli pozvat hosta možnost pozvat. Toto nastavení umožňuje řízené procesy spolupráce, jak je uvedeno v následujícím příkladu.

* Vlastník týmu odešle lístek, kterému má být přiřazena role Pozvánka hosta a

   * Se bude zodpovědná za všechny pozvánky hosta.

   * Souhlasí s tím, že nepřidá uživatele do příslušné služby SharePoint.

   * Je možné provádět běžné kontroly přístupu a odvolávat přístup podle potřeby.

* Centrála provede následující

   * Umožňuje externí sdílení tím, že po dokončení školení udělí požadovanou roli.

   * Přiřadí licenci Azure AD P2 skupině Microsoft 365 vlastníkovi povolit kontroly přístupu.
   * Vytvoří kontrolu přístupu ke skupině Microsoft 365.

   * Potvrdí, že se objevují recenze přístupu.

   * Odebere uživatele přímo přidané do příslušné služby SharePoint.

 Nastavte **možnost Povolit jednorázové e-mailová hesla pro hosty (Preview) a povolit samoobslužný podpis hosta prostřednictvím toků uživatelů** na **Ano**. Toto nastavení využívá integraci s nastavením externí spolupráce Azure AD.

### <a name="collaboration-restrictions"></a>Omezení spolupráce

Omezení spolupráce mají tři možnosti. Vaše obchodní požadavky určují, kterou si zvolíte.

* **Povoluje odeslání pozvánek do libovolné domény** znamená, že může být libovolný uživatel pozván ke spolupráci.

* **Odepřít pozvánky zadaným doménám** znamená, že je možné pozvat libovolného uživatele mimo ty, aby mohli spolupracovat.

* **Možnost povoluje pozvánky pouze do zadaných domén** znamená, že nelze pozvat libovolného uživatele mimo tyto zadané domény. 

## <a name="govern-access-in-teams"></a>Řízení přístupu v týmech

[Týmy rozlišují externí uživatele (všichni mimo vaši organizaci) a uživatelé typu Host (s účty hosta)](/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH%e2%80%8b)). Nastavení spolupráce můžete spravovat na [portálu pro správu týmů](https://admin.teams.microsoft.com/company-wide-settings/external-communications) v části nastavení v rámci organizace. 

> [!NOTE]
> Nastavení spolupráce externích identit v Azure Active Directory řídí efektivní oprávnění. Můžete zvýšit omezení týmů, ale nemusíte je snížit z toho, co je ve službě Azure AD nastaveno.

* **Nastavení externího přístupu**. Ve výchozím nastavení týmy umožňují externí přístup, což znamená, že organizace může komunikovat se všemi externími doménami. Pokud chcete omezit nebo povolit konkrétní domény jenom pro týmy, můžete to udělat tady.

* **Přístup hosta** Přístup hosta určuje, co můžou uživatelé typu Host dělat v týmech.

Další informace o správě externího přístupu v týmech najdete v následujících zdrojích.

* [Správa externího přístupu v Microsoft Teams](/microsoftteams/manage-external-access)

* [Microsoft 365 modely identity a Azure Active Directory](/microsoft-365/enterprise/about-microsoft-365-identity)

* [Modely identit a ověřování pro Microsoft Teams](/MicrosoftTeams/identify-models-authentication)

* [Popisky citlivosti pro Microsoft Teams](/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>Řízení přístupu na SharePointu a OneDrivu

Správci služby SharePoint mají k dispozici mnoho nastavení pro spolupráci. Nastavení na úrovni organizace se spravují z centra pro správu SharePointu. Nastavení lze upravit pro jednotlivé weby služby SharePoint. Doporučujeme, aby vaše organizace měla potřebná minimální úroveň zabezpečení a aby podle potřeby zvýšila zabezpečení konkrétních webů. Například pro vysoce rizikové projekty budete možná chtít omezit uživatele na určité domény a zakázat možnost členů zvát hosty.

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>Integrace služby SharePoint a jedné jednotky pomocí Azure AD B2B

V rámci celkové strategie pro řízení externí spolupráce doporučujeme, abyste [povolili předběžnou integraci SharePointu a OneDrivu s Azure AD B2B](/sharepoint/sharepoint-azureb2b-integration-preview) .

Azure AD B2B poskytuje ověřování a správu uživatelů typu Host. Díky integraci SharePointu a OneDrivu se používají [jednorázová hesla Azure AD B2B](../external-identities/one-time-passcode.md) pro externí sdílení souborů, složek, položek seznamu, knihoven dokumentů a webů. Tato funkce nabízí upgradované prostředí ze stávajícího [uživatelského prostředí pro externí sdílení](/sharepoint/what-s-new-in-sharing-in-targeted-release).

> [!NOTE]
> Pokud povolíte verzi Preview pro integraci Azure AD B2B, pak se sdílení SharePointu a OneDrive řídí nastavením vztahů organizace Azure AD, jako jsou **členové můžou pozvat** a **pozvat hosty**.

### <a name="sharing-policies"></a>Zásady sdílení

*Externí sdílení* se dá nastavit pro SharePoint i OneDrive. Omezení OneDrivu nemůžou být přísnější než nastavení SharePointu.

 ![Snímek obrazovky s nastavením externího sdílení na SharePointu a OneDrivu](media/secure-external-access/9-sharepoint-settings.png)

Integrace služby SharePoint se službou Azure AD B2B mění způsob, jakým ovládací prvky komunikují s účty.

* **Kdokoli**. Nedoporučuje se

   * Bez ohledu na stav integrace, který povoluje odkazy na všechny uživatele, se při použití tohoto typu odkazu nepoužijí žádné zásady Azure. 

   * Ve scénáři řízení spolupráce tuto funkci nepovolujte. 
   > [!NOTE]
   > Může se stát, že budete chtít povolit toto nastavení pro konkrétní lokalitu. v takovém případě ji můžete povolit a nastavit větší omezení na jednotlivých lokalitách.

* **Noví a stávající hosté**. Doporučuje se, pokud máte povolenou integraci.

   * **S povolenou integrací služby Azure AD B2B** budou mít noví a stávající hosté účet Guest Azure AD B2B, který se dá spravovat pomocí zásad Azure AD.

   * **Bez povoleného integrace služby Azure AD B2B** nebudou noví hosté mít vytvořený účet Azure AD B2B a nebude je možné spravovat ze služby Azure AD. Jestli mají stávající hosté účet Azure AD B2B, záleží na tom, jak byl host vytvořen.

* **Stávající hosté**. Doporučuje se, pokud nemáte povolenou integraci.

   * Když je tato možnost povolená, můžou uživatelé sdílet jenom s ostatními uživateli, kteří jsou už ve vašem adresáři.

* **Pouze lidé ve vaší organizaci**. Nedoporučuje se, pokud potřebujete spolupracovat s externími uživateli.

   * Bez ohledu na stav integrace budou uživatelé moct sdílet jenom s uživateli ve vaší organizaci. 

* **Omezte externí sdílení podle domény**. Ve výchozím nastavení umožňuje SharePoint externí přístup, což znamená, že sdílení je povolené u všech externích domén. Pokud chcete omezit nebo povolit konkrétní domény jenom pro SharePoint, můžete to udělat tady.

* **Povolí sdílení externě jenom uživatelům z konkrétních skupin zabezpečení**.  Toto nastavení omezuje, kdo může sdílet obsah v SharePointu a OneDrivu, ale nastavení ve službě Azure AD platí pro všechny aplikace. Omezení toho, kdo může sdílet, může být užitečné, pokud chcete, aby vaši uživatelé mohli pořizovat školení týkající se bezpečného sdílení, a pak je po dokončení přidal do skupiny zabezpečení schválené sdílení. Pokud je toto nastavení vybrané a uživatelé nemají způsob, jak získat přístup k "schválenému sdílejícímu", můžou místo toho najít neschválené způsoby sdílení. 

* **Umožněte hostům sdílet položky, které nevlastní**. Doporučujeme ponechat tuto zakázanou.

* **Lidé, kteří používají ověřovací kód, se musí znovu ověřit po tomto počtu dnů (výchozí hodnota je 30)**. Toto nastavení doporučujeme povolit.

### <a name="access-controls"></a>Řízení přístupu

Nastavení řízení přístupu bude mít vliv na všechny uživatele ve vaší organizaci. Vzhledem k tom, že možná nebudete moci určit, jestli externí uživatelé mají zařízení splňující předpisy, tyto ovládací prvky nebudeme řešit tady. 

* **Odhlášení nečinné relace**. Doporučujeme povolit tento ovládací prvek, který vám po určité době nečinnosti umožní upozorňovat uživatele na nespravovaná zařízení a přihlašovat se na ně. Můžete nakonfigurovat období neaktivity a upozornění. 

* **Síťové umístění**. Nastavení tohoto ovládacího prvku znamená, že můžete dovolit přístup pouze k IP adresám, které vaše organizace vlastní. V případě externích scénářů spolupráce nastavte tuto hodnotu pouze v případě, že všichni externí partneři budou přistupovat k prostředkům pouze v rámci vaší sítě nebo prostřednictvím sítě VPN.

### <a name="file-and-folder-links"></a>Odkazy na soubory a složky

V centru pro správu SharePointu můžete také nastavit, jak se mají sdílet odkazy na soubory a složky. Pro každou lokalitu můžete také nakonfigurovat tato nastavení. 

 ![Snímek obrazovky s nastavením odkazu na soubor a složku](media/secure-external-access/9-file-folder-links.png)

Pokud jste povolili integraci s Azure AD B2B, sdílení souborů a složek s těmito objekty mimo organizaci bude mít za následek vytvoření uživatele B2B při sdílení souborů a složek.

Doporučujeme nastavit výchozí typ propojení **jenom na lidi ve vaší organizaci** a na výchozí oprávnění pro **Úpravy**. Tím zajistíte, aby se položky sdílely Thoughtfully. Pak můžete přizpůsobit toto nastavení pro výchozí nastavení pro jednotlivé lokality, která splňují konkrétní požadavky na spolupráci.

### <a name="anyone-links"></a>Odkazy na všechny uživatele

Nedoporučujeme povolování odkazů na všechny uživatele. Pokud tak učiníte, doporučujeme nastavit vypršení platnosti a zvážit jejich omezení na zobrazení oprávnění. Pokud zvolíte možnost Zobrazit pouze oprávnění pro soubory nebo složky, uživatelé nebudou moci změnit žádné odkazy, aby zahrnovaly oprávnění k úpravám.

Další informace o řízení přístupu k externímu přístupu ke službě SharePoint najdete v následujících tématech:

* [Přehled externího sdílení SharePointu](/sharepoint/external-sharing-overview)

* [Integrace SharePointu a OneDrivu s Azure AD B2B](/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích o zabezpečení externího přístupu k prostředkům. Doporučujeme, abyste provedli akce v uvedeném pořadí.

1. [Určení stav zabezpečení pro externí přístup](1-secure-access-posture.md)

2. [Zjistit aktuální stav](2-secure-access-current-state.md)

3. [Vytvoření plánu zásad správného řízení](3-secure-access-plan.md)

4. [Použití skupin pro zabezpečení](4-secure-access-groups.md)

5. [Přechod do Azure AD B2B](5-secure-access-b2b.md)

6. [Zabezpečený přístup se správou oprávnění](6-secure-access-entitlement-managment.md)

7. [Zabezpečený přístup pomocí zásad podmíněného přístupu](7-secure-access-conditional-access.md)

8. [Zabezpečený přístup s popisky citlivosti](8-secure-access-sensitivity-labels.md)

9. [Zabezpečený přístup k Microsoft teams, OneDrivu a SharePointu](9-secure-access-teams-sharepoint.md) (jste tady.)