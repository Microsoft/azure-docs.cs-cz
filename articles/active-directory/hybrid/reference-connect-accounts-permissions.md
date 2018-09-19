---
title: 'Azure AD Connect: Účty a oprávnění | Dokumentace Microsoftu'
description: Toto téma popisuje účty používat a vytvořit a oprávněních.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 96d8977a63d26576d4d783dd0661409fdcee90f8
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46314031"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Účty a oprávnění

## <a name="accounts-used-for-azure-ad-connect"></a>Účty používané pro služby Azure AD Connect

![](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect používá 3 účty, aby bylo možné synchronizovat informace z místní nebo Windows Server Active Directory do Azure Active Directory.  Tyto účty jsou:

- **Účet AD DS konektoru**: umožňuje čtení nebo zápisu informací do systému Windows Server Active Directory

- **Účet služby ADSync**: používá ke spuštění synchronizační služby a přístup k databázi SQL

- **Účet Azure AD Connector**: použít při zápisu informací do služby Azure AD

Kromě tyto tři účty používají ke spouštění služby Azure AD Connect budete také potřebovat následující další účty k instalaci Azure AD Connect.  Jsou to:

- **Účet správce podnikové sítě AD DS**: použitý k instalaci Azure AD Connect
- **Účet Azure AD globálního správce**: použít k vytvoření účtu Azure AD Connector a nakonfigurovat služby Azure AD.

- **Účet SQL SA (volitelné)**: použili k vytvoření databáze ADSync při použití plnou verzi systému SQL Server.  Tento Server SQL může být místní nebo vzdálené instalace služby Azure AD Connect.  Tento účet může být stejný účet jako správce podnikové sítě.  Zřizování může databáze nyní možné provádět vzdáleně správce SQL a následně je nainstalován Správce služby Azure AD Connect s oprávněními vlastníka databáze.  Informace o najdete v článku [instalace služby Azure AD Connect pomocí oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md)

## <a name="installing-azure-ad-connect"></a>Instaluje se služba Azure AD Connect.
Průvodce instalací Azure AD Connect poskytuje dvě různé cesty:

* V expresním nastavení Průvodce vyžaduje další oprávnění.  Je to tak, aby ho konfiguraci nastavení, aniž by bylo potřeba vytvořit uživatele nebo nakonfigurovat oprávnění.
* V části vlastní nastavení Průvodce nabízí další možnosti a možnosti. Existují však některé situace, ve kterých je potřeba zajistit, že máte správná oprávnění, sami.



## <a name="express-settings-installation"></a>Expresní nastavení instalace
V nastavení Express Průvodce instalací vyzve k zadání následujících akcí:

  - Přihlašovací údaje podnikového správce AD DS
  - Přihlašovací údaje Azure AD globálního správce

### <a name="ad-ds-enterprise-admin-credentials"></a>Přihlašovací údaje služby AD DS podnikový správce
Účet správce podnikové služby AD DS se používá ke konfiguraci vaší místní Active Directory. Tyto přihlašovací údaje se používají jenom při instalaci a nepoužívají se po dokončení instalace. Podnikový správce není správce domény by měl nezapomeňte že ve všech doménách lze nastavit oprávnění ve službě Active Directory.

Pokud provádíte upgrade z nástroje DirSync, AD DS Enterprise Admins přihlašovací údaje se používají k resetování hesla pro účet, která nástroj DirSync používá. Budete také potřebovat přihlašovací údaje globálního správce Azure AD.

### <a name="azure-ad-global-admin-credentials"></a>Přihlašovací údaje Azure AD globálního správce
Tyto přihlašovací údaje se používají jenom při instalaci a nepoužívají se po dokončení instalace. Používá se k vytvoření [účtu Azure AD Connector](#azure-ad-service-account) používají k synchronizaci změn do služby Azure AD. Účet také umožňuje synchronizaci jako funkci v Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Účet AD DS konektoru požadovaná oprávnění pro expresní nastavení
[Účet AD DS konektoru](#active-directory-account) se vytvoří pro čtení a zápis do systému Windows Server AD a tato oprávnění při vytvoření pomocí expresního nastavení:

| Oprávnění | Používá pro |
| --- | --- |
| <li>Replikace změn adresáře</li><li>Replikace adresáře se změní všechny |Synchronizace hodnot hash hesel |
| Čtení a zápis všech vlastností uživatele |Import a serveru Exchange hybridní |
| Čtení a zápis všech iNetOrgPerson vlastnosti |Import a serveru Exchange hybridní |
| Čtení a zápis všech vlastností skupiny |Import a serveru Exchange hybridní |
| Obraťte se na čtení a zápis všech vlastností |Import a serveru Exchange hybridní |
| Resetování hesla |Příprava k povolení zpětného zápisu hesla |

### <a name="express-installation-wizard-summary"></a>Souhrn Průvodce Expresní instalace

![Expresní instalace](./media/reference-connect-accounts-permissions/express.png)

Následuje souhrn express installlation stránkách průvodce, přihlašovací údaje shromáždí, a jejich použití.

| Stránka Průvodce | Přihlašovací údaje shromážděné | Oprávnění vyžadovaná | Používá pro |
| --- | --- | --- | --- |
| neuvedeno |Uživatel, který spouští Průvodce instalací |Správce místního serveru |<li>Vytvoří [účtu službu ADSync](#azure-ad-connect-sync-service-account) účet, který se používá ke spuštění synchronizační služby. |
| Připojení k Azure AD |Přihlašovací údaje Azure AD directory |Roli globálního správce ve službě Azure AD |<li>Povoluje se synchronizace v adresáři Azure AD.</li>  <li>Vytváření [účtu Azure AD Connector](#azure-ad-service-account) , který se používá pro operace probíhající synchronizace ve službě Azure AD.</li> |
| Připojení ke službě AD DS |Místní přihlašovací údaje služby Active Directory |Člen skupiny Enterprise Admins (EA) ve službě Active Directory |<li>Vytvoří [účet AD DS konektoru](#active-directory-account) v Active Directory a udělí oprávnění k němu. Vytvoření účtu slouží ke čtení a zápisu informací adresáře během synchronizace.</li> |


## <a name="custom-installation-settings"></a>Nastavení vlastní instalace

Vlastní nastavení instalace Průvodce nabízí další možnosti a možnosti. 

### <a name="custom-installation-wizard-summary"></a>Souhrn Průvodce vlastní instalace

Následuje souhrn vlastní installlation stránkách průvodce, přihlašovací údaje shromáždí, a jejich použití.

![Expresní instalace](./media/reference-connect-accounts-permissions/customize.png)

| Stránka Průvodce | Přihlašovací údaje shromážděné | Oprávnění vyžadovaná | Používá pro |
| --- | --- | --- | --- |
| neuvedeno |Uživatel, který spouští Průvodce instalací |<li>Správce místního serveru</li><li>Pokud používáte plnou instalaci systému SQL Server, uživatel musí být správce systému (SA) v SQL</li> |Ve výchozím nastavení, vytvoří místní účet, který se používá jako [synchronizovat účet služby engine](#azure-ad-connect-sync-service-account). Účet je vytvořen pouze při správce neurčuje konkrétního účtu. |
| Nainstalovat synchronizační služby, možnost účet služby |AD nebo přihlašovací údaje místního uživatelského účtu |Uživatel, jsou udělena oprávnění pomocí Průvodce instalací |Pokud správce zadá účtu, tento účet se používá jako účet služby pro službu synchronizace. |
| Připojení k Azure AD |Přihlašovací údaje Azure AD directory |Roli globálního správce ve službě Azure AD |<li>Povoluje se synchronizace v adresáři Azure AD.</li>  <li>Vytváření [účtu Azure AD Connector](#azure-ad-service-account) , který se používá pro operace probíhající synchronizace ve službě Azure AD.</li> |
| Připojení adresářů |Místní přihlašovací údaje služby Active Directory pro každou doménovou strukturu, která je připojená ke službě Azure AD |Oprávnění závisí na funkcí, které slouží k povolení a lze nalézt v [vytvořit účet AD DS konektoru](#create-the-ad-dso-connector-account) |Tento účet slouží ke čtení a zápisu informací adresáře během synchronizace. |
| Servery služby AD FS |Pro každý server v seznamu Průvodce shromažďuje přihlašovací údaje, když jsou přihlašovací údaje uživatel spustí průvodce k připojení |Správce domény |Instalace a konfigurace role serveru AD FS. |
| Proxy servery webových aplikací |Pro každý server v seznamu Průvodce shromažďuje přihlašovací údaje, když jsou přihlašovací údaje uživatel spustí průvodce k připojení |Místní správce na cílovém počítači |Instalace a konfigurace role serveru WAP. |
| Přihlašovací údaje pro vztah důvěryhodnosti proxy |Služba FS důvěřovat pověření (přihlašovací údaje proxy serveru používá k registraci pro důvěryhodný certifikát z služby FS |Účet domény, který slouží jako místní správce serveru služby AD FS |Počáteční registraci FS WAP důvěřovat certifikátu. |
| Stránka účtu služby FS služby AD, "Použít možnost účet uživatele domény" |Přihlašovací údaje účtu uživatele AD |Doména uživatel |Uživatelský účet AD jsou k dispozici jehož přihlašovací údaje slouží jako přihlašovací účet služby AD FS. |

### <a name="create-the-ad-ds-connector-account"></a>Vytvoření konektoru služby AD DS účtu
Zadaný účet na **připojení k adresářům** stránky musí být k dispozici ve službě Active Directory před instalací.  Azure AD Connect verze 1.1.524.0 a novější je možnost nechat vytvořit průvodce Azure AD Connect **účet AD DS konektoru** používaná k připojení ke službě Active Directory.  

Také musí mít udělena potřebná oprávnění. Průvodce instalací neověřuje, že oprávnění a všech problémech se nacházejí pouze během synchronizace.

Oprávnění, která budete potřebovat, závisí na volitelné funkce povolíte. Pokud máte více domén, musí být uděleno oprávnění pro všechny domény v doménové struktuře. Pokud nepovolíte některé z těchto funkcí, výchozí **uživatele domény** jsou dostatečná oprávnění.

| Funkce | Oprávnění |
| --- | --- |
| MS-DS-ConsistencyGuid funkce |Oprávnění k zápisu na atribut ms-DS-ConsistencyGuid dokumentovány v článku [koncepty návrhu – použití ms-DS-ConsistencyGuid jako parametru sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synchronizace hodnot hash hesel |<li>Replikace změn adresáře</li>  <li>Replikace adresáře se změní všechny |
| Hybridní nasazení systému Exchange |Oprávnění k zápisu do atributy uvedené v [zpětný zápis hybridní Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) pro uživatele, skupiny nebo kontakty. |
| Veřejné složky pošty Exchange |Oprávnění ke čtení pro atributy uvedené v [veřejné složky pošty Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) u veřejných složek. | 
| Zpětný zápis hesla |Oprávnění k zápisu do atributy uvedené v [Začínáme se správou hesel](../authentication/howto-sspr-writeback.md) pro uživatele. |
| Zpětný zápis zařízení |Oprávnění udělená pomocí skriptu prostředí PowerShell, jak je popsáno v [zpětný zápis zařízení](how-to-connect-device-writeback.md). |
| Zpětný zápis skupin |Přečtěte si, vytvoření, aktualizace a odstranění skupiny objektů, pro synchronizována **skupiny Office 365**.  Další informace najdete v části [zpětný zápis skupin](how-to-connect-preview.md#group-writeback).|

## <a name="upgrade"></a>Upgrade
Když upgradujete z jedné verze nástroje Azure AD Connect na novou verzi, potřebujete následující oprávnění:

>[!IMPORTANT]
>Od verze sestavení 1.1.484, Azure AD Connect zavedené regrese chybu, která vyžaduje oprávnění správce systému pro upgrade databáze SQL.  Tato chyba se vyřeší v sestavení 1.1.647.  Pokud provádíte upgrade na toto sestavení, budete potřebovat oprávnění správce.  Nejsou dostatečná oprávnění dbo.  Pokud se pokusíte upgradovat bez oprávnění správce služby Azure AD Connect, dojde k selhání a Azure AD Connect se už nebude správně fungovat později.  Společnost Microsoft je tomu věnovat pozornost a funguje to pokud chcete opravit.


| Objekt zabezpečení | Oprávnění vyžadovaná | Používá pro |
| --- | --- | --- |
| Uživatel, který spouští Průvodce instalací |Správce místního serveru |Aktualizujte binární soubory. |
| Uživatel, který spouští Průvodce instalací |Člen ADSyncAdmins |Změny synchronizační pravidla a další konfiguraci. |
| Uživatel, který spouští Průvodce instalací |Pokud používáte plnou instalaci systému SQL server: vlastník databáze (nebo podobnou) modul databáze synchronizace |Ujistěte se, změny na úrovni databáze, jako je aktualizace s novými sloupci tabulky. |

## <a name="more-about-the-created-accounts"></a>Další informace o vytvořené účty
### <a name="ad-ds-connector-account"></a>Účet AD DS konektoru
Pokud používáte expresního nastavení, účet se vytvoří ve službě Active Directory, který je používán k synchronizaci. Vytvořený účet se nachází v kořenové doméně doménové struktury v kontejneru Users a má s předponou názvu **MSOL_**. Účet je vytvořen s dlouho složité heslo, nemá prošlou platnost. Pokud máte zásady hesel ve vaší doméně, ujistěte se, že dlouhá a složitá hesla by bylo možné pro tento účet.

![Účet AD](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Pokud použijete vlastní nastavení, pak budete muset pro vytvoření účtu před zahájením instalace.  Zobrazit [vytvořit účet AD DS konektoru](#create-the-ad-dso-connector-account).

### <a name="adsync-service-account"></a>Účet služby ADSync
Synchronizační služba může běžet pod různými účty. Můžou běžet pod **účet virtuální služby** (Atribut) **skupinový účet spravované služby** (gMSA nebo sMSA) nebo běžný uživatelský účet. Podporované možnosti byly změněny 2017 dubna verze služby Connect při instalaci. Pokud upgradujete ze starší verze služby Azure AD Connect, nejsou k dispozici tyto možnosti.

| Typ účtu | Možnost instalace | Popis |
| --- | --- | --- |
| [Účet virtuální služby](#virtual-service-account) | 2017 Express a vlastní, duben a novější | Toto je používán pro všechny Expresní instalace, s výjimkou zařízení na řadiči domény. Pro vlastní je výchozí možnost, není-li použít jinou možnost. |
| [Účet spravované služby skupiny](#group-managed-service-account) | 2017 vlastní, duben a novější | Pokud používáte vzdálený SQL server, pak doporučujeme použít účet skupiny spravované služby. |
| [Uživatelský účet](#user-account) | 2017 Express a vlastní, duben a novější | Uživatelský účet s předponou AAD_ je vytvořen pouze během instalace při instalaci v systému Windows Server 2008 a nainstalovaný na řadiči domény. |
| [Uživatelský účet](#user-account) | 2017 Express a vlastní, dne a starší | Místní účet s předponou AAD_ je vytvořena během instalace. Pokud používáte vlastní instalaci, je možné zadat jiný účet. |

Pokud používáte připojení k sestavení z 2017 dne nebo starší, pak by neměl resetujete heslo k účtu služby od Windows zničí šifrovací klíče z bezpečnostních důvodů. Nelze změnit účet na jiný účet bez nutnosti opětovné instalace služby Azure AD Connect. Pokud upgradujete na sestavení z 2017 dubna nebo později, pak je podporované Chcete-li změnit heslo k účtu služby, ale nelze změnit účet použít.

> [!Important]
> Účet služby lze nastavit pouze při první instalaci. Není možné změnit účet služby, po dokončení instalace.

Toto je tabulka výchozí doporučené a podporované možnosti pro účet synchronizační služby.

Legenda:

- **Tučné** označuje výchozí možnost a ve většině případů doporučujeme.
- *Kurzíva* určuje doporučená možnost, pokud není výchozí možnost.
- 2008 - výchozí možnost, při instalaci v systému Windows Server 2008
- Non tučné – podporovaná možnost.
- Místní účet – místní uživatelský účet na serveru
- Účet domény – účet uživatele domény
- sMSA - [samostatný účet spravované služby](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [skupinový účet spravované služby](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Vlastní | Vzdálený server SQL</br>Vlastní |
| --- | --- | --- | --- |
| **počítač samostatného nebo pracovní skupiny** | Nepodporuje se | **VSA**</br>Místní účet (2008)</br>Místní účet |  Nepodporuje se |
| **počítače připojené k doméně** | **VSA**</br>Místní účet (2008) | **VSA**</br>Místní účet (2008)</br>Místní účet</br>Účet domény</br>sMSA, gMSA | **gMSA**</br>Účet domény |
| **Řadič domény** | **Účet domény** | *gMSA*</br>**Účet domény**</br>sMSA| *gMSA*</br>**Účet domény**|

#### <a name="virtual-service-account"></a>Účet virtuální služby
Účet virtuální služby je speciální typ účtu, který nemá žádné heslo a spravuje Windows.

![VSA](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

Atribut je určena pro použití se scénáři technické podpory kde jsou synchronizační modul a SQL na stejném serveru. Pokud používáte vzdálený server SQL, pak doporučujeme používat [skupinový účet spravované služby](#managed-service-account) místo.

Tato funkce vyžaduje Windows Server 2008 R2 nebo novější. Pokud Azure AD Connect instalujete na Windows Server 2008, pak instalace přejde k použití [uživatelský účet](#user-account) místo.

#### <a name="group-managed-service-account"></a>Účet skupiny spravované služby
Pokud používáte vzdálený SQL server, pak doporučujeme používat **skupinový účet spravované služby**. Další informace o tom, jak připravit účet spravované služby skupiny služby Active Directory najdete v tématu [přehled účtů spravované služby skupiny](https://technet.microsoft.com/library/hh831782.aspx).

Chcete použít tuto možnost na [instalace požadovaných součástí](how-to-connect-install-custom.md#install-required-components) stránce **použít existující účet služby**a vyberte **účet spravované služby**.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Je také podporována pro použití [samostatný účet spravované služby](https://technet.microsoft.com/library/dd548356.aspx). Ale tyto jde použít jenom na místním počítači a neexistuje žádná výhoda pro jejich použití nad výchozí účet virtuální služby.

Tato funkce vyžaduje Windows Server 2012 nebo novější. Pokud je nutné použít starší operační systém a použít vzdálený server SQL, pak je nutné použít [uživatelský účet](#user-account).

#### <a name="user-account"></a>Uživatelský účet
S účtem místní služby se vytvoří pomocí Průvodce instalací (Pokud nezadáte účet, který chcete použít vlastní nastavení). Účet má předponu **AAD_** a použít pro skutečné synchronizační službu spustit jako. Pokud Azure AD Connect instalujete na řadiči domény, účet se vytvoří v doméně. **AAD_** účet služby musí být umístěn v doméně, pokud:
   - použití vzdáleného serveru se systémem SQL server
   - používat proxy server, který vyžaduje ověření

![Účet synchronizační služby](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

Účet je vytvořen s dlouho složité heslo, nemá prošlou platnost.

Tento účet slouží k ukládání hesel pro tyto účty bezpečným způsobem. Tato hesla jiných účtů jsou uloženy v zašifrované podobě v databázi. Privátní klíče pro šifrovací klíče jsou chráněné pomocí šifrování tajného klíče kryptografické služby, pomocí Windows Data Protection API (DPAPI).

Pokud používáte plnou instalaci systému SQL Server, účet služby je DBO vytvořené databáze pro synchronizační modul. Služba nebude fungovat tak, jak má s jinými oprávněními. Přihlašovací jméno SQL se vytvoří také.

Účet také uděleno oprávnění k souborům, klíče registru a další objekty související s synchronizačního modulu.

### <a name="azure-ad-connector-account"></a>Účet Azure AD Connector.
Vytvoření účtu ve službě Azure AD pro použití služby synchronizace. Tento účet lze identifikovat podle jeho zobrazovaného názvu.

![Účet AD](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Název serveru, který účet se používá na lze identifikovat v druhé části uživatelského jména. Na obrázku je název serveru řadiče domény DC1. Pokud máte testovací servery, každý server má svůj vlastní účet.

Účet je vytvořen s dlouho složité heslo, nemá prošlou platnost. Jsou udělena zvláštní roli **účty pro synchronizaci adresářů** , který má pouze oprávnění k provádění úloh synchronizace adresáře. Tato speciální předdefinovanou roli nelze udělit mimo Průvodce Azure AD Connect. Tento účet s rolí na portálu Azure zobrazuje **uživatele**.

Platí omezení 20 účtů služeb synchronizace ve službě Azure AD. Pokud chcete získat seznam existujících účtů služby Azure AD ve službě Azure AD, spusťte následující rutinu Azure AD Powershellu: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Chcete-li odebrat nepoužívané služby Azure AD účty služeb, spusťte následující rutinu Azure AD Powershellu: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="related-documentation"></a>Související dokumentace
Pokud jste si v dokumentaci na [integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md), následující tabulka obsahuje odkazy na související témata.

|Téma |Odkaz|  
| --- | --- |
|Stažení služby Azure AD Connect | [Stažení služby Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalace s expresním nastavením | [Expresní instalace služby Azure AD Connect](how-to-connect-install-express.md)|
|Instalace s vlastním nastavením | [Vlastní instalace služby Azure AD Connect](./how-to-connect-install-custom.md)|
|Upgrade z nástroje DirSync | [Upgrade ze synchronizačního nástroje služby Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Po instalaci | [Ověření instalace a přiřazení licencí](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
