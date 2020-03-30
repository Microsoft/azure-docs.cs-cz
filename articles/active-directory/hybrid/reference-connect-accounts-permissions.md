---
title: 'Azure AD Connect: Účty a oprávnění | Dokumenty společnosti Microsoft'
description: Toto téma popisuje použité a vytvořené účty a požadovaná oprávnění.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6071e6553fb1275fea63a37b4897aef2685bd509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253764"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Účty a oprávnění

## <a name="accounts-used-for-azure-ad-connect"></a>Účty používané pro Azure AD Connect

![přehled účtů](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect používá 3 účty k synchronizaci informací z místního prostředí nebo služby Windows Server Active Directory do služby Azure Active Directory.  Jedná se o tyto účty:

- **Účet konektoru služby AD DS**: používá se ke čtení a zápisu informací do služby Active Directory systému Windows Server

- **Účet služby ADSync:** slouží ke spuštění synchronizační služby a přístupu k databázi SQL

- **Účet Konektor Azure AD:** slouží k zápisu informací do Azure AD

Kromě těchto tří účtů používaných ke spuštění služby Azure AD Connect budete k instalaci služby Azure AD Connect také potřebovat následující další účty.  Jsou to:

- **Účet místního správce**: Správce, který instaluje Službu Azure AD Connect a který má v počítači oprávnění místního správce.

- **Účet správce rozlehlé sítě Služby AD DS**: Volitelně se používá k vytvoření výše uvedeného účtu konektoru služby AD DS.

- **Účet globálního správce Azure AD:** slouží k vytvoření účtu konektoru Azure AD a konfiguraci Služby Azure AD.

- **Účet SQL SA (volitelné):** slouží k vytvoření databáze ADSync při použití plné verze serveru SQL Server.  Tento SQL Server může být místní nebo vzdálené instalace Azure AD Connect.  Tento účet může být stejný jako podnikový správce.  Zřizování databáze teď může provádět mimo pásmo správcem SQL a pak nainstalovat správce azure ad connect s právy vlastníka databáze.  Informace o tomto [tématu Instalace Azure AD Connect pomocí oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md)


>[!IMPORTANT]
> Od sestavení 1.4.###.# již není podporováno používat účet správce rozlehlé sítě nebo správce domény jako účet konektoru služby AD DS.  Pokud se pokusíte zadat účet, který je správcem rozlehlé sítě nebo správcem domény při zadávání **použití existujícího účtu**, zobrazí se chyba.

> [!NOTE]
> Je podporována pro správu účtů pro správu používaných v Azure AD Connect z doménové struktury pro správu ESAE (také známá jako "červená doménová struktura").
> Vyhrazené doménové struktury pro správu umožňují organizacím hostovat účty, pracovní stanice a skupiny pro správu v prostředí, které mají silnější zabezpečovací mechanizmy než produkční prostředí.
> Další informace o vyhrazených správních lesích naleznete v [esae administrative forest design approach](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach).

> [!NOTE]
> Role globálního správce není po počátečním nastavení vyžadována a jediným požadovaným účtem bude účet role **Účty synchronizace adresářů.** To nutně neznamená, že budete chtít pouze odebrat účet s rolí globálního správce. Je lepší změnit roli na méně výkonnou roli, protože úplné odebrání účtu může způsobit problémy, pokud budete někdy potřebovat znovu spustit průvodce. Snížením oprávnění role můžete vždy znovu zvýšit oprávnění, pokud budete muset znovu využít průvodce Azure AD Connect. 

## <a name="installing-azure-ad-connect"></a>Instalace služby Azure AD Connect
Průvodce instalací služby Azure AD Connect nabízí dvě různé cesty:

* V expresním nastavení vyžaduje průvodce více oprávnění.  To je tak, že můžete nastavit konfiguraci snadno, aniž byste museli vytvářet uživatele nebo konfigurovat oprávnění.
* V aplikaci Vlastní nastavení vám průvodce nabízí další možnosti a možnosti. Existují však některé situace, ve kterých je třeba zajistit, že máte správná oprávnění sami.



## <a name="express-settings-installation"></a>Expresní instalace nastavení
V nastavení expressu vás průvodce instalací požádá o následující:

  - Pověření správce rozlehlé sítě služby AD DS
  - Přihlašovací údaje globálního správce Azure AD

### <a name="ad-ds-enterprise-admin-credentials"></a>Přihlašovací údaje správce služby AD DS Enterprise
Účet správce rozlehlé sítě AD DS Enterprise slouží ke konfiguraci místní služby Active Directory. Tato pověření se používají pouze během instalace a nejsou použita po dokončení instalace. Správce rozlehlé sítě, nikoli správce domény, by měl zajistit, aby oprávnění ve službě Active Directory mohla být nastavena ve všech doménách.

Pokud upgradujete z DirSync, přihlašovací údaje správce služby AD DS Enterprise se používají k resetování hesla pro účet používaný společností DirSync. Potřebujete také přihlašovací údaje globálního správce Azure AD.

### <a name="azure-ad-global-admin-credentials"></a>Přihlašovací údaje globálního správce Azure AD
Tato pověření se používají pouze během instalace a nejsou použita po dokončení instalace. Používá se k vytvoření účtu Konektor Azure AD slouží k synchronizaci změn ve službě Azure AD. Účet také umožňuje synchronizaci jako funkci ve službě Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Účet konektoru služby AD DS vyžaduje oprávnění pro expresní nastavení
Účet služby AD DS Connector je vytvořen pro čtení a zápis do služby Windows Server AD a má následující oprávnění při vytvoření expresnímnastavením:

| Oprávnění | Použití |
| --- | --- |
| <li>Replikovat změny adresáře</li><li>Replikovat všechny změny adresáře |Synchronizace hash hesla |
| Číst/zapisovat všechny vlastnosti Uživatele |Import a exchange hybrid |
| Čtení a zápis všech vlastností iNetOrgPerson |Import a exchange hybrid |
| Skupina číst/zapisovat všechny vlastnosti |Import a exchange hybrid |
| Čtení/zápis všech vlastností Kontakt |Import a exchange hybrid |
| Resetování hesla |Příprava na povolení zpětného zápisu hesla |

### <a name="express-installation-wizard-summary"></a>Souhrn průvodce expresní instalací

![Expresní instalace](./media/reference-connect-accounts-permissions/express.png)

Následuje souhrn stránek průvodce expresní instalací, shromážděných přihlašovacích údajů a jejich použití.

| Stránka průvodce | Shromážděná pověření | Je vyžadována oprávnění. | Používá se pro |
| --- | --- | --- | --- |
| Není dostupné. |Uživatel, který spouštěl Průvodce instalací |Správce místního serveru |<li>Vytvoří účet služby ADSync, který se používá ke spuštění synchronizační služby. |
| Připojení k Azure AD |Pověření adresáře Azure AD |Role globálního správce ve službě Azure AD |<li>Povolení synchronizace v adresáři Azure AD.</li>  <li>Vytvoření účtu Konektor Azure AD, který se používá pro operace on-going synchronizace ve službě Azure AD.</li> |
| Připojení ke službě AD DS |Místní pověření služby Active Directory |Člen skupiny Enterprise Admins (EA) ve službě Active Directory |<li>Vytvoří účet konektoru služby AD DS ve službě Active Directory a udělí mu oprávnění. Tento vytvořený účet se používá ke čtení a zápisu informací o adresáři během synchronizace.</li> |


## <a name="custom-installation-settings"></a>Vlastní nastavení instalace

Při instalaci vlastního nastavení vám průvodce nabídne více možností a možností. 

### <a name="custom-installation-wizard-summary"></a>Souhrn průvodce vlastní instalací

Následuje souhrn stránek průvodce vlastní instalací, shromážděná pověření a jejich použití.

![Expresní instalace](./media/reference-connect-accounts-permissions/customize.png)

| Stránka průvodce | Shromážděná pověření | Je vyžadována oprávnění. | Používá se pro |
| --- | --- | --- | --- |
| Není dostupné. |Uživatel, který spouštěl Průvodce instalací |<li>Správce místního serveru</li><li>Pokud používáte úplný SQL Server, musí být uživatel správcem systému (SA) v SQL</li> |Ve výchozím nastavení vytvoří místní účet, který se používá jako účet služby synchronizačního modulu. Účet je vytvořen pouze v případě, že správce nezadává konkrétní účet. |
| Instalace synchronizačních služeb, možnost účtu služby |Pověření uživatelského účtu ad nebo místního uživatelského účtu |Uživateli, oprávnění jsou udělena průvodcem instalací |Pokud správce určuje účet, tento účet se používá jako účet služby pro synchronizační službu. |
| Připojení k Azure AD |Pověření adresáře Azure AD |Role globálního správce ve službě Azure AD |<li>Povolení synchronizace v adresáři Azure AD.</li>  <li>Vytvoření účtu Konektor Azure AD, který se používá pro operace on-going synchronizace ve službě Azure AD.</li> |
| Připojení adresářů |Místní pověření služby Active Directory pro každou doménovou strukturu připojenou ke službě Azure AD |Oprávnění závisí na tom, které funkce povolíte a které najdete v části Vytvoření účtu konektoru ad DS |Tento účet se používá ke čtení a zápisu informací o adresáři během synchronizace. |
| Servery služby AD FS |Pro každý server v seznamu průvodce shromažďuje pověření, pokud přihlašovací pověření uživatele spuštěného průvodce nejsou dostatečná pro připojení |Správce domény |Instalace a konfigurace role serveru služby AD FS. |
| Proxy servery webových aplikací |Pro každý server v seznamu průvodce shromažďuje pověření, pokud přihlašovací pověření uživatele spuštěného průvodce nejsou dostatečná pro připojení |Místní správce v cílovém počítači |Instalace a konfigurace role serveru WAP. |
| Pověření pro důvěryhodnost serveru proxy |Pověření vztahu důvěryhodnosti služby Federation Service (pověření, která proxy server používá k zápisu certifikátu důvěryhodnosti z FS |Účet domény, který je místním správcem serveru služby AD FS |Počáteční zápis certifikátu důvěryhodnosti FS-WAP. |
| Stránka Účet služby Služby AD FS s možností Použít uživatelský účet domény |Pověření uživatelského účtu ad |Uživatel domény |Uživatelský účet Azure AD, jehož přihlašovací údaje jsou k dispozici, se používá jako přihlašovací účet služby AD FS. |

### <a name="create-the-ad-ds-connector-account"></a>Vytvoření účtu konektoru ad DS

>[!IMPORTANT]
>Nový modul prostředí PowerShell s názvem ADSyncConfig.psm1 byl zaveden s sestavením **1.1.880.0** (vydanév srpnu 2018), který obsahuje kolekci rutin, které vám pomohou nakonfigurovat správná oprávnění služby Active Directory pro účet Konektor Azure AD DS.
>
>Další informace naleznete v [tématu Azure AD Connect: Konfigurace oprávnění účtu konektoru služby AD DS](how-to-connect-configure-ad-ds-connector-account.md)

Účet zadaný na stránce **Připojit adresáře** musí být před instalací ve službě Active Directory k dispozici.  Azure AD Connect verze 1.1.524.0 a novější má možnost nechat průvodce Azure AD Connect vytvořit **účet konektoru služby AD DS,** který slouží k připojení ke službě Active Directory.  

Musí mít také požadovaná oprávnění udělena. Průvodce instalací neověřuje oprávnění a všechny problémy jsou nalezeny pouze během synchronizace.

Požadovaná oprávnění závisí na volitelných funkcích, které povolíte. Pokud máte více domén, musí být oprávnění udělena pro všechny domény v doménové struktuře. Pokud žádnou z těchto funkcí nepovolíte, budou dostatečná výchozí oprávnění **uživatele domény.**

| Funkce | Oprávnění |
| --- | --- |
| funkce ms-DS-ConsistencyGuid |Oprávnění k zápisu do atributu ms-DS-ConsistencyGuid zdokumentovaného v [konceptech návrhu – pomocí ms-DS-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synchronizace hash hesla |<li>Replikovat změny adresáře</li>  <li>Replikovat všechny změny adresáře |
| Hybridní nasazení Exchange |Zapisujte oprávnění k atributům dokumentovaným v [hybridním zpětném zápisu exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) pro uživatele, skupiny a kontakty. |
| Veřejná složka exchange mailu |Oprávnění ke čtení atributů zdokumentovaných ve [veřejné složce Pošty Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) pro veřejné složky. | 
| Zpětný zápis hesla |Oprávnění k zápisu k atributům zdokumentovaným v [části Začínáme se správou hesel](../authentication/howto-sspr-writeback.md) pro uživatele. |
| Zpětný zápis zařízení |Oprávnění udělená skriptem prostředí PowerShell, jak je popsáno v [zápisu zařízení](how-to-connect-device-writeback.md). |
| Zpětný zápis skupin |Umožňuje odepsat **skupiny Office 365** do doménové struktury s nainstalovaným Exchangem.  Další informace naleznete v [tématu Skupinový zpětný zápis](how-to-connect-preview.md#group-writeback).|

## <a name="upgrade"></a>Upgrade
Při upgradu z jedné verze Služby Azure AD Connect na novou verzi potřebujete následující oprávnění:

>[!IMPORTANT]
>Počínaje sestavením 1.1.484 azure ad connect zavedla regresní chybu, která vyžaduje oprávnění sysadmin k upgradu databáze SQL.  Tato chyba je opravena v sestavení 1.1.647.  Pokud upgradujete na toto sestavení, budete potřebovat oprávnění sysadmin.  Oprávnění Dbo nejsou dostatečná.  Pokud se pokusíte upgradovat Azure AD Connect bez oprávnění sysadmin, upgrade se nezdaří a Azure AD Connect už nebude fungovat správně poté.  Společnost Microsoft si je toho vědoma a pracuje na nápravě.


| Objekt zabezpečení | Je vyžadována oprávnění. | Použití |
| --- | --- | --- |
| Uživatel, který spouštěl Průvodce instalací |Správce místního serveru |Aktualizujte binární soubory. |
| Uživatel, který spouštěl Průvodce instalací |Člen adsyncadmins |Proveďte změny pravidel synchronizace a další konfigurace. |
| Uživatel, který spouštěl Průvodce instalací |Pokud používáte úplný SQL server: DBO (nebo podobné) databáze synchronizačního motoru |Proveďte změny na úrovni databáze, například aktualizaci tabulek s novými sloupci. |

## <a name="more-about-the-created-accounts"></a>Další informace o vytvořených účtech
### <a name="ad-ds-connector-account"></a>Účet konektoru služby AD DS
Pokud používáte expresní nastavení, vytvoří se ve službě Active Directory účet, který se používá pro synchronizaci. Vytvořený účet je umístěn v kořenové doméně doménové struktury v kontejneru Uživatelé a má svůj název předponou **MSOL_**. Účet je vytvořen s dlouhým složitým heslem, jehož platnost nevyprší. Pokud máte zásady hesel ve vaší doméně, ujistěte se, že pro tento účet budou povolena dlouhá a složitá hesla.

![Účet ad](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Pokud používáte vlastní nastavení, pak jste zodpovědní za vytvoření účtu před zahájením instalace.  Viz Vytvoření účtu konektoru ad DS.

### <a name="adsync-service-account"></a>Účet služby ADSync
Synchronizační služba může běžet pod různými účty. Může být spuštěn a spuštěn pod **účtem virtuální chslužeb** (VSA), **účtem spravované služby skupiny** (gMSA/sMSA) nebo běžným uživatelským účtem. Podporované možnosti byly změněny s dubnovou verzí connect 2017, když děláte novou instalaci. Pokud upgradujete z dřívější verze Služby Azure AD Connect, tyto další možnosti nejsou k dispozici.

| Typ účtu | Možnost instalace | Popis |
| --- | --- | --- |
| [Účet virtuální ch služeb](#virtual-service-account) | Express a custom, 2017 duben a novější | Toto je možnost používaná pro všechny expresní instalace s výjimkou instalací v řadiči domény. Pro vlastní, je výchozí možnost, pokud je použita jiná možnost. |
| [Účet spravované služby skupiny](#group-managed-service-account) | Vlastní, 2017 duben a novější | Pokud používáte vzdálený server SQL, doporučujeme použít účet spravované služby skupiny. |
| [Uživatelský účet](#user-account) | Express a custom, 2017 duben a novější | Uživatelský účet s předponou AAD_ je vytvořen pouze během instalace při instalaci v systému Windows Server 2008 a při instalaci do řadiče domény. |
| [Uživatelský účet](#user-account) | Express a custom, 2017 březen a dříve | Místní účet předponou s AAD_ je vytvořen během instalace. Při použití vlastní instalace lze zadat jiný účet. |

Pokud používáte připojit s sestavením od března 2017 nebo dříve, neměli byste resetovat heslo na účtu služby, protože systém Windows z bezpečnostních důvodů zničí šifrovací klíče. Účet nelze změnit na jiný účet bez přeinstalace služby Azure AD Connect. Pokud upgradujete na sestavení od dubna 2017 nebo novějšího, je podporováno změnit heslo na účtu služby, ale nemůžete změnit použitý účet.

> [!Important]
> Účet služby lze nastavit pouze při první instalaci. Po dokončení instalace není podporována změna účtu služby.

Toto je tabulka výchozích, doporučených a podporovaných možností pro účet služby synchronizace.

Legenda:

- **Tučně** označuje výchozí možnost a ve většině případů doporučenou možnost.
- *Kurzíva* označuje doporučenou možnost, pokud není výchozí možností.
- 2008 – Výchozí možnost při instalaci v systému Windows Server 2008
- Netučné – podporovaná volba
- Místní účet - Místní uživatelský účet na serveru
- Účet domény – uživatelský účet domény
- sMSA - [samostatný účet spravované služby](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [účet spravované služby skupiny](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Vlastní | Vzdálený SQL</br>Vlastní |
| --- | --- | --- | --- |
| **samostatný počítač/počítač pro pracovní skupiny** | Nepodporuje se | **Vsa**</br>Místní účet (2008)</br>Místní účet |  Nepodporuje se |
| **počítač spojený s doménou** | **Vsa**</br>Místní účet (2008) | **Vsa**</br>Místní účet (2008)</br>Místní účet</br>Účet domény</br>sMSA,gMSA | **gMSA**</br>Účet domény |
| **Řadič domény** | **Účet domény** | *gMSA*</br>**Účet domény**</br>sMSA| *gMSA*</br>**Účet domény**|

#### <a name="virtual-service-account"></a>Účet virtuálních služeb
Účet virtuální služby je zvláštní typ účtu, který nemá heslo a je spravován systémem Windows.

![Vsa](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

VSA je určen pro použití se scénáři, kde synchronizační modul a SQL jsou na stejném serveru. Pokud používáte vzdálené SQL, doporučujeme použít účet spravované služby skupiny místo.

Tato funkce vyžaduje systém Windows Server 2008 R2 nebo novější. Pokud nainstalujete Službu Azure AD Connect v systému Windows Server 2008, instalace se místo toho vrátí k použití [uživatelského účtu.](#user-account)

#### <a name="group-managed-service-account"></a>Účet spravovaných služeb skupiny
Pokud používáte vzdálený server SQL, doporučujeme použít **účet služby spravované skupinou**. Další informace o přípravě účtu služby Active Directory pro skupinovou spravovanou službu naleznete v [tématu Přehled účtů spravované služby skupiny](https://technet.microsoft.com/library/hh831782.aspx).

Chcete-li použít tuto možnost, vyberte na stránce [Instalovat požadované součásti](how-to-connect-install-custom.md#install-required-components) **použít existující účet služby**a vyberte **účet spravované služby**.  
![Vsa](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Je také podporováno použití [samostatného účtu spravované služby](https://technet.microsoft.com/library/dd548356.aspx). Ty však lze použít pouze v místním počítači a není k dispozici žádná výhoda pro jejich použití přes výchozí účet virtuální služby.

Tato funkce vyžaduje Systém Windows Server 2012 nebo novější. Pokud potřebujete použít starší operační systém a vzdálený SQL, musíte použít [uživatelský účet](#user-account).

#### <a name="user-account"></a>Uživatelský účet
Místní účet služby je vytvořen průvodcem instalací (pokud nezadáte účet, který má být používán ve vlastních nastaveních). Účet je předponou **AAD_** a používá se pro spuštění skutečné synchronizační služby. Pokud nainstalujete Azure AD Connect na řadič domény, účet se vytvoří v doméně. Účet **služby AAD_** musí být umístěn v doméně, pokud:
   - používáte vzdálený server se systémem SQL server
   - používáte proxy server, který vyžaduje ověření

![Účet služby Synchronizace](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

Účet je vytvořen s dlouhým složitým heslem, jehož platnost nevyprší.

Tento účet se používá k bezpečnému ukládání hesel pro ostatní účty. Tato další hesla účtů jsou uložena šifrovaná v databázi. Soukromé klíče pro šifrovací klíče jsou chráněny šifrováním tajného klíče kryptografických služeb pomocí rozhraní Windows Data Protection API (DPAPI).

Pokud používáte úplný SQL Server, pak účet služby je DBO vytvořené databáze pro synchronizační modul. Služba nebude fungovat tak, jak bylo zamýšleno s jinými oprávněními. Je také vytvořeno přihlášení SQL.

Účtu jsou také udělena oprávnění k souborům, klíčům registru a dalším objektům souvisejícím s synchronizačním strojem.

### <a name="azure-ad-connector-account"></a>Účet konektoru služby Azure AD
Účet ve službě Azure AD se vytvoří pro použití synchronizační služby. Tento účet lze identifikovat podle zobrazované ho slku.

![Účet ad](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Název serveru, na který se účet používá, lze identifikovat v druhé části uživatelského jména. Na obrázku je název serveru DC1. Pokud máte pracovní servery, každý server má svůj vlastní účet.

Účet je vytvořen s dlouhým složitým heslem, jehož platnost nevyprší. Je mu udělena zvláštní role **Účty synchronizace adresářů,** která má pouze oprávnění k provádění úloh synchronizace adresářů. Tuto speciální předdefinovanou roli nelze udělit mimo průvodce Azure AD Connect. Portál Azure zobrazuje tento účet s rolí **Uživatel**.

Ve službě Azure AD je limit 20 účtů synchronizačních služeb. Pokud chcete získat seznam existujících účtů služeb Azure AD ve vašem Azure AD, spusťte následující rutinu prostředí Azure AD PowerShell:`Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Pokud chcete odebrat nepoužívané účty služeb Azure AD, spusťte následující rutinu prostředí Azure AD PowerShell:`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Než budete moct použít výše uvedené příkazy Prostředí PowerShell, budete muset nainstalovat [modul Azure Active Directory PowerShell pro graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0#installing-the-azure-ad-module) a připojit se ke své instanci Azure AD pomocí [connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

Další informace o tom, jak spravovat nebo resetovat heslo pro účet konektoru Azure AD, najdete [v tématu Správa účtu Azure AD Connect](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>Související dokumentace
Pokud jste si nepřečetli dokumentaci [k integraci místních identit pomocí služby Azure Active Directory](whatis-hybrid-identity.md), následující tabulka obsahuje odkazy na související témata.

|Téma |Odkaz|  
| --- | --- |
|Stažení služby Azure AD Connect | [Stažení služby Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalace s expresním nastavením | [Expresní instalace Azure AD Connect](how-to-connect-install-express.md)|
|Instalace s vlastním nastavením | [Vlastní instalace služby Azure AD Connect](./how-to-connect-install-custom.md)|
|Upgrade z nástroje DirSync | [Upgrade ze synchronizačního nástroje služby Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Po instalaci | [Ověření instalace a přiřazení licencí](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
