---
title: Azure Active Directory Connect – nejčastější dotazy – | Dokumentace Microsoftu
description: Tento článek obsahuje odpovědi na nejčastější dotazy ohledně služby Azure AD Connect.
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
ms.date: 11/02/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 594fb736b8ec4b49cf1525047ff8a7ce5e5dc959
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194967"
---
# <a name="azure-active-directory-connect-faq"></a>Nejčastější dotazy ke službě Azure Active Directory Connect

## <a name="general-installation"></a>Obecné instalace
**Otázka: Bude instalace fungovat, pokud globální správce Azure Active Directory (Azure AD) je povolené dvoufaktorové ověřování (2FA)?**  
Tento scénář se podporuje od verze sestavení. února 2016.

**Otázka: Existuje způsob, jak nainstalovat Azure AD Connect bezobslužné?**  
Instalace služby Azure AD Connect je podporována pouze v případě, že pomocí Průvodce instalací. Bezobslužné tichou instalaci se nepodporuje.

**Otázka: Mám doménovou strukturu, kdy nelze jednu doménu kontaktovat. Jak nainstalovat Azure AD Connect?**  
Tento scénář se podporuje od verze sestavení. února 2016.

**Otázka: Funguje agent Azure Active Directory Domain Services (Azure AD DS) stavu na jádru serveru?**  
Ano. Po instalaci agenta, můžete dokončit proces registrace pomocí následující rutiny Powershellu: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Otázka: Podporuje Azure AD Connect synchronizuje z dvou domén pro na Azure AD?**  
Ano, tento scénář se podporuje. Odkazovat na [více domén](how-to-connect-install-multiple-domains.md).
 
**Otázka: Může obsahovat více konektorů stejné domény služby Active Directory ve službě Azure AD Connect?**  
Ne, více konektorů pro stejnou doménu AD nejsou podporovány. 

**Otázka: Můžu přesunout databázi služby Azure AD Connect z místní databáze do vzdálené instance systému SQL Server?**   
Ano, následující kroky obsahují obecné pokyny o tom, jak to provést. Aktuálně pracujeme na podrobnější dokumentu.
1. Proveďte zálohu databáze LocalDB ADSync.
Nejjednodušší způsob, jak to provést, je použít SQL Server Management Studio nainstalované ve stejném počítači jako Azure AD Connect. Připojte se k *(LocalDb). \ADSync*a pak proveďte zálohu databáze ADSync.

2. Obnovení databáze ADSync na vzdálenou instanci SQL serveru.

3. Nainstalujte Azure AD Connect s existující [místní databáze SQL](how-to-connect-install-existing-database.md).
   Tento článek ukazuje, jak migrovat místní databázi SQL pomocí. Pokud provádíte migraci k použití vzdáleného SQL database, v kroku 5 tohoto procesu můžete musíte také zadat existující účet, který se spustí služba synchronizace Windows jako služby. Tento účet služby synchronizační modul je popsaný tady:
   
      **Použít existující účet služby**: Ve výchozím nastavení používá Azure AD Connect virtuální účet služby pro služby synchronizace používat. Pokud používáte vzdálenou instanci systému SQL Server nebo použít proxy server vyžadující ověření, použít účet spravované služby nebo účet služby v doméně a znát heslo. V těchto případech zadejte účet, který chcete použít. Ujistěte se, že uživatelé mají spuštěnou instalaci jsou správci systému SQL tak, že je možné vytvořit přihlašovací údaje pro účet služby. Další informace najdete v tématu [účtech a oprávněních Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      S nejnovějším sestavením teď může databáze vzdáleně zřizovat správce SQL a pak je instalovat správce služby Azure AD Connect s oprávněními vlastníka databáze. Další informace najdete v tématu [instalace služby Azure AD Connect pomocí oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md).

Abychom si to nekomplikovali, doporučujeme vám, že uživatelé, kteří instalace služby Azure AD Connect se správci systému SQL. Však s nejnovější sestavení teď můžete pomocí delegovaného správce SQL, jak je popsáno v [instalace služby Azure AD Connect pomocí oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md).

## <a name="network"></a>Síť
**Otázka: Mám bránu firewall, síťové zařízení nebo něco jiného, který omezuje čas, který připojení můžou zůstat otevřené v síti. Co by měl Můj mezní hodnotu časového limitu na straně klienta se při použití služby Azure AD Connect?**  
Veškerý software sítě, fyzické zařízení nebo cokoli jiného, který omezí maximální dobu, po připojení zůstat otevřené používejte prahovou hodnotu minimálně pět minut (300 sekund) pro připojení mezi serverem, kde je nainstalován klient služby Azure AD Connect a Azure Active Directory. Toto doporučení platí také pro všechny dřív vydaných nástroje synchronizace Microsoft Identity.

**Otázka: Podporují se domény bez přípony (domény SLD)?**  
Když vám doporučujeme proti takové konfiguraci sítě ([najdete v článku](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), pomocí synchronizace Azure AD Connect s názvem bez přípony domény je podporován, tak dlouho, dokud konfiguraci sítě pro jednu doménu úrovně funguje správně.

**Otázka: Jsou doménové struktury s nesouvislým doménami AD podporovány?**  
Ne, Azure AD Connect není podporováno místními doménovými strukturami, které obsahují nesouvislé obory názvů.

**Otázka: Jsou "tečkované" názvy NetBIOS podporovány?**  
Ne, Azure AD Connect nepodporuje místními doménovými strukturami nebo doménami, kde název rozhraní NetBIOS obsahuje tečku (.).

**Otázka: Je podporováno čisté prostředí IPv6?**  
Ne, Azure AD Connect nepodporuje čisté prostředí IPv6.

**Q: Mám prostředí s více doménovými strukturami a síť mezi dvěma doménovými strukturami používá NAT (Network Address Translation). Mezi těmito dvěma doménovými strukturami podporována je pomocí Azure AD Connect?**</br>
 Ne, použití služby Azure AD Connect prostřednictvím NAT se nepodporuje. 

## <a name="federation"></a>metadata
**Otázka: Co mám dělat, když mohu dostávat e-mailu, který požadovaná pro prodloužení platnosti certifikátu Moje Office 365?**  
Informace o obnovení certifikátu najdete v tématu [prodloužit platnost certifikátů](how-to-connect-fed-o365-certs.md).

**Otázka: Mám "Automaticky aktualizovat předávající stranu" sadu pro Office 365 předávající strany. Je nutné provádět žádnou akci, když můj automaticky podpisový certifikát tokenu navyšování?**  
Použijte pokyny, který je popsaný v článku [prodloužit platnost certifikátů](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Prostředí
**Otázka: Se podporuje přejmenování serveru po instalaci služby Azure AD Connect?**  
Ne. Změna názvu serveru vykreslí synchronizační modul nelze se připojit k instanci databáze SQL a službu nelze spustit.

## <a name="identity-data"></a>Data identit
**Otázka: Proč neodpovídá atribut userPrincipalName (UPN) ve službě Azure AD s místními hlavní název uživatele?**  
Informace najdete v těchto článcích:

* [Uživatelská jména v Office 365, Azure nebo Intune se neshodují, hlavní název uživatele v místním nebo alternativním přihlašovacím ID](https://support.microsoft.com/kb/2523192)
* [Změny nejsou synchronizovány podle synchronizačního nástroje služby Azure Active Directory po změně UPN uživatelský účet použít jinou federovanou doménu](https://support.microsoft.com/kb/2669550)

Můžete také konfigurovat Azure AD povolit synchronizační modul aktualizovat hlavní název uživatele, jak je popsáno v [funkce služby Azure AD Connect sync](how-to-connect-syncservice-features.md).

**Otázka: Je podporováno do konfigurace soft-match skupiny Azure AD s místními nebo objektu kontaktu s existující skupiny Azure AD nebo se obraťte na objekt?**  
Ano, tuto obnovitelně shodu podle proxyAddress. Obnovitelně odpovídající není podporována pro skupiny, které nejsou povoleným e-mailem.

**Otázka: Je podporováno pro ručně nastavit atribut ImmutableId na existující skupinu Azure AD nebo se obraťte na objekt má pevný match je ke skupině Azure AD s místními nebo se obraťte na objekt?**  
Ne, ručně na existující skupiny Azure AD nebo objektu kontaktu pro pevné match je nastavení atributu ImmutableId není aktuálně podporováno.

## <a name="custom-configuration"></a>Vlastní konfigurace
**Otázka: Kde jsou popsané rutiny Powershellu pro Azure AD Connect?**  
S výjimkou rutin, které jsou popsané v této lokalitě nejsou podporovány jinými rutinami prostředí PowerShell najít ve službě Azure AD Connect pro používání zákazníka.

**Otázka: Můžete použít možnost "Serveru serveru pro export/import", která se nachází v Synchronization Service Manageru pro přesun mezi servery pro konfiguraci?**  
Ne. Tato možnost nenačte všechna nastavení konfigurace a neměl by se používat. Místo toho použijte Průvodce pro vytvoření základní konfigurace na druhém serveru a použijte editor pravidel synchronizace se vygenerovat skripty Powershellu pro přesun jakékoli vlastní pravidlo mezi servery. Další informace najdete v tématu [Postupná migrace](how-to-upgrade-previous-version.md#swing-migration).

**Otázka: Můžete hesla do mezipaměti Azure přihlašovací stránku a lze tento ukládání do mezipaměti zabránit, protože obsahuje element input pro heslo pomocí *automatické dokončování = "false"* atribut?**  
V současné době změna atributů HTML **heslo** pole, včetně automatického dokončování značek, není podporováno. Aktuálně pracujeme na funkce, která umožňuje vlastní jazyka JavaScript, který umožňuje přidat libovolného atributu **heslo** pole.

**Otázka: Azure přihlašovací stránky zobrazí uživatelská jména uživatelů, kteří dříve úspěšném přihlášení. Můžete toto chování vypnout?**  
V současné době změna atributů HTML **heslo** vstupní pole, včetně automatického dokončování značek, není podporováno. Aktuálně pracujeme na funkce, která umožňuje vlastní jazyka JavaScript, který umožňuje přidat libovolného atributu **heslo** pole.

**Otázka: Existuje způsob, jak zabránit souběžných relací?**  
Ne.

## <a name="auto-upgrade"></a>Automatický upgrade

**Otázka: Jaké jsou výhody a důsledky použití automatický upgrade?**  
Můžeme se předobjednávky všem zákazníkům, aby povolit automatický upgrade pro jejich instalace služby Azure AD Connect. Výhodou je, že vždy zobrazí nejnovější opravy, včetně aktualizací zabezpečení ohrožení zabezpečení, které byly nalezeny ve službě Azure AD Connect. Proces upgradu je Bezproblémová a probíhá automaticky, jakmile je k dispozici nová verze. Tisíce zákazníků Azure AD Connect použít automatický upgrade s každou novou verzi.

Proces automatického upgradu vždy nejprve vytvoří, zda je vhodné k upgradu automaticky při instalaci. Pokud je oprávněné, upgrade provést a otestovat. Proces je také hledáte vlastní změny pravidel a konkrétní faktorech prostředí. Pokud testy zobrazit, neproběhne upgrade, se automaticky obnoví předchozí verze.

V závislosti na velikosti prostředí může trvat několik hodin. Žádná synchronizace mezi službami Windows Server Active Directory a Azure AD se stane, když probíhá upgrade.

**Otázka: Zobrazila se mi e-mail s oznámením, která už funguje Moje automatický upgrade a je potřeba nainstalovat novou verzi. Proč je potřeba to udělat?**  
V minulém roce jsme vydali verzi služby Azure AD Connect, které za určitých okolností může mít zakázáno funkci Automatický upgrade na serveru. Opravili jsme problém ve službě Azure AD Connect verze 1.1.750.0. Pokud jste byly ovlivněny problém, můžete ji zmírnit spuštěním skriptu prostředí PowerShell ji opravit nebo ručně upgradovat na nejnovější verzi služby Azure AD Connect. 

Chcete-li spustit skript prostředí PowerShell [stáhnout skript](https://aka.ms/repairaadconnect) a spusťte ho na server Azure AD Connect v správce okno Powershellu. Další informace o spuštění skriptu, [zobrazit v tomto krátkém videu](https://aka.ms/repairaadcau).

Pokud chcete ručně upgradovat, musíte stáhnout a spustit nejnovější verzi souboru AADConnect.msi.
 
-  Pokud vaše aktuální verze je starší než 1.1.750.0, [stáhnout a upgradujte na nejnovější verzi](https://www.microsoft.com/download/details.aspx?id=47594).
- Pokud je vaše verze Azure AD Connect 1.1.750.0 nebo novější, není nutná žádná další akce. Už používáte verzi, která obsahuje opravu automatický upgrade. 

**Otázka: Zobrazila se mi e-mail s oznámením pro upgrade na nejnovější verzi, aby znovu povolte automatický upgrade. Používám verzi 1.1.654.0. Je potřeba upgradovat?**  
Ano, musíte upgradovat na verzi 1.1.750.0 nebo později znovu zapnout automatický upgrade. [Stáhněte si a upgradujte na nejnovější verzi](https://www.microsoft.com/download/details.aspx?id=47594).

**Otázka: Zobrazila se mi e-mail s oznámením pro upgrade na nejnovější verzi, aby znovu povolte automatický upgrade. Pokud chcete povolit automatický upgrade jsem využil(a) Powershellu, stále musím nainstalujte nejnovější verzi?**  
Ano, stále musíte upgradovat na verzi 1.1.750.0 nebo novější. Povolení služby na automatický upgrade pomocí Powershellu není zmírnit nalezen ve verzích před 1.1.750.0 problém automatický upgrade.

**Otázka: Chci upgradovat na novější verzi, ale nejste si jisti, kteří nainstalovali Azure AD Connect a nejsou dostupné žádné uživatelské jméno a heslo. Potřebujeme to?**
Není nutné znát uživatelské jméno a heslo, které byl zpočátku použít k upgradu služby Azure AD Connect. Použijte libovolný účet Azure AD, který má roli globálního správce.

**Otázka: Jak zjistím, která verze služby Azure AD Connect, já používám?**  
Pokud chcete ověřit, která verze služby Azure AD Connect je nainstalovaná na serveru, přejděte do ovládacích panelů a vyhledejte nainstalovaná verze Microsoft Azure AD Connect tak, že vyberete **programy** > **programy a funkce** , jak je znázorněno zde:

![Verze služby Azure AD Connect v Ovládacích panelech](./media/reference-connect-faq/faq1.png)

**Otázka: Jak upgradovat na nejnovější verzi služby Azure AD Connect?**  
Zjistěte, jak upgradovat na nejnovější verzi, najdete v článku [Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi](how-to-upgrade-previous-version.md). 

**Otázka: Jsme už upgradovali na nejnovější verzi služby Azure AD Connect minulý rok. Je potřeba upgradovat znovu?**  
Tým služby Azure AD Connect vytvoří ve službě časté aktualizace. Abyste využili výhod opravy a aktualizace zabezpečení, jakož i nové funkce, je důležité udržovat aktuální pomocí nejnovější verze serveru. Pokud povolíte automatický upgrade, se automaticky aktualizuje verzi softwaru. Historie verzí služby Azure AD Connect najdete v tématu [Azure AD Connect: Historie vydaných verzí](reference-connect-version-history.md).

**Otázka: Jak dlouho trvá k provedení upgradu a jaký bude dopad na uživatele?**  
Čas potřebný k upgradu závisí na velikosti vašeho tenanta. Pro větší organizace může být vhodné provést upgrade v večer nebo o víkendech. Během upgradu žádná aktivita synchronizace probíhá.

**Otázka: Věřím, že mám upgradovat na Azure AD Connect, ale Office portálu stále zmínky DirSync. Proč je to?**  
Týmu služby Office pracuje na získání aktualizace portálu Office tak, aby odrážela aktuální název produktu. Nereflektuje, které použijete nástroj pro synchronizaci.

**Otázka: Říká automatický upgrade stavu "Pozastaveno." Proč to je pozastaven? By měl povolit ho?**  
Chyba byla zavedena v předchozí verzi, které za určitých okolností opustí automatický upgrade stav, který je nastaven na "Pozastaveno." Ruční povolení je technicky možné, ale vyžaduje to složité několik kroků. Nejlepší, co můžete dělat je nainstalovat nejnovější verzi služby Azure AD Connect.

**Otázka: Moje společnost má požadavky na striktní správy změn a budu chtít řídit, kdy je vynuceně instalují. Můžete řídit, kdy se spustí automatický upgrade?**  
Ne, není žádné takové funkce ještě dnes. Tato funkce se vyhodnocuje pro budoucí verzi.

**Otázka: Budou týkat e-mailu, pokud automatický upgrade nezdařil? Jak se dozvím, že byla úspěšná?**  
Nebude informováni o výsledek upgradu. Tato funkce se vyhodnocuje pro budoucí verzi.

**Otázka: Při plánování vydat automatických upgradů publikovat časovou osu pro?**  
Automatický upgrade je prvním krokem v procesu vydání verze novější verze. Vždycky, když je nová verze, automaticky vložení upgradů. Novější verze služby Azure AD Connect se v předběžně oznámené [přehled Azure AD](../fundamentals/whats-new.md).

**Otázka: Automatický upgrade upgradujte Azure AD Connect Health?**  
Ano, upgrade automaticky upgraduje i Azure AD Connect Health.

**Otázka: Je taky automatický upgrade servery Azure AD Connect v pracovním režimu?**  
Ano, je možné automatický upgrade serveru služby Azure AD Connect, který je v pracovní režimu.

**Otázka: Když automatické aktualizace se nezdaří a server Azure AD Connect se nespustí, co mám dělat?**  
Ve výjimečných případech služba Azure AD Connect nespustí po upgradu na verzi. V těchto případech se řeší problém, obvykle restartování serveru. Pokud služba Azure AD Connect stále nespustí, otevřete lístek podpory. Další informace najdete v tématu [vytvořit žádost o služby se obrátit na podporu služeb Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**Otázka: Nejste si jisti, jaké jsou rizika při upgradu na novější verzi služby Azure AD Connect. Můžete je zavolat mi na pomoc s upgradem?**  
Pokud potřebujete pomoc, upgrade na novější verzi služby Azure AD Connect, otevřete lístek podpory na [vytvořit žádost o služby se obrátit na podporu služeb Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Řešení potíží
**Otázka: Jak získat pomoc s Azure AD Connect?**

[Bázi Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Hledejte KB pro technická řešení běžných problémů typu break-fix o podpoře pro Azure AD Connect.

[Fóra služby Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Vyhledejte technické dotazy a odpovědi nebo zadat vlastní otázky tak, že přejdete do [komunity Azure AD](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Získat podporu pro Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
