---
title: Nejčastější dotazy k Azure Active Directory Connect | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure AD Connect.
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
ms.openlocfilehash: 29c0ae8ec210356f6027a46ed01f2a7126ea4a49
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644727"
---
# <a name="azure-active-directory-connect-faq"></a>Nejčastější dotazy k Azure Active Directory Connect

## <a name="general-installation"></a>Obecná instalace

**Otázka: Jak můžu posílit Azure AD Connect Server, aby se snížila plocha pro útok na zabezpečení?**

Společnost Microsoft doporučuje posílit Azure AD Connect Server, aby se snížila plocha pro útok na zabezpečení pro tuto kritickou součást vašeho IT prostředí.  Podle následujících doporučení se sníží rizika zabezpečení vaší organizace.

* Nasazení Azure AD Connect na serveru připojeném k doméně a omezení přístupu pro správu k správcům domény nebo jiným důkladně kontrolovaným skupinám zabezpečení

Další informace najdete v následujících tématech: 

* [Zabezpečení skupin správců](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Zabezpečení předdefinovaných účtů správců](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Vylepšení a udržování zabezpečení snížením počtu napadených ploch](/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Snížení prostoru pro útoky na službu Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**Otázka: instalace bude fungovat, pokud má globální správce Azure Active Directory (Azure AD) povolený dvojúrovňové ověřování (2FA)?**  
Od buildu únor 2016 je tento scénář podporován.

**Otázka: existuje způsob, jak nainstalovat Azure AD Connect bezobslužné instalace?**  
Instalace Azure AD Connect se podporuje jenom v případě, že použijete Průvodce instalací nástroje. Bezobslužná, bezobslužná instalace není podporována.

**Otázka: Mám doménovou strukturu, ve které nelze kontaktovat jednu doménu. Návody nainstalovat Azure AD Connect?**  
Od buildu únor 2016 je tento scénář podporován.

**Otázka: umožňuje Agent stavu Azure Active Directory Domain Services (Azure služba AD DS) pracovat v jádru serveru?**  
Ano. Po instalaci agenta můžete dokončit proces registrace pomocí následující rutiny prostředí PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Otázka: podporuje Azure AD Connect synchronizaci ze dvou domén do služby Azure AD?**  
Ano, tento scénář je podporován. Přečtěte si [více domén](how-to-connect-install-multiple-domains.md).
 
**Otázka: je možné, že máte více konektorů pro stejnou doménu služby Active Directory v Azure AD Connect?**  
Ne, víc konektorů pro stejnou doménu AD se nepodporuje. 

**Otázka: Mohu přesunout databázi Azure AD Connect z místní databáze do instance vzdáleného SQL Server?**   
Ano, následující kroky poskytují obecné pokyny k tomu, jak to provést. V současné době pracujeme na podrobnějším dokumentu.
1. Zálohujte databázi LocalDB ADSync.
Nejjednodušší způsob, jak to provést, je použít SQL Server Management Studio nainstalované na stejném počítači jako Azure AD Connect. Připojte se k *(LocalDB) .\ADSync* a pak zálohujte databázi AdSync.

2. Obnovte databázi ADSync do instance vzdáleného SQL Server.

3. Nainstalujte Azure AD Connect do existující [vzdálené databáze SQL](how-to-connect-install-existing-database.md).
   Tento článek ukazuje, jak provést migraci na používání místní databáze SQL. Pokud provádíte migraci na používání vzdálené databáze SQL, v kroku 5 tohoto procesu musíte také zadat existující účet služby, pod kterým bude služba Windows Sync běžet. Tento účet služby synchronizačního modulu je popsaný tady:
   
      **Použít existující účet služby**: ve výchozím nastavení Azure AD Connect používá účet virtuální služby pro synchronizační služby, které se mají použít. Pokud používáte instanci vzdáleného SQL Server nebo používáte proxy server, který vyžaduje ověření, použijte účet spravované služby nebo účet služby v doméně a zjistěte heslo. V těchto případech zadejte účet, který chcete použít. Ujistěte se, že uživatelé, kteří používají instalaci, jsou správci systému v SQL, aby bylo možné vytvořit přihlašovací údaje pro účet služby. Další informace najdete v tématu [Azure AD Connect účty a oprávnění](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      S nejnovějším sestavením teď může databáze vzdáleně zřizovat správce SQL a pak je instalovat správce služby Azure AD Connect s oprávněními vlastníka databáze. Další informace najdete v tématu [instalace Azure AD Connect pomocí oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md).

Chcete-li zachovat věci jednoduché, doporučujeme, aby uživatelé, kteří Azure AD Connect, byli správci systému v SQL. S nejnovějšími sestaveními teď ale můžete používat delegované správce SQL, jak je popsáno v tématu [instalace Azure AD Connect pomocí oprávnění delegovaného správce SQL](how-to-connect-install-sql-delegation.md).

**Otázka: Jaké jsou některé z osvědčených postupů z tohoto pole?**  

Tady je informační dokument, který představuje některé z osvědčených postupů, které vyvinula technická, podporovaná a naši konzultanty v průběhu let.  Zobrazí se v seznamu odrážek, na které se dá rychle odkazovat.  I když se tento seznam bude považovat za komplexní, můžou existovat další osvědčené postupy, které by se zatím neudělaly v seznamu.

- Pokud používáte úplný příkaz SQL, měl by zůstat místní vs. vzdálená.
    - Menší počet segmentů směrování
    - Jednodušší řešení potíží
    - Méně složitosti
    - Je potřeba určit prostředky do SQL a vyhradit režii pro Azure AD Connect a operační systém.
- Nepoužívat proxy server, pokud je to možné, pokud nemůžete proxy obejít, musíte zajistit, aby byla hodnota časového limitu větší než 5 minut.
- Pokud je vyžadován proxy server, je nutné přidat proxy server do souboru machine.config
- Mějte na paměti, že místní úlohy a údržba SQL a jak budou mít dopad na Azure AD Connect – zejména opětovné indexování
- Zajistěte, aby se služba DNS mohla přeložit externě.
- Zajistěte, aby byly [specifikace serveru](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) na doporučení bez ohledu na to, jestli používáte fyzické nebo virtuální servery.
- Ujistěte se, že pokud používáte virtuální server, který je vyhrazený pro požadované prostředky
- Ujistěte se, že máte konfiguraci disku a disku, která vyhovuje osvědčeným postupům pro SQL Server
- Instalace a konfigurace Azure AD Connect Health pro monitorování
- Použijte práh odstranění, který je integrovaný do Azure AD Connect.
- Pečlivě zkontrolujte aktualizace vydaných verzí, které se připravují pro všechny změny a nové atributy, které se dají přidat.
- Zálohovat vše
    - Záložní klíče
    - Pravidla synchronizace zálohování
    - Konfigurace záložního serveru
    - SQL Database zálohy
- Ujistěte se, že neexistují žádní agenti pro zálohování třetích stran, kteří zálohují SQL bez služby SQL VSS Writer (běžné na virtuálních serverech se snímky třetích stran).
- Omezení množství vlastních synchronizačních pravidel, která se používají při přidávání složitosti
- Považovat servery Azure AD Connect jako servery vrstvy 0
- Leery se na úpravu pravidel synchronizace cloudu bez skvělého porozumění dopadu a správných obchodních ovladačů.
- Ujistěte se, že jsou otevřené správné porty adresy URL a brány firewall pro podporu Azure AD Connect a Azure AD Connect Health
- Využití filtrovaného atributu cloudu k řešení problémů a zabránění fiktivním objektům
- Pomocí přípravného serveru se ujistěte, že k zajištění konzistence mezi servery používáte konfigurační dokument Azure AD Connect.
- Pracovní servery by měly být v samostatných datacentrech (fyzické umístění
- Pracovní servery nejsou určeny jako řešení vysoké dostupnosti, ale můžete mít několik pracovních serverů.
- Zavedení "prodlevy" přípravného serveru může zmírnit některé možné výpadky v případě chyby.
- Nejprve otestujte a ověřte všechny upgrady na přípravném serveru.
- Před přepnutím na pracovní server vždy ověřte export.  Využití přípravného serveru pro úplné importy a úplné synchronizace, aby se snížil dopad na firmu
- Udržování konzistence verzí mezi Azure AD Connect servery co nejvíce 

**Otázka: můžu Azure AD Connect vytvořit účet konektoru Azure AD na počítači pracovní skupiny?**
No.  Aby bylo možné Azure AD Connect automaticky vytvořit účet konektoru služby Azure AD, musí být počítač připojený k doméně.  

## <a name="network"></a>Síť
**Otázka: Mám bránu firewall, síťové zařízení nebo něco jiného, co omezuje dobu, po kterou mohou připojení zůstat otevřená v síti. Co má prahová hodnota časového limitu na straně klienta při použití Azure AD Connect?**  
Veškerý síťový software, fyzická zařízení nebo cokoli jiného, co omezuje maximální dobu, po kterou můžou připojení zůstat otevřená, by mělo pro připojení mezi serverem, na kterém je nainstalovaný klient Azure AD Connect a Azure Active Directory, používat prahovou hodnotu nejméně pět minut (300 sekund). Toto doporučení platí také pro všechny dříve vydané nástroje Microsoft Synchronizace identity.

**Otázka: jsou podporovány domény s jedním popiskem (SLDs)?**  
Důrazně doporučujeme oproti této konfiguraci sítě ([Viz článek](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), pokud je v případě, že konfigurace sítě pro doménu s jedinou úrovní funguje správně, podporuje Azure AD Connect synchronizace s jednou doménou s názvem.

**Otázka: jsou doménové struktury s nesouvislými doménami AD podporované?**  
Ne, Azure AD Connect nepodporuje místní doménové struktury, které obsahují nesouvislé obory názvů.

**Otázka: jsou podporované názvy rozhraní NetBIOS s tečkami?**  
Ne, Azure AD Connect nepodporuje místní doménové struktury ani domény, kde název rozhraní NetBIOS obsahuje tečku (.).

**Otázka: je podporováno čistě prostředí IPv6?**  
Ne, Azure AD Connect nepodporuje čistě prostředí IPv6.

**Q:I Používejte prostředí s více doménovými strukturami a síť mezi oběma doménovými strukturami používá překlad adres (NAT). Používá se Azure AD Connect mezi těmito dvěma doménovými strukturami?**</br>
Ne, použití Azure AD Connect přes překlad adres (NAT) se nepodporuje. 

## <a name="federation"></a>metadata
**Otázka: co mám dělat, když obstávám e-mail s výzvou k prodloužení Microsoft 365ho certifikátu?**  
Pokyny k obnovení certifikátu najdete v tématu [obnovení certifikátů](how-to-connect-fed-o365-certs.md).

**Otázka: Mám nastavenou možnost automaticky aktualizovat předávající stranu u Microsoft 365 předávající strany. Musím provést jakoukoli akci, když se podpisový certifikát tokenu automaticky zaregistruje?**  
Postupujte podle pokynů uvedených v článku [obnovení certifikátů](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Prostředí
**Otázka: je podporováno přejmenování serveru po instalaci nástroje Azure AD Connect?**  
No. Změna názvu serveru vykresluje synchronizační modul se nemůže připojit k instanci databáze SQL a službu nelze spustit.

**Otázka: jsou podporovaná pravidla synchronizace služby NGC (Next Generation Cryptographic) podporovaná na počítači s podporou standardu FIPS?**  
No.  Není to podporováno.

**Č. Pokud je v Azure Portal zakázané synchronizované zařízení (například: HAADJ), proč se znovu aktivuje?**<br>
Synchronizovaná zařízení můžou být vytvořená nebo spravovaná místně. Pokud je synchronizované zařízení povolené místně, může se v Azure Portal znovu povolit, i když ho správce předtím zakázal. Pokud chcete zakázat synchronizované zařízení, použijte k zakázání účtu počítače místní službu Active Directory.

**Č. Když zablokujem přihlášení uživatele na Microsoft 365 nebo na portálu Azure AD pro synchronizované uživatele, proč se při opětovném přihlášení odblokuje?**<br>
Synchronizovaná uživatelé mohou být vytvořeni nebo v místním prostředí. Pokud je účet povolený místně, může odblokovat blok přihlášení umístěný správcem.

## <a name="identity-data"></a>Data identity
**Otázka: Proč atribut userPrincipalName (UPN) ve službě Azure AD neodpovídá místnímu hlavnímu názvu uživatele (UPN)?**  
Informace najdete v těchto článcích:

* [Uživatelská jména v Microsoft 365, Azure nebo Intune se neshodují s místním hlavním názvem uživatele (UPN) nebo alternativním přihlašovacím ID.](https://mskb.pkisolutions.com/kb/2523192)
* [Po změně hlavního názvu uživatele (UPN) uživatelského účtu na používání jiné federované domény nejsou změny synchronizované nástrojem Azure Active Directory Sync.](https://mskb.pkisolutions.com/kb/2669550)

Můžete taky nakonfigurovat službu Azure AD tak, aby synchronizačnímu modulu umožnila aktualizovat hlavní název uživatele (UPN), jak je popsáno v tématu [funkce služby Azure AD Connect Sync](how-to-connect-syncservice-features.md).

**Otázka: je podporováno, aby se poshodovala s místní skupinou Azure AD nebo objektem kontaktu s existujícím objektem skupiny nebo kontaktu služby Azure AD?**  
Ano, Tato tichá shoda je založena na proxyAddress. Pro skupiny, které nejsou povolené poštou, se nepodporují předběžné porovnání.

**Otázka: je podporováno ruční nastavení atributu ImmutableId ve stávající skupině nebo kontaktu objektu služby Azure AD tak, aby odpovídala místní skupině nebo objektu kontaktu služby Azure AD?**  
Ne, ručně nastavovat atribut ImmutableId u stávající skupiny Azure AD nebo objektu kontaktu na nepodporovaný objekt se v tuto chvíli nepodporuje.

## <a name="custom-configuration"></a>Vlastní konfigurace
**Otázka: kde jsou zdokumentovány rutiny prostředí PowerShell pro Azure AD Connect zdokumentované?**  
S výjimkou rutin, které jsou popsány v tomto webu, jiné rutiny prostředí PowerShell nalezené v Azure AD Connect nejsou podporovány pro použití zákazníkem.

**Otázka: je možné použít možnost Import serveru/import serveru, která se nachází v Synchronization Service Manager pro přesunutí konfigurace mezi servery?**  
No. Tato možnost nenačte všechna nastavení konfigurace a neměla by se používat. Místo toho použijte průvodce k vytvoření základní konfigurace na druhém serveru a pomocí editoru pravidla synchronizace vygenerujte skripty PowerShellu pro přesunutí libovolného vlastního pravidla mezi servery. Další informace najdete v tématu věnovaném [migraci](how-to-upgrade-previous-version.md#swing-migration).

**Otázka: je možné ukládat hesla do mezipaměti pro přihlašovací stránku Azure a může být ukládání do mezipaměti znemožněno, protože obsahuje element vstupu hesla s atributem *AutoComplete = "false"* ?**  
V současné době není podporována změna atributů HTML pole **heslo** , včetně značky automatického dokončování. V současné době pracujeme na funkci, která umožňuje vlastní JavaScript, který umožňuje přidat libovolný atribut do pole **heslo** .

**Otázka: přihlašovací stránka Azure zobrazuje uživatelská jména uživatelů, kteří se úspěšně přihlásili. Může být toto chování vypnuté?**  
V současné době není podporována změna atributů HTML pole pro zadání **hesla** , včetně značky automatického dokončování. V současné době pracujeme na funkci, která umožňuje vlastní JavaScript, který umožňuje přidat libovolný atribut do pole **heslo** .

**Otázka: existuje způsob, jak zabránit souběžným relacím?**  
No.

## <a name="auto-upgrade"></a>Automatický upgrade

**Otázka: Jaké jsou výhody a důsledky použití automatického upgradu?**  
Doporučujeme všem zákazníkům povolit pro instalaci Azure AD Connect automatický upgrade. Výhodou je, že vždycky obdržíte nejnovější opravy, včetně aktualizací zabezpečení pro chyby zabezpečení, které byly nalezeny v Azure AD Connect. Proces upgradu je bezproblémově a probíhá automaticky, jakmile bude k dispozici nová verze. Řada tisíc Azure AD Connect zákazníkům používá automatický upgrade při každé nové vydané verzi.

Proces automatického upgradu vždy nejprve zjistí, zda je instalace způsobila pro automatický upgrade. Pokud má nárok, upgrade se provede a otestuje. Tento proces také zahrnuje hledání vlastních změn pravidel a specifických faktorů prostředí. Pokud testy ukazují, že upgrade je neúspěšný, předchozí verze se obnoví automaticky.

V závislosti na velikosti prostředí může proces trvat několik hodin. Během upgradu probíhá žádná synchronizace mezi službami Windows Server Active Directory a Azure AD.

**Otázka: zobrazil jsem se e-mail s oznámením, že můj automatický upgrade už nefunguje a potřebuji nainstalovat novou verzi. Proč je potřeba to udělat?**  
Minulý rok jsme vydali verzi Azure AD Connect, že za určitých okolností může být funkce automatického upgradu na vašem serveru zakázaná. Vyřešili jsme problém v Azure AD Connect verze 1.1.750.0. Pokud jste to ovlivnili problémem, můžete ho zmírnit spuštěním skriptu PowerShellu k jeho opravě nebo ručním upgradem na nejnovější verzi Azure AD Connect. 

Pokud chcete spustit skript PowerShellu, [Stáhněte si skript](/samples/browse/?redirectedfrom=TechNet-Gallery) a spusťte ho na serveru Azure AD Connect v okně pro správu prostředí PowerShell. Pokud se chcete dozvědět, jak skript spustit, podívejte se na [Toto krátké video](https://aka.ms/repairaadcau).

Chcete-li provést ruční upgrade, je nutné stáhnout a spustit nejnovější verzi AADConnect.msi souboru.
 
-  Pokud je aktuální verze starší než 1.1.750.0, [Stáhněte a upgradujte na nejnovější verzi](https://www.microsoft.com/download/details.aspx?id=47594).
- Pokud je vaše verze Azure AD Connect 1.1.750.0 nebo novější, nevyžaduje se žádná další akce. Už používáte verzi, která obsahuje opravu automatického upgradu. 

**Otázka: obdržel jsem e-mail s oznámením, že chci upgradovat na nejnovější verzi a znovu povolit automatický upgrade. Používám 1.1.654.0 verze. Potřebuji upgradovat?**  
Ano, pokud chcete znovu povolit automatický upgrade, musíte upgradovat na verzi 1.1.750.0 nebo novější. [Stáhněte a upgradujte na nejnovější verzi](https://www.microsoft.com/download/details.aspx?id=47594).

**Otázka: obdržel jsem e-mail s oznámením, že chci upgradovat na nejnovější verzi a znovu povolit automatický upgrade. Pokud jsem k povolení automatického upgradu použil (a) PowerShell, musím si I nadále instalovat nejnovější verzi?**  
Ano, stále musíte upgradovat na verzi 1.1.750.0 nebo novější. Povolení služby automatického upgradu pomocí prostředí PowerShell nesnižuje riziko potíží s automatickým upgradem nalezených ve verzích před 1.1.750.0.

**Otázka: Chci upgradovat na novější verzi, ale nejste si jisti, kdo Azure AD Connect, a nemáme uživatelské jméno a heslo. Potřebujeme to?**
Nemusíte znát uživatelské jméno a heslo, které se původně používaly k upgradu Azure AD Connect. Použijte libovolný účet služby Azure AD, který má roli globálního správce.

**Otázka: Jak můžu zjistit, kterou verzi Azure AD Connect používám?**  
Chcete-li ověřit, která verze Azure AD Connect je nainstalována na vašem serveru, klikněte na položku Ovládací panely a vyhledejte nainstalovanou verzi Microsoft Azure AD připojit výběrem **Možnosti programy**  >  **programy a funkce**, jak je znázorněno zde:

![Azure AD Connect verze v Ovládacích panelech](./media/reference-connect-faq/faq1.png)

**Otázka: Návody upgradu na nejnovější verzi Azure AD Connect?**  
Informace o tom, jak upgradovat na nejnovější verzi, najdete v tématu [Azure AD Connect: upgrade z předchozí verze na nejnovější](how-to-upgrade-previous-version.md)verzi. 

**Otázka: provedli jsme upgrade na nejnovější verzi Azure AD Connect minulý rok. Musíme provést upgrade znovu?**  
Tým Azure AD Connect provádí časté aktualizace služby. Aby bylo možné využít opravy chyb a aktualizace zabezpečení a nové funkce, je důležité udržovat Server v aktuálním stavu pomocí nejnovější verze. Pokud povolíte automatický upgrade, vaše verze softwaru se automaticky aktualizuje. Historii verze Azure AD Connect najdete v tématu [Azure AD Connect: historie](reference-connect-version-history.md)verzí.

**Otázka: jak dlouho trvá provedení upgradu a jaký je dopad na moje uživatele?**  
Čas potřebný k upgradu závisí na velikosti vašeho tenanta. U větších organizací může být nejlepší provést upgrade za večer nebo víkend. Během upgradu neprobíhá žádná synchronizační aktivita.

**Otázka: beru na Azure AD Connect, ale na portálu Office se stále zmiňuje DirSync. Proč je to?**  
Tým Office pracuje na tom, aby získal aktualizace portálu Office, aby odrážel aktuální název produktu. Neodráží to, který nástroj pro synchronizaci používáte.

**Otázka: můj stav automatického upgradu říká "pozastaveno". Proč je pozastavená? Mám ji povolit?**  
Chyba byla představena v předchozí verzi, která za určitých okolností ponechá stav automatického upgradu nastaven na hodnotu pozastaveno. Ruční aktivace je technicky možná, ale vyžaduje několik složitých kroků. Nejlepší je, že můžete nainstalovat nejnovější verzi Azure AD Connect.

**Otázka: Moje společnost má přísné požadavky na správu změn a chci určit, kdy se má nastavit. Můžu řídit, kdy se spustí automatický upgrade?**  
Ne, tato funkce ještě není k dispozici. Tato funkce se vyhodnocuje v budoucí verzi.

**Otázka: přijde mi e-mail, když se automatický upgrade nepovedl? Jak zjistím, že proběhla úspěšně?**  
Nebudete upozorněni na výsledek upgradu. Tato funkce se vyhodnocuje v budoucí verzi.

**Otázka: Chystáte se publikovat časovou osu, když plánujete automatické upgrady?**  
Automatický upgrade je prvním krokem v procesu vydávání verzí novější verze. Pokaždé, když je k dispozici nová verze, upgrady se automaticky přidávají. V plánu [Azure AD](../fundamentals/whats-new.md)jsou předem ohlášeny novější verze Azure AD Connect.

**Otázka: provede upgrade také Azure AD Connect Health?**  
Ano, automatický upgrade také upgraduje Azure AD Connect Health.

**Otázka: provedete také automatické upgrade Azure AD Connectch serverů v pracovním režimu?**  
Ano, můžete automaticky upgradovat Azure AD Connect Server, který je v pracovním režimu.

**Otázka: Pokud se automatický upgrade nepodaří a můj Azure AD Connect Server nezačne, co mám dělat?**  
Ve výjimečných případech se služba Azure AD Connect po provedení upgradu nespustí. V těchto případech je obvykle problém vyřešen restartováním serveru. Pokud se služba Azure AD Connect ještě nespustí, otevřete lístek podpory. Další informace najdete v tématu [Vytvoření žádosti o službu pro kontaktování podpory Microsoft 365](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support). 

**Otázka: Nejsem si jisti, jaká rizika jsou při upgradu na novější verzi Azure AD Connect. Můžete zavolat mi na pomoc s upgradem?**  
Pokud potřebujete pomoc s upgradem na novější verzi Azure AD Connect, otevřete lístek podpory v tématu [Vytvoření žádosti o službu, aby se kontaktovala Microsoft 365 podpora](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support).

## <a name="operational-best-practice"></a>Provozní osvědčený postup    
Níže jsou uvedeny některé osvědčené postupy, které byste měli implementovat při synchronizaci mezi službou Windows Server Active Directory a Azure Active Directory.

**Použít Multi-Factor Authentication u všech synchronizovaných účtů** Azure AD Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím a současně zachovává jednoduchost uživatelů. Poskytuje dodatečné zabezpečení tím, že vyžaduje druhou formu ověřování a zajišťuje silné ověřování prostřednictvím řady snadno použitelných metod ověřování. Na základě rozhodnutí týkajících se konfigurace, která správce provede, se uživatelé můžou nebo nemusí vyvolávat na MFA. Další informace o MFA získáte tady: https://www.microsoft.com/security/business/identity/mfa?rtc=1

**Postupujte podle pokynů pro zabezpečení Azure AD Connect serveru** . Azure AD Connect Server obsahuje kritická data identity a měla by se považovat za součást vrstvy 0, jak je popsáno v [modelu vrstvy správy služby Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material). Přečtěte si prosím naše [pokyny k zabezpečení AADConnect serveru](./how-to-connect-install-prerequisites.md#azure-ad-connect-server).

**Povolit kosmetice pro detekci nevrácených přihlašovacích údajů** Synchronizace hodnot hash hesel taky umožňuje [zjišťování nevrácených přihlašovacích údajů](../identity-protection/concept-identity-protection-risks.md) pro vaše hybridní účty. Společnost Microsoft spolupracuje s tmavými webovými výzkumníky a orgány činnými v oblasti a hledá veřejně dostupné páry uživatelského jména a hesla. Pokud některý z těchto párů odpovídá vašim uživatelům, je přidružený účet přesunutý na vysoké riziko. 


## <a name="troubleshooting"></a>Řešení potíží
**Otázka: Jak můžu získat pomoc s Azure AD Connect?**

[Hledání ve znalostní bázi Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Prohledejte v článku znalostní báze KB o běžných problémech s opravou chyb týkajících se podpory Azure AD Connect.

[Microsoft Q&Stránka s otázkou pro Azure Active Directory](/answers/topics/azure-active-directory.html)

* Vyhledejte technické dotazy a odpovědi nebo položte své dotazy na [komunitu Azure AD](/answers/topics/azure-active-directory.html).

[Získání podpory pro Azure AD](../fundamentals/active-directory-troubleshooting-support-howto.md)

**Otázka: Proč se mi zobrazují události 6311 a 6401 po chybách kroku synchronizace?**

V případě událostí 6311- **v serveru došlo k neočekávané chybě při zpětném volání** a 6401- **kontroler agenta pro správu zjistil neočekávanou chybu** – jsou vždy protokolovány po chybě kroku synchronizace. Chcete-li tyto chyby vyřešit, je nutné vyčistit chyby kroku synchronizace.  Další informace najdete v tématu [řešení chyb při synchronizaci](tshoot-connect-sync-errors.md) a [odstraňování potíží s synchronizací objektů pomocí Azure AD Connect synchronizace](tshoot-connect-objectsync.md) .