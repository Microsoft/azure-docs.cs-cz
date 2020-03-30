---
title: Nejčastější dotazy k připojení služby Azure Active Directory – | Dokumenty společnosti Microsoft
description: Tento článek odpovídá na nejčastější dotazy týkající se služby Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5aa30bc819531ee8cc9cd337648a6cbc661bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149809"
---
# <a name="azure-active-directory-connect-faq"></a>Nejčastější dotazy k připojení služby Azure Active Directory

## <a name="general-installation"></a>Obecná instalace

**Otázka: Jak mohu posílit můj server Azure AD Connect snížit úroveň útoku zabezpečení?**

Společnost Microsoft doporučuje zpevnění serveru Azure AD Connect snížit úroveň útoku zabezpečení pro tuto důležitou součást vašeho IT prostředí.  Podle níže uvedených doporučení snížíte bezpečnostní rizika pro vaši organizaci.

* Nasazení služby Azure AD Connect na serveru s připojením domény a omezení přístupu správce pro správu na správce domény nebo jiné přísně řízené skupiny zabezpečení

Další informace naleznete v tématu: 

* [Zabezpečení skupin správců](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Zabezpečení předdefinovaných účtů správce](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Zlepšení zabezpečení a udržení snížením útočných ploch](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Zmenšení povrchu útoku služby Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**Otázka: Bude instalace fungovat, pokud má globální správce Azure Active Directory (Azure AD) povoleno dvoufaktorové ověřování (2FA)?**  
Od února 2016 sestavení, tento scénář je podporován.

**Otázka: Existuje způsob, jak nainstalovat Azure AD Connect bez dozoru?**  
Instalace služby Azure AD Connect je podporovaná pouze v případě, že použijete průvodce instalací. Bezobslužná, nemaší instalace není podporována.

**Otázka: Mám doménovou strukturu, kde nelze kontaktovat jednu doménu. Jak nainstaluji Azure AD Connect?**  
Od února 2016 sestavení, tento scénář je podporován.

**Otázka: Pracuje agent adlu na jádru serveru agent ad(Azure AD DS) služby Azure Active Directory Domain Services (Azure AD DS)?**  
Ano. Po instalaci agenta můžete dokončit proces registrace pomocí následující rutiny prostředí PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Otázka: Podporuje Azure AD Connect synchronizaci ze dvou domén do Azure AD?**  
Ano, tento scénář je podporován. Odkazovat na [více domén](how-to-connect-install-multiple-domains.md).
 
**Otázka: Můžete mít více konektorů pro stejnou doménu služby Active Directory ve službě Azure AD Connect?**  
Ne, více konektorů pro stejnou doménu služby AD není podporováno. 

**Otázka: Můžu přesunout databázi Azure AD Connect z místní databáze do vzdálené instance SERVERU SQL?**   
Ano, následující kroky poskytují obecné pokyny, jak to provést. V současné době pracujeme na podrobnějším dokumentu.
1. Zálohujte databázi LocalDB ADSync.
Nejjednodušší způsob, jak to udělat, je použít SQL Server Management Studio nainstalované ve stejném počítači jako Azure AD Connect. Připojte se k *(LocalDb).\ADSync*a potom zálohujte databázi ADSync.

2. Obnovte databázi ADSync do vzdálené instance serveru SQL Server.

3. Nainstalujte Azure AD Connect do existující [vzdálené databáze SQL](how-to-connect-install-existing-database.md).
   Článek ukazuje, jak migrovat pomocí místní databáze SQL. Pokud migrujete na používání vzdálené databáze SQL, musíte v kroku 5 procesu také zadat existující účet služby, za který bude služba Windows Sync spuštěna. Tento účet služby synchronizačního modulu je popsán zde:
   
      **Použití existujícího účtu služby**: Ve výchozím nastavení azure ad připojení používá účet virtuální služby pro synchronizační služby používat. Pokud používáte vzdálenou instanci serveru SQL Server nebo používáte proxy server, který vyžaduje ověření, použijte účet spravované služby nebo účet služby v doméně a znáte heslo. V těchto případech zadejte účet, který chcete použít. Ujistěte se, že uživatelé, kteří jsou spuštěny instalace jsou správci systému v SQL, aby bylo možné vytvořit přihlašovací údaje pro účet služby. Další informace najdete v tématu [Účty azure a služby Azure AD Connect a oprávnění](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      S nejnovějším sestavením teď může databáze vzdáleně zřizovat správce SQL a pak je instalovat správce služby Azure AD Connect s oprávněními vlastníka databáze. Další informace najdete [v tématu Instalace Služby Azure AD Connect pomocí oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md).

Aby to bylo jednoduché, doporučujeme, aby uživatelé, kteří instalují Azure AD Connect, byli správci systému v SQL. S nedávnými sestaveními však můžete nyní používat delegované správce SQL, jak je popsáno v [článku Instalace služby Azure AD Connect pomocí oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md).

**Otázka: Jaké jsou některé z osvědčených postupů z oboru?**  

Následuje informační dokument, který představuje některé z osvědčených postupů, které inženýrství, podpora a naši konzultanti vyvinuli v průběhu let.  To je uvedeno v seznamu odrážek, na které lze rychle odkazovat.  Přestože se tento seznam pokouší být komplexní, mohou existovat další osvědčené postupy, které se ještě v seznamu nestaly.

- Pokud používáte Úplné SQL, pak by měl zůstat místní vs vzdálené
    - Méně chmele
    - Snadnější řešení potíží
    - Menší složitost
    - Potřeba určit prostředky pro SQL a povolit režii pro Azure AD Connect a OS
- Obejít Proxy pokud je to vůbec možné, pokud nejste schopni obejít proxy pak je třeba zajistit, aby hodnota časového času je větší než 5 minut.
- Pokud je vyžadován proxy server, musíte přidat proxy server do souboru machine.config
- Buďte si vědomi místních úloh SQL a údržby a jak budou mít vliv na Azure AD Connect – zejména re-indexování
- Ujistěte se, že než DNS může vyřešit externě
- Ujistěte se, že [specifikace serveru](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) jsou podle doporučení, zda používáte fyzické nebo virtuální servery
- Ujistěte se, že pokud používáte virtuální server, že potřebné prostředky jsou vyhrazené
- Ujistěte se, že konfigurace disku a disku splňuje osvědčené postupy pro SQL Server
- Instalace a konfigurace stavu Azure AD Connect pro monitorování
- Použijte prahovou hodnotu odstranění, která je integrovaná do Azure AD Connect.
- Pečlivě zkontrolujte aktualizace verzí, které mají být připraveny pro všechny změny a nové atributy, které mohou být přidány
- Zálohovat vše
    - Záložní klíče
    - Pravidla synchronizace zálohování
    - Konfigurace zálohovacího serveru
    - Záložní databáze SQL
- Ujistěte se, že neexistují žádné agenty zálohování třetích stran, které zálohují SQL bez SQL VSS Writer (běžné ve virtuálních serverech se snímky třetích stran)
- Omezení množství vlastních pravidel synchronizace, která se používají při přidávání složitosti
- Zacházejte se servery Azure AD Connect jako se servery úrovně 0
- Buďte lhostejní k úpravám pravidel synchronizace cloudu bez velkého pochopení dopadu a správných obchodních faktorů
- Ujistěte se, že jsou otevřené správné porty URL a firewall pro podporu Azure AD Connect a Azure AD Connect Health
- Využijte atribut filtrovaný v cloudu k řešení potíží a prevenci fiktivních objektů
- S pracovní server ujistěte se, že používáte Azure AD Connect Konfigurace Documenter pro konzistenci mezi servery
- Pracovní servery by měly být v samostatných datových centrech (fyzická umístění
- Pracovní servery nejsou určeny jako řešení s vysokou dostupností, ale můžete mít více pracovních serverů.
- Zavedení pracovních serverů "Lag" by mohlo zmírnit některé potenciální prostoje v případě chyby
- Nejprve otestovat a ověřit všechny upgrady na pracovním serveru
- Před přepnutím na pracovní server vždy ověřte exporty.  Využijte pracovní server pro úplné importy a úplné synchronizace ke snížení dopadu na podnikání
- Zachování konzistence verzí mezi servery Azure AD Connect co nejvíce 

**Otázka: Můžu povolit Azure AD Connect k vytvoření účtu Konektor Azure AD na počítači pracovní skupiny?**
Ne.  Aby bylo možné povolit Azure AD Connect automaticky vytvářet účet Konektor Azure AD, musí být počítač připojen k doméně.  

## <a name="network"></a>Network (Síť)
**Otázka: Mám bránu firewall, síťové zařízení nebo něco jiného, co omezuje dobu, po kterou mohou připojení zůstat v síti otevřená. Jaká by měla být prahová hodnota časového limitu na straně klienta, když používám Azure AD Connect?**  
Veškerý síťový software, fyzická zařízení nebo cokoli jiného, co omezuje maximální dobu, po kterou mohou připojení zůstat otevřená, by měly používat prahovou hodnotu alespoň pět minut (300 sekund) pro připojení mezi serverem, kde je nainstalovaný klient Azure AD Connect. a služby Azure Active Directory. Toto doporučení platí také pro všechny dříve vydané nástroje pro synchronizaci identity společnosti Microsoft.

**Otázka: Jsou podporovány domény s jedním popiskem (SLD)?**  
I když důrazně doporučujeme proti této konfiguraci sítě ([viz článek](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), pomocí Azure AD Connect synchronizace s doménou s jedním popiskem je podporována, tak dlouho, dokud konfigurace sítě pro jednu úroveň domény funguje správně.

**Otázka: Jsou podporovány doménové struktury s nesouvislými doménami služby AD?**  
Ne, Azure AD Connect nepodporuje místní doménové struktury, které obsahují nesouvislé obory názvů.

**Otázka: Jsou podporovány "tečkované" názvy NetBIOS?**  
Ne, Azure AD Connect nepodporuje místní doménové struktury nebo domény, kde název NetBIOS obsahuje tečku (.).

**Otázka: Je čisté prostředí IPv6 podporováno?**  
Ne, Azure AD Connect nepodporuje čisté prostředí IPv6.

**Otázka:I mají prostředí s více doménovými strukturami a síť mezi dvěma doménovými strukturami používá překlad adres (NAT). Je podporováno používání Azure AD Connect mezi těmito dvěma doménovými strukturami?**</br>
Ne, použití Azure AD Connect přes NAT není podporováno. 

## <a name="federation"></a>metadata
**Otázka: Co mám dělat, když dostanu e-mail s žádostí o obnovení certifikátu Office 365?**  
Pokyny k obnovení certifikátu naleznete v tématu [obnovení certifikátů](how-to-connect-fed-o365-certs.md).

**Otázka: Pro předávající stranu Office 365 mám nastavenou automatickou aktualizaci předávající strany. Musím provést nějakou akci, když se můj podpisový certifikát tokenu automaticky převrátí?**  
Použijte pokyny, které jsou uvedeny v článku [obnovit certifikáty](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Prostředí
**Otázka: Je podporováno přejmenování serveru po instalaci služby Azure AD Connect?**  
Ne. Změna názvu serveru vykreslí synchronizační modul nelze připojit k instanci databáze SQL a služba nelze spustit.

**Otázka: Jsou pravidla synchronizace nové generace cryptographic (NGC) podporována v počítači s podporou FIPS?**  
Ne.  Není to podporováno.

**Dotaz: Pokud jsem zakázal synchronizované zařízení (například: HAADJ) na webu Azure Portal, proč je znovu povolena?**<br>
Synchronizovaná zařízení mohou být vytvářena nebo mastered místně. Pokud je synchronizované zařízení místní, může být znovu povoleno na webu Azure Portal, i když ho dříve zakázal správce. Chcete-li synchronizované zařízení zakázat, zakažte účet počítače pomocí místní služby Active Directory.

**Otázka: Pokud zablokuji přihlášení uživatelů na portálu Office 365 nebo Azure AD pro synchronizované uživatele, proč se odblokuje při přihlášení znovu?**<br>
Synchronizovaní uživatelé mohou být vytvářeni nebo mastered místně. Pokud je účet povolen místně, může odblokovat přihlašovací blok umístěný správcem.

## <a name="identity-data"></a>Údaje o identitě
**Otázka: Proč atribut userPrincipalName (UPN) ve službě Azure AD neodpovídá místnímu názvu UŽIVATELE?**  
Další informace naleznete v těchto článcích:

* [Uživatelská jména v Office 365, Azure nebo Intune neodpovídají místnímu UPN nebo alternativnímu přihlašovacímu ID](https://support.microsoft.com/kb/2523192)
* [Po změně hlavního názvu uživatele uživatelského účtu na jinou federovosadu nejsou synchronizovány změny nástrojem synchronizace služby Azure Active Directory.](https://support.microsoft.com/kb/2669550)

Azure AD můžete také nakonfigurovat tak, aby modul synchronizace mohl aktualizovat hlavní pracovní místo, jak je popsáno v [funkcích synchronizační služby Azure AD Connect](how-to-connect-syncservice-features.md).

**Otázka: Je podporována pro soft-match místní skupiny Azure AD nebo kontaktní objekt s existující skupiny Azure AD nebo objekt kontaktu?**  
Ano, tato měkká shoda je založena na proxyAddress. Měkké párování není podporováno pro skupiny, které nejsou povoleny poštou.

**Otázka: Je podporováno ručně nastavit atribut ImmutableId na existující skupině Azure AD nebo objektu kontaktu tak, aby se pevně shodovals s místní skupinou Azure AD nebo objektem kontaktu?**  
Ne, ruční nastavení atributu ImmutableId na existující skupině Azure AD nebo objektu kontaktu na pevný, který není aktuálně podporován.

## <a name="custom-configuration"></a>Vlastní konfigurace
**Otázka: Kde jsou zdokumentované rutiny Prostředí PowerShell pro Azure AD Connect?**  
S výjimkou rutin, které jsou popsány na tomto webu, ostatní rutiny prostředí PowerShell nalezené v Azure AD Connect nejsou podporovány pro použití zákazníkem.

**Otázka: Lze použít možnost "Export serveru/import serveru", která se nachází ve Správci synchronizačních služeb, k přesunutí konfigurace mezi servery?**  
Ne. Tato možnost nenačte všechna nastavení konfigurace a neměla by být použita. Místo toho pomocí průvodce vytvořte základní konfiguraci na druhém serveru a pomocí editoru pravidel synchronizace vygenerujte skripty prostředí PowerShell k přesunutí libovolného vlastního pravidla mezi servery. Další informace naleznete v tématu [Migrace švihu](how-to-upgrade-previous-version.md#swing-migration).

**Otázka: Hesla lze uložit do mezipaměti pro přihlašovací stránku Azure a lze zabránit tomuto ukládání do mezipaměti, protože obsahuje prvek pro zadání hesla s atributem *automatického dokončování = "false"?***  
V současné době není úprava atributů HTML pole **Heslo,** včetně značky automatického dokončování, podporována. V současné době pracujeme na funkci, která umožňuje vlastní JavaScript, který vám umožní přidat libovolný atribut do pole **Heslo.**

**Otázka: Přihlašovací stránka Azure zobrazuje uživatelská jména uživatelů, kteří se dříve úspěšně přihlásili. Lze toto chování vypnout?**  
V současné době není úprava atributů HTML vstupního pole **Heslo,** včetně značky automatického dokončování, podporována. V současné době pracujeme na funkci, která umožňuje vlastní JavaScript, který vám umožní přidat libovolný atribut do pole **Heslo.**

**Otázka: Existuje způsob, jak zabránit souběžným relacím?**  
Ne.

## <a name="auto-upgrade"></a>Automatický upgrade

**Otázka: Jaké jsou výhody a důsledky použití automatického upgradu?**  
Doporučujeme všem zákazníkům, aby povolili automatický upgrade pro jejich instalaci Azure AD Connect. Výhodou je, že vždy obdržíte nejnovější opravy, včetně aktualizací zabezpečení pro chyby zabezpečení, které byly nalezeny v Azure AD Connect. Proces upgradu je bezbolestný a stane se automaticky, jakmile je k dispozici nová verze. Mnoho tisíc zákazníků Azure AD Connect používá automatický upgrade s každou novou verzí.

Proces automatického upgradu vždy nejprve zjistí, zda je instalace způsobilá pro automatický upgrade. Pokud je způsobilý, upgrade se provádí a testuje. Tento proces také zahrnuje hledání vlastních změn pravidel a konkrétních faktorů životního prostředí. Pokud testy ukazují, že upgrade je neúspěšný, předchozí verze se automaticky obnoví.

V závislosti na velikosti prostředí může proces trvat několik hodin. Během upgradu nedojde k žádné synchronizaci mezi službou Windows Server Active Directory a službou Azure AD.

**Otázka: Obdržel jsem e-mail s upozorněním, že můj automatický upgrade již nefunguje a potřebuji nainstalovat novou verzi. Proč to musím dělat?**  
V loňském roce jsme vydali verzi Služby Azure AD Connect, která za určitých okolností mohla zakázat funkci automatického upgradu na serveru. Problém jsme opravili ve službě Azure AD Connect verze 1.1.750.0. Pokud jste byli ovlivněni problémem, můžete zmírnit spuštěním skriptu Prostředí PowerShell opravit nebo ruční upgrade na nejnovější verzi Azure AD Connect. 

Chcete-li spustit skript PowerShellu, [stáhněte si skript](https://aka.ms/repairaadconnect) a spusťte ho na serveru Azure AD Connect v okně PowerShellu pro správu. Chcete-li se dozvědět, jak spustit skript, [podívejte se na toto krátké video](https://aka.ms/repairaadcau).

Chcete-li provést ruční upgrade, je nutné stáhnout a spustit nejnovější verzi souboru AADConnect.msi.
 
-  Pokud je vaše aktuální verze starší než 1.1.750.0, [stáhněte a upgradujte na nejnovější verzi](https://www.microsoft.com/download/details.aspx?id=47594).
- Pokud je vaše verze Azure AD Connect 1.1.750.0 nebo novější, není nutná žádná další akce. Již používáte verzi, která obsahuje opravu automatického upgradu. 

**Otázka: Obdržel jsem e-mail s upozorněním, že mám upgradovat na nejnovější verzi, abych znovu povolil automatický upgrade. Používám verzi 1.1.654.0. Musím provést upgrade?**  
Ano, chcete-li znovu povolit automatický upgrade, je třeba upgradovat na verzi 1.1.750.0 nebo novější. [Stáhněte a upgradujte na nejnovější verzi](https://www.microsoft.com/download/details.aspx?id=47594).

**Otázka: Obdržel jsem e-mail s upozorněním, že mám upgradovat na nejnovější verzi, abych znovu povolil automatický upgrade. Pokud jsem k automatickému upgradu použil PowerShell, musím nainstalovat nejnovější verzi?**  
Ano, stále je třeba upgradovat na verzi 1.1.750.0 nebo novější. Povolení služby automatického upgradu pomocí prostředí PowerShell nezmírní problém s automatickým upgradem zjištěný ve verzích před verzí 1.1.750.0.

**Otázka: Chci upgradovat na novější verzi, ale nejsem si jistý, kdo nainstaloval Azure AD Connect a nemáme uživatelské jméno a heslo. Potřebujeme to?**
Nepotřebujete znát uživatelské jméno a heslo, které bylo původně použito k upgradu Služby Azure AD Connect. Použijte libovolný účet Azure AD, který má roli globálního správce.

**Otázka: Jak najdu, kterou verzi Azure AD Connect používám?**  
Pokud chcete ověřit, která verze Služby Azure AD Connect je nainstalovaná na vašem serveru, přejděte na Ovládací panely a vyhledejte nainstalovanou verzi microsoft azure ad connect výběrem **programů** > **a funkcí**, jak je znázorněno tady:

![Verze Azure AD Connect v Ovládacích panelech](./media/reference-connect-faq/faq1.png)

**Otázka: Jak můžu upgradovat na nejnovější verzi Azure AD Connect?**  
Informace o tom, jak upgradovat na nejnovější verzi, najdete v [tématu Azure AD Connect: Upgrade z předchozí verze na nejnovější](how-to-upgrade-previous-version.md). 

**Otázka: Už jsme upgradovali na nejnovější verzi Azure AD Connect v loňském roce. Musíme znovu upgradovat?**  
Tým Azure AD Connect provádí časté aktualizace služby. Chcete-li těžit z oprav chyb a aktualizací zabezpečení, stejně jako z nových funkcí, je důležité udržovat server v aktuálním stavu s nejnovější verzí. Pokud povolíte automatický upgrade, verze softwaru se aktualizuje automaticky. Pokud chcete najít historii verzí Azure AD Connect, přečtěte si část [Azure AD Connect: Historie verzí](reference-connect-version-history.md)verze .

**Otázka: Jak dlouho trvá provedení upgradu a jaký je dopad na mé uživatele?**  
Čas potřebný k upgradu závisí na velikosti vašeho tenanta. Pro větší organizace může být nejlepší provést upgrade ve večerních nebo víkendových. Během upgradu probíhá žádná aktivita synchronizace.

**Otázka: Věřím, že jsem upgradoval na Azure AD Connect, ale portál Office stále zmiňuje DirSync. Proč?**  
Tým Office pracuje na tom, aby aktualizace portálu Office odrážely aktuální název produktu. Neodráží, který synchronizační nástroj používáte.

**Otázka: Stav automatického upgradu říká" Pozastaveno." Proč je pozastaveno? Mám to povolit?**  
Chyba byla zavedena v předchozí verzi, která za určitých okolností ponechá stav automatického upgradu nastavený na "Pozastaveno". Ruční povolení je technicky možné, ale bude vyžadovat několik složitých kroků. Nejlepší věc, kterou můžete udělat, je nainstalovat nejnovější verzi Azure AD Connect.

**Otázka: Moje společnost má přísné požadavky na správu změn a já chci kontrolovat, kdy je vytlačena. Mohu řídit, kdy je spuštěn automatický upgrade?**  
Ne, dnes žádná taková funkce neexistuje. Tato funkce je vyhodnocována pro budoucí verzi.

**Otázka: Dostanu e-mail, pokud se nepodařilo provést automatický upgrade? Jak poznám, že byla úspěšná?**  
O výsledku upgradu nebudete informováni. Tato funkce je vyhodnocována pro budoucí verzi.

**Otázka: Publikujete časovou osu, kdy plánujete vysunout automatické upgrady?**  
Automatický upgrade je prvním krokem v procesu vydání novější verze. Kdykoli je k dispozici nová verze, upgrady se automaticky posunou. Novější verze Azure AD Connect jsou předem oznámeny v [plánu Azure AD](../fundamentals/whats-new.md).

**Otázka: Upgraduje také automatický upgrade azure ad connect health?**  
Ano, automatický upgrade také upgraduje Azure AD Connect Health.

**Otázka: Můžete také automaticky upgradovat servery Azure AD Connect v pracovním režimu?**  
Ano, můžete automaticky upgradovat server Azure AD Connect, který je v pracovním režimu.

**Otázka: Pokud se automatický upgrade nezdaří a můj server Azure AD Connect se nespustí, co mám dělat?**  
Ve výjimečných případech se služba Azure AD Connect nespustí po provedení upgradu. V těchto případech restartování serveru obvykle řeší problém. Pokud služba Azure AD Connect stále nespustí, otevřete lístek podpory. Další informace najdete [v tématu Vytvoření žádosti o službu a obraťte se na podporu Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**Otázka: Nejsem si jistý, jaká jsou rizika při upgradu na novější verzi Azure AD Connect. Můžete mi zavolat, aby mi pomohl s upgradem?**  
Pokud potřebujete pomoc s upgradem na novější verzi Služby Azure AD Connect, otevřete lístek podpory na [webu Vytvořit žádost o službu a obraťte se na podporu Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Řešení potíží
**Otázka: Jak můžu získat pomoc s Azure AD Connect?**

[Hledání ve znalostní bázi Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Vyhledejte kb technická řešení běžných problémů break-fix o podpoře Azure AD Connect.

[Fóra služby Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Vyhledejte technické otázky a odpovědi nebo se zeptejte vlastních otázek na stránce [Komunita Azure AD](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Získání podpory pro Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

**Otázka: Proč se po chybách kroku synchronizace zobrazují události 6311 a 6401?**

Události 6311 - **Server došlo k neočekávané chybě při provádění zpětného volání** a 6401 - řadič **agenta správy došlo k neočekávané chybě** - jsou vždy zaznamenány po chybě kroku synchronizace. Chcete-li tyto chyby vyřešit, je třeba vyčistit chyby kroku synchronizace.  Další informace najdete [v tématu Řešení chyb během synchronizace](tshoot-connect-sync-errors.md) a [poradce při potížích s synchronizací objektů pomocí synchronizace Azure AD Connect](tshoot-connect-objectsync.md)
