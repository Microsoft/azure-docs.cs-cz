---
title: Plánování nasazení služby Azure Active Directory Application Proxy
description: Začátku do konce Příručka pro plánování nasazení proxy aplikace v rámci vaší organizace
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: 04a2e9968e8716818637a34adea86de88e1f848c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388314"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Plánování nasazení služby Azure AD Application Proxy

Proxy aplikací služby Azure Active Directory (Azure AD) je řešení nákladově efektivní a zabezpečený vzdálený přístup pro místní aplikace. Poskytuje cestu k okamžitý přechod pro "Cloud první" organizacím spravovat přístup k starší verze místních aplikací, které zatím nejsou schopny použití moderních protokoly. Další úvodní informace najdete v tématu [co je Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Proxy aplikací se doporučuje že vzdáleným uživatelům udělíte přístup k interním prostředkům. Proxy aplikace nahrazuje potřebu síť VPN nebo reverzního proxy serveru pro tyto případy použití vzdáleného přístupu. Není určena pro uživatele, kteří jsou v podnikové síti. Tito uživatelé, kteří používají Proxy aplikace přístup k intranetu setkat s problémy s výkonem nežádoucí.

Tento článek obsahuje prostředky, které je potřeba naplánovat, provozovat a spravovat Azure AD Application Proxy. 

## <a name="plan-your-implementation"></a>Plánování implementace

Následující část poskytuje široký přehled klíč plánování prvky, které můžete vytvořit efektivní nasazení prostředí. 

### <a name="prerequisites"></a>Požadavky

Je potřeba splnit následující požadavky před zahájením implementace. Zobrazí se další informace o nastavení prostředí, včetně těchto nezbytných podmínkách, v tomto [kurzu](application-proxy-add-on-premises-application.md).

* **Konektory**: Konektory jsou zjednodušené agenty, které můžete nasadit na:
   * Fyzický hardware na místě
   * Virtuální počítač hostovaný v rámci jakékoli řešení hypervisoru
   * Virtuální počítač hostovaný v Azure a umožňuje odchozí připojení ke službě Proxy aplikací.

* Zobrazit [pochopit Proxy aplikace Azure AD konektory](application-proxy-connectors.md) podrobnější přehled.

     * Konektor stroje musí [povolit pro protokol TLS 1.2](application-proxy-add-on-premises-application.md) před instalací konektory.

     * Pokud je to možné, nasaďte konektorů [stejné síti](application-proxy-network-topology.md) a segment jako servery back endové webové aplikace. Doporučujeme nasadit konektorů po dokončení zjišťování aplikací.
     * Doporučujeme vám, že má každá skupina konektor aspoň dva konektory k poskytování vysoké dostupnosti a škálování. V případě, že budete muset služby počítače v libovolném bodě je mít tři konektory optimální. Zkontrolujte [tabulce kapacitní konektor](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) který usnadní rozhodování o tom, jaký typ počítače chcete konektory nainstalovat. Konektor budou mít větší počítače další vyrovnávací paměti a výkonné.

* **Přístup k nastavení sítě**: Azure AD Application Proxy konektory [připojení k Azure přes protokol HTTPS (TCP Port 443) a protokolu HTTP (TCP Port 80)](application-proxy-add-on-premises-application.md). 

   * Ukončující konektor přenosů TLS se nepodporuje a nebudou moct spojnice navázáním zabezpečeného kanálu pomocí jejich příslušných koncových bodů Proxy aplikace Azure.

   * Vyhněte se všechny formy kontroly vložené na odchozí komunikaci protokolu TLS mezi konektory a Azure. Interní kontroly mezi konektoru a back-endu aplikace je možné, ale může snížit uživatelské prostředí a v důsledku toho se nedoporučuje.

   * Konektorů samotné služby Vyrovnávání zatížení je také podporována, nebo dokonce nezbytné.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Důležité informace před konfigurací Azure AD Application Proxy

Aby bylo možné konfigurovat a implementovat Proxy aplikací Azure AD musí být splněny následující požadavky core.

*  **Registrace Azure**: Před nasazením proxy aplikací, musí být identit uživatelů synchronizované z místního adresáře nebo přímo v rámci vašich tenantů Azure AD. Umožňuje synchronizaci identit Azure AD pro předběžné ověření uživatelů před udělením přístupu k Proxy aplikace publikované aplikace a mít informace potřebné uživatelské identifikátor provádět jednotné přihlašování (SSO).

* **Podmíněný přístup požadavky**: Nedoporučujeme, protože to zvyšuje latenci, která bude mít dopad na uživatele pomocí Proxy aplikace přístup k intranetu. Doporučujeme používat Proxy aplikací se zásadami předběžné ověření a podmíněného přístupu pro vzdálený přístup z Internetu.  Přístup k poskytování je podmíněný přístup pro použití v intranetu k modernizaci aplikací tak mohou diretly ověřování pomocí AAD. Odkazovat na [zdroje pro migraci aplikací do AAD](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) Další informace. 

* **Omezení služby**: K ochraně proti jsou overconsumption prostředků jednotlivé tenanty existuje omezení nastavte jednotlivé aplikace a tenanta. Do těchto omezeních najdete v tématu [limity a omezení služby Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Tato omezení jsou založené na srovnávací test mnohem vyšší než svazku typickému využití a poskytuje dostatek vyrovnávací paměti pro většinu nasazení.

* **Veřejný certifikát**: Pokud používáte vlastní názvy domén, musíte pořídit veřejný certifikát vydaný jiného subjektu než Microsoft důvěryhodné certifikační autority. V závislosti na požadavcích organizace získat certifikát může nějakou dobu trvat a doporučujeme zahájením procesu napravovat nejvíce. Proxy aplikací Azure podporuje standard, [zástupný znak](application-proxy-wildcard.md), nebo certifikáty založené na síti SAN.

* **Požadavky na domény**: Jednotné přihlašování pro aplikace publikované pomocí protokolu Kerberos omezené delegování (KCD) vyžaduje, že konektor hostitele je připojený ke stejné doméně AD jako aplikace probíhá publikování. Podrobné informace o tomto tématu najdete v tématu [KCD pro jednotné přihlašování](application-proxy-configure-single-sign-on-with-kcd.md) pomocí Proxy aplikace. Služba konektoru spouští v kontextu místního systému a nesmí být nakonfigurovaný na použití vlastní identitu.

* **Záznamy DNS pro adresy URL**

   * Před použitím vlastních domén v Proxy aplikací musíte vytvořit záznam CNAME ve veřejném DNS, což klientům přeložit vlastní definované externí adresu URL na adresu předem definovaných Proxy aplikací. K vytvoření záznamu CNAME pro aplikaci, která používá vlastní doménu selhání zabrání vzdáleným uživatelům připojení k aplikaci. Kroky potřebné k přidání záznamů CNAME se mohou lišit od DNS poskytovatele, takže zjistěte, jak [Správa záznamů a sad záznamů DNS pomocí webu Azure portal](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * Podobně konektor hostitelé musí být schopen převést interní adresa URL publikování aplikací.

* **Práva správce a role**

   * **Instalace konektoru** vyžaduje oprávnění místního správce k Windows serveru, který se instaluje na. Také budete potřebovat minimálně *správce aplikace* role k ověření a zaregistrovat instanci konektoru pro vašeho tenanta Azure AD. 

   * **Publikování aplikací a správa** vyžadují *správce aplikace* role. Správci aplikace mohou spravovat všechny aplikace v adresáři, včetně registrace, nastavení jednotného přihlašování, uživatele a přiřazení skupin a licencování, nastavení Proxy aplikací a vyjádření souhlasu. To není udělit schopnost spravovat podmíněný přístup. *Správce cloudové aplikace* role má všechny schopnosti nástroje Správce aplikace s tím rozdílem, že neumožňuje správu nastavení Proxy aplikací.

* **Licencování**: Proxy aplikací je k dispozici prostřednictvím předplatného Azure AD Basic. Odkazovat [stránce s cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) úplný seznam licencování možnosti a funkce.  

### <a name="application-discovery"></a>Zjišťování aplikací

Soupis všech aplikací v oboru, které jsou právě publikované prostřednictvím Proxy aplikací tak, že shromažďují následující informace:

| Typ informací| Informace ke shromažďování |
|---|---|
| Typ služby| Příklad: SharePoint, SAP, CRM, vlastní webové aplikace, rozhraní API |
| Aplikační platforma | Příklad: Windows služby IIS, Apache na Linuxu, Tomcat, serveru NGINX |
| Členství v doméně.| Webový server plně kvalifikovaný název domény (FQDN) |
| Umístění aplikace | Kde se nachází na webovém serveru nebo farmy ve vaší infrastruktuře |
| Interní přístup | Přesná adresa URL používá při přístupu k aplikaci interně. <br> Pokud farmu, jaký typ Vyrovnávání zatížení se používá? <br> Určuje, zda aplikace vykreslí obsah ze zdrojů než na sebe.<br> Určete, pokud aplikace pracuje přes WebSockets. |
| Externí přístup | Řešení dodavatele, který aplikace je již vystaven externě. <br> Adresa URL, kterou chcete použít pro externí přístup. Pokud SharePoint, zkontrolujte mapování alternativních adres URL jsou nakonfigurované na [návod](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). V opačném případě budete muset definovat externí adresy URL. |
| Veřejný certifikát | Pokud používáte vlastní doménu, pořídit certifikát s odpovídajícím názvem subjektu. Pokud existuje certifikátu Poznámka: sériové číslo a umístění, ze které lze získat. |
| Typ ověřování| Typ ověřování podporovaný podporu aplikace, jako jsou Basic, založené na formulářích, založené na hlavičkách, integrované ověřování Windows a deklarace identity. <br>Pokud aplikace je nakonfigurovaná pro spuštění pod účtem konkrétní domény, pamatujte plně kvalifikovaný název domény (FQDN) účtu služby.<br> Pokud na základě SAML, identifikátoru a odpověď adresy URL. <br> Pokud založeným na hlavičkách, dodavatele řešení a specifické požadavky pro zpracování ověřování typu. |
| Název skupiny pro konektor | Logický název pro skupinu konektorů, které bude určen k poskytování přenos a jednotné přihlašování k této aplikaci back-endu. |
| Přístup uživatele nebo skupiny | Uživatelé nebo skupiny uživatelů, kteří budou mít externí přístup k aplikaci. |
| Další požadavky | Poznámka: všechny další vzdáleného přístupu nebo požadavky na zabezpečení, které by měl být naopak do publikování aplikace. |

Je možné stáhnout [tabulky inventáře aplikací](https://aka.ms/appdiscovery) k inventarizaci vašich aplikací.

### <a name="define-organizational-requirements"></a>Definujte organizační požadavky

Níže jsou oblasti, pro které byste měli definovat obchodní požadavky vaší organizace. Obsahuje příklady požadavků na jednotlivé oblasti

 **Přístup**

* Vzdálení uživatelé s přidaní do domény nebo uživatelé zařízení připojených k Azure AD můžete přístup k publikovaným aplikacím bezpečně pomocí bezproblémového jednotného přihlašování (SSO).

* Vzdálení uživatelé s schválené osobních zařízeních mít bezpečný přístup k publikovaným aplikacím předpokladu, že jsou zaregistrovaná v MFA a zaregistrovali aplikaci Microsoft Authenticator na jejich mobilního telefonu jako metodu ověřování.

**Zásady správného řízení** 

* Správci můžou definovat a sledovat životního cyklu přiřazení uživatele pro aplikace publikované prostřednictvím Proxy aplikací.

**Zabezpečení**

* Jenom uživatelé přiřazené k aplikacím prostřednictvím členství ve skupině nebo jednotlivě mají přístup k těmto aplikacím.

**Výkon**

* Neexistuje žádné snížení výkonu aplikace v porovnání s přístup k aplikaci z interní sítě.

**Činnost koncového uživatele**

* Uživatelé informováni o tom, jak získat přístup k jejich aplikacím s použitím adresy URL zkušenosti společnosti na jakoukoli platformu zařízení.

**Auditování**
* Správci budou moct auditovat jejich aktivitu přístup.


### <a name="best-practices-for-a-pilot"></a>Osvědčené postupy pro pilotní nasazení

Určení množství času a úsilí potřebné ke plně Komise jedné aplikace pro vzdálený přístup s jednotným přihlašováním (SSO). Uděláte to tak spuštěním pilotního projektu, který bere v úvahu jeho počáteční zjišťování, publikování a obecné testování. Použití jednoduché webové služby IIS aplikace, která už je předem nakonfigurovaný pro integrované ověřování Windows (IWA) by pomohl stanovení základní úrovně, protože tato instalace vyžaduje minimálním úsilím úspěšně pilotního nasazení vzdáleného přístupu a jednotné přihlašování.

Následující prvky návrhu by měl zvýšit úspěchu pilotního nasazení implementace přímo do produkčního tenanta.  

**Konektor řízení**:  

* Konektory hrát klíčovou roli při poskytování přenos místních aplikací. Použití **výchozí** skupina konektorů je vhodná pro počáteční pilotní testování publikované aplikace před uvedením do nich provozu do produkčního prostředí. Úspěšně testované aplikace můžete poté přesunut do skupiny konektorů produkčního prostředí.

**Správa aplikací**:

* Vaše pracovní síly stále nejpravděpodobněji mějte na paměti, že externí adresa URL jsou známá a relevantní. Vyhněte se publikování aplikace pomocí našich předem definovaných msappproxy.net nebo onmicrosoft.com přípony. Místo toho zadejte známých nejvyšší úrovně ověřenou doménou, jako předponu logický název hostitele *intranetu. < customers_domain > .com*.

* Omezte viditelnost ikona pilotního nasazení aplikace do pilotní skupiny tak, že skrytím jeho spuštění ikonu formulář portálu Azure MyApps. Až bude připravený pro produkční prostředí můžete omezit rozsah jeho příslušné cílové skupině, buď ve stejném tenantovi předprodukčním prostředí, nebo také publikování aplikace ve vašem tenantovi produkční aplikaci.

**Jednotné přihlašování – nastavení**: Některá nastavení jednotného přihlašování mají specifické vzájemné závislosti, které může trvat dobu nastaven, vyhněte se řízení změn, které zpoždění zajištěním závislosti se tak vyřeší, předem. To zahrnuje připojení hostitele konektor provádět jednotné přihlašování pomocí protokolu Kerberos omezené delegování (KCD) a přitom se bezpečně postará z jiných aktivit časově náročné domény. Například nastavení Pingaccessu instance, pokud by bylo jednotné přihlašování založené na hlavičkách.

**SSL mezi hostitelem konektoru a cílovou aplikaci**: Zabezpečení je prvořadá, takže TLS mezi těmito aplikacemi hostitele a cílovém konektoru se musí použít značka. Zejména v případě, že webová aplikace je nakonfigurován pro ověřování pomocí formulářů (FBA), protože přihlašovací údaje uživatele jsou pak efektivně odesílané informace ve formátu prostého textu.

**Postupně implementaci a testování jednotlivých kroků**. Chování, základní funkční testování po publikování aplikace k zajištění, že jsou splněny všechny požadavky uživatelů a firmy podle následujícího postupu:

1. Testování a ověřování obecného přístupu k webové aplikaci pomocí předběžné ověření zakázáno.
2. V případě úspěšného ověření povolte předběžné ověření a přiřadit uživatelům a skupinám. Testování a ověřování přístupu.
3. Pak přidejte metodu jednotného přihlašování pro vaši aplikaci a znovu otestujte ověření přístupu.
4. Použití podmíněného přístupu a zásad MFA podle potřeby. Testování a ověřování přístupu.

**Řešení potíží s nástroji**: Při odstraňování potíží, vždy začněte tím, že ověření přístupu k publikované aplikaci z prohlížeče na hostiteli konektoru a potvrďte, že aplikace funguje podle očekávání. Tím jednodušší instalaci, tím snazší tak, aby určit hlavní příčinu, proto zvažte pro reprodukci problémy s minimální konfigurací, jako je třeba použití pouze jeden konektor a bez jednotného přihlašování. V některých případech může být velmi nepostradatelnost pro řešení potíží s přístup nebo obsah v aplikacích, které jsou přístupné prostřednictvím proxy serveru webové ladění nástrojů, jako jsou společnosti Telerik Fiddler. Fiddler se mohou chovat i jako proxy server, které vám pomůžou trasování a ladění provoz pro mobilní platformy, jako je iOS a Android a prakticky cokoli, které lze nastavit na trasu přes proxy server. Zobrazit [Průvodce odstraňováním potíží](application-proxy-troubleshoot.md) Další informace.

## <a name="implement-your-solution"></a>Implementovat řešení

### <a name="deploy-application-proxy"></a>Nasazení služby Proxy aplikací

Postup nasazení Proxy aplikací jsou popsané v tomto [kurz pro přidání lokální aplikace pro vzdálený přístup](application-proxy-add-on-premises-application.md). Pokud nebude instalace úspěšná, vyberte **Poradce při potížích s Proxy aplikací** na portálu nebo pomocí Průvodce odstraňováním potíží [problémů s instalací konektorem agenta aplikačního proxy serveru](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publikování aplikace prostřednictvím Proxy aplikací

Publikování aplikací se předpokládá, že jste splnili všechny předpoklady a zda máte několik konektorů znázorněný jako registrované a aktivní na stránce Proxy aplikací.

Můžete také publikovat aplikace pomocí [Powershellu](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Níže jsou uvedeny některé doporučené postupy při publikování aplikace:

* **Pomocí skupin konektorů**: Přiřadíte skupinu konektorů, který je určený pro publikování každé příslušné aplikace. Doporučujeme vám, že má každá skupina konektor aspoň dva konektory k poskytování vysoké dostupnosti a škálování. V případě, že budete muset služby počítače v libovolném bodě je mít tři konektory optimální. Podívejte se taky [publikování aplikací na samostatných sítí a umístění s využitím skupiny konektorů](application-proxy-connector-groups.md) zobrazíte, jak můžete také použít skupiny konektorů k segmentaci své konektory pomocí sítě nebo umístění.

* **Nastavit časový limit pro back-endu aplikace**: Toto nastavení je užitečné v situacích, kde aplikace může vyžadovat více než 75 sekund na zpracování klientská transakce. Například když klient odešle dotaz na webovou aplikaci, která funguje jako front-endu do databáze. Front-endu, odešle tento dotaz na jeho databáze back-end server a čeká na odpověď, ale době obdrží odpověď na straně klienta konverzace vyprší časový limit. Nastavení časového limitu pro dlouhý poskytuje 180 sekund déle transakce dokončí.

* **Použití typů odpovídající soubor Cookie**

   * **HTTP-Only Cookie**: Tím, že zahrňte příznak HTTPOnly v hlavičkách odpovědi set-cookie HTTP Proxy aplikací poskytuje dodatečné zabezpečení. Toto nastavení pomáhá zmírnit zneužití, jako je skriptování napříč weby (XSS). Ponechte tuto sadu na ne pro klienty nebo Uživatelští agenti, které vyžadují přístup k souboru cookie relace. Připojení služby Brána vzdálené plochy klienta protokolu RDP/MTSC například publikované prostřednictvím Proxy aplikací.

   * **Zabezpečení souboru Cookie**: Pokud soubor cookie je nastaven s atributem zabezpečené, uživatelský agent (aplikace na straně klienta) bude obsahovat pouze soubor cookie v požadavcích HTTP Pokud je požadavek přenosu přes zabezpečený kanál TLS. To pomáhá zmírnit riziko do souboru cookie, jejichž zabezpečení je ohrožené přes nešifrovaných kanály, takže by měla být povolená.

   * **Trvalého souboru Cookie**: Umožňuje zachovat mezi uzavření prohlížeče podle zbývající platná, dokud ho vyprší platnost nebo odstranění souboru cookie relace Proxy aplikací. Používá pro scénáře, kde bohaté aplikace, jako je například office přistupuje k dokumentu v publikované webové aplikaci, aniž by uživatel se znovu zobrazí výzva k ověření. Povolit s rozmyslem však jako trvalé soubory cookie můžete nakonec nechat službu na nebezpečí neoprávněného přístupu, pokud není použit ve spojení s jinými ovládacími prvky kompenzační. Toto nastavení by měla sloužit pouze pro starší aplikace, které nelze sdílení souborů cookie mezi procesy. Je lepší aktualizovat vaši aplikaci obsluhování sdílení souborů cookie mezi procesy místo použití tohoto nastavení.

* **Překládat adresy URL v hlavičkách**: Můžete povolit pro scénáře, ve kterém nejde interního serveru DNS nakonfigurovat tak, aby odpovídaly organizace veřejné obor názvů (známé jako rozdělení DNS). Pokud vaše aplikace vyžaduje hlavičku původního hostitele v žádosti klienta, ponechte tuto hodnotu nastavenou na Ano. Alternativou je, aby konektor použít plně kvalifikovaný název v interní adresa URL pro skutečný provoz a plně kvalifikovaný název domény v externí adresu URL jako hlavičku hostitele směrování. Ve většině případů tuto alternativu by měl umožnit aplikaci fungovat jako obvykle, když přístup ke vzdálené, ale vaši uživatelé přijít o o výhodách odpovídající uvnitř a vně adresy URL.

* **Překládat adresy URL v těle žádosti**: Pokud chcete odkazy z této aplikace, které mají být převedeny do odpovědí zpět do klienta, zapněte překlad odkazu tělo aplikace pro aplikaci. Pokud je povoleno, tato funkce poskytuje nejlepší pokus úsilí při překladu všechny vnitřní propojení, které Proxy aplikace vyhledá v odpovědi HTML a CSS vracené klientům. Je užitečné při publikování aplikací, které obsahují pevně zakódované absolutní nebo NetBIOS shortname odkazů v obsahu nebo aplikací s obsahem, který odkazuje na další místní aplikace.

Pro scénáře, ve kterém publikované aplikace odkazy na další publikované aplikace povolte překlad odkazu pro každou aplikaci, abyste měli kontrolu nad komfortem při uživatele na úrovni pro aplikaci.

Předpokládejme například, že budete mít tři aplikace publikované prostřednictvím Proxy aplikací, které jsou všechny se navzájem propojují: Výhody, náklady a cesty, plus čtvrtý aplikace, zpětnou vazbu, která není publikované prostřednictvím Proxy aplikací.

![Obrázek 1](media/App-proxy-deployment-plan/link-translation.png)

Když povolíte překlad odkazu pro aplikaci výhody, odkazy na výdaje a cestovní ruch přesměrování na externí adresy URL pro tyto aplikace tak, aby k nim může přistupovat uživatele, kteří používají aplikace z mimo podnikovou síť. Odkazy z výdaje a cestovní ruch zpět na výhody nefungují, protože překladu odkazu není povolená pro tyto dvě aplikace. Odkaz na zpětnou vazbu není přesměrovat, protože neexistuje žádná externí adresa URL, takže uživatelé pomocí aplikace výhody nebudou mít přístup k aplikaci zpětné vazby z mimo podnikovou síť. Zobrazit podrobné informace o [propojit překladu a další možnosti přesměrování](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Přístup k aplikaci

Existuje několik možností pro správu přístup k Proxy aplikace publikované prostředky, proto zvolte nejvhodnější pro vaše potřeby daný scénář a škálovatelnost. Zahrnout běžné přístupy: pomocí místních skupin, které se synchronizuje pomocí služby Azure AD Connect, vytvoření dynamické skupiny ve službě Azure AD podle uživatelských atributů, pomocí samoobslužných skupin, které spravuje nástroj vlastník prostředku nebo jejich kombinaci. Zobrazit propojené prostředky pro výhodami každého z nich.

Nejvíce přímo dopředné způsob přiřazení uživatelé přístup k aplikaci se data přesunou do **uživatelů a skupin** možnosti v levém podokně publikované aplikace a přímo přiřazení skupiny nebo jednotlivce.

![Obrázek 24](media/App-proxy-deployment-plan/add-user.png)

Uživatelům samoobslužné služby přístup do vaší aplikace můžete také povolit přiřazením skupiny, které nejsou aktuálně členem a samoobslužné možnosti konfigurace.

![Obrázek 25](media/App-proxy-deployment-plan/allow-access.png)

Pokud je povoleno, uživatelé pak budou moct přihlásit k portálu a požádat o přístup MyApps a buď být automaticky schválí a přidat do již povolené samoobslužné skupiny nebo vyžadují schválení od určené schvalovatele.

Uživatelé typu Host může být také [pozvat interní aplikace publikované prostřednictvím Proxy aplikací pomocí Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

K místním aplikacím, které jsou normálně dostupné anonymně, bez ověřování, které vyžadují můžete chtít zakázat možnost nacházejících se v aplikaci prvku **vlastnosti**.

![Obrázek 26](media/App-proxy-deployment-plan/assignment-required.png)


Opuštění tato možnost nastavená na Ne umožňuje uživatelům přístup k místní aplikaci prostřednictvím Proxy aplikace Azure AD bez oprávnění, takže používejte opatrně.

Po publikování vaší aplikace bude dostupná tak, že zadáte jeho externí adresu URL v prohlížeči nebo na jeho ikonu [ https://myapps.microsoft.com ](https://myapps.microsoft.com/).

### <a name="enable-pre-authentication"></a>Povolit předběžné ověření

Ověřte, že je vaše aplikace přístupné prostřednictvím Proxy aplikací, přístupu přes externí adresu URL. 

1. Přejděte do **Azure Active Directory** > **podnikové aplikace** > **všechny aplikace** a zvolte aplikaci, kterou chcete spravovat.

2. Vyberte **Proxy aplikací**.

3. V **předběžné ověření** pole, použijte rozevírací seznam a vyberte **Azure Active Directory**a vyberte **Uložit**.

Pomocí předběžného ověřování povoleno Azure AD vyzve uživatele nejprve pro ověření a pokud jednotného přihlašování je configued pak back endové aplikace se také ověří uživatele před udělením přístupu k aplikaci. Změna režimu předběžné ověření z průchod do služby Azure AD také nakonfiguruje externí adresu URL pomocí protokolu HTTPS, takže jakékoli aplikace pro protokol HTTP ve výchozím nastavení bude nyní zabezpečen pomocí protokolu HTTPS.

### <a name="enable-single-sign-on"></a>Povolit jednotné přihlašování

Jednotné přihlašování poskytuje nejlepší uživatelské prostředí a zabezpečení, protože uživatelé stačí přihlásit se jednou při přístupu k Azure AD. Jakmile uživatel provedl předběžné ověření, jednotné přihlašování se provádí pomocí ověřování Proxy aplikací služby konektoru pro místní aplikace jménem uživatele. Back-end aplikace zpracovává přihlášení, jako by šlo uživatel sami. 

Výběr **průchozí** možnost umožňuje uživatelům přístup k publikované aplikaci, aniž byste museli ověření do služby Azure AD.

Provádí se jednotné přihlašování je možné jenom že pokud Azure AD můžete identifikovat uživatele, žádosti o přístup k prostředku, takže vaše aplikace musí být nakonfigurován pro předběžné ověřování uživatelů pomocí Azure AD při přístupu pro jednotné přihlašování funkci, v opačném případě možnosti jednotného přihlašování se deaktivuje.

Čtení [jednotné přihlašování k aplikacím ve službě Azure AD](what-is-single-sign-on.md) vám pomůže vybrat nejvhodnější metodu jednotného přihlašování, při konfiguraci vašich aplikací.

###  <a name="working-with-other-types-of-applications"></a>Práce s jinými typy aplikací

Azure AD Application Proxy můžete také podpora aplikací, které bylo vyvinuto pomocí našich knihovna ověřování Azure AD ([ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) nebo knihovna Microsoft Authentication Library ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)). Podporuje nativní klientské aplikace prostřednictvím služby Azure AD, které jsou vystavené tokeny doručení informací hlavičky požadavku klienta, aby provedl předběžné ověření jménem uživatele.

Čtení [publikování mobilní a nativní klientské aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) a [aplikace nezaložené na deklaracích](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) Další informace o dostupné konfigurace proxy aplikací.

### <a name="use-conditional-access-to-strengthen-security"></a>Použití podmíněného přístupu k posílení zabezpečení

Zabezpečení aplikací vyžaduje sadu pokročilé možnosti zabezpečení, které můžete chránit a odpovědět na složité hrozby v místním prostředí i v cloudu. Útočníci často získat přístup k podnikové síti prostřednictvím weak, výchozích nebo zcizených přihlašovacích údajů uživatelů.  Zabezpečení založené na identitách Microsoft snižuje zatížení krádeže přihlašovacích údajů správou a ochranou privilegovaných a neprivilegovaných identit.

Tyto funkce slouží k podpoře Azure AD Application Proxy:

* A podmíněného přístupu na základě umístění uživatele: Zachovat citlivých dat chráněných omezením přístupu uživatelů na základě geografického umístění nebo IP adresu se [zásad podmíněného přístupu podle umístění](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Podmíněný přístup podle zařízení: Ujistěte se pouze zaregistrovaná, schválené a vyhovujících zařízeních přístup k firemní data s [podmíněného přístupu podle zařízení](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Podmíněný přístup založený na aplikaci: Pracovní nemá zastaví, pokud uživatel není v podnikové síti. [Zabezpečený přístup k podnikovým aplikacím cloudové a místní](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) a zachování kontroly s podmíněným přístupem.

* Na základě rizik podmíněného přístupu: Chraňte svoje data před hackery se zlými úmysly s [zásady podmíněného přístupu na základě rizik](https://www.microsoft.com/cloud-platform/conditional-access) , který lze použít na všechny aplikace a uživatele, zda v místním nebo v cloudu.

* Azure AD Access Panel: Proxy aplikací služby nasazené, a aplikace bezpečně publikovat nabízejí uživatelům jednoduchý hub ke zjištění a přístupu k aplikacím. Zvyšte svou produktivitu pomocí samoobslužné funkce, jako je například možnost žádat o přístup k nové aplikace a skupiny nebo spravovat přístup k těmto prostředkům jménem jiných uživatelů, až [přístupového panelu](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Správu vaší implementace.

### <a name="required-roles"></a>Požadované role

Microsoft nejzávažnějších zásady poskytování nejmenšími možnými oprávněními a provádět požadované úlohy s využitím Azure AD. [Projděte si různých rolí Azure, které jsou k dispozici](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) a zvolit ten správný, uspokojit požadavky každé osoby. Některé role možná muset použít dočasně a odebrat po dokončení nasazení.

| Role na podnikání| Obchodní úlohy| Role Azure AD |
|---|---|---|
| Pomoc správci helpdesku | Obvykle omezené nebo kvalifikující koncový uživatel hlášené problémy a provádět omezené úlohy, jako je například změna hesel uživatelů, proto už není platná obnovovací tokeny a monitorují stav služeb. | Správce technické podpory |
| Správce identit| Problémy související s čtení AD přihlášení k Azure a protokoly auditu chcete-li ladit Proxy aplikací.| Čtenář zabezpečení |
| Vlastník aplikace.| Vytvářejte a spravujte všechny aspekty podnikové aplikace, registrace aplikací a nastavení proxy aplikací.| Správce aplikace |
| Správce infrastruktury | Certifikát výměny vlastníka | Správce aplikace |

Minimalizace počtu uživatelů, kteří mají přístup k zabezpečené informace nebo materiály vám pomůže snížit pravděpodobnost škodlivých objektu actor získání neoprávněného přístupu nebo neúmyslně vliv na prostředek citlivé autorizovaný uživatel. 
 
Nicméně uživatelé stále potřebují provádět každodenní privilegované operace, tak prosazování just-in-time (JIT) na základě [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) zásady, které poskytují na vyžádání privilegovaného přístupu k prostředkům Azure a Azure AD je naše doporučenému přístupu k efektivní správě přístup pro správu a auditování.

### <a name="reporting-and-monitoring"></a>Monitorování a vytváření sestav

Azure AD můžete poskytování dalších přehledů o zřizování využití a provozního stavu pomocí protokolů auditu a sestav uživatelů vaší organizace. 

#### <a name="application-audit-logs"></a>Protokoly auditu aplikací

Tyto protokoly poskytuje podrobné informace o přihlašování k aplikacím nakonfigurovaná s Proxy aplikací a zařízení a uživatele, kteří používají aplikaci. Protokoly auditu se nachází na webu Azure Portal a rozhraní API auditu pro export.

#### <a name="windows-event-logs-and-performance-counters"></a>Protokoly událostí Windows a čítače výkonu

Konektory mají správce a relace protokoly. Protokoly správce obsahují klíče události a jejich chyby. Relace protokolů zahrnout všechny transakce a jejich podrobnosti zpracování. Protokoly a čítače jsou umístěny v protokolu událostí Windows a použít tento [kurz ke konfiguraci zdroje dat protokolu událostí ve službě Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Průvodce odstraňováním potíží a kroky

Další informace o běžných problémech a způsob jejich řešení s naší příručce ke [řešení potíží s](application-proxy-troubleshoot.md) chybové zprávy. 

V následujících článcích zahrnují běžné scénáře, které lze použít také k vytvoření průvodce řešením potíží pro podpora v organizaci. 

* [Potíže při zobrazování stránky aplikace](application-proxy-page-appearance-broken-problem.md)
* [Načítání aplikace trvá příliš dlouho](application-proxy-page-load-speed-problem.md)
* [Odkazy na stránce aplikace nefungují](application-proxy-page-links-broken-problem.md)
* [Jaké porty je potřeba odemknout pro mou aplikaci](application-proxy-connectivity-ports-how-to.md)
* [Ve skupině konektorů není žádný fungující konektor pro mou aplikaci](application-proxy-connectivity-no-working-connector.md)
* [Konfigurace na portálu pro správu](application-proxy-config-how-to.md)
* [Konfigurace jednotného přihlašování k mé aplikaci](application-proxy-config-sso-how-to.md)
* [Potíže při vytváření aplikace na portálu pro správu](application-proxy-config-problem.md)
* [Konfigurace omezeného delegování Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Konfigurace s využitím PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [Nelze získat přístup k této chybě podnikové aplikace](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Potíže při instalaci konektoru agenta proxy aplikací](application-proxy-connector-installation-problem.md)
* [Problém přihlášení](application-sign-in-problem-on-premises-application-proxy.md)
