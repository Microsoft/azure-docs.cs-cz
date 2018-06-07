---
title: 'Azure Active Directory Connect: Časté otázky – | Microsoft Docs'
description: Tato stránka obsahuje časté otázky k Azure AD Connect.
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
ms.openlocfilehash: 4cef685d71a64f8a6681a3449e4fe0b67899c67c
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808600"
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Nejčastější dotazy pro Azure Active Directory Connect

## <a name="general-installation"></a>Obecné instalace
**Otázka: bude fungovat instalace Azure AD globálního správce má 2FA povolené?**  
S sestavení z února 2016 tento scénář se podporuje.

**Otázka: je způsob, jak nainstalovat Azure AD Connect bezobslužné?**  
Je podporován pouze pro instalaci Azure AD Connect s použitím Průvodce instalací. Nejsou podporovány bezobslužných a bezobslužné instalace.

**Otázka: je nutné do doménové struktury, kde nelze kontaktovat jednu doménu. Instalace Azure AD Connect**  
S sestavení z února 2016 tento scénář se podporuje.

**Otázka: agenta stavu služby AD DS funguje na jádro serveru?**  
Ano. Po instalaci agenta, můžete dokončit proces registrace pomocí následující rutiny prostředí PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Otázka: AADConnect podporuje synchronizace ze dvou domén na Azure AD?**</br>
Ano, tento scénář se podporuje. Odkazovat na [více domén](active-directory-aadconnect-multiple-domains.md)
 
**Otázka: je možné mít více konektorů pro stejné domény služby Active Directory ve službě Azure AD connect?**</br> Ne, více konektorů pro ve stejné doméně AD nejsou podporovány. 

**Otázka: lze přesunout databázi Azure AD Connect z místní databáze na vzdálený server SQL?**</br> Ano, bude následující kroky obsahují obecné pokyny o tom, jak to udělat.  Právě pracujeme na podrobnější dokument, který bude brzy k dispozici.


   1. Zálohování databáze LocalDB "ADSync" nejjednodušší způsob, jak to udělat, je použít SQL Server Management Studio instalovaného na stejném počítači jako Azure AD Connect. Připojit k "(localdb)\.\ADSync" – pak zálohujte databázi ADSync
   2. Obnovit databázi "ADSync" k instanci vzdáleného SQL
   3. Nainstalujte Azure AD Connect s existující [vzdálené databáze SQL](active-directory-aadconnect-existing-database.md) odkaz ukazuje na kroky potřebné při migraci pomocí místní databáze SQL. Pokud provádíte migraci k použití vzdáleného SQL Database pak v kroku 5 tohoto procesu taky musíte zadat existující účet služby, který synchronizační služby systému Windows se spustí jako. Tento účet služby synchronizační modul je popsaný tady:</br></br>
   **Použít existující účet služby**– ve výchozím nastavení Azure AD Connect používá účet virtuální služby pro synchronizační služby používat. Pokud používáte vzdálený SQL server nebo použít proxy server, který vyžaduje ověření, musíte použít účet spravované služby nebo použijte účet služby v doméně a znát heslo. V těchto případech zadejte účet, který chcete použít. Ujistěte se, jestli uživatel, který provádí instalaci, je SA v SQL, aby bylo možné vytvořit přihlašovací jméno pro účet služby. Viz téma [Účty a oprávnění Azure AD Connect](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account).</br></br> S nejnovějším sestavením teď může databáze vzdáleně zřizovat správce SQL a pak je instalovat správce služby Azure AD Connect s oprávněními vlastníka databáze. Další informace najdete v tématu [Instalace služby Azure AD Connect pomocí oprávnění delegovaného správce SQL](active-directory-aadconnect-sql-delegation.md).

Pro zjednodušení doporučuje se, že uživatel instalaci Azure AD Connect je SA v SQL. (Ale s poslední sestavení teď můžete použít delegovaný správce SQL, jak je popsáno [zde](active-directory-aadconnect-sql-delegation.md).

## <a name="network"></a>Síť
**Otázka: je nutné bránu firewall, síťové zařízení, nebo něco jiného, který omezuje maximální dobu, po připojení můžete zůstat otevřené v mé síti. Jak dlouho mají Moje prahová hodnota vypršení časového limitu klienta se při použití Azure AD Connect?**  
Všechny sítě softwaru, fyzických zařízení nebo cokoliv jiného, který omezuje maximální dobu, po připojení může zůstat otevřené by měl používat prahovou hodnotu alespoň 5 minut (300 sekund) pro připojení mezi serverem, kde je nainstalován klient Azure AD Connect a Azure Active Directory. Toto doporučení platí také pro všechny dřív vydaných synchronizace nástroje Microsoft Identity.

**Otázka: jsou podporovány domény SLD (jedné domény štítek)?**  
Ne, Azure AD Connect místními doménovými strukturami nebo doménami pomocí domény SLD nepodporuje.

**Otázka: jsou doménových strukturách s nesouvislým oborem doménami AD podporovány?**  
Ne, Azure AD Connect nepodporuje místními doménovými strukturami obsahující nesouvislé obory názvů.

**Otázka: je "s tečkami" s názvem pro rozhraní NetBios podporovány?**  
Ne, Azure AD Connect nepodporuje místními doménovými strukturami nebo doménami, kde název NetBios obsahuje tečku "." v názvu.

**Otázka: je čistě IPv6 prostředí podporovaná?**  
Ne, Azure AD Connect nepodporuje čistý prostředí IPv6.

## <a name="federation"></a>metadata
**Otázka: Co mám dělat, když se zobrazí e-mailu, zpráva se žádostí o obnovení certifikátu Moje Office 365**  
Použijte pokyny, které je uvedené v [obnovení certifikátů](active-directory-aadconnect-o365-certs.md) dokumentu o tom, jak obnovit certifikát.

**Otázka: je nutné "Automaticky aktualizovat předávající strany" sadu O365 předávající strany. Je nutné provádět žádnou akci, když můj automaticky podpisový certifikát tokenu navyšování?**  
Použijte pokyny, které je popsané v článku [obnovení certifikátů](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Prostředí
**Otázka: je podporována přejmenování serveru po instalaci Azure AD Connect?**  
Ne. Změna názvu serveru způsobí, že synchronizační modul nebude moct připojit k databázi SQL a služba nebude možné spustit.

## <a name="identity-data"></a>Data identit
**Otázka: vybraný atribut UPN (userPrincipalName) ve službě Azure AD neodpovídá názvu UPN místní - proč?**  
Najdete v těchto článcích:

* [Uživatelská jména v Office 365, Azure nebo Intune se neshodují místní UPN nebo alternativním přihlašovacím ID](https://support.microsoft.com/en-us/kb/2523192)
* [Změny nejsou synchronizovány pomocí nástroje Azure Active Directory Sync po změně UPN uživatelský účet používat jiné federované domény](https://support.microsoft.com/en-us/kb/2669550)

Můžete také konfigurovat Azure AD umožňuje synchronizační modul aktualizovat userPrincipalName, jak je popsáno v [funkce služby Azure AD Connect sync](active-directory-aadconnectsyncservice-features.md).

**Otázka: je to, že nepodporuje logicky shodu místní AD skupiny nebo kontaktu objekty s existující objekty Azure AD skupiny nebo kontaktu?**  
Ano, bude tato logicky shodu vycházet proxyAddress.  Logicky odpovídající není podporována pro skupiny, které nejsou povoleným e-mailem.

**Otázka: je to, že nepodporuje ručně nastavit atribut ImmutableId u stávajících objektů Azure AD skupiny nebo kontaktu pevného odpovídat jej do místní AD skupiny nebo kontaktu objekty?**  
Ne, ručně nastavení atribut ImmutableId na existující objekt Azure AD skupiny nebo kontaktu na pevné shoda se aktuálně nepodporuje.

## <a name="custom-configuration"></a>Vlastní konfigurace
**Otázka: kde popsané rutiny prostředí PowerShell pro Azure AD Connect?**  
S výjimkou rutiny popsané v této lokalitě nejsou podporované jinými rutinami prostředí PowerShell nalezen ve službě Azure AD Connect pro použití zákazníka.

**Otázka: je možné použít "Serveru serveru pro export/import" nalezena v *Synchronization Service Manager* přesunutí konfigurací mezi servery?**  
Ne. Tato možnost nebude načíst všechna nastavení konfigurace a by se neměla používat. Místo toho používejte Průvodce a vytvořte základní konfigurace na druhý server, použijte editor pravidla synchronizace ke generování skriptů prostředí PowerShell přesunout všechny vlastní pravidlo mezi servery. V tématu [kyvu migrace](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**Otázka: je možné hesla do mezipaměti Azure přihlašovací stránku a můžete to možné, protože obsahuje element input pro heslo pomocí automatického dokončování = "false" atribut?**</br>
Aktuálně změna atributů HTML vstupní pole hesla, včetně automatického dokončování značky, není podporována. Funkce, která vám umožní pro vlastní Javascript, abyste mohli přidat všechny atributy, do pole heslo je aktuálně pracuje.

**Otázka: na Azure přihlašovací stránce se zobrazí uživatelská jména pro uživatele, kteří mají dříve úspěšně přihlášení.  Lze toto chování vypnout?**</br>
Aktuálně změna atributů HTML vstupní pole hesla, včetně automatického dokončování značky, není podporována. Funkce, která vám umožní pro vlastní Javascript, abyste mohli přidat všechny atributy, do pole heslo je aktuálně pracuje.

**Otázka: je způsob, jak zabránit souběžných relací?**</br>
Ne.

## <a name="auto-upgrade"></a>Automatický upgrade

**Otázka: jaké jsou výhody a důsledky použití automatické aktualizace?**</br>
Všichni zákazníci by měli povolit automatický upgrade pro jejich instalaci Azure AD Connect. Výhody se, že vždy obdrží nejnovější opravy, včetně aktualizací zabezpečení pro chyby zabezpečení, které byly nalezeny ve službě Azure AD Connect. Proces upgradu je bezproblémově a proběhne automaticky, jakmile je k dispozici nová verze. Mnoho tisíc, Azure AD Connect zákazníků použít automatický upgrade s každou novou verzi.

Proces automatického upgradu vždy nejprve vytvoří, jestli má tento nárok pro automatický upgrade instalace (Tento proces zahrnuje hledá vlastní změny pravidel, konkrétní faktorech prostředí atd.) a pokud ano, je upgrade provádět a testovat. Pokud testy naznačují, že upgrade nebyl úspěšný, se získat automaticky obnovit předchozí verzi.

V závislosti na velikosti prostředí proces může trvat několik hodin a při upgradu se stane, bude provedena žádná synchronizace mezi Windows Server AD a Azure AD.

**Otázka: zobrazila e-mailu zobrazuje oznámení, který již nefunguje Moje automatický upgrade a potřebuji nainstalovat novou verzi. Proč je potřeba to udělat?**</br>
Poslední rok, na verzi služby Azure AD Connect, který za určitých okolností může mít zakázán funkci automatického upgradu na vašem serveru byl vydán. Tento problém byl opraven v Azure AD Connect verze 1.1.750.0. Zákazníci, kteří mohou mít vliv tento problém je třeba ručně upgradovat na nejnovější verzi služby Azure AD Connect zmírnit problém. Postup při ručním upgradu, musíte stáhnout a spustit nejnovější verzi souboru AADConnect.msi.
 
-  Pokud vaše aktuální verze je starší než 1.1.750.0, musíte provést upgrade na nejnovější verzi [který můžete stáhnout zde](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
- Pokud vaše verze Azure AD Connect je 1.1.750.0 nebo novější, není nutné provádět žádnou akci zmírnit upgradu problém automaticky, jak jste už na verzi, která má pro tuto opravu. 

**Otázka: zobrazila e-mailu zobrazuje oznámení pro upgrade na nejnovější verzi a znovu povolte automatický upgrade. Počítač nachází v 1.1.654.0, je nutné upgradovat?** </br>    
Ano, musíte upgradovat na 1.1.750 nebo novější znovu povolte automatický upgrade. Zde je odkaz, který vysvětluje, jak upgradovat na novější verzi

**Otázka: zobrazila e-mailu zobrazuje oznámení pro upgrade na nejnovější verzi a znovu povolte automatický upgrade. I použili prostředí PowerShell povolit automatický upgrade, stále nutné nainstalovat nejnovější verzi?**</br>    
Ano, stále musíte upgradovat na verzi 1.1.750.0 nebo novější. Povolení automatického upgradu služby pomocí prostředí PowerShell není problém automatického upgradu z verzí před 1.1.750 zmírnit

**Otázka: Chcete provést upgrade na novější verzi, ale nejste si jisti, kteří nainstalovali Azure AD Connect a nemáme uživatelské jméno a heslo.  To potřebujeme?**</br>
Nemusíte znát uživatelské jméno a heslo, které bylo původně použít k upgradu Azure AD Connect – všechny účet Azure AD, který má roli globálního správce lze použít.

**Otázka: jak najít, kterou verzi služby Azure AD Connect jsem na?**</br>   
Pokud chcete ověřit, která verze služby Azure AD Connect je nainstalovaná na serveru, přejděte do ovládacích panelů a vyhledat nainstalovaná verze systému Microsoft Azure AD Connect v programy > programy a funkce.":

![verze](media/active-directory-aadconnect-faq/faq1.png)

**Otázka: jak provádět upgrade na nejnovější verzi služby AADConnect?**</br>    
To [článku](active-directory-aadconnect-upgrade-previous-version.md) vysvětluje, jak upgradovat na novější verzi. 

**Otázka: jsme již provedli upgrade na nejnovější verzi služby AADConnect poslední rok, potřebujeme upgrade znovu?**</br> Azure AD Connect týmy provede časté aktualizace služby a je důležité, aby se používala aktuální pomocí nejnovější verze, abyste mohli využívat výhod oprav chyb a aktualizace zabezpečení, jakož i nové funkce serveru. Pokud povolíte automatický upgrade softwaru verze se aktualizují automaticky. Najít historie verzí služby Azure AD Connect, postupujte podle to [odkaz](active-directory-aadconnect-version-history.md).

**Otázka: jak dlouho bude trvat k provedení upgradu a jaký bude dopad na Moji uživatelé?**</br>    
Čas potřebný k upgradu závisí na velikosti vašeho klienta a pro větší organizace může být vhodné to provést v večer nebo o víkendech. Během upgradu žádná aktivita synchronizace probíhá.

**Otázka: I domníváte po upgradu na službu AADConnect, ale na portálu Office stále uvádí DirSync.  Proč je to?**</br>    
Tým Office pracuje, chcete-li získat aktualizace portálu Office tak, aby odrážela aktuální název produktu – nevyjadřuje které synchronizační nástroj služby používáte.

**Otázka: po kontrole stavu automatický Upgrade a zobrazuje "pozastaveno". Proč ji je pozastaveno? By měl povolit ji?**</br>     
Chyby byla zavedena v předchozí verzi, která za určitých okolností by nechte stav upgradu automaticky nastavena na "pozastavenou". Ruční povolení ho je technicky možné ale komplexní několik kroků, by vyžadovaly, takže nainstalujte nejnovější verzi služby Azure AD Connect je nejlepší věcí, které můžete provést

**Otázka: Moje společnost má požadavky na správu striktní změny a chcete řídit, kdy je vložena. Můžete řídit, kdy se spustí automatický Upgrade?**</br> Ne, je dnes žádné takové funkce, tato funkce je něco, co se vyhodnocení pro budoucí použití.

**Otázka: bude získat e-mailu, pokud se nezdařilo automatický upgrade? Jak bude vědět, že bylo úspěšné.**</br>     
Nebudete upozorněni, v důsledku upgradu, tato funkce je něco, co se vyhodnocení pro budoucí použití.

**Q:do publikujete časová osa, když plánujete push na automatické upgrady?**</br>    
Automatický upgrade je prvním krokem v procesu verze na novější verzi, tak pokaždé, když je nová verze, automatické upgrady nabídnou. Novější verze služby Azure AD Connect jsou předběžně oznámené v [přehled Azure AD](../../active-directory/whats-new.md).

**Otázka: automatický upgrade upgradu AAD Connect Health?**</br>   Ano, upgrade automaticky upgraduje i AAD Connect Health

**Otázka: máte také automatický upgrade AAD Connect servery v pracovní režimu?**</br>   
Ne, které nelze automatický upgrade server služby Azure AD Connect, který je v pracovním režimu.

**Otázka: Pokud automatický Upgrade selže a server AAD Connect se nespustí, co je třeba udělat?**</br>   
Ve výjimečných případech se nespustí služba Azure AD Connect po provedení upgradu. V těchto případech restartujte server, které obvykle opravy problému. Pokud služba Azure AD Connect stále nespustí, otevřete lístek podpory. Tady je [odkaz](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) to vysvětluje, jak to provést. 

**Otázka: nejste si jisti, jaké jsou rizika při upgradu na novější verzi služby Azure AD Connect. Můžete na pomoc při upgradu volat?**</br>
Pokud potřebujete pomoc, upgrade na novější verzi služby Azure AD Connect, otevřete lístek podpory, zde je [odkaz](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) který ukazuje, jak to udělat.

## <a name="troubleshooting"></a>Řešení potíží
**Otázka: jak můžete získat pomoc s Azure AD Connect?**

[Znalostní bázi Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Vyhledejte Microsoft Knowledge Base (KB) pro technické řešení pro běžné problémy opravu o podpoře pro Azure AD Connect.

[Fóra Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Můžete vyhledat a procházet technical otázky a odpovědi od komunity nebo položte svou vlastní otázku kliknutím [zde](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Jak získat podporu pro Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)



