---
title: "Používání služby Azure AD Connect Health se službou AD FS | Dokumentace Microsoftu"
description: "Toto je stránka o službě Azure AD Connect Health, která popisuje postup monitorování místní infrastruktury služby AD FS."
services: active-directory
documentationcenter: 
author: karavar
manager: mtillman
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4928364ffd732f587e41a788c4d834e91339a7fc
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>Sledování služby AD FS pomocí služby Azure AD Connect Health
Následující dokumentace se věnuje sledování infrastruktury služby AD FS ve službě Azure AD Connect Health. Informace o sledování služby Azure AD Connect (Sync) pomocí služby Azure AD Connect Health najdete v článku [Používání služby Azure AD Connect Health pro synchronizaci](active-directory-aadconnect-health-sync.md). Informace o sledování služby Active Directory Domain Services pomocí služby Azure AD Connect Health najdete v článku [Používání služby Azure AD Connect Health se službou AD DS](active-directory-aadconnect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>Upozornění služby AD FS
Část pojednávající o upozorněních služby Azure AD Connect Health uvádí seznam aktivních upozornění. Každé upozornění obsahuje důležité informace, postup řešení a odkazy na související dokumentaci.

Dvojitým kliknutím na aktivní nebo vyřešené upozornění můžete otevřít nové okno s doplňujícími informacemi, kroky, které můžete k vyřešení upozornění použít, a odkazy na relevantní dokumentaci. Můžete si zobrazit i historické údaje o dříve vyřešených upozorněních.

![Portál služby Azure AD Connect Health](./media/active-directory-aadconnect-health/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>Funkce analýzy využití služby AD FS
Funkce analýzy využití služby Azure AD Connect Health analyzuje ověřovací provoz na federačních serverech. Dvojitým kliknutím na políčko funkce analýzy využití můžete otevřít okno analýzy využití, ve kterém je zobrazeno několik metrik a seskupení.

> [!NOTE]
> Pokud chcete použít funkci analýzy využití ve službě AD FS, povolte auditování AD FS. Další informace najdete v článku o [povolení auditování služby AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).
>
>

![Portál služby Azure AD Connect Health](./media/active-directory-aadconnect-health/report1.png)

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

![Portál služby Azure AD Connect Health](./media/active-directory-aadconnect-health/perf1.png)

Výběrem možnosti Filtrovat (v horní části okna) můžete filtrovat podle serveru a prohlédnout si metriky na jednotlivých serverech. Pokud chcete změnit metriky, klikněte pravým tlačítkem na graf sledování pod oknem sledování a vyberte Upravit graf (nebo vyberte tlačítko Upravit graf). V nově otevřeném okně můžete vybrat další metriky pomocí rozevíracího seznamu a také zadat časový rozsah pro zobrazení dat výkonu.

## <a name="reports-for-ad-fs"></a>Sestavy služby AD FS
Azure AD Connect Health poskytuje sestavy s informacemi o činnosti a výkonu služby AD FS. Tyto sestavy pomáhají správcům získat přehled o aktivitách na jejich serverech AD FS.

### <a name="top-50-users-with-failed-usernamepassword-logins"></a>Nejčastějších 50 uživatelů s neúspěšným přihlášením kvůli uživatelskému jména nebo heslu
Jednou z běžných příčin neúspěšného požadavku na ověření na serveru AD FS je požadavek provedený s neplatnými přihlašovacími údaji, tedy s nesprávným uživatelským jménem nebo heslem. Do této situace se uživatelé zpravidla dostávají v důsledku používání složitých hesel, zapomenutí hesel nebo překlepů.

Existují však i další důvody, které mohou mít za následek neočekávaný počet požadavků zpracovávaných vašimi servery služby AD FS. Příklady: Aplikace, která ukládá do mezipaměti přihlašovací údaje uživatelů a vypršení jejich platnosti nebo pokus uživatele se zlými úmysly o přihlášení k účtu s použitím řady známých hesel. Tyto dva příklady jsou legitimními důvody, které by mohly vést k prudkému nárůstu množství požadavků.

Azure AD Connect Health pro ADFS poskytuje sestavy s nejčastějšími 50 uživateli, kteří se neúspěšně přihlašovali pomocí neplatného uživatelského jména nebo hesla. Tuto sestavu můžete vytvořit zpracováním událostí auditu, které generují všechny servery AD FS ve farmách.

![Portál služby Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report1a.png)

V rámci této sestavy máte snadný přístup k následujícím informacím:

* Celkový počet neúspěšných požadavků s nesprávným uživatelským jménem nebo heslem za posledních 30 dní.
* Průměrný počet uživatelů, kteří mají problém s přihlašováním kvůli chybnému uživatelskému jménu nebo heslu, za jeden den.

Kliknutím na tuto část přejdete do hlavního okna sestavy, které vám nabídne další podrobnosti. Toto okno obsahuje graf informace o trendech, které vám usnadní vytváření směrného plánu pro požadavky s nesprávným uživatelským jménem nebo heslem. Kromě toho obsahuje seznam 50 uživatelů s nejvyšším počtem neúspěšných pokusů za poslední týden.

Graf obsahuje následující informace:

* Celkový počet neúspěšných přihlášení z důvodu chybného uživatelského jména nebo hesla na denní bázi.
* Celkový počet jedinečných uživatelů s neúspěšným přihlášení na denní bázi.
* IP adresa klienta posledního požadavku

![Portál služby Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report3a.png)

Sestava obsahuje následující informace:

| Položky sestavy | Popis |
| --- | --- |
| ID uživatele |Zobrazuje použité ID uživatele. Tato hodnota odpovídá hodnotě zadané uživatelem, což je v některých případech nesprávné ID uživatele, které bylo použito. |
| Neúspěšné pokusy |Zobrazuje celkový počet neúspěšných pokusů s konkrétním ID uživatele. Tabulka je řazená podle největšího počtu neúspěšných pokusů v sestupném pořadí. |
| Poslední chyba |Zobrazuje časové razítko výskytu poslední chyby. |
| IP adresa poslední chyby |Zobrazuje IP adresu klienta z posledního neúspěšného požadavku. |

> [!NOTE]
> Sestava se každých 12 hodin automaticky aktualizuje novými informacemi, které se za tu dobu shromáždily. V důsledku tohoto postupu nemusí být v sestavě zahrnuté přihlašovací pokusy za poslední dvě hodiny.
>
>

## <a name="related-links"></a>Související odkazy
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalace agenta služby Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operace služby Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Používání služby Azure AD Connect Health pro synchronizaci](active-directory-aadconnect-health-sync.md)
* [Používání služby Azure AD Connect Health se službou AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health – nejčastější dotazy](active-directory-aadconnect-health-faq.md)
* [Historie verzí služby Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
