---
title: Azure Active Directory Connect – nejčastější dotazy - | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 7edabc99da5e1466e848336c647a33213c9edd8b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132855"
---
# <a name="azure-active-directory-connect-faq"></a>Nejčastější dotazy ke službě Azure Active Directory Connect

## <a name="general-installation"></a>Obecné instalace
**Otázka: bude fungovat instalace globální správce Azure Active Directory (Azure AD) má povoleno dvoufaktorové ověřování (2FA)?**  
Od února 2016 sestavení tento scénář se podporuje.

**Otázka: je způsob, jak nainstalovat Azure AD Connect bezobslužné?**  
Instalace služby Azure AD Connect je podporována pouze při použití Průvodce instalací. Bezobslužné, bezobslužné instalace není podporována.

**Otázka: je nutné do doménové struktury, kde nelze kontaktovat jednu doménu. Instalace Azure AD Connect**  
Od února 2016 sestavení tento scénář se podporuje.

**Otázka: nemá Azure Active Directory Domain Services (Azure AD DS) stav agenta fungovat na jádro serveru?**  
Ano. Po instalaci agenta, můžete dokončit proces registrace pomocí následující rutiny prostředí PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Otázka: podporuje Azure AD Connect podporu synchronizace ze dvou domén na Azure AD?**  
Ano, tento scénář se podporuje. Odkazovat na [více domén](active-directory-aadconnect-multiple-domains.md).
 
**Otázka: je možné mít více konektorů pro stejné domény služby Active Directory v Azure AD Connect?**  
Ne, více konektorů pro ve stejné doméně AD nejsou podporovány. 

**Otázka: lze přesunout databázi Azure AD Connect z místní databáze do vzdálené instance systému SQL Server?**   
Ano, následující kroky obsahují obecné pokyny o tom, jak to udělat. Právě pracujeme na podrobnější dokumentu.
1. Proveďte zálohu databáze LocalDB ADSync.
Nejjednodušší způsob, jak to udělat, je použít SQL Server Management Studio instalovaného na stejném počítači jako Azure AD Connect. Připojení k *(localdb)\.\ADSync*a pak vytvořte zálohu databáze ADSync.

2. Obnovení databáze ADSync na vzdálené instanci serveru SQL.

3. Nainstalujte Azure AD Connect s existující [vzdálené databáze SQL](active-directory-aadconnect-existing-database.md).
   Článek ukazuje, jak migrovat pomocí místní databáze SQL. Pokud provádíte migraci k použití vzdáleného SQL databáze, v kroku 5 také zadejte existující účet služby, které služba synchronizace systému Windows se spustí jako. Tento účet služby synchronizační modul je popsaný tady:
   
      **Použít existující účet služby**: ve výchozím nastavení používá Azure AD Connect účet virtuální služby pro synchronizační služby používat. Pokud používáte vzdálenou instanci systému SQL Server nebo použít proxy server, který vyžaduje ověření, použít účet spravované služby nebo účet služby v doméně a znát heslo. V těchto případech zadejte účet, který chcete použít. Zajistěte, aby uživatelé, kteří běží instalace byla správce systému v systému SQL, tak aby bylo možné vytvořit přihlašovací údaje pro účet služby. Další informace najdete v tématu [účty a oprávnění Azure AD Connect](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account). 
   
      S nejnovějším sestavením teď může databáze vzdáleně zřizovat správce SQL a pak je instalovat správce služby Azure AD Connect s oprávněními vlastníka databáze. Další informace najdete v tématu [instalaci Azure AD Connect pomocí oprávnění správce SQL delegovaný](active-directory-aadconnect-sql-delegation.md).

Pro zjednodušení doporučujeme, aby uživatelé, kteří nainstalujte Azure AD Connect být správci systému v systému SQL. Však s poslední sestavení teď můžete použít delegované správce SQL, jak je popsáno v [instalace služby Azure AD Connect s použitím SQL delegovaného správce oprávnění](active-directory-aadconnect-sql-delegation.md).

## <a name="network"></a>Síť
**Otázka: je nutné je brána firewall, síťové zařízení nebo něco jiného, který omezuje čas, který připojení může zůstat otevřené v mé síti. Co mají Moje prahová hodnota vypršení časového limitu klienta se při použití Azure AD Connect?**  
Všechny sítě softwaru, fyzických zařízení nebo cokoliv jiného, který omezuje maximální doba, kterou připojení zůstat otevřené by měl používat prahovou hodnotu alespoň pět minut (300 sekund) pro připojení mezi serverem, kde je nainstalován klient Azure AD Connect a Azure Active Directory. Toto doporučení platí také pro všechny dřív vydaných synchronizace nástroje Microsoft Identity.

**Otázka: je názvem bez přípony domén (domény SLD) podporované?**  
Ne, Azure AD Connect nepodporuje místními doménovými strukturami nebo doménami, které používají domény SLD.

**Otázka: jsou doménových strukturách s nesouvislým oborem doménami AD podporovány?**  
Ne, Azure AD Connect nepodporuje místními doménovými strukturami, které obsahují nesouvislé obory názvů.

**Otázka: jsou "s tečkami" názvy pro rozhraní NetBIOS, které jsou podporovány?**  
Ne, Azure AD Connect nepodporuje místními doménovými strukturami nebo doménách, kde název NetBIOS obsahuje tečku (.).

**Otázka: je čistě IPv6 prostředí podporovaná?**  
Ne, Azure AD Connect nepodporuje čistý prostředí IPv6.

## <a name="federation"></a>metadata
**Otázka: Co mám dělat, když se zobrazí e-mailu, který požaduje obnovit certifikát Moje Office 365?**  
Informace o obnovení certifikátu, najdete v části [obnovení certifikátů](active-directory-aadconnect-o365-certs.md).

**Otázka: je nutné "Automaticky aktualizovat předávající strany" sadu pro Office 365, předávající strany. Je nutné provádět žádnou akci, když můj automaticky podpisový certifikát tokenu navyšování?**  
Použijte pokyny, které je popsané v článku [obnovení certifikátů](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Prostředí
**Otázka: je podporována přejmenování serveru po instalaci Azure AD Connect?**  
Ne. Změna názvu serveru vykreslí synchronizační modul nelze se připojit k instanci databáze SQL a službu nelze spustit.

## <a name="identity-data"></a>Data identit
**Otázka: Proč atribut userPrincipalName (UPN) ve službě Azure AD místní hlavní název uživatele neodpovídá?**  
Informace najdete v těchto článcích:

* [Uživatelská jména v Office 365, Azure nebo Intune se neshodují. místní UPN nebo alternativním přihlašovacím ID](https://support.microsoft.com/en-us/kb/2523192)
* [Změny nejsou synchronizovány pomocí nástroje Azure Active Directory sync po změně UPN uživatelský účet používat jiné federované domény](https://support.microsoft.com/en-us/kb/2669550)

Můžete také konfigurovat Azure AD umožňuje synchronizační modul aktualizovat hlavní název uživatele, jak je popsáno v [funkce služby Azure AD Connect sync](active-directory-aadconnectsyncservice-features.md).

**Otázka: je podporována konfigurace soft-match je místní skupina Azure AD nebo kontaktní objekt s existující skupinu pro Azure AD, případně kontaktujte objektu?**  
Ano, tato logicky shoda podle proxyAddress. Logicky odpovídající není podporována pro skupiny, které nejsou povoleným e-mailem.

**Otázka: je podporována pro ručně nastavit atribut ImmutableId na existující skupinu pro Azure AD nebo se obraťte na objekt, který má pevný match ji do místní skupiny služby Azure AD nebo se obraťte na objekt?**  
Ne, ručně nastavení atributu ImmutableId na stávající služby Azure AD skupiny nebo objekt kontaktu na pevné match je aktuálně nepodporuje.

## <a name="custom-configuration"></a>Vlastní konfigurace
**Otázka: kde popsané rutiny prostředí PowerShell pro Azure AD Connect?**  
S výjimkou rutiny, které jsou popsané v této lokalitě nejsou podporované jinými rutinami prostředí PowerShell nalezen ve službě Azure AD Connect pro použití zákazníka.

**Otázka: je možné použít možnost "Serveru serveru pro export/import", která se nachází v Synchronization Service Manager k přesunutí konfiguraci mezi servery?**  
Ne. Tato možnost není načíst všechna nastavení konfigurace a neměl by být použit. Místo toho použijte Průvodce pro vytvoření základní konfigurace na druhém serveru a pomocí editoru pravidlo synchronizace ke generování skriptů prostředí PowerShell pro přesun mezi servery žádné vlastní pravidlo. Další informace najdete v tématu [kyvu migrace](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**Otázka: je možné hesla do mezipaměti pro stránku Azure přihlášení, a můžete toto použití mezipaměti u možné, protože obsahuje element input pro heslo pomocí *automatického dokončování = "false"* atribut?**  
V současné době změna atributů HTML **heslo** pole, včetně automatického dokončování značky, není podporováno. Právě pracujeme na funkce, která umožňuje vlastní JavaScript, který vám umožňuje přidat kterýkoli atribut **heslo** pole.

**Otázka: Azure přihlašovací stránka zobrazí uživatelská jména uživatelů, kteří mají dříve úspěšně přihlášení. Lze toto chování vypnout?**  
V současné době změna atributů HTML **heslo** vstupní pole, včetně automatického dokončování značky, není podporováno. Právě pracujeme na funkce, která umožňuje vlastní JavaScript, který vám umožňuje přidat kterýkoli atribut **heslo** pole.

**Otázka: je způsob, jak zabránit souběžných relací?**  
Ne.

## <a name="auto-upgrade"></a>Automatický upgrade

**Otázka: jaké jsou výhody a důsledky použití automatické aktualizace?**  
Nemůžeme se radí všem zákazníkům, aby povolte automatický upgrade pro jejich instalaci Azure AD Connect. Výhodou je, že vždycky získat nejnovější opravy, včetně aktualizací zabezpečení pro chyby zabezpečení, které byly nalezeny ve službě Azure AD Connect. Proces upgradu je bezproblémově a probíhá automaticky, jakmile je k dispozici nová verze. Mnoho tisíc, Azure AD Connect zákazníků použít automatický upgrade s každou novou verzi.

Proces automatického upgradu vždy nejprve Určuje, zda je vhodné k upgradu automaticky při instalaci. Pokud je vhodné, je upgrade provést a otestovat. Tento proces také zahrnuje hledá vlastní změny pravidel a konkrétní faktorech prostředí. Pokud testy zobrazit, neproběhne upgrade, se automaticky obnoví předchozí verzi.

V závislosti na velikosti prostředí může trvat několik hodin. Žádná synchronizace mezi Windows Server Active Directory a Azure AD se stane, když probíhá upgrade.

**Otázka: zobrazila e-mail s oznámením, která Moje automatický upgrade přestane fungovat a bude nutné instalace nové verze. Proč je potřeba to udělat?**  
Poslední rok jsme vydali verzi služby Azure AD Connect, který za určitých okolností může mít zakázán funkci Automatický upgrade na vašem serveru. Vyřešili jsme problém v Azure AD Connect verze 1.1.750.0. Pokud jste byly ovlivněny problém, můžete ji zmírnit spuštěním skriptu prostředí PowerShell ji opravit nebo ručně upgrade na nejnovější verzi služby Azure AD Connect. 

Chcete-li spustit skript prostředí PowerShell [stáhnout skript](https://aka.ms/repairaadconnect) a potom ho spusťte na serveru Azure AD Connect v okně prostředí PowerShell pro správu. Další informace o spuštění skriptu, [zobrazit následující krátké video](https://aka.ms/repairaadcau).

Postup při ručním upgradu, musíte stáhnout a spustit nejnovější verzi souboru AADConnect.msi.
 
-  Pokud vaše aktuální verze je starší než 1.1.750.0, [stáhněte a upgradujte na nejnovější verzi](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
- Pokud vaše verze Azure AD Connect je 1.1.750.0 nebo novější, není nutná žádná další akce. Již používáte verzi, která obsahuje opravy automatický upgrade. 

**Otázka: zobrazila e-mailu zobrazuje oznámení pro upgrade na nejnovější verzi a znovu povolte automatický upgrade. Používám verze 1.1.654.0. Je potřeba upgradovat?**  
Ano, je nutné upgradovat na verzi 1.1.750.0 nebo později znovu povolte automatický upgrade. [Stáhněte a upgradujte na nejnovější verzi](https://www.microsoft.com/en-us/download/details.aspx?id=47594).

**Otázka: zobrazila e-mailu zobrazuje oznámení pro upgrade na nejnovější verzi a znovu povolte automatický upgrade. Pokud chcete-li povolit automatický upgrade I použili prostředí PowerShell, stále potřebuji nainstalovat nejnovější verzi?**  
Ano, stále musíte upgradovat na verzi 1.1.750.0 nebo novější. Povolení služby automatický upgrade pomocí prostředí PowerShell zmírnit není nalezena ve verzi před 1.1.750.0 problém automatický upgrade.

**Otázka: Chcete provést upgrade na novější verzi, ale nejste si jisti, kteří nainstalovali Azure AD Connect a nemáme uživatelské jméno a heslo. To potřebujeme?**
Nepotřebujete vědět, uživatelské jméno a heslo, které bylo původně použít k upgradu Azure AD Connect. Použijte všechny účet Azure AD, který má roli globálního správce.

**Otázka: jak najít, kterou verzi služby Azure AD Connect používám?**  
Pokud chcete ověřit, která verze služby Azure AD Connect je nainstalovaná na serveru, přejděte do ovládacích panelů a vyhledejte nainstalovaná verze systému Microsoft Azure AD Connect výběrem **programy** > **programy a funkce** , jak je vidět tady:

![Verze služby Azure AD Connect v Ovládacích panelech](media/active-directory-aadconnect-faq/faq1.png)

**Otázka: jak upgradovat na nejnovější verzi služby Azure AD Connect?**  
Pokud chcete dozvědět, jak upgradovat na nejnovější verzi, přečtěte si téma [Azure AD Connect: Upgrade z předchozí verze na nejnovější](active-directory-aadconnect-upgrade-previous-version.md). 

**Otázka: jsme již upgradována na nejnovější verzi služby Azure AD Connect poslední rok. Je potřeba upgradovat znovu?**  
Tým služby Azure AD Connect vytváří časté aktualizace pro službu. Abyste mohli využívat výhod oprav chyb a aktualizace zabezpečení, jakož i nové funkce, je důležité udržovat serverem aktuální pomocí nejnovější verze. Pokud povolíte automatický upgrade, se automaticky aktualizuje vaše verze softwaru. Historie verzí verze služby Azure AD Connect, najdete v tématu [Azure AD Connect: historie verzí](active-directory-aadconnect-version-history.md).

**Otázka: jak dlouho trvá k provedení upgradu a jaký bude dopad na Moji uživatelé?**  
Čas potřebný k upgradu závisí na velikosti vašeho klienta. Pro větší organizace může být vhodné provést upgrade v večer nebo o víkendech. Během upgradu žádná aktivita synchronizace probíhá.

**Otázka:, které se domníváte, že po upgradu na Azure AD Connect, ale portálu Office stále uvádí DirSync. Proč je to?**  
Tým Office pracuje získat aktualizace portálu Office tak, aby odrážela aktuální název produktu. Které synchronizační nástroj služby používáte nemusí odpovídat.

**Otázka: můj stav automatický upgrade říká "Pozastaveno." Proč ji je pozastaveno? By měl povolit ji?**  
Chyby byla zavedena v předchozí verzi, která za určitých okolností opustí automatický upgrade stav, který je nastaven na "Pozastaveno." Ruční povolení ho je technicky možné ale by vyžadovaly několik kroků komplexní. Nejlepší, jak je, nainstalujte nejnovější verzi služby Azure AD Connect.

**Otázka: Moje společnost má požadavky striktní správy změn a chcete řídit, kdy má proniknout. Můžete řídit, kdy se spustí automatický upgrade?**  
Ne, neexistuje žádný takový funkce ještě dnes. Tato funkce se vyhodnocuje pro budoucí použití.

**Otázka: bude získat e-mailu, pokud se nezdařilo automatický upgrade? Jak bude vědět, že bylo úspěšné.**  
Nebudete upozorněni výsledku upgradu. Tato funkce se vyhodnocuje pro budoucí použití.

**Otázka: při plánování push na automatické upgrady publikovat časovou osu pro?**  
Automatický upgrade je prvním krokem v procesu verze na novější verzi. Vždy, když je nová verze, upgrade se instaluje automaticky. Novější verze služby Azure AD Connect jsou předběžně oznámené v [přehled Azure AD](../fundamentals/whats-new.md).

**Otázka: automatický upgrade upgradovat Azure AD Connect Health?**  
Ano, upgrade automaticky upgraduje i Azure AD Connect Health.

**Otázka: je taky automatický upgrade servery Azure AD Connect v pracovním režimu?**  
Ano, vám může automatický upgrade server služby Azure AD Connect, který je v pracovním režimu.

**Otázka: Pokud automatický upgrade nezdaří a my server Azure AD Connect se nespustí, co mám dělat?**  
Ve výjimečných případech služba Azure AD Connect se nespustí po provedení upgradu. V těchto případech se řeší problém, obvykle restartování serveru. Pokud služba Azure AD Connect stále nespustí, otevřete lístek podpory. Další informace najdete v tématu [vytvořit žádost o služby se obrátit na podporu Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**Otázka: nejste si jisti, jaké jsou rizika při upgradu na novější verzi služby Azure AD Connect. Můžete na pomoc při upgradu volat?**  
Pokud potřebujete pomoc, upgrade na novější verzi služby Azure AD Connect, otevřete lístek podpory v [vytvořit žádost o služby se obrátit na podporu Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Řešení potíží
**Otázka: jak můžete získat pomoc s Azure AD Connect?**

[Znalostní bázi Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Hledat KB pro technické řešení pro běžné problémy opravu o podpoře pro Azure AD Connect.

[Fóra služby Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Hledání technické dotazy a odpovědi nebo vlastní ptají přechodem na [komunitou Azure AD](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Získat podporu pro Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
