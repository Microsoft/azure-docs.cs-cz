---
title: Nejčastější dotazy k ochraně heslem v místní službě Azure AD
description: Přečtěte si nejčastější dotazy k ochraně heslem Azure AD v místním prostředí Active Directory Domain Services.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80990854fd0c584d8e6582fdf35108e67d9202b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625123"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Nejčastější dotazy k ochraně hesel Azure AD

V této části najdete odpovědi na mnoho nejčastějších dotazů týkajících se ochrany heslem Azure AD.

## <a name="general-questions"></a>Obecné otázky

**Otázka: Jaké pokyny by se měly vydávat uživatelům při výběru zabezpečeného hesla?**

Aktuální pokyny Microsoftu k tomuto tématu najdete na následujícím odkazu:

[Pokyny k heslu Microsoftu](https://www.microsoft.com/research/publication/password-guidance)

**Otázka: je místní ochrana heslem Azure AD podporovaná v jiných než veřejných cloudech?**

Místní ochrana heslem Azure AD je podporovaná ve veřejném cloudu a v Arlington cloudu. Nebylo oznámeno žádné datum k dostupnosti v jiných cloudech.

Portál Azure AD umožňuje úpravy místní ochrany heslem pro konfiguraci služby Windows Server Active Directory i v nepodporovaných cloudech. Tyto změny budou trvalé, ale jinak se neprojeví. Registrace místních agentů proxy nebo doménových struktur není podporovaná v nepodporovaných cloudech a všechny takové pokusy o registraci selžou vždycky.

**Otázka: Jak můžu použít výhody ochrany hesel Azure AD pro podmnožinu místních uživatelů?**

Nepodporováno Po nasazení a povolení ochrany heslem Azure AD nezáleží na tom, že všichni uživatelé získají stejné výhody zabezpečení.

**Otázka: Jaký je rozdíl mezi změnou hesla a nastavením hesla (nebo resetováním)?**

Změna hesla je v případě, že uživatel po prokázání, že má informace o starém hesle, zvolí nové heslo. Například změna hesla je to, co se stane, když se uživatel přihlásí k Windows, a zobrazí se výzva k výběru nového hesla.

Sada hesel (někdy označované jako resetování hesla) je v případě, že správce nahradí heslo k účtu novým heslem, například pomocí nástroje pro správu Uživatelé a počítače služby Active Directory. Tato operace vyžaduje vysokou úroveň oprávnění (obvykle správce domény) a osoba provádějící tuto operaci obvykle nemá znalosti o původním hesle. Scénáře technické podpory často provádějí sady hesel, například při pomoci uživateli, který zapomněl heslo. Při prvním vytváření nového uživatelského účtu s heslem se zobrazí také události nastavování hesel.

Zásady ověřování hesla se chovají stejně, bez ohledu na to, jestli se provádí změna nebo nastavení hesla. Služba agenta řadiče domény Azure AD pro ochranu hesel zaznamenává různé události, které vás informují o tom, jestli byla operace změny nebo nastavení hesla hotová.  Viz téma [monitorování a protokolování ochrany heslem služby Azure AD](./howto-password-ban-bad-on-premises-monitor.md).

**Otázka: ochrana heslem Azure AD po instalaci ověří stávající hesla?**

Ochrana heslem v Azure AD může vyhovět zásadám hesla jenom pro hesla ve formě prostého textu během operace změny nebo nastavení hesla. Jakmile je heslo přijímáno službou Active Directory, budou trvalé jenom hodnoty hash tohoto hesla specifické pro ověřování. Heslo nešifrovaných textů není nikdy zachované, proto ochrana heslem Azure AD nemůže ověřit stávající hesla.

Po počátečním nasazení ochrany heslem služby Azure AD začnou všichni uživatelé a účty začít používat heslo ověřované ochranou hesel Azure AD, jako by jejich stávající hesla normálně prošla časem. V případě potřeby tento proces můžete zrychlit jednorázovým ručním vypršením hesla uživatelských účtů.

Účty nakonfigurované s heslem bez platnosti hesla nikdy nebudou nuceně měnit heslo, pokud neproběhne ruční vypršení platnosti.

**Otázka: Proč se při pokusu o nastavení slabého hesla pomocí modulu snap-in Správa uživatelů a počítačů služby Active Directory zaznamenaly duplicitní události odmítnutí hesla?**

Modul snap-in Správa uživatelů a počítačů služby Active Directory se nejprve pokusí nastavit nové heslo pomocí protokolu Kerberos. Po selhání se modul snap-in postará o nastavení hesla pomocí staršího protokolu (SAM RPC) (používané konkrétní protokoly nejsou důležité). Pokud se nové heslo považuje za slabé pomocí ochrany heslem služby Azure AD, toto chování modulu snap-in bude mít za následek, že budou protokolovány dvě sady událostí odmítnutí resetování hesla.

**Otázka: Proč se události ověřování hesla ochrany heslem služby Azure AD zaprotokolují s prázdným uživatelským jménem?**

Služba Active Directory podporuje možnost testování hesla, aby bylo možné zjistit, zda splňuje aktuální požadavky na složitost hesla domény, například pomocí rozhraní [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) API. Když se heslo tímto způsobem ověří, testování zahrnuje taky ověřování v produktech založených na filtrech hesel, jako je třeba ochrana heslem Azure AD. uživatelská jména předaná do dané knihovny DLL filtru hesel ale budou prázdná. V tomto scénáři ochrana heslem Azure AD stále ověří heslo pomocí zásad hesel v současné době a vydá zprávu protokolu událostí pro zachycení výsledku, ale zpráva protokolu událostí bude mít prázdná pole uživatelského jména.

**Otázka: je podporována instalace ochrany heslem služby Azure AD vedle jiných produktů založených na filtrech hesel?**

Ano. Podpora více registrovaných knihoven DLL filtru hesel je základní funkcí Windows, která není specifická pro ochranu heslem Azure AD. Před přijetím hesla musí všechny knihovny DLL registrovaných filtrů hesel souhlasit.

**Otázka: Jak můžu nasadit a nakonfigurovat ochranu hesel Azure AD v prostředí služby Active Directory bez použití Azure?**

Nepodporováno Ochrana heslem Azure AD je funkce Azure, která podporuje rozšíření do místního prostředí Active Directory.

**Otázka: Jak mohu změnit obsah zásady na úrovni služby Active Directory?**

Nepodporováno Zásady se dají spravovat jenom pomocí portálu Azure AD. Podívejte se také na předchozí otázku.

**Otázka: Proč je Služba DFSR nutná pro replikaci adresáře SYSVOL?**

Služba replikace souborů (z předchůdce do DFSR) má mnoho známých problémů a je zcela nepodporovaná v novějších verzích služby Windows Server Active Directory. Žádné testování ochrany heslem Azure AD se provede na doménách konfigurovaných pro službu FRS.

Další informace najdete v následujících článcích:

[Případ migrace replikace adresáře SYSVOL do DFSR](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[Konec je Nigh pro službu FRS.](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Pokud vaše doména již nepoužívá službu DFSR, je nutné ji před instalací ochrany hesel služby Azure AD migrovat na používání služby DFSR. Další informace najdete na následujícím odkazu:

[Průvodce migrací replikace SYSVOL: replikace do Replikace DFS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Software agenta řadiče domény Azure AD pro ochranu heslem se v tuto chvíli nainstaluje na řadičích domény v doménách, které ještě používají FRS pro replikaci SYSVOL, ale software v tomto prostředí fungovat nebude správně. Mezi další negativní vedlejší účinky patří neúspěšná replikace jednotlivých souborů a procedury obnovení adresáře SYSVOL, které jsou úspěšné, ale tiché selhání při replikaci všech souborů. Vaši doménu byste měli migrovat tak, aby používala službu DFSR co nejdříve, a to jak z hlediska výhod, tak i z důvodu odblokování nasazení ochrany heslem služby Azure AD. Budoucí verze softwaru budou automaticky zakázány při spuštění v doméně, která stále používá službu replikace souborů (FRS).

**Otázka: kolik místa na disku vyžaduje funkce ve sdílené složce SYSVOL v doméně?**

Přesné využití místa se liší, protože závisí na faktorech, jako je počet a délka zakázaných tokenů v seznamu globálních zakázaných společností Microsoft a na vlastní seznam pro jednotlivé klienty, a na režii šifrování. Obsah těchto seznamů se v budoucnu může zvětšovat. V takovém případě je vhodné předpokládat, že tato funkce bude potřebovat nejméně pět (5) MB místa na sdílené složce SYSVOL v doméně.

**Otázka: Proč je k instalaci nebo upgradu softwaru agenta DC vyžadován restart?**

Tento požadavek způsobuje základní chování systému Windows.

**Otázka: existuje nějaký způsob, jak nakonfigurovat agenta řadiče domény pro použití konkrétního proxy server?**

No. Vzhledem k tomu, že proxy server je Bezstavová, není důležité použít konkrétní proxy server.

**Otázka: je v pořádku nasazovat službu proxy ochrany heslem Azure AD vedle jiných služeb, jako je například Azure AD Connect?**

Ano. Služba proxy ochrany heslem Azure AD a Azure AD Connect by nikdy neměly kolidovat přímo.

Nastala nekompatibilita mezi verzí služby Microsoft Azure AD Connect agent, která je nainstalovaná softwarem proxy ochrany heslem Azure AD, a verzí služby, kterou instaluje software [proxy aplikací služby Azure Active Directory](../manage-apps/application-proxy.md) . Výsledkem této nekompatibility může být, že služba aktualizace agenta nemůže kontaktovat Azure kvůli aktualizacím softwaru. Nedoporučujeme instalovat proxy ochranu heslem Azure AD a Proxy aplikací služby Azure Active Directory ve stejném počítači.

**Otázka: v jakém pořadí by se měly agenti a proxy řadiče domény instalovat a registrovat?**

Podporuje se jakékoli pořadí instalace agenta proxy, instalace agenta DC, registrace doménové struktury a registrace proxy serveru.

**Otázka: Mám obavy o úspěšnost výkonu na řadičích domény z nasazení této funkce?**

Služba agenta řadiče domény služby Azure AD Password Protection by neměla významně ovlivnit výkon řadiče domény v existujícím nasazení služby Active Directory v pořádku.

U většiny nasazení služby Active Directory jsou operace změny hesla malým podílem celkového zatížení na jakémkoli daném řadiči domény. Představte si například doménu služby Active Directory s uživatelskými účty 10000 a zásadami MaxPasswordAge nastavenými na 30 dní. V průměru bude tato doména zobrazovat 10 000/30 = ~ 333 operace změny hesla každý den, což je menší počet operací pro i jeden řadič domény. Vezměte v úvahu možný nejhorší scénář: Předpokládejme, že se na jednom řadiči domény provedly změny hesel 333 za jednu hodinu. K tomuto scénáři může například dojít, když spousta zaměstnanců pracuje na pondělí ráno. I v takovém případě se pořád zobrazujeme přibližně 333/60 minut = šest změn hesel za minutu, což ještě není významné zatížení.

Pokud jsou však stávající řadiče domény již spuštěny na úrovni omezené výkonem (například vyčerpáním z hlediska procesoru, místa na disku, v/v disku atd.), je před nasazením této funkce vhodné přidat další řadiče domény nebo rozšířit dostupné místo na disku. Podívejte se také na otázku výše o využití místa na disku SYSVOL.

**Otázka: Chci otestovat ochranu heslem Azure AD na několika řadičích domény v doméně. Je možné vynutit, aby se změny hesla uživatele používaly pro tyto konkrétní řadiče domény?**

No. Klientský operační systém Windows řídí, který řadič domény se používá, když uživatel změní heslo. Řadič domény je vybraný na základě faktorů, jako je například lokalita a přiřazení podsítě služby Active Directory, konfigurace sítě specifická pro prostředí atd. Ochrana heslem Azure AD tyto faktory neřídí a nemůže ovlivnit, který řadič domény je vybraný pro změnu hesla uživatele.

Jedním ze způsobů, jak částečně dosáhnout tohoto cíle, je nasazení ochrany heslem Azure AD na všech řadičích domény v dané lokalitě služby Active Directory. Tento přístup poskytne přiměřené pokrytí pro klienty systému Windows, kteří jsou k této lokalitě přiřazeni, a proto také pro uživatele, kteří se přihlásí do těchto klientů a mění hesla.

**Otázka: Pokud nainstalujete službu agenta řadiče domény služby Azure AD heslem na jenom primární řadič domény, budou se taky chránit všechny ostatní řadiče domény v doméně?**

No. Když se heslo uživatele změní na daném řadiči domény, který není řadičem domény, heslo nešifrovaných textů se nikdy nepošle na primární řadič domény (Tento nápad je běžnou přívnímáním). Jakmile se v daném řadiči domény přijme nové heslo, tento řadič domény toto heslo použije k vytvoření různých hash hodnot specifických pro ověřování pro dané heslo a pak tyto hodnoty hash uchovává v adresáři. Heslo nešifrovaných textů není trvalé. Aktualizované hodnoty hash se pak replikují na primární řadič domény. Uživatelská hesla se můžou v některých případech změnit přímo na primárním řadiči domény, a to v závislosti na různých faktorech, jako je síťová topologie a návrh lokality služby Active Directory. (Podívejte se na předchozí otázku.)

V souhrnu je potřeba nasazení služby agenta řadiče domény služby Azure AD Password na primárním řadiči domény dosáhnout 100% pokrytí zabezpečení funkce v rámci domény. Nasazení funkce na primární řadič domény jenom neposkytuje výhody zabezpečení ochrany heslem Azure AD pro žádné jiné řadiče domény v doméně.

**Otázka: Proč vlastní inteligentní uzamčení nefunguje ani po instalaci agentů v místním prostředí Active Directory?**

Vlastní inteligentní uzamčení se podporuje jenom v Azure AD. Změny nastavení vlastního inteligentního uzamčení na portálu Azure AD nemají žádný vliv na místní prostředí Active Directory, a to ani na nainstalovaných agentech.

**Otázka: je Management Pack System Center Operations Manager k dispozici pro ochranu heslem Azure AD?**

No.

**Otázka: Proč služba Azure AD stále odmítá slabé heslo, i když jsem nakonfigurovali zásady tak, aby byly v režimu auditování?**

Režim auditování se podporuje jenom v prostředí místní služby Active Directory. Služba Azure AD je implicitně vždy v režimu vynucení, když vyhodnocuje hesla.

**Otázka: uživatelé uvidí tradiční chybovou zprávu Windows, když heslo odmítne ochrana heslem Azure AD. Je možné přizpůsobit tuto chybovou zprávu, aby uživatelé věděli, co se skutečně stalo?**

No. Chybová zpráva zobrazená uživateli, když je heslo odmítnuto řadičem domény, řídí klientským počítačem, nikoli řadičem domény. K tomuto chování dochází, pokud je heslo odmítnuté pomocí výchozích zásad hesel služby Active Directory nebo řešení založeného na filtrech hesel, jako je třeba ochrana heslem Azure AD.

## <a name="password-testing-procedures"></a>Procedury testování hesla

Je možné, že budete chtít provést několik základních testů různých hesel, aby bylo možné ověřit správnou činnost softwaru a lépe porozumět [algoritmům pro vyhodnocování hesla](concept-password-ban-bad.md#how-are-passwords-evaluated). Tato část popisuje metodu pro takové testování, která je navržena pro vytvoření opakujících se výsledků.

Proč je nutné postupovat podle těchto kroků? Je k dispozici několik faktorů, které usnadňují řízení, opakované testování hesel v místním prostředí Active Directory:

* Zásady hesel se konfigurují a ukládají v Azure a kopie zásad jsou pravidelně synchronizované místními agenty řadiče domény pomocí mechanismu cyklického dotazování. Latence spočívající v tomto cyklu cyklického dotazování může způsobit nejasnost. Pokud například nakonfigurujete zásadu v Azure, ale zapomenete ji synchronizovat s agentem řadiče domény, testy nemusí vracet očekávané výsledky. Interval dotazování se aktuálně pevně zakódované na jednu hodinu, ale čekání na jednu hodinu mezi změnami zásad není ideální pro scénář interaktivního testování.
* Jakmile se nové zásady hesel synchronizují na řadič domény, dojde k větší latenci při replikaci na jiné řadiče domény. Tato zpoždění můžou způsobit neočekávané výsledky, pokud testujete změnu hesla na řadiči domény, který ještě neobdržel nejnovější verzi zásad.
* Testování změn hesel prostřednictvím uživatelského rozhraní ztěžuje spolehlivost vašich výsledků. Například je snadné zaměnit neplatné heslo do uživatelského rozhraní, zejména vzhledem k tomu, že většina uživatelských rozhraní skrývá uživatelský vstup (například Windows CTRL-ALT-DELETE-> změnit heslo uživatelského rozhraní).
* Není možné přesně řídit, který řadič domény se používá při testování změn hesel z klientů připojených k doméně. Klientský operační systém Windows vybere řadič domény na základě faktorů, jako je například lokalita a přiřazení podsítě služby Active Directory, konfigurace sítě specifická pro prostředí atd.

Aby se tyto problémy předešly, následující kroky jsou založené na testování resetování hesla z příkazového řádku během přihlášení do řadiče domény.

> [!WARNING]
> Tyto postupy by se měly používat jenom v testovacím prostředí, protože všechny příchozí změny a resetování hesla se budou přijímat bez ověření, když je služba agenta řadiče domény zastavená, a taky se vyhnout zvýšeným rizikům, která spočívají v přihlašování do řadiče domény.

V následujících krocích se předpokládá, že máte nainstalovaný agent DC aspoň na jednom řadiči domény, nainstalovanou aspoň jeden proxy server a zaregistrovali jste proxy a doménovou strukturu.

1. Přihlaste se k řadiči domény pomocí přihlašovacích údajů správce domény (nebo jiných přihlašovacích údajů, které mají dostatečná oprávnění k vytváření testovacích uživatelských účtů a resetování hesel), které mají nainstalovaný software agenta DC a byl restartován.
1. Otevřete Prohlížeč událostí a přejděte do [protokolu událostí správce agenta řadiče domény](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log).
1. Otevřete okno příkazového řádku se zvýšenými oprávněními.
1. Vytvoření testovacího účtu pro testování hesla

   Existuje mnoho způsobů, jak vytvořit uživatelský účet, ale možnost příkazového řádku je k dispozici jako způsob, jak to zjednodušit během opakovaných zkušebních cyklů:

   ```text
   net.exe user <testuseraccountname> /add <password>
   ```

   Pro účely diskuze Předpokládejme, že jsme vytvořili testovací účet s názvem "ContosoUser", například:

   ```text
   net.exe user ContosoUser /add <password>
   ```

1. Otevřete webový prohlížeč (možná budete muset místo řadiče domény použít samostatné zařízení), přihlaste se k [Azure Portal](https://portal.azure.com)a přejděte do Azure Active Directory > metody ověřování zabezpečení > > ochrana heslem.
1. Upravte zásady ochrany hesel Azure AD podle potřeby pro testování, které chcete provést.  Můžete se třeba rozhodnout, že budete chtít nakonfigurovat buď režim vynucení nebo auditování, nebo se můžete rozhodnout upravit seznam zakázaných podmínek v seznamu vlastních zakázaných hesel.
1. Nové zásady synchronizujte zastavením a restartováním služby agenta řadiče domény.

   Tento krok lze provést různými způsoby. Jedním ze způsobů, jak použít konzolu pro správu služby Service Management, je kliknout pravým tlačítkem na službu agenta řadiče domény Azure AD pro ochranu heslem a zvolíte restartovat. Jiný způsob může být proveden z okna příkazového řádku, například takto:

   ```text
   net stop AzureADPasswordProtectionDCAgent && net start AzureADPasswordProtectionDCAgent
   ```
    
1. Zkontrolujte Prohlížeč událostí a ověřte, že se stáhla nová zásada.

   Pokaždé, když je služba agenta řadiče domény zastavená a spuštěná, měla by se zobrazit 2 30006 událostí vydaných v případě úspěchu ukončení. První událost 30006 bude odpovídat zásadám, které byly uloženy do mezipaměti na disku ve sdílené složce SYSVOL. Druhá událost 30006 (Pokud je k dispozici) by měla mít aktualizované datum zásad tenanta, a pokud ano, bude odrážet zásadu staženou z Azure. Hodnota datum zásady tenanta je aktuálně kódována tak, aby zobrazovala přibližné časové razítko, které bylo staženo z Azure.
   
   Pokud se druhá událost 30006 nezobrazí, měli byste problém vyřešit, než budete pokračovat.
   
   Události 30006 budou vypadat podobně jako v tomto příkladu:
 
   ```text
   The service is now enforcing the following Azure password policy.

   Enabled: 1
   AuditOnly: 0
   Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
   Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
   Enforce tenant policy: 1
   ```

   Například změna mezi vynutilou a režimem auditu bude mít za následek změnu příznaku AuditOnly (výše uvedená zásada s AuditOnly = 0 je v režimu vymáhání); změny v seznamu vlastních zakázaných hesel se přímo neprojeví ve výše uvedené události 30006 (a z bezpečnostních důvodů se neprotokolují nikde jinde). Zásady se úspěšně stáhly z Azure, i když tato změna bude obsahovat také upravený seznam vlastních zakázaných hesel.

1. Spusťte test tak, že se pokusíte resetovat nové heslo na testovacím uživatelském účtu.

   Tento krok lze provést z okna příkazového řádku, například takto:

   ```text
   net.exe user ContosoUser <password>
   ```

   Po spuštění příkazu můžete získat další informace o výsledku příkazu tak, že prohlížíte v prohlížeči událostí. Události výsledku ověřování hesla jsou popsány v tématu [protokol událostí správce agenta řadiče domény](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log) . Tyto události použijete k ověření výsledku testu kromě interaktivního výstupu z příkazů net.exe.

   Pojďme se pokusit příklad: pokus o nastavení hesla, které je zakázáno globálním seznamem společnosti Microsoft (Všimněte si, že tento seznam není [dokumentován](concept-password-ban-bad.md#global-banned-password-list) , ale můžete ho otestovat na známý zakázaný termín). V tomto příkladu se předpokládá, že jste nakonfigurovali zásady tak, aby byly v režimu vymáhání a aby se do vlastního seznamu zakázaných hesel přidaly nulové výrazy.

   ```text
   net.exe user ContosoUser PassWord
   The password does not meet the password policy requirements. Check the minimum password length, password complexity and password history requirements.

   More help is available by typing NET HELPMSG 2245.
   ```

   Na základě dokumentace, protože náš test byl operace resetování hesla, měla by se zobrazit událost 10017 a 30005 pro uživatele ContosoUser.

   Událost 10017 by měla vypadat jako v tomto příkladu:

   ```text
   The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   Událost 30005 by měla vypadat jako v tomto příkladu:

   ```text
   The reset password for the specified user was rejected because it matched at least one of the tokens present in the Microsoft global banned password list of the current Azure password policy.
 
   UserName: ContosoUser
   FullName: 
   ```

   To bylo zábavné – Pojďme vyzkoušet jiný příklad! Tentokrát se pokusíte nastavit heslo, které je zakázané vlastním seznamem zakázané, zatímco zásady jsou v režimu auditování. V tomto příkladu se předpokládá, že jste provedli následující kroky: nakonfigurovali jste zásady do režimu auditování, Přidali jste pojem "lachrymose" do vlastního seznamu zakázaných hesel a synchronizovalii výsledné nové zásady k řadiči domény, a to tak, že zadáte službu agenta řadiče domény, jak je popsáno výše.

   OK nastavte variaci zakázaného hesla:

   ```text
   net.exe user ContosoUser LaChRymoSE!1
   The command completed successfully.
   ```

   Zapamatujte si, že je tato operace úspěšná, protože zásada je v režimu auditování. Pro uživatele ContosoUser by se měla zobrazit událost 10025 a 30007.

   Událost 10025 by měla vypadat jako v tomto příkladu:
   
   ```text
   The reset password for the specified user would normally have been rejected because it did not comply with the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   Událost 30007 by měla vypadat jako v tomto příkladu:

   ```text
   The reset password for the specified user would normally have been rejected because it matches at least one of the tokens present in the per-tenant banned password list of the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted.
 
   UserName: ContosoUser
   FullName: 
   ```

1. Pokračujte v testování různých hesel podle vašeho výběru a kontrolou výsledků v prohlížeči událostí pomocí postupů popsaných v předchozích krocích. Pokud potřebujete změnit zásadu v Azure Portal, nezapomeňte synchronizovat nové zásady s agentem řadiče domény, jak je popsáno výše.

Zavedli jsme postupy, které vám umožní provádět řízené testování chování ověřování hesel v ochraně heslem služby Azure AD. Resetování uživatelských hesel z příkazového řádku přímo na řadiči domény se může zdát, že se jedná o takové testování, ale jak bylo popsáno dříve, je navrženo tak, aby přineslo opakované výsledky. Při testování různých hesel mějte na paměti, že k vyhodnocení výsledků, které jste neočekávali, doporučujeme [algoritmus vyhodnocování hesel](concept-password-ban-bad.md#how-are-passwords-evaluated) .

> [!WARNING]
> Po dokončení všech testování nezapomeňte odstranit všechny uživatelské účty vytvořené pro účely testování.

## <a name="additional-content"></a>Další obsah

Následující odkazy nejsou součástí základní dokumentace k ochraně hesel Azure AD, ale mohou být užitečným zdrojem dalších informací o této funkci.

[Ochrana heslem Azure AD je teď všeobecně dostupná!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Průvodce ochranou útoků phishing – část 15: implementace služby ochrany heslem Microsoft Azure AD (v místním prostředí)](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Ochrana hesel Azure AD a inteligentní uzamčení jsou teď v Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Dostupné školení podpory Microsoft Premier\Unified

Pokud vás zajímá více o ochraně heslem Azure AD a jejich nasazení ve vašem prostředí, můžete využít výhod služby Microsoft Proactive Service dostupné zákazníkům se smlouvou Premier nebo Unified support. Služba se nazývá Azure Active Directory: ochrana heslem. Další informace vám poskytne váš správce technického účtu.

## <a name="next-steps"></a>Další kroky

Pokud máte místní otázku ochrany heslem služby Azure AD, která tady není zodpovězená, pošlete nám níže uvedenou položku zpětné vazby.

[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)