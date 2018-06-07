---
title: 'Azure AD Connect: Účty a oprávnění | Microsoft Docs'
description: Toto téma popisuje účty používá a vytvořit a oprávněních.
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
ms.date: 06/06/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 30763f88a7d78678411abd7fe7cc6375e00cb6f6
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824264"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Účty a oprávnění
Průvodce instalací služby Azure AD Connect nabízí dva různé cesty:

* Expresní nastavení Průvodce vyžaduje další oprávnění.  Toto je tak, aby ho můžete nastavit konfiguraci snadno, aniž by bylo potřeba vytvořit uživatele nebo konfigurace oprávnění.
* V části vlastní nastavení Průvodce nabízí další možnosti a možnosti. Existují však některé situace, ve kterých je potřeba zajistit, že máte správná oprávnění, sami.

## <a name="related-documentation"></a>Související dokumentace
Pokud jste si v dokumentaci na [integrace místních identit s Azure Active Directory](../active-directory-aadconnect.md), následující tabulka obsahuje odkazy na související témata.

|Téma |Odkaz|  
| --- | --- |
|Stažení služby Azure AD Connect | [Stažení služby Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalace s expresním nastavením | [Expresní instalace služby Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Instalace s vlastním nastavením | [Vlastní instalace služby Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Upgrade z nástroje DirSync | [Upgrade ze synchronizačního nástroje služby Azure AD (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|Po instalaci | [Ověření instalace a přiřazení licencí](active-directory-aadconnect-whats-next.md)|

## <a name="express-settings-installation"></a>Expresní nastavení instalace
V nastavení Express Průvodce instalací požádá o pověření správce podniku AD DS.  Toto je tak místní Active Directory se dá nakonfigurovat s požadovaná oprávnění pro Azure AD Connect. Pokud provádíte upgrade z nástroje DirSync, AD DS Enterprise Admins přihlašovací údaje se používají k resetování hesla pro účet používaný nástrojem DirSync. Je také nutné přihlašovací údaje Azure AD globálního správce.

| Stránka Průvodce | Přihlašovací údaje shromážděné | Oprávnění vyžadovaná | Použít pro |
| --- | --- | --- | --- |
| neuvedeno |Uživatel, který spouští Průvodce instalací |Správce místního serveru |<li>Vytvoří místní účet, který se používá jako [synchronizovat účet služby modul](#azure-ad-connect-sync-service-account). |
| Připojení k Azure AD |Přihlašovací údaje pro adresář Azure AD |Roli globálního správce ve službě Azure AD |<li>Zapíná se synchronizace v adresáři služby Azure AD.</li>  <li>Vytváření [účet Azure AD](#azure-ad-service-account) který se používá pro synchronizační průběžné operace ve službě Azure AD.</li> |
| Připojení ke službě AD DS |Místní přihlašovací údaje služby Active Directory |Člen skupiny Enterprise Admins (EA) ve službě Active Directory |<li>Vytvoří [účet](#active-directory-account) ve službě Active Directory a uděluje oprávnění k němu. Tento účet vytvořil slouží ke čtení a zápisu informací v adresáři directory během synchronizace.</li> |

### <a name="enterprise-admin-credentials"></a>Pověření správce podniku
Tyto přihlašovací údaje se používají jenom při instalaci a nepoužívají se po dokončení instalace. Správce podnikové sítě, není správce domény Ujistěte se, že ve všech doménách lze nastavit oprávnění ve službě Active Directory.

### <a name="global-admin-credentials"></a>Globální přihlašovací údaje správce
Tyto přihlašovací údaje se používají jenom při instalaci a nepoužívají se po dokončení instalace. Se používá k vytvoření [účet Azure AD](#azure-ad-service-account) používá pro synchronizaci změny do Azure AD. Účet taky umožňuje synchronizaci jako funkci ve službě Azure AD.

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>Expresní nastavení účtu oprávnění pro vytvoření služby AD DS
[Účet](#active-directory-account) vytvořené pro čtení a zápis do služby AD DS mít následující oprávnění, když vytvořený Expresní nastavení:

| Oprávnění | Použít pro |
| --- | --- |
| <li>Replikovat změny adresáře</li><li>Replikace adresáře všechny změny |Synchronizace hodnot hash hesel |
| Pro čtení a zápis všech vlastností uživatele |Hybridní importu a serveru Exchange |
| Pro čtení a zápis všech iNetOrgPerson vlastnosti |Hybridní importu a serveru Exchange |
| Skupina všech vlastností čtení/zápisu |Hybridní importu a serveru Exchange |
| Obraťte se na čtení/zápisu všechny vlastnosti |Hybridní importu a serveru Exchange |
| Resetování hesla |Příprava k povolení zpětného zápisu hesla |

## <a name="custom-settings-installation"></a>Vlastní nastavení instalace
Azure AD Connect verze 1.1.524.0 a novějším je možnost povolit, aby průvodce Azure AD Connect vytvořit účet používaný pro připojení ke službě Active Directory.  Starší verze vyžadovat, že je účet vytvořený před instalací. Tento účet musí udělit oprávnění naleznete v [vytvořit účet služby AD DS](#create-the-ad-ds-account). 

| Stránka Průvodce | Přihlašovací údaje shromážděné | Oprávnění vyžadovaná | Použít pro |
| --- | --- | --- | --- |
| neuvedeno |Uživatel, který spouští Průvodce instalací |<li>Správce místního serveru</li><li>Pokud používáte plnou instalaci systému SQL Server, uživatel musí být správce systému (SA) v systému SQL</li> |Ve výchozím nastavení, vytvoří místní účet, který se používá jako [synchronizovat účet služby modul](#azure-ad-connect-sync-service-account). Účet se vytvoří jen tehdy, když správce neurčuje určitého účtu. |
| Nainstalovat synchronizační služby, možnost účet služby |AD nebo pověření místního uživatelského účtu |Uživatel, oprávnění pomocí Průvodce instalací |Pokud správce určuje účet, tento účet slouží jako účet služby pro službu synchronizace. |
| Připojení k Azure AD |Přihlašovací údaje pro adresář Azure AD |Roli globálního správce ve službě Azure AD |<li>Zapíná se synchronizace v adresáři služby Azure AD.</li>  <li>Vytváření [účet Azure AD](#azure-ad-service-account) který se používá pro synchronizační průběžné operace ve službě Azure AD.</li> |
| Připojení adresářů |Místní přihlašovací údaje služby Active Directory pro jednotlivé doménové struktury, která je připojena k Azure AD |Oprávnění závisí na funkce, které můžete povolit a lze nalézt v [vytvořit účet služby AD DS](#create-the-ad-ds-account) |Tento účet slouží ke čtení a zápisu informací v adresáři directory během synchronizace. |
| Servery služby AD FS |Pro každý server v seznamu Průvodce shromažďuje přihlašovací údaje, když se přihlašovací údaje uživatele spuštěním Průvodce nejsou dostatečná k připojení |Správce domény |Instalace a konfigurace role serveru služby AD FS. |
| Proxy servery webových aplikací |Pro každý server v seznamu Průvodce shromažďuje přihlašovací údaje, když se přihlašovací údaje uživatele spuštěním Průvodce nejsou dostatečná k připojení |Místní správce na cílovém počítači |Instalace a konfigurace role serveru WAP. |
| Přihlašovací údaje pro vztah důvěryhodnosti proxy |Federační služba důvěřovat přihlašovací údaje (přihlašovací údaje k proxy serveru používá k registraci certifikátu vztahu důvěryhodnosti pomocí služby FS |Účet domény, který slouží jako místní správce serveru služby AD FS |Počáteční registraci certifikátu důvěryhodnosti WAP služby FS. |
| Stránka účtu služby FS služby AD, "Použít možnost účet uživatele domény" |Přihlašovací údaje účtu uživatele AD |Uživatel domény |Jsou k dispozici jejichž přihlašovací údaje účtu uživatele AD se používá jako přihlašovací účet služby AD FS. |

### <a name="create-the-ad-ds-account"></a>Vytvoření účtu služby AD DS
Účet, zadejte na **připojení adresářů** stránky musí být ve službě Active Directory před instalací.  Také musí mít udělena požadovaná oprávnění. Průvodce instalací neověřuje oprávnění a všechny problémy jsou pouze zjištěno, že během synchronizace.

Oprávnění, která budete potřebovat, závisí na volitelné funkce povolíte. Pokud máte více domén, musí udělit oprávnění pro všechny domény v doménové struktuře. Pokud nepovolíte některý z těchto funkcí, výchozí **uživatele domény** jsou dostatečná oprávnění.

| Funkce | Oprávnění |
| --- | --- |
| Funkce msDS-ConsistencyGuid |Oprávnění k zápisu do atribut msDS-ConsistencyGuid zdokumentována [koncepty návrhu - pomocí msDS-ConsistencyGuid jako sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). | 
| Synchronizace hodnot hash hesel |<li>Replikovat změny adresáře</li>  <li>Replikace adresáře všechny změny |
| Hybridní nasazení systému Exchange |Oprávnění k zápisu do atributy zdokumentována [zpětný zápis hybridní Exchange](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) pro uživatele, skupiny a kontakty. |
| Veřejné složky e-mailu Exchange |Oprávnění ke čtení pro atributy, které jsou dokumentovány v článku [veřejné složky e-mailu Exchange](active-directory-aadconnectsync-attributes-synchronized.md#exchange-mail-public-folder) pro veřejných složek. | 
| Zpětný zápis hesla |Oprávnění k zápisu do atributy zdokumentována [Začínáme se správou hesel](../authentication/howto-sspr-writeback.md) pro uživatele. |
| Zpětný zápis zařízení |Oprávnění udělená pomocí skriptu prostředí PowerShell, jak je popsáno v [zpětný zápis zařízení](active-directory-aadconnect-feature-device-writeback.md). |
| Zpětný zápis skupin |Čtení, vytvoření, aktualizace a odstranění skupiny objekty, pro synchronizována **skupiny Office 365**.  Další informace najdete v části [zpětný zápis skupin](active-directory-aadconnect-feature-preview.md#group-writeback).|

## <a name="upgrade"></a>Upgrade
Při upgradu z jedné verze služby Azure AD Connect na novou verzi, potřebujete následující oprávnění:

>[!IMPORTANT]
>Od verze sestavení 1.1.484, Azure AD Connect zavedl regrese chyb, který vyžaduje oprávnění správce systému a Upgradujte databázi SQL.  Tato chyba je v sestavení 1.1.647 opravena.  Pokud provádíte upgrade na tento build, budete potřebovat oprávnění správce.  Oprávnění dbo nestačí.  Pokud se pokusíte o upgrade Azure AD Connect bez oprávnění správce, se upgrade nezdaří a Azure AD Connect již nebude fungovat správně později.  Společnost Microsoft je uvědomte o tom a funguje to opravit.


| Objekt zabezpečení | Oprávnění vyžadovaná | Použít pro |
| --- | --- | --- |
| Uživatel, který spouští Průvodce instalací |Správce místního serveru |Aktualizace binárních souborů. |
| Uživatel, který spouští Průvodce instalací |Člen skupiny ADSyncAdmins |Proveďte změny synchronizačního pravidla a další konfigurace. |
| Uživatel, který spouští Průvodce instalací |Pokud používáte plnou instalaci systému SQL server: DBO (nebo podobnou) z databáze synchronizačního modulu |Proveďte změny úrovně databáze, například aktualizace tabulek s novými sloupci. |

## <a name="more-about-the-created-accounts"></a>Další informace o vytvořené účty
### <a name="active-directory-account"></a>Účet Active Directory
Pokud chcete použít Expresní nastavení, je vytvořen účet ve službě Active Directory, který je používán k synchronizaci. Vytvořený účet se nachází v kořenové doméně doménové struktury v kontejneru Users a má názvu předponu s **MSOL_**. Účet je vytvořen s dlouho složité heslo, které nevyprší platnost. Pokud máte zásad hesel ve vaší doméně, ujistěte se, že dlouhá a složitá hesla bude mít možnost pro tento účet.

![Účet AD](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

Pokud chcete použít vlastní nastavení, pak jste zodpovědní za vytváření účtu před zahájením instalace.

### <a name="azure-ad-connect-sync-service-account"></a>Účtu synchronizační služby Azure AD Connect
Synchronizační služba může běžet pod různými účty. Může běžet pod **virtuální účet služby** (Atribut), **skupinový účet spravované služby** (gMSA nebo sMSA) nebo běžného uživatelského účtu. Podporované možnosti byly změněny s 2017 duben verzi připojit, pokud se o novou instalaci. Pokud provádíte upgrade ze starší verze služby Azure AD Connect, nejsou k dispozici tyto další možnosti.

| Typ účtu | Možnost instalace | Popis |
| --- | --- | --- |
| [Účet služby virtuální](#virtual-service-account) | Rychlé a vlastní, 2017 Duben a novější | Toto je možnost použít pro všechny Expresní instalace, s výjimkou instalace na řadiči domény. Pro vlastní je výchozí možnost, pokud se používá jinou možnost. |
| [Účet spravované služby skupiny](#group-managed-service-account) | Vlastní, 2017 Duben a novější | Pokud používáte vzdálený SQL server, pak doporučujeme používat účet skupiny spravované služby. |
| [Uživatelský účet](#user-account) | Rychlé a vlastní, 2017 Duben a novější | Uživatelský účet s předponou AAD_ je vytvořen pouze během instalace při instalaci v systému Windows Server 2008 a nainstalovat na řadič domény. |
| [Uživatelský účet](#user-account) | Rychlé a vlastní, 2017 března a starší | Místní účet s předponou AAD_ je vytvořen během instalace. Pokud používáte vlastní instalaci, můžete zadat jiný účet. |

Pokud používáte připojení s sestavení z 2017 března nebo starší, pak by neměl resetujete heslo pro účet služby od Windows zničí šifrovacích klíčů z bezpečnostních důvodů. Nelze změnit účet na jiný účet bez opětovné instalace Azure AD Connect. Pokud upgradujete na sestavení z 2017 duben nebo novější, pak je podporována Chcete-li změnit heslo pro účet služby, ale nelze změnit účet používaný.

> [!Important]
> Účet služby můžete nastavit pouze na první instalaci. Změna účtu služby po dokončení instalace není podporována.

Toto je tabulku výchozím doporučené a podporované možnosti pro účet synchronizační služby.

Legenda:

- **Tučné** znamená výchozí možnost a ve většině případů doporučená možnost.
- *Kurzíva* určuje doporučená možnost, pokud není výchozí možnost.
- 2008 - výchozí možnost při instalaci v systému Windows Server 2008
- Non-bold – podporované možnost
- Místní účet - místní uživatelský účet na serveru
- Účet domény - uživatelský účet domény
- sMSA - [samostatný účet spravované služby](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [skupinový účet spravované služby](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Vlastní | Vzdálený server SQL</br>Vlastní |
| --- | --- | --- | --- |
| **samostatné nebo pracovní skupiny počítače** | Nepodporuje se | **VSA**</br>Místní účet (2008)</br>Místní účet |  Nepodporuje se |
| **počítač připojený k doméně** | **VSA**</br>Místní účet (2008) | **VSA**</br>Místní účet (2008)</br>Místní účet</br>Účet domény</br>sMSA, gMSA | **gMSA**</br>Účet domény |
| **Řadič domény** | **Účet domény** | *gMSA*</br>**Účet domény**</br>sMSA| *gMSA*</br>**Účet domény**|

#### <a name="virtual-service-account"></a>Účet služby virtuální
Účet služby virtuální je speciální typ účtu, který nemá heslo a je spravována službou Windows.

![VSA](./media/active-directory-aadconnect-accounts-permissions/aadsyncvsa.png)

Tyto Atributy se má být použitá se scénáři, kdy jsou synchronizační modul a SQL na stejném serveru. Pokud používáte vzdálený SQL, pak doporučujeme používat [skupinový účet spravované služby](#managed-service-account) místo.

Tato funkce vyžaduje Windows Server 2008 R2 nebo novější. Pokud Azure AD Connect instalujete na Windows Server 2008, pak instalace se vrátí k používání [uživatelský účet](#user-account) místo.

#### <a name="group-managed-service-account"></a>Účet skupiny spravované služby
Pokud používáte vzdálený SQL server, pak doporučujeme pomocí **skupiny účet spravované služby**. Další informace o tom, jak připravit služby Active Directory pro účet spravované služby skupiny najdete v tématu [přehled účtů spravované služby skupiny](https://technet.microsoft.com/library/hh831782.aspx).

Tuto možnost použít na [instalace požadovaných součástí](active-directory-aadconnect-get-started-custom.md#install-required-components) vyberte **použít existující účet služby**a vyberte **účet spravované služby**.  
![VSA](./media/active-directory-aadconnect-accounts-permissions/serviceaccount.png)  
Je podporováno také používat [samostatný účet spravované služby](https://technet.microsoft.com/library/dd548356.aspx). Ale tyto lze použít pouze v místním počítači a nepřináší žádný užitek přes výchozí účet virtuální službu používat.

Tato funkce vyžaduje Windows Server 2012 nebo novější. Pokud potřebujete použít staršího operačního systému a použití vzdáleného SQL, pak je nutné použít [uživatelský účet](#user-account).

#### <a name="user-account"></a>Uživatelský účet
Účet místní služby se vytvoří pomocí Průvodce instalací (Pokud nezadáte účet, který chcete použít vlastní nastavení). Účet je předponou **AAD_** a použít pro skutečné synchronizační službu spustit jako. Pokud Azure AD Connect instalujete na řadiči domény, vytvoří se účet v doméně. **AAD_** účet služby musí být umístěn v doméně, pokud:
   - použití vzdáleného serveru se systémem SQL server
   - použít proxy server, který vyžaduje ověření

![Účtu synchronizační služby](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

Účet je vytvořen s dlouho složité heslo, které nevyprší platnost.

Tento účet se používá k uložení hesla pro ostatní účty zabezpečené způsobem. Tato hesla jiných účtů jsou uloženy v zašifrované podobě v databázi. Privátního klíče pro šifrovací klíče jsou chráněny pomocí šifrování tajného klíče kryptografické služby pomocí systému Windows Data Protection API (DPAPI).

Pokud používáte plnou instalaci systému SQL Server, účet služby je vlastníka vytvořené databáze pro synchronizační modul. Služba nebude fungovat tak, jak má s jinými oprávněními. Je taky vytvořit přihlašovací jméno SQL.

Účet je také udělena oprávnění k souborům, klíčů registru a jiných objektech vztahujících se k synchronizační modul.

### <a name="azure-ad-service-account"></a>Účet služby Azure AD
Pro použití služby sync je vytvořen účet ve službě Azure AD. Tento účet lze identifikovat podle jeho zobrazovaný název.

![Účet AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount2.png)

Název serveru, který účet se používá na lze identifikovat v druhé části uživatelského jména. Na obrázku je název serveru řadiče domény DC1. Pokud máte pracovní serverů, každý server má svůj vlastní účet.

Účet služby se vytvoří s dlouho složité heslo, které nevyprší platnost. Jsou udělena zvláštní role **účty synchronizace adresáře** který má pouze oprávnění k provedení úlohy synchronizace adresáře. Tento speciální předdefinovaná role nelze udělit mimo Průvodce službou Azure AD Connect. Portál Azure ukazuje tento účet s rolí **uživatele**.

Existuje omezení 20 účtů služeb synchronizace ve službě Azure AD. Chcete-li získat seznam stávajících účtů služby Azure AD ve službě Azure AD, spusťte následující rutinu Azure AD PowerShell: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Chcete-li odebrat nepoužité Azure AD účtům služby, spusťte následující rutinu Azure AD PowerShell: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](../active-directory-aadconnect.md).
