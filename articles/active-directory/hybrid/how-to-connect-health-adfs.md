---
title: Používání služby Azure AD Connect Health se službou AD FS | Dokumentace Microsoftu
description: Toto je stránka o službě Azure AD Connect Health, která popisuje postup monitorování místní infrastruktury služby AD FS.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: mtillman
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2018
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 66dd3edefb44a0394216eeff58c6cccb5828c98b
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46302053"
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>Sledování služby AD FS pomocí služby Azure AD Connect Health
Následující dokumentace se věnuje sledování infrastruktury služby AD FS ve službě Azure AD Connect Health. Informace o sledování služby Azure AD Connect (Sync) pomocí služby Azure AD Connect Health najdete v článku [Používání služby Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md). Informace o sledování služby Active Directory Domain Services pomocí služby Azure AD Connect Health najdete v článku [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>Upozornění služby AD FS
Část pojednávající o upozorněních služby Azure AD Connect Health uvádí seznam aktivních upozornění. Každé upozornění obsahuje důležité informace, postup řešení a odkazy na související dokumentaci.

Dvojitým kliknutím na aktivní nebo vyřešené upozornění můžete otevřít nové okno s doplňujícími informacemi, kroky, které můžete k vyřešení upozornění použít, a odkazy na relevantní dokumentaci. Můžete si zobrazit i historické údaje o dříve vyřešených upozorněních.

![Portál služby Azure AD Connect Health](./media/how-to-connect-health-adfs/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>Funkce analýzy využití služby AD FS
Funkce analýzy využití služby Azure AD Connect Health analyzuje ověřovací provoz na federačních serverech. Dvojitým kliknutím na políčko funkce analýzy využití můžete otevřít okno analýzy využití, ve kterém je zobrazeno několik metrik a seskupení.

> [!NOTE]
> Pokud chcete použít funkci analýzy využití ve službě AD FS, povolte auditování AD FS. Další informace najdete v článku o [povolení auditování služby AD FS](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs).
>
>

![Portál služby Azure AD Connect Health](./media/how-to-connect-health-adfs/report1.png)

Pokud chcete vybrat další metriky, určit časový rozsah nebo změnit seskupení, klikněte pravým tlačítkem na graf analýzy využití a vyberte Upravit graf. Potom můžete zadat časový rozsah, vybrat jiné metriky a změnit seskupení. Distribuci ověřovacího provozu můžete zobrazit podle různých „metrik“ a jednotlivé metriky můžete seskupit pomocí příslušných parametrů možnosti „Seskupit podle“, které jsou uvedené v následující části:

**Metrika: Celkový počet požadavků** – Celkový počet požadavků zpracovaných servery AD FS.

|Seskupit podle | Co seskupení znamená a proč je užitečné? |
| --- | --- |
| Vše | Zobrazí celkový počet požadavků zpracovaných všemi servery AD FS.|
| Aplikace | Seskupí celkový počet požadavků podle cílové přijímající strany. Toto seskupení vás seznámí s procentem celkového provozu, které jednotlivé aplikace přijímají. |
|  Server |Seskupí celkový počet požadavků podle serveru, který požadavek zpracoval. Toto seskupení vás seznámí s distribucí zatížení celkového provozu.
| Připojení k pracovišti |Seskupí celkový počet požadavků podle toho, jestli přicházejí ze zařízení, která jsou připojená k pracovišti (známá). Toto seskupení vás seznámí s tím, jestli se k vašim prostředkům přistupuje pomocí zařízení, které infrastruktura identity nezná. |
|  Metoda ověřování | Seskupí celkový počet požadavků podle metody ověřování, která se k ověřování používá. Toto seskupení vás seznámí s běžnou metodu ověřování, která se k ověřování používá. Níže jsou uvedené možné metody ověřování. <ol> <li>integrované ověřování systému Windows (Windows)</li> <li>ověřování pomocí formulářů (formuláře)</li> <li>jednotné přihlašování (SSO)</li> <li>ověření certifikátem X509 (certifikát)</li> <br>Pokud federační servery požadavek přijmou pomocí souboru cookie jednotného přihlašování, příslušný požadavek se počítá jako jednotné přihlašování (SSO). V takových případech (pokud je soubor cookie platný) se od uživatele nevyžadují přihlašovací údaje a uživatel získá bezproblémový přístup k aplikaci. Toto chování je běžné v případě, kdy máte několik přijímajících stran, které jsou chráněné federačními servery. |
| Umístění v síti | Seskupí celkový počet požadavků podle umístění uživatele v síti. Může to být intranet nebo extranet. Toto seskupení vás seznámí s procentuálním podílem provozu z intranetu vzhledem k provozu z extranetu. |


**Metrika: Celkový počet neúspěšných požadavků** – Celkový počet neúspěšných požadavků zpracovaných službou FS. (Tato metrika je dostupná pouze ve službě AD FS pro Windows Server 2012 R2.)

|Seskupit podle | Co seskupení znamená a proč je užitečné? |
| --- | --- |
| Typ chyby | Zobrazí počet chyb podle předdefinovaných typů chyb. Toto seskupení vás seznámí s běžnými typy chyb. <ul><li>Nesprávné uživatelské jméno nebo heslo: Chyby způsobené zadáním nesprávného uživatelského jména nebo hesla.</li> <li>„Uzamčení extranetu“: Selhání způsobené požadavky přijatými od uživatele, který má uzamčený přístup do extranetu. </li><li> „Prošlé heslo“: Chyby způsobené uživateli, kteří se přihlašují pomocí hesla, kterému vypršela platnost.</li><li>„Deaktivovaný účet“: Chyby způsobené uživateli, kteří se přihlašují pomocí deaktivovaného účtu.</li><li>„Ověřování zařízení“: Chyby způsobené uživateli, kteří neprovádějí ověřování pomocí ověření zařízení.</li><li>„Ověřování certifikátu uživatele“: Chyby způsobené uživateli, kterým nefunguje ověřování kvůli neplatnému certifikátu.</li><li>„MFA“: Chyby způsobené neúspěšným ověřením uživatele v případě použití Multi-Factor Authentication.</li><li>„Jiné přihlašovací údaje“: „Autorizace vystavení“: Chyby způsobené selháním autorizace.</li><li>„Delegování vystavení“: Chyby způsobené chybami delegace vystavení.</li><li>„Přijetí tokenu“: Chyby způsobené tím, že služba ADFS odmítla token od zprostředkovatele identity z řad třetích stran.</li><li>„Protokol“: Chyba způsobená chybami protokolu.</li><li>„Neznámá“: Zachytit vše. Jakékoli jiné chyby, které se nehodí do definovaných kategorií.</li> |
| Server | Seskupí chyby podle serveru. Toto seskupení vás seznámí s distribucí chyb mezi servery. Nerovnoměrná distribuce může naznačovat vadný stav serveru. |
| Umístění v síti | Seskupí chyby podle umístění požadavků v síti (intranet vs. extranet). Toto seskupení vás seznámí s typy neúspěšných požadavků. |
|  Aplikace | Seskupí chyby podle cílové aplikace (přijímající strany). Toto seskupení vás seznámí s tím, která cílová aplikace zaznamenává největší počet chyb. |

**Metrika: Počet uživatelů** – Průměrný počet jedinečných uživatelů aktivně ověřujících pomocí AD FS

|Seskupit podle | Co seskupení znamená a proč je užitečné? |
| --- | --- |
|Vše |Tato metrika poskytuje průměrný počet uživatelů, kteří používají službu FS ve vybraném časovém intervalu. Uživatelé nejsou seskupení. <br>Průměr závisí na vybraném časovém intervalu. |
| Aplikace |Seskupí průměrný počet uživatelů podle cílové aplikace (přijímající strany). Toto seskupení vás seznámí s počtem uživatelů používajících jednotlivé aplikace. |

## <a name="performance-monitoring-for-ad-fs"></a>Sledování výkonu služby AD FS
Sledování výkonu služby Azure AD Connect Health poskytuje sledovací informace o metrikách. Po zaškrtnutí políčka sledování se otevře nové okno s podrobnými informacemi o metrikách.

![Portál služby Azure AD Connect Health](./media/how-to-connect-health-adfs/perf1.png)

Výběrem možnosti Filtrovat (v horní části okna) můžete filtrovat podle serveru a prohlédnout si metriky na jednotlivých serverech. Pokud chcete změnit metriky, klikněte pravým tlačítkem na graf sledování pod oknem sledování a vyberte Upravit graf (nebo vyberte tlačítko Upravit graf). V nově otevřeném okně můžete vybrat další metriky pomocí rozevíracího seznamu a také zadat časový rozsah pro zobrazení dat výkonu.

## <a name="top-50-users-with-failed-usernamepassword-logins"></a>Nejčastějších 50 uživatelů s neúspěšným přihlášením kvůli uživatelskému jména nebo heslu
Jednou z běžných příčin neúspěšného požadavku na ověření na serveru AD FS je požadavek provedený s neplatnými přihlašovacími údaji, tedy s nesprávným uživatelským jménem nebo heslem. Do této situace se uživatelé zpravidla dostávají v důsledku používání složitých hesel, zapomenutí hesel nebo překlepů.

Existují však i další důvody, které mohou mít za následek neočekávaný počet požadavků zpracovávaných vašimi servery služby AD FS. Příklady: Aplikace, která ukládá do mezipaměti přihlašovací údaje uživatelů a vypršení jejich platnosti nebo pokus uživatele se zlými úmysly o přihlášení k účtu s použitím řady známých hesel. Tyto dva příklady jsou legitimními důvody, které by mohly vést k prudkému nárůstu množství požadavků.

Azure AD Connect Health pro ADFS poskytuje sestavy s nejčastějšími 50 uživateli, kteří se neúspěšně přihlašovali pomocí neplatného uživatelského jména nebo hesla. Tuto sestavu můžete vytvořit zpracováním událostí auditu, které generují všechny servery AD FS ve farmách.

![Portál služby Azure AD Connect Health](./media/how-to-connect-health-adfs/report1a.png)

V rámci této sestavy máte snadný přístup k následujícím informacím:

* Celkový počet neúspěšných požadavků s nesprávným uživatelským jménem nebo heslem za posledních 30 dní.
* Průměrný počet uživatelů, kteří mají problém s přihlašováním kvůli chybnému uživatelskému jménu nebo heslu, za jeden den.

Kliknutím na tuto část přejdete do hlavního okna sestavy, které vám nabídne další podrobnosti. Toto okno obsahuje graf informace o trendech, které vám usnadní vytváření směrného plánu pro požadavky s nesprávným uživatelským jménem nebo heslem. Kromě toho obsahuje seznam 50 uživatelů s nejvyšším počtem neúspěšných pokusů za poslední týden. Všimněte si, že těchto 50 uživatelů z minulého týdne může pomoct s identifikací špiček zadání špatného hesla.  

Graf obsahuje následující informace:

* Celkový počet neúspěšných přihlášení z důvodu chybného uživatelského jména nebo hesla na denní bázi.
* Celkový počet jedinečných uživatelů s neúspěšným přihlášení na denní bázi.
* IP adresa klienta posledního požadavku

![Portál služby Azure AD Connect Health](./media/how-to-connect-health-adfs/report3a.png)

Sestava obsahuje následující informace:

| Položky sestavy | Popis |
| --- | --- |
| ID uživatele |Zobrazuje použité ID uživatele. Tato hodnota odpovídá hodnotě zadané uživatelem, což je v některých případech nesprávné ID uživatele, které bylo použito. |
| Neúspěšné pokusy |Zobrazuje celkový počet neúspěšných pokusů s konkrétním ID uživatele. Tabulka je řazená podle největšího počtu neúspěšných pokusů v sestupném pořadí. |
| Poslední chyba |Zobrazuje časové razítko výskytu poslední chyby. |
| IP adresa poslední chyby |Zobrazuje IP adresu klienta z posledního neúspěšného požadavku. Pokud se v této hodnotě zobrazí více než jedna IP adresa, může zahrnovat IP adresu klienta přesměrování i IP adresu požadavku z posledního pokusu uživatele.  |

> [!NOTE]
> Sestava se každých 12 hodin automaticky aktualizuje novými informacemi, které se za tu dobu shromáždily. V důsledku tohoto postupu nemusí být v sestavě zahrnuté pokusy o přihlášení za posledních 12 hodin.
>
>

## <a name="risky-ip-report-public-preview"></a>Sestava rizikových IP adres (Public Preview)
Zákazníci služby AD FS můžou zveřejnit koncové body ověřování pomocí hesla na internetu a poskytnout tak koncovým zákazníkům ověřovací služby pro přístup k aplikacím SaaS, jako je Office 365. V takovém případě může pochybný aktér zkoušet přihlášení do systému AD FS za účelem uhodnutí hesla koncového uživatele a získání přístupu k prostředkům aplikace. Služba AD FS od verze AD FS ve Windows Serveru 2012 R2 poskytuje funkci uzamčení účtu pro extranet, která brání těmto typům útoku. Pokud používáte nižší verzi, důrazně doporučujeme upgradovat systém AD FS na Windows Server 2016. <br />
Kromě toho je možné, aby se jedna IP adresa pokoušela o přihlášení za několik uživatelů. V takových případech je možné počet pokusů na jednoho uživatele omezit prahovou hodnotou pro ochranu uzamčení účtu ve službě AD FS. Azure AD Connect Health nyní poskytuje sestavu rizikových IP adres, která tuto podmínku detekuje a upozorňuje na její výskyt správce. Klíčové výhody této sestavy jsou následující: 
- Detekce IP adres, které překročí prahovou hodnotu neúspěšných pokusů o přihlášení na základě hesla
- Podpora neúspěšných přihlášení kvůli špatnému heslu nebo kvůli stavu uzamčení extranetu
- E-mailová oznámení s přizpůsobitelným nastavením e-mailu, která okamžitě upozorní správce, když k tomu dojde
- Přizpůsobitelná nastavení prahových hodnot, která odpovídají zásadám zabezpečení organizace
- Sestavy ke stažení pro účely offline analýzy a integrace s dalšími systémy prostřednictvím automatizace

> [!NOTE]
> Pokud chcete tuto sestavu použít, ujistěte se, že je povolené auditování AD FS. Další informace najdete v článku o [povolení auditování služby AD FS](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Pro přístup k náhledu jsou vyžadována oprávnění Globálního správce nebo [Čtenáře zabezpečení](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader).  
> 

### <a name="what-is-in-the-report"></a>Obsah sestavy
Každá položka v sestavě rizikových IP adres ukazuje agregované informace o neúspěšných aktivitách přihlášení ke službě AD FS, které překročí určenou prahovou hodnotu. Obsahuje následující informace: ![Portál služby Azure AD Connect Health](./media/how-to-connect-health-adfs/report4a.png)

| Položky sestavy | Popis |
| ------- | ----------- |
| Časové razítko | Ukazuje časové razítko na základě místního času webu Azure Portal při zahájení časového intervalu zjišťování.<br /> Všechny denní události se generují o půlnoci UTC. <br />Hodinové události mají hodnotu časového razítka zaokrouhlenou na celou hodinu. Čas spuštění první aktivity můžete vyhledat v položce firstAuditTimestamp v exportovaném souboru. |
| Typ triggeru | Ukazuje typ časového intervalu zjišťování. Typ triggeru agregace určuje, jestli se aktivuje každou hodinu nebo každý den. To je užitečně k rozpoznání útoku hrubou silou s vysokou frekvencí od pomalého útoku, při kterém se počet pokusů distribuuje během celého dne. |
| IP adresa | Jedna riziková IP adresa, ze které probíhaly aktivity přihlášení se špatným heslem nebo uzamčením extranetu. Může se jednat o adresu IPv4 nebo IPv6. |
| Počet chybných zadání hesla | Počet chybných zadání hesla, ke kterým z dané IP adresy došlo během časového intervalu zjišťování. K chybnému zadání hesla může u určitých uživatelů dojít vícekrát. Všimněte si, že toto číslo nezahrnuje neúspěšné pokusy kvůli heslům, kterým vypršela platnost. |
| Počet chyb uzamčení extranetu | Počet chyb uzamčení extranetu, ke kterým z dané IP adresy došlo během časového intervalu zjišťování. K chybám uzamčení extranetu může u určitých uživatelů dojít vícekrát. Tato položka se zobrazí pouze v případě, že je uzamčení extranetu nakonfigurované ve službě AD FS (verze 2012R2 nebo novější). <b>Poznámka:</b> Důrazně doporučujeme tuto funkci zapnout, pokud umožňujete přihlášení pomocí hesel z extranetu. |
| Počet vyzkoušených jedinečných uživatelů | Počet vyzkoušených jedinečných uživatelských účtů, ke kterým z dané IP adresy došlo během časového intervalu zjišťování. Tato položka poskytuje mechanismus pro odlišení vzorce útoku na jednoho uživatele od vzorce útoku na více uživatelů.  |

Například níže uvedená položka sestavy značí, že 28. 2. 2018 v časovém intervalu od 18:00 do 19:00 u IP adresy <i>104.2XX.2XX.9</i> nedošlo k žádnému chybnému zadání hesla, ale došlo k 284 chybám uzamčení extranetu. V rámci kritérií to mělo dopad na 14 jedinečných uživatelů. Událost aktivity překročila určenou hodinovou prahovou hodnotu sestavy. 

![Portál služby Azure AD Connect Health](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - V seznamu sestavy se zobrazí pouze aktivity překračující určenou prahovou hodnotu. 
> - V této sestavě je možné dojít maximálně 30 dnů nazpět.
> - V této sestavě upozornění se nezobrazují IP adresy Exchange ani privátní IP adresy. Ty jsou však stále součástí exportovaného seznamu. 
>

![Portál služby Azure AD Connect Health](./media/how-to-connect-health-adfs/report4c.png)

### <a name="load-balancer-ip-addresses-in-the-list"></a>IP adresy nástroje pro vyrovnávání zatížení v seznamu
Nástroj pro vyrovnávání zatížení agreguje aktivity přihlášení, které selhaly, a překročení prahové hodnoty výstrahy. Pokud se vám zobrazují IP adresy nástroje pro vyrovnávání zatížení, je vysoce pravděpodobné, že váš externí nástroj pro vyrovnávání zatížení při předávání požadavku na proxy server webové aplikace neodesílá IP adresu klienta. Nakonfigurujte ve svém nástroji pro vyrovnávání zatížení správně předávání IP adresy klienta. 

### <a name="download-risky-ip-report"></a>Stažení sestavy rizikových IP adres 
Pomocí funkce **Stáhnout** můžete z portálu služby Connect Health exportovat celý seznam rizikových IP adres za posledních 30 dnů. Výsledek exportu bude obsahovat všechny neúspěšné aktivity přihlášení ke službě AD FS v jednotlivých časových intervalech zjišťování, abyste si po exportu mohli upravit filtrování. Kromě zvýrazněných agregací na portálu bude výsledek exportu obsahovat také další podrobnosti o neúspěšných aktivitách přihlášení podle IP adresy:

|  Položky sestavy  |  Popis  | 
| ------- | ----------- | 
| firstAuditTimestamp | Ukazuje časové razítko prvního výskytu neúspěšných aktivit během časového intervalu zjišťování.  | 
| lastAuditTimestamp | Ukazuje časové razítko posledního výskytu neúspěšných aktivit během časového intervalu zjišťování.  | 
| attemptCountThresholdIsExceeded | Příznak, který značí, jestli aktuální aktivity překračují prahovou hodnotu pro upozornění.  | 
| isWhitelistedIpAddress | Příznak, který značí, jestli se daná IP adresa filtruje z upozorňování a generování sestav. Privátní IP adresy (<i>10.x.x.x, 172.x.x.x a 192.168.x.x</i>) a IP adresy Exchange se filtrují a jsou označené hodnotou True. Pokud se vám zobrazují rozsahy privátních IP adres, je vysoce pravděpodobné, že váš externí nástroj pro vyrovnávání zatížení při předávání požadavku na proxy server webové aplikace neodesílá IP adresu klienta.  | 

### <a name="configure-notification-settings"></a>Konfigurace nastavení oznámení
Kontakty pro správu sestavy je možné aktualizovat prostřednictvím **Nastavení oznámení**. Ve výchozím nastavení je e-mailové oznámení o upozornění na rizikové IP adresy vypnuté. Oznámení můžete zapnout přepnutím tlačítka v části Dostávat e-mailová oznámení o sestavě IP adres, které překročily prahovou hodnotu neúspěšné aktivity. Podobně jako v případě nastavení oznámení o obecných upozornění ve službě Connect Health můžete na tomto místě upravit určený seznam příjemců oznámení o sestavě rizikových IP adres. Při provádění změny můžete také upozornit všechny globální správce. 

### <a name="configure-threshold-settings"></a>Konfigurace nastavení prahových hodnot
Prahovou hodnotu pro upozornění můžete upravit prostřednictvím nastavení prahových hodnot. Pro začátek má systém nastavené výchozí prahové hodnoty. Nastavení prahových hodnot pro sestavu rizikových IP adres obsahuje čtyři kategorie:

![Portál služby Azure AD Connect Health](./media/how-to-connect-health-adfs/report4d.png)

| Položka prahové hodnoty | Popis |
| --- | --- |
| (Chybné U/P + uzamčení extranetu) / den  | Nastavení prahové hodnoty pro ohlášení aktivity a aktivaci oznámení o upozornění, když součet počtu chybných zadání hesla a uzamčení extranetu za **den** překročí tuto prahovou hodnotu. |
| (Chybné U/P + uzamčení extranetu) / hodina | Nastavení prahové hodnoty pro ohlášení aktivity a aktivaci oznámení o upozornění, když součet počtu chybných zadání hesla a uzamčení extranetu za **hodinu** překročí tuto prahovou hodnotu. |
| Uzamčení extranetu / den | Nastavení prahové hodnoty pro ohlášení aktivity a aktivaci oznámení o upozornění, když počet uzamčení extranetu za **den** překročí tuto prahovou hodnotu. |
| Uzamčení extranetu / hodina| Nastavení prahové hodnoty pro ohlášení aktivity a aktivaci oznámení o upozornění, když počet uzamčení extranetu za **hodinu** překročí tuto prahovou hodnotu. |

> [!NOTE]
> - Změna prahových hodnot pro sestavu se projeví za hodinu od provedení změny nastavení. 
> - Stávající ohlášené položky změna prahových hodnot neovlivní. 
> - Doporučujeme analyzovat počet událostí, ke kterým ve vašem prostředí dochází, a upravit prahové hodnoty odpovídajícím způsobem. 
>
>

### <a name="faq"></a>Nejčastější dotazy
1. Proč se v sestavě zobrazují rozsahy privátních IP adres?  <br />
Privátní IP adresy (<i>10.x.x.x, 172.x.x.x a 192.168.x.x</i>) a IP adresy Exchange se filtrují a v seznamu povolených IP adres jsou označené hodnotou True. Pokud se vám zobrazují rozsahy privátních IP adres, je vysoce pravděpodobné, že váš externí nástroj pro vyrovnávání zatížení při předávání požadavku na proxy server webové aplikace neodesílá IP adresu klienta.

2. Proč se v sestavě zobrazují IP adresy nástroje pro vyrovnávání zatížení?  <br />
Pokud se vám zobrazují IP adresy nástroje pro vyrovnávání zatížení, je vysoce pravděpodobné, že váš externí nástroj pro vyrovnávání zatížení při předávání požadavku na proxy server webové aplikace neodesílá IP adresu klienta. Nakonfigurujte ve svém nástroji pro vyrovnávání zatížení správně předávání IP adresy klienta. 

3. Jak můžu zablokovat IP adresu?  <br />
Zjištěné škodlivé IP adresy byste měli přidat do brány firewall nebo je blokovat v Exchange.   <br />

4. Proč se mi v této sestavě nezobrazují žádné položky? <br />
   - Neúspěšné aktivity přihlášení nepřekračují nastavení prahových hodnot. 
   - Ujistěte se, že v seznamu serverů AD FS nejsou žádná aktivní upozornění Služba Health není aktuální.  Další informace o [řešení potíží s tímto upozorněním](how-to-connect-health-data-freshness.md).
   - Na farmách služby AD FS není povolené auditování.
 
5. Proč k sestavě nemám přístup?  <br />
Vyžadují se oprávnění globálního správce nebo [čtenáře zabezpečení](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader). Obraťte se s žádostí o udělení přístupu na globálního správce.


## <a name="related-links"></a>Související odkazy
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalace agenta služby Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operace služby Azure AD Connect Health](how-to-connect-health-operations.md)
* [Používání služby Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md)
* [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)
* [Historie verzí služby Azure AD Connect Health](reference-connect-health-version-history.md)
