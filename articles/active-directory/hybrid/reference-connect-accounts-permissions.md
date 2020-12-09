---
title: 'Azure AD Connect: účty a oprávnění | Microsoft Docs'
description: Toto téma popisuje používané a vytvořené účty a požadovaná oprávnění.
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
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c075e19422341ad7ccfd3ad951517876ab26a495
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858412"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Účty a oprávnění

## <a name="accounts-used-for-azure-ad-connect"></a>Účty používané pro Azure AD Connect

![Přehled účtů](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect používá pro Azure Active Directory k synchronizaci informací z místního systému nebo služby Windows Server Active Directory 3 účty.  Tyto účty jsou:

- **Účet konektoru služba AD DS**: používá se ke čtení a zápisu informací do služby Windows Server Active Directory.

- **Účet služby AdSync**: používá se ke spuštění synchronizační služby a přístupu ke službě SQL Database.

- **Účet konektoru Azure AD**: používá se k zápisu informací do Azure AD.

Kromě těchto tří účtů, které se používají ke spouštění Azure AD Connect, budete k instalaci Azure AD Connect potřebovat taky tyto další účty.  Jsou to:

- **Účet místního správce**: správce, který instaluje Azure AD Connect a který má oprávnění místního správce k tomuto počítači.

- **Služba AD DS účet správce rozlehlé sítě**: Volitelně můžete použít k vytvoření účtu konektoru služba AD DS výše.

- **Účet globálního správce Azure AD**: používá se k vytvoření účtu konektoru Azure AD a konfiguraci služby Azure AD.

- **Účet SQL SA (volitelné)**: používá se k vytvoření databáze AdSync při použití plné verze SQL Server.  Tento SQL Server může být pro Azure AD Connect instalaci místní nebo vzdálený.  Tento účet může být stejný jako účet správce podnikové sítě.  Zřizování databáze teď může provést vzdálená správa SQL a pak ji nainstalovat správce Azure AD Connect s právy k vlastnictví databáze.  Další informace najdete v tématu [instalace Azure AD Connect s použitím oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md) .


>[!IMPORTANT]
> Od buildu 1.4. # # #. # už se nepodporuje použití účtu správce organizace ani účtu správce domény jako účtu služba AD DSového konektoru.  Pokud se pokusíte zadat účet, který je správcem podnikové sítě nebo správcem domény při zadání **použít existující účet**, zobrazí se chyba.

> [!NOTE]
> Správa účtů pro správu používaných v Azure AD Connect se podporuje z doménové struktury pro správu zvýšeným zabezpečením (taky se jedná o "červenou doménovou strukturu").
> Vyhrazené doménové struktury pro správu umožňují organizacím hostovat účty, pracovní stanice a skupiny pro správu v prostředí, které mají silnější zabezpečovací mechanizmy než produkční prostředí.
> Další informace o vyhrazených doménových strukturách pro správu najdete v tématu [zvýšeným zabezpečením administrativního přístupu k návrhu doménové struktury](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach).

> [!NOTE]
> Role globálního správce se po počátečním nastavení nevyžaduje a jediným požadovaným účtem bude účet role **synchronizace adresářů** . To nemusí nutně znamenat, že budete chtít jenom odebrat účet s rolí globálního správce. Je lepší roli změnit na méně efektivní roli, protože zcela odebrání tohoto účtu může způsobit problémy, pokud budete někdy muset průvodce znovu znovu spustit. Omezením oprávnění role můžete kdykoli znovu zvýšit úroveň oprávnění, pokud budete muset znovu využít průvodce Azure AD Connect. 

## <a name="installing-azure-ad-connect"></a>Instalace Azure AD Connect
Průvodce instalací Azure AD Connect nabízí dvě různé cesty:

* V expresním nastavení průvodce vyžaduje více oprávnění.  To je tak, že může nastavit konfiguraci snadno, aniž byste museli vytvářet uživatele nebo konfigurovat oprávnění.
* V části vlastní nastavení vám průvodce nabídne více možností a možností. Existují však situace, kdy potřebujete mít jistotu, že máte správná oprávnění.



## <a name="express-settings-installation"></a>Instalace expresního nastavení
V expresním nastavení Průvodce instalací požádá o následující:

  - služba AD DS přihlašovací údaje správce podniku
  - Přihlašovací údaje globálního správce Azure AD

### <a name="ad-ds-enterprise-admin-credentials"></a>služba AD DS přihlašovací údaje podnikového správce
Účet správce služba AD DS Enterprise se používá ke konfiguraci vaší místní služby Active Directory. Tyto přihlašovací údaje se používají jenom během instalace a po dokončení instalace se nepoužívají. Podnikový správce, ne správce domény, by měl mít jistotu, že oprávnění ve službě Active Directory je možné nastavit ve všech doménách.

Pokud provádíte upgrade z DirSync, použijí se přihlašovací údaje služba AD DS Enterprise Admins k resetování hesla pro účet, který používá DirSync. Budete také potřebovat přihlašovací údaje globálního správce služby Azure AD.

### <a name="azure-ad-global-admin-credentials"></a>Přihlašovací údaje globálního správce Azure AD
Tyto přihlašovací údaje se používají jenom během instalace a po dokončení instalace se nepoužívají. Slouží k vytvoření účtu konektoru Azure AD, který se používá pro synchronizaci změn ve službě Azure AD. Účet taky umožňuje synchronizaci jako funkce ve službě Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Účet konektoru služba AD DS vyžaduje oprávnění pro expresní nastavení.
Účet konektoru služba AD DS se vytvoří pro čtení a zápis do Windows Server AD a má následující oprávnění, když se vytvoří pomocí expresního nastavení:

| Oprávnění | Použití |
| --- | --- |
| <li>Replikovat změny adresáře</li><li>Replikovat všechny změny adresáře |Synchronizace hodnot hash hesel |
| Číst/zapisovat všechny vlastnosti uživatele |Import a Exchange Hybrid |
| Číst a zapisovat všechny vlastnosti iNetOrgPerson |Import a Exchange Hybrid |
| Čtení a zápis – skupina všech vlastností |Import a Exchange Hybrid |
| Kontakt pro čtení a zápis všech vlastností |Import a Exchange Hybrid |
| Resetování hesla |Příprava pro povolení zpětného zápisu hesla |

### <a name="express-installation-wizard-summary"></a>Souhrn Průvodce instalací Express

![Expresní instalace](./media/reference-connect-accounts-permissions/express.png)

Následuje souhrn stránek průvodce expresní instalací, shromažďovaných přihlašovacích údajů a jejich použití.

| Stránka průvodce | Shromážděné přihlašovací údaje | Požadovaná oprávnění | Používá se pro |
| --- | --- | --- | --- |
| – |Uživatel, který spouští Průvodce instalací |Správce místního serveru |<li>Vytvoří účet služby ADSync, který se používá jako ke spuštění synchronizační služby. |
| Připojení k Azure AD |Přihlašovací údaje k adresáři Azure AD |Role globálního správce v Azure AD |<li>Povoluje se synchronizace v adresáři Azure AD.</li>  <li>Vytvoření účtu konektoru služby Azure AD, který se používá pro probíhající operace synchronizace ve službě Azure AD.</li> |
| Připojení ke službě AD DS |Místní přihlašovací údaje služby Active Directory |Člen skupiny Enterprise Admins (EA) ve službě Active Directory |<li>Vytvoří účet konektoru služba AD DS ve službě Active Directory a udělí mu oprávnění. Tento vytvořený účet se používá ke čtení a zápisu informací o adresáři během synchronizace.</li> |


## <a name="custom-installation-settings"></a>Vlastní nastavení instalace

Díky instalaci vlastních nastavení vám průvodce nabídne více možností a možností. 

### <a name="custom-installation-wizard-summary"></a>Souhrn Průvodce vlastní instalací

Následuje souhrn stránek průvodce vlastní instalace, shromážděných pověření a jejich použití.

![Snímek obrazovky zobrazující stránky Průvodce vlastní instalací](./media/reference-connect-accounts-permissions/customize.png)

| Stránka průvodce | Shromážděné přihlašovací údaje | Požadovaná oprávnění | Používá se pro |
| --- | --- | --- | --- |
| – |Uživatel, který spouští Průvodce instalací |<li>Správce místního serveru</li><li>Pokud používáte úplný SQL Server, musí být uživatel správcem systému (SA) v SQL.</li> |Ve výchozím nastavení vytvoří místní účet, který se používá jako účet služby synchronizačního modulu. Účet je vytvořen pouze v případě, že správce nezadá konkrétní účet. |
| Instalace synchronizačních služeb, možnost účet služby |Přihlašovací údaje účtu služby AD nebo místního uživatele |Uživatel, oprávnění jsou udělována Průvodcem instalací |Pokud správce zadá účet, použije se tento účet jako účet služby pro synchronizační službu. |
| Připojení k Azure AD |Přihlašovací údaje k adresáři Azure AD |Role globálního správce v Azure AD |<li>Povoluje se synchronizace v adresáři Azure AD.</li>  <li>Vytvoření účtu konektoru služby Azure AD, který se používá pro probíhající operace synchronizace ve službě Azure AD.</li> |
| Připojení adresářů |Místní přihlašovací údaje služby Active Directory pro každou doménovou strukturu, která je připojená k Azure AD |Oprávnění závisí na tom, které funkce povolíte a najdete v části Vytvoření účtu služba AD DS Connectoru. |Tento účet slouží ke čtení a zápisu informací o adresáři během synchronizace. |
| Servery služby AD FS |Pro každý server v seznamu shromažďuje Průvodce přihlašovací údaje, když přihlašovací údaje uživatele, který spouští Průvodce, nejsou pro připojení k dispozici. |Správce domény |Instalace a konfigurace role serveru AD FS. |
| Proxy servery webových aplikací |Pro každý server v seznamu shromažďuje Průvodce přihlašovací údaje, když přihlašovací údaje uživatele, který spouští Průvodce, nejsou pro připojení k dispozici. |Místní správce na cílovém počítači |Instalace a konfigurace role serveru WAP. |
| Přihlašovací údaje vztahu důvěryhodnosti proxy serveru |Pověření vztahu důvěryhodnosti federační služby (přihlašovací údaje, které proxy server používá k registraci certifikátu důvěryhodnosti z FS) |Doménový účet, který je místním správcem serveru AD FS |Počáteční registrace certifikátu důvěryhodnosti FS-WAP. |
| Stránka AD FS účet služby, možnost použít účet uživatele domény |Přihlašovací údaje k účtu uživatele služby AD |Uživatel domény |Uživatelský účet Azure AD, jehož přihlašovací údaje jsou k dispozici, se používá jako přihlašovací účet služby AD FS. |

### <a name="create-the-ad-ds-connector-account"></a>Vytvoření účtu konektoru služba AD DS

>[!IMPORTANT]
>Nový modul PowerShellu s názvem ADSyncConfig. psm1 byl představený pomocí buildu **1.1.880.0** (vydaný v srpnu 2018), který obsahuje kolekci rutin, které vám pomůžou nakonfigurovat správná oprávnění služby Active Directory pro účet konektoru Azure služba AD DS.
>
>Další informace najdete v tématu [Azure AD Connect: Konfigurace oprávnění účtu konektoru služba AD DS](how-to-connect-configure-ad-ds-connector-account.md)

Účet, který zadáte na stránce **připojit adresáře** , musí být přítomen ve službě Active Directory před instalací.  Azure AD Connect verze 1.1.524.0 a novější má možnost nechat průvodce Azure AD Connectm vytvořit **účet služba AD DSho konektoru** , který se používá pro připojení ke službě Active Directory.  

Musí mít taky udělená požadovaná oprávnění. Průvodce instalací neověřuje oprávnění a všechny problémy jsou zjištěny pouze během synchronizace.

Požadovaná oprávnění závisí na volitelných funkcích, které povolíte. Pokud máte více domén, musí být oprávnění udělena pro všechny domény v doménové struktuře. Pokud nepovolíte žádnou z těchto funkcí, budou stačit výchozí oprávnění **uživatele domény** .

| Příznak | Oprávnění |
| --- | --- |
| funkce MS-DS-ConsistencyGuid |Oprávnění k zápisu do atributu ms-DS-ConsistencyGuid dokumentovaného v [konceptech návrhu – použití MS-DS-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synchronizace hodnot hash hesel |<li>Replikovat změny adresáře</li>  <li>Replikovat všechny změny adresáře |
| Hybridní nasazení Exchange |Oprávnění k zápisu pro atributy dokumentované v rámci [hybridního zpětného zápisu systému Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) pro uživatele, skupiny a kontakty. |
| Veřejná složka pošty Exchange |Oprávnění ke čtení pro atributy dokumentované ve [veřejné složce Exchange mail](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) pro veřejné složky. | 
| Zpětný zápis hesla |Oprávnění k zápisu do atributů popsaných v článku [Začínáme se správou hesel](../authentication/tutorial-enable-sspr-writeback.md) pro uživatele. |
| Zpětný zápis zařízení |Oprávnění udělená pomocí skriptu prostředí PowerShell, jak je popsáno v tématu [zpětný zápis zařízení](how-to-connect-device-writeback.md). |
| Zpětný zápis skupin |Umožňuje zpětný zápis **skupin Microsoft 365** do doménové struktury s nainstalovaným systémem Exchange.|

## <a name="upgrade"></a>Upgrade
Při upgradu z jedné verze Azure AD Connect na novou verzi budete potřebovat následující oprávnění:

>[!IMPORTANT]
>Počínaje sestavou Build 1.1.484 Azure AD Connect představili regresní chybu, která vyžaduje oprávnění sysadmin pro upgrade databáze SQL.  Tato chyba je opravena v 1.1.647 sestavení.  Pokud provádíte upgrade na toto sestavení, budete potřebovat oprávnění správce systému.  Oprávnění dbo nejsou dostatečná.  Pokud se pokusíte upgradovat Azure AD Connect bez oprávnění sysadmin, upgrade se nezdaří a Azure AD Connect nebude nadále fungovat správně.  Společnost Microsoft je o tom vědomá a pracuje na tom, aby ji opravila.


| Objekt zabezpečení | Požadovaná oprávnění | Použití |
| --- | --- | --- |
| Uživatel, který spouští Průvodce instalací |Správce místního serveru |Aktualizace binárních souborů. |
| Uživatel, který spouští Průvodce instalací |Člen ADSyncAdmins |Proveďte změny pravidel synchronizace a jiné konfigurace. |
| Uživatel, který spouští Průvodce instalací |Pokud používáte úplný SQL Server: DBO (nebo podobný) databáze synchronizačního modulu |Proveďte změny na úrovni databáze, jako je například aktualizace tabulek s novými sloupci. |

## <a name="more-about-the-created-accounts"></a>Další informace o vytvořených účtech
### <a name="ad-ds-connector-account"></a>Účet konektoru služby AD DS
Pokud použijete expresní nastavení, účet se vytvoří ve službě Active Directory, která se používá k synchronizaci. Vytvořený účet je umístěný v kořenové doméně doménové struktury v kontejneru Users a má název s předponou **MSOL_**. Účet se vytvoří s dlouhým složitým heslem, jehož platnost nevyprší. Pokud máte v doméně zásady pro hesla, zajistěte, aby pro tento účet byla povolená dlouhá a složitá hesla.

![Účet AD](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Pokud používáte vlastní nastavení, zodpovídáte za vytvoření účtu před zahájením instalace.  Viz Vytvoření účtu konektoru služba AD DS.

### <a name="adsync-service-account"></a>Účet služby ADSync
Synchronizační služba může běžet pod různými účty. Může běžet pod **účtem virtuální služby** (VSA), **skupinový účet spravované služby** (gMSA/SMSA) nebo běžný uživatelský účet. Podporované možnosti se změnily s 2017. dubna Connect při nové instalaci. Pokud upgradujete ze starší verze Azure AD Connect, tyto další možnosti nejsou k dispozici.

| Typ účtu | Možnost instalace | Popis |
| --- | --- | --- |
| [Účet virtuální služby](#virtual-service-account) | Express a Custom, 2017. dubna a novější | Tato možnost se používá pro všechny Expresní instalace, s výjimkou instalací na řadič domény. Pro vlastní je výchozí možnost, pokud nepoužijete jinou možnost. |
| [Účet spravované služby skupiny](#group-managed-service-account) | Vlastní, 2017. dubna a novější | Pokud používáte vzdálený SQL Server, doporučujeme použít skupinový účet spravované služby. |
| [Uživatelský účet](#user-account) | Express a Custom, 2017. dubna a novější | Uživatelský účet s předponou AAD_ se vytvoří jenom během instalace, pokud je nainstalovaný na Windows serveru 2008 a je nainstalovaný na řadiči domény. |
| [Uživatelský účet](#user-account) | Express a Custom, 2017 březen a starší | Během instalace se vytvoří místní účet s předponou AAD_. Při použití vlastní instalace je možné zadat jiný účet. |

Použijete-li příkaz připojit se sestavou z 2017. března nebo staršího, neměli byste heslo resetovat na účet služby, protože systém Windows odstraní šifrovací klíče z bezpečnostních důvodů. Účet nemůžete změnit na žádný jiný účet, aniž byste museli přeinstalovat Azure AD Connect. Pokud upgradujete na sestavení z verze 2017. dubna nebo novější, je podporována Změna hesla na účtu služby, ale nemůžete změnit použitý účet.

> [!Important]
> Účet služby můžete nastavit jenom při první instalaci. Po dokončení instalace není podporována Změna účtu služby.

Toto je tabulka výchozích, doporučených a podporovaných možností pro účet synchronizační služby.

Legenda:

- **Tučné** označuje výchozí možnost a ve většině případů doporučenou možnost.
- *Kurzíva* označuje doporučenou možnost, pokud není výchozí možností.
- 2008 – výchozí možnost při instalaci v systému Windows Server 2008
- Možnost, která není dostupná tučně
- Místní účet – místní uživatelský účet na serveru
- Doménový účet – účet uživatele domény
- sMSA – [samostatný účet spravované služby](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA – [skupinový účet spravované služby](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Vlastní | Vzdálený SQL</br>Vlastní |
| --- | --- | --- | --- |
| **počítač připojený k doméně** | **ATRIBUT**</br>Místní účet (2008) | **ATRIBUT**</br>Místní účet (2008)</br>Místní účet</br>Účet domény</br>sMSA, gMSA | **gMSA**</br>Účet domény |
| **Řadič domény** | **Účet domény** | *gMSA*</br>**Účet domény**</br>sMSA| *gMSA*</br>**Účet domény**|

#### <a name="virtual-service-account"></a>Účet virtuální služby
Účet virtuální služby je speciální typ účtu, který nemá heslo a spravuje ho Windows.

![Snímek obrazovky, který zobrazuje účet virtuální služby (VSA).](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

Atributy VSA mají být použity ve scénářích, kde je synchronizační modul a SQL na stejném serveru. Pokud používáte vzdálený SQL Server, doporučujeme místo toho použít skupinový účet spravované služby.

Tato funkce vyžaduje systém Windows Server 2008 R2 nebo novější. Pokud nainstalujete Azure AD Connect na Windows Server 2008, instalace se místo toho vrátí k použití [uživatelského účtu](#user-account) .

#### <a name="group-managed-service-account"></a>Skupinový účet spravované služby
Pokud používáte vzdálený SQL Server, doporučujeme použít **skupinový účet spravované služby**. Další informace o tom, jak připravit službu Active Directory pro skupinový účet spravované služby, najdete v tématu [Přehled skupinových účtů spravované služby](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)).

Chcete-li použít tuto možnost, vyberte na stránce [instalovat požadované součásti](how-to-connect-install-custom.md#install-required-components) možnost **použít existující účet služby** a vyberte možnost **účet spravované služby**.  
![ATRIBUT](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Také se podporuje použití [samostatného účtu spravované služby](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10)). Ty se ale dají použít jenom na místním počítači a pro jejich použití přes výchozí účet virtuální služby se nevyužívají výhody.

Tato funkce vyžaduje systém Windows Server 2012 nebo novější. Pokud potřebujete použít starší operační systém a použít vzdálený SQL, musíte použít [uživatelský účet](#user-account).

#### <a name="user-account"></a>Uživatelský účet
Účet místní služby se vytvoří pomocí Průvodce instalací (Pokud nezadáte účet, který se má použít ve vlastním nastavení). Účet je předem opraven **AAD_** a slouží ke spuštění skutečné synchronizační služby jako. Pokud nainstalujete Azure AD Connect na řadič domény, účet se vytvoří v doméně. Účet služby **AAD_** musí být v doméně umístěn v těchto případech:
   - používáte vzdálený server, na kterém běží SQL Server.
   - použijete proxy server, který vyžaduje ověření.

![Synchronizovat účet služby](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

Účet se vytvoří s dlouhým složitým heslem, jehož platnost nevyprší.

Tento účet se používá k bezpečnému ukládání hesel pro ostatní účty. Tyto další účty hesla se ukládají v databázi jako šifrované. Privátní klíče pro šifrovací klíče jsou chráněné pomocí šifrování tajného klíče kryptografických služeb pomocí Windows Data Protection API (DPAPI).

Pokud používáte úplný SQL Server, účet služby je DBO databáze vytvořenou pro modul synchronizace. Služba nebude fungovat tak, jak má žádná jiná oprávnění. Vytvoří se také přihlášení SQL.

Účtu je také uděleno oprávnění k souborům, klíčům registru a dalším objektům, které se vztahují k synchronizačnímu modulu.

### <a name="azure-ad-connector-account"></a>Účet konektoru služby Azure AD
Pro použití synchronizační služby se vytvoří účet ve službě Azure AD. Tento účet může identifikovat jeho zobrazované jméno.

![Snímek obrazovky, který zobrazuje účet služby Azure AD.](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Název serveru, na kterém se účet používá, se dá identifikovat v druhé části uživatelského jména. Na obrázku je název serveru DC1. Pokud máte pracovní servery, každý server má svůj vlastní účet.

Účet se vytvoří s dlouhým složitým heslem, jehož platnost nevyprší. Přidělí se speciální účty pro **synchronizaci adresářů** rolí, které mají pouze oprávnění k provádění úloh synchronizace adresáře. Tato speciální předdefinovaná role nemůže být udělena mimo Průvodce Azure AD Connect. Azure Portal zobrazuje tento účet s **uživatelem** role.

Ve službě Azure AD je povolený limit 20 účtů synchronizačních služeb. Pokud chcete získat seznam existujících účtů služby Azure AD ve službě Azure AD, spusťte následující rutinu Azure AD PowerShellu: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Pokud chcete odebrat nepoužívané účty služby Azure AD, spusťte následující rutinu Azure AD PowerShellu: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Než budete moct použít výše uvedené příkazy PowerShellu, musíte si nainstalovat [modul Azure Active Directory PowerShell for Graph](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module) a připojit se k vaší instanci Azure AD pomocí příkazu [Connect-AzureAD](/powershell/module/azuread/connect-azuread) .

Další informace o tom, jak spravovat nebo resetovat heslo pro účet konektoru Azure AD, najdete v tématu [Správa účtu Azure AD Connect](how-to-connect-azureadaccount.md) .

## <a name="related-documentation"></a>Související dokumentace
Pokud jste nečetli dokumentaci k [integraci místních identit s Azure Active Directory](whatis-hybrid-identity.md), v následující tabulce najdete odkazy na související témata.

|Téma |Odkaz|  
| --- | --- |
|Stažení služby Azure AD Connect | [Stažení služby Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalace s expresním nastavením | [Expresní instalace Azure AD Connect](how-to-connect-install-express.md)|
|Instalace s vlastním nastavením | [Vlastní instalace služby Azure AD Connect](./how-to-connect-install-custom.md)|
|Upgrade z nástroje DirSync | [Upgrade ze synchronizačního nástroje služby Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Po instalaci | [Ověření instalace a přiřazení licencí](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
