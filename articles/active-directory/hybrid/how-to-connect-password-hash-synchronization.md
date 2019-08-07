---
title: Implementace synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect | Dokumentace Microsoftu
description: Poskytuje informace o fungování synchronizaci hodnot hash hesel a jak nastavit.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2019
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98101973627750f87fd06d3f617a1af764a837ee
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774236"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementace synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect
Tento článek obsahuje informace, které potřebujete k synchronizaci hesel uživatelů mezi místní instancí Active Directory a instanci cloudové služby Azure Active Directory (Azure AD).

## <a name="how-password-hash-synchronization-works"></a>Jak funguje synchronizace hodnot hash hesel
Active Directory domain Services ukládá ve formě reprezentaci hodnoty hash hesla skutečného uživatelského hesla. Hodnota hash je výsledek jednosměrné matematické funkce ( *algoritmus hash*). Neexistuje žádný způsob, jak výsledek jednosměrné funkce převést zpět na heslo v prostém textu. 

Synchronizace Azure AD Connect synchronizaci hesla, extrahuje vaše hodnoty hash hesla z místní instance Active Directory. Zpracování dodatečné zabezpečení se použije pro hodnoty hash hesla předtím, než se synchronizují do služby ověřování Azure Active Directory. Hesla se nesynchronizují na jednotlivé uživatele a v chronologickém pořadí.

Samotný datový tok proces synchronizace hodnot hash hesel se podobá synchronizace dat uživatele. Hesla se však nesynchronizují častěji než časový interval synchronizace standardní adresář pro jiné atributy. Proces synchronizace hodnot hash hesel se spouští každé dvě minuty. Nelze změnit frekvenci tohoto procesu. Při synchronizaci hesla, přepíše stávající heslo cloudu.

Při prvním povolení funkce synchronizace hodnot hash hesla, provede počáteční synchronizaci hesel všech uživatelů v oboru. Nelze explicitně definovat podmnožinu hesla uživatelů, které se mají synchronizovat.

Pokud změníte místní heslo, aktualizované heslo synchronizována, nejčastěji v řádu minut.
Funkce synchronizace hodnot hash hesel automaticky opakovat pokusy o synchronizaci se nezdařilo. Pokud dojde k chybě při pokusu o synchronizaci hesla, je zaznamenána chyba do prohlížeče událostí.

Synchronizace hesla nemá žádný vliv na uživatele, který je aktuálně přihlášený.
Aktuální relace cloudové služby není okamžitě ovlivněny změnou synchronizovaných hesel, ke které dochází, když se přihlásíte, cloudové službě. Ale při Cloudová služba vyžaduje, abyste znovu provést ověření, budete muset zadat nové heslo.

Uživatel musí zadat své podnikové přihlašovací údaje podruhé k ověření do služby Azure AD, bez ohledu na to, zda jsou přihlášeni k podnikové síti. Tento vzor lze minimalizovat, ale pokud si uživatel vybere zůstat přihlášeni (políčko zůstat Přihlášeni) políčko při přihlášení. Tento výběr nastaví soubor cookie relace, který obchází ověřování po dobu 180 dnů. Políčko zůstat Přihlášeni chování můžete povolit nebo zakázat pomocí Správce Azure AD. Kromě toho můžete snížit na zadání hesla zapnutí [bezproblémového jednotného přihlašování](how-to-connect-sso.md), který automaticky přihlásí uživatele v případě, že nejsou na firemní zařízení připojených k podnikové síti.

> [!NOTE]
> Synchronizace hesla je podporována pouze pro typ objektu uživatele ve službě Active Directory. Není podporována pro typ objektu iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Podrobný popis toho, jak funguje synchronizace hodnot hash hesel
Následující část popisuje, podrobné, jak funguje synchronizace hodnot hash hesel mezi službami Active Directory a Azure AD.

![Podrobné heslo toku](./media/how-to-connect-password-hash-synchronization/arch3b.png)


1. Každé dvě minuty, agent synchronizace hodnot hash hesel na žádosti serveru AD Connect uložených hodnot hash hesel (atributu unicodePwd) z řadiče domény.  Tento požadavek je pomocí standardu [MS DRSR](https://msdn.microsoft.com/library/cc228086.aspx) replikace protokol použitý k synchronizaci dat mezi řadiče domény. Účet služby musí mít replikace změn adresáře a replikovat všechny změny adresáře AD oprávněním (ve výchozím nastavení na instalaci) k získání hodnoty hash hesla.
2. Před odesláním, řadič domény šifruje hodnota hash MD4 hesla pomocí klíče, který je [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) hash klíče relace vzdáleného volání Procedur a sůl. Potom pošle výsledek k agentovi synchronizace hodnoty hash hesla přes RPC. Řadič domény taky předá sůl agenta synchronizace, aby pomocí protokolu replikace řadiče domény, tak agent bude schopná dešifrovat obálky.
3.  Šifrované obálky po agenta synchronizace hodnot hash hesel používá [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) a hodnota salt chcete vygenerovat klíč pro dešifrování přijatých dat. zpět na původním formátu MD4. Agent synchronizace hodnot hash hesel nikdy má přístup k hesla v nešifrovaném textu. Heslo hash agenta synchronizace, aby pro použití algoritmu MD5 je určené výhradně pro kompatibilitu protokolů replikace s řadiči domény a používá se pouze v místním prostředí mezi řadiči domény a agenta synchronizace hodnot hash hesel.
4.  Agent synchronizace hodnot hash hesel rozšíří hodnotu hash hesla binární 16 bajtů 64 bajtů převedením první-the-hash na 32 bajtů šestnáctkový řetězec, pak převedete tento řetězec zpět do binárního souboru s kódováním UTF-16.
5.  Přidá agenta synchronizace hodnot hash hesel za uživatele hodnota salt, skládající se z hodnota salt délku 10 bajtů na binární 64 bajtů dalším stupněm ochrany původní hodnoty hash.
6.  Agent synchronizace hodnot hash hesel pak kombinuje-the-hash MD4 plus za uživatele hodnota salt a do vstupů [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) funkce. 1000 iterací [HMAC SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) algoritmu hash s klíčem se používají. 
7.  Agent synchronizace hodnot hash hesel trvá výslednou hodnotu hash 32 bajtů, zřetězí obojí hodnota salt uživatele a počtu SHA256 iterace k němu (pro použití službou Azure AD), pak přenáší řetězec, ze služby Azure AD Connect ke službě Azure AD přes protokol SSL.</br> 
8.  Když se uživatel pokusí přihlásit k Azure AD a zadá své heslo, heslo se spustí prostřednictvím stejné MD4 + řetězce salt + PBKDF2 + HMAC SHA256 procesu. Pokud výsledná hodnota hash odpovídající hodnotě hash uložené ve službě Azure AD, uživatel zadá správné heslo a je ověřený. 

>[!Note] 
>Původní hodnota hash MD4 se nebudou přenášet do služby Azure AD. Místo toho se přenášejí hash SHA256 objektu původní hodnota hash MD4. V důsledku toho pokud je hodnota hash uložené ve službě Azure AD, ho nelze použít v místních útoku pass-the-hash.

### <a name="security-considerations"></a>Aspekty zabezpečení
Při synchronizaci hesel heslo ve formátu prostého textu se nevystaví funkci synchronizace hodnoty hash hesla ke službě Azure AD, nebo žádné související služby.

Ověření uživatele se provádí pomocí služby Azure AD, nikoli s instancí služby Active Directory organizace. SHA256 heslo data uložená ve službě Azure AD – hodnoty hash původní hodnota hash MD4 – je bezpečnější, než se ukládá ve službě Active Directory. Dále protože tato hodnota hash SHA256 nelze dešifrovat, nemohou být vrátil do prostředí služby Active Directory organizace a zobrazí jako platného uživatele heslo v rámci útoku pass-the-hash.

### <a name="password-policy-considerations"></a>Požadavky zásad hesel
Existují dva druhy zásady pro hesla, které jsou ovlivněny povolení synchronizace hodnot hash hesel:

* Zásadám složitosti hesel
* Zásady vypršení platnosti hesla

#### <a name="password-complexity-policy"></a>Zásadám složitosti hesel  
Pokud je povolená synchronizace hodnot hash hesel, zásady složitost hesel v místní instanci Active Directory přepsat zásady složitosti v cloudu pro synchronizovaní uživatelé. Můžete všechny platné hesel z vaší místní instance Active Directory pro přístup ke službám Azure AD.

> [!NOTE]
> Hesla pro uživatele, které jsou vytvořené přímo v cloudu se stále mohou zásady pro hesla, jak jsou definovány v cloudu.

#### <a name="password-expiration-policy"></a>Zásady vypršení platnosti hesla  
Pokud je uživatel v rozsahu synchronizace hodnot hash hesel, heslo účtu cloudu nastavená na *nikdy nevyprší*.

Můžete pokračovat k přihlášení k vašim cloudovým službám pomocí synchronizovaných heslo, které vypršelo ve vašem místním prostředí. Vaše heslo cloudu se aktualizuje při další změně hesla v místním prostředí.

#### <a name="account-expiration"></a>Vypršení platnosti účtu
Pokud vaše organizace používá atribut AccountExpires zadává jako součást správy uživatelských účtů, není tento atribut synchronizovaný do služby Azure AD. V důsledku toho účet služby Active Directory vypršela platnost v prostředí nakonfigurovaná pro synchronizaci hodnot hash hesel bude nadále aktivní ve službě Azure AD. Doporučujeme vám, že pokud platnost účtu vypršela, by měly aktivovat akce pracovního postupu, skript prostředí PowerShell, který zakáže účtu uživatele Azure AD (použít [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) rutiny). Naopak pokud účet je zapnuté, instance služby Azure AD by být zapnutý.

### <a name="overwrite-synchronized-passwords"></a>Přepište synchronizovaných hesel
Správce může ručně resetovat heslo pomocí prostředí Windows PowerShell.

V takovém případě nové heslo přepíše synchronizované heslo a nové heslo se použijí všechny zásady hesel, které jsou definovány v cloudu.

Pokud změníte svoje heslo v místním znovu nové heslo se synchronizují do cloudu, a přepíše ručně aktualizované heslo.

Synchronizace hesla nemá žádný vliv na uživatele Azure, který je přihlášen. Aktuální relace cloudové služby není okamžitě ovlivněny změnou synchronizovaných hesel, ke které dochází, když jste přihlášeni s cloudovou službou. Políčko zůstat Přihlášeni rozšiřuje dobu trvání tohoto rozdílu. Cloudová služba vyžaduje, abyste znovu provést ověření, budete muset zadat nové heslo.

### <a name="additional-advantages"></a>Další výhody

- Obecně platí je jednodušší než federační služby implementace synchronizace hodnot hash hesel. Nevyžaduje žádné další servery a eliminuje závislost na vysoce dostupných federačních služeb k ověřování uživatelů.
- Synchronizace hodnot hash hesel se dá povolit i kromě federace. To může sloužit jako záložní, pokud vaše služba federation service dojde k výpadku.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Proces synchronizace hodnot hash hesel pro Azure AD Domain Services

Pokud používáte Azure AD Domain Services k zajištění staršího ověřování pro aplikace a služby, které potřebují používat Keberos, LDAP nebo NTLM, některé další procesy jsou součástí toku synchronizace hodnot hash hesel. Azure AD Connect používá další postup k synchronizaci hodnot hash hesel do služby Azure AD pro použití v Azure AD Domain Services:

> [!IMPORTANT]
> Pokud povolíte Azure služba AD DS pro vašeho tenanta Azure AD, Azure AD Connect synchronizuje jenom starší hodnoty hash hesel. Následující kroky se nepoužívají, pokud k synchronizaci místního prostředí služba AD DS pomocí Azure AD používáte jenom Azure AD Connect.
>
> Pokud vaše starší aplikace nepoužívají ověřování NTLM nebo jednoduché vazby LDAP, doporučujeme pro Azure služba AD DS zakázat synchronizaci hodnot hash hesel protokolu NTLM. Další informace najdete v tématu [zakázání slabých šifrovacích sad a synchronizace hodnot hash přihlašovacích údajů NTLM](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect načte veřejný klíč pro instanci Azure AD Domain Services klienta.
1. Když uživatel změní heslo, uloží místní řadič domény výsledek změny hesla (hodnoty hash) ve dvou atributech:
    * *unicodePwd* pro hodnotu hash hesla protokolu NTLM.
    * *supplementalCredentials* pro hodnotu hash hesla protokolu Kerberos.
1. Azure AD Connect detekuje změny hesel prostřednictvím kanálu replikace adresářů (změny atributů, které se musí replikovat do jiných řadičů domény).
1. Pro každého uživatele, jehož heslo bylo změněno, Azure AD Connect provede následující kroky:
    * Generuje náhodný symetrický klíč AES 256.
    * Generuje náhodnou inicializační vektor potřebný pro první kolo šifrování.
    * Extrahuje hodnoty hash hesla protokolu Kerberos z atributů *supplementalCredentials* .
    * Kontroluje nastavení *SyncNtlmPasswords* zabezpečení konfigurace Azure AD Domain Services.
        * Pokud je toto nastavení zakázané, vygeneruje náhodnou a vysokou entropii hodnotu hash NTLM (odlišnou od hesla uživatele). Tato hodnota hash je pak kombinována s přesnou hodnotou hash hesla protokolu Kerberos z atributu *supplementalCrendetials* do jedné struktury dat.
        * Pokud je povoleno, kombinuje hodnotu atributu *unicodePwd* s extrahovanou hodnotou hash hesla protokolu Kerberos z atributu *supplementalCredentials* do jedné struktury dat.
    * Šifruje jedinou datovou strukturu pomocí symetrického klíče AES.
    * Šifruje symetrický klíč AES pomocí Azure AD Domain Services veřejného klíče klienta.
1. Azure AD Connect přenáší zašifrovaný symetrický klíč AES, šifrovanou strukturu dat obsahující hodnoty hash hesla a inicializační vektor do Azure AD.
1. Azure AD ukládá šifrovaný symetrický klíč AES, šifrovanou strukturu dat a inicializační vektor pro uživatele.
1. Azure AD přenáší zašifrovaný symetrický klíč AES, šifrovanou strukturu dat a inicializační vektor pomocí mechanismu interní synchronizace přes šifrovanou relaci HTTP, aby Azure AD Domain Services.
1. Azure AD Domain Services načte privátní klíč pro instanci tenanta z trezoru klíčů Azure.
1. Pro každou zašifrovanou sadu dat (představující změnu hesla jednoho uživatele) Azure AD Domain Services pak proveďte následující kroky:
    * K dešifrování symetrického klíče AES používá jeho privátní klíč.
    * Používá symetrický klíč AES s inicializačním vektorem k dešifrování šifrované datové struktury, která obsahuje hodnoty hash hesla.
    * Zapíše hodnoty hash hesla protokolu Kerberos, které obdrží, do Azure AD Domain Services řadiče domény. Hodnoty hash jsou uloženy do atributu *supplementalCredentials* objektu uživatele, který je zašifrovaný do veřejného klíče řadiče domény Azure AD Domain Services.
    * Azure AD Domain Services zapíše hodnotu hash hesla protokolu NTLM získanou do řadiče domény Azure AD Domain Services. Hodnota hash se uloží do atributu *unicodePwd* objektu uživatele, který se zašifruje do veřejného klíče řadiče domény Azure AD Domain Services.

## <a name="enable-password-hash-synchronization"></a>Povolit synchronizaci hodnot hash hesel

>[!IMPORTANT]
>Při migraci ze služby AD FS (nebo jiné technologie federace) k synchronizaci hodnot Hash hesel, důrazně doporučujeme, abyste postupovali podle našeho podrobné nasazení Průvodce publikování [tady](https://aka.ms/adfstophsdpdownload).

Při instalaci Azure AD Connect s použitím **Expresní nastavení** automaticky povolena možnost, synchronizaci hodnot hash hesel. Další informace najdete v tématu [Začínáme se službou Azure AD Connect s použitím expresního nastavení](how-to-connect-install-express.md).

Pokud použijete vlastní nastavení při instalaci Azure AD Connect, je k dispozici na na přihlašovací stránku uživatele synchronizace hodnot hash hesel. Další informace najdete v tématu [vlastní instalace služby Azure AD Connect](how-to-connect-install-custom.md).

![Povolení synchronizace hodnot hash hesel](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synchronizace hodnot hash hesel a FIPS
Pokud váš server byl uzamčen podle federální informace o zpracování Standard (FIPS), je zakázané MD5.

**Pokud chcete povolit MD5 pro synchronizaci hodnot hash hesel, postupujte následovně:**

1. Přejdete na Sync\Bin %programfiles%\Azure AD.
2. Otevřete miiserver.exe.config.
3. Přejděte do uzlu Konfigurace nebo modulu runtime na konci souboru.
4. Přidejte následující uzel: `<enforceFIPSPolicy enabled="false"/>`
5. Uložte provedené změny.

Tento fragment kódu pro informaci je to, co by měl vypadat jako:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Informace o zabezpečení a FIPS najdete v tématu [synchronizace hodnot hash hesel Azure AD, šifrování a kompatibilita se standardem FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Řešení potíží s synchronizaci hodnot hash hesel
Pokud máte problémy se synchronizace hodnot hash hesel, přečtěte si téma [řešit synchronizaci hodnot hash hesel](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Další postup
* [Synchronizace Azure AD Connect: Přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
* [Získejte podrobný postup nasazení plánu pro migrace ze služby AD FS na synchronizaci hodnot Hash hesel](https://aka.ms/authenticationDeploymentPlan)
