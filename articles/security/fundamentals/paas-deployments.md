---
title: Doporučené postupy pro zabezpečená nasazení PaaS – Microsoft Azure
description: Seznamte se s osvědčenými postupy pro navrhování, vytváření a správu zabezpečených cloudových aplikací v Azure a seznamte se s výhodami zabezpečení PaaS oproti jiným modelům cloudových služeb.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 0643ae792c890b65f239d9a0c16a05639dd4f8b9
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811473"
---
# <a name="securing-paas-deployments"></a>Zabezpečení nasazení PaaS

Tento článek obsahuje informace, které vám pomohou:

- Seznamte se s výhodami zabezpečení hostování aplikací v cloudu
- Vyhodnoťte výhody zabezpečení platformy jako služby (PaaS) oproti jiným modelům cloudových služeb
- Změna zaměření zabezpečení z zaměřeného na síť na přístup zabezpečení perimetru zaměřený na identitu
- Implementace obecných doporučení týkajících se osvědčených postupů zabezpečení PaaS

[Vývoj zabezpečených aplikací v Azure](abstract-develop-secure-apps.md) je obecným průvodcem bezpečnostními otázkami a ovládacími prvky, které byste měli zvážit v každé fázi životního cyklu vývoje softwaru při vývoji aplikací pro cloud.

## <a name="cloud-security-advantages"></a>Výhody zabezpečení cloudu
Je důležité porozumět [rozdělení odpovědnosti](shared-responsibility.md) mezi vás a společnost Microsoft. Místně vlastníte celý zásobník, ale když přejdete do cloudu, některé povinnosti se přenesou do Microsoftu.

Být [v cloudu](shared-responsibility.md#cloud-security-advantages)má své výhody zabezpečení . V místním prostředí mají organizace pravděpodobně k dispozici nesplněné povinnosti a omezené prostředky, které mohou investovat do zabezpečení, což vytváří prostředí, ve kterém útočníci mohou zneužít chyby zabezpečení ve všech vrstvách.

Organizace mohou zlepšit svou detekci hrozeb a dobu odezvy pomocí cloudových funkcí zabezpečení poskytovatele a cloudové inteligence.  Přesunutím odpovědnosti na poskytovatele cloudu mohou organizace získat větší pokrytí zabezpečení, což jim umožní přerozdělit zdroje zabezpečení a rozpočet na jiné obchodní priority.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Výhody zabezpečení modelu cloudových služeb PaaS
Podívejme se na výhody zabezpečení nasazení Azure PaaS versus místní.

![Bezpečnostní výhody PaaS](./media/paas-deployments/advantages-of-paas.png)

Počínaje v dolní části zásobníku, fyzické infrastruktury, Microsoft zmírňuje společná rizika a odpovědnosti. Vzhledem k tomu, že cloud společnosti Microsoft je neustále sledován společností Microsoft, je těžké zaútočit. Nemá smysl, aby útočník sledoval cloud Microsoftu jako cíl. Pokud útočník nemá mnoho peněz a prostředků, je pravděpodobné, že se přesune k jinému cíli.  

Uprostřed zásobníku není žádný rozdíl mezi nasazením PaaS a místní. Na aplikační vrstvě a vrstvě správy účtu a přístupu máte podobná rizika. V části další kroky tohoto článku vás provedeme doporučenými postupy pro odstranění nebo minimalizaci těchto rizik.

V horní části zásobníku, správy dat a správy práv, budete mít na jedno riziko, které lze zmírnit správu klíčů. (Správa klíčů je zahrnuta v osvědčených postupech.) Zatímco správa klíčů je další odpovědnost, máte oblasti v nasazení PaaS, které již není třeba spravovat, takže můžete přesunout prostředky na správu klíčů.

Platforma Azure také poskytuje silnou ochranu DDoS pomocí různých technologií založených na síti. Všechny typy metod ochrany DDoS založené na síti však mají své limity na základě propojení a datového centra. Abyste se vyhnuli dopadu velkých útoků DDoS, můžete využít základní cloudové funkce Azure, která vám umožní rychle a automaticky škálovat kapacitu pro obranu proti útokům DDoS. Podrobněji se podíváme na to, jak to můžete udělat v článcích doporučených postupů.

## <a name="modernizing-the-defenders-mindset"></a>Modernizace myšlení obránce
S nasazením PaaS přichází posun ve vašem celkovém přístupu k zabezpečení. Přejdete od nutnosti kontrolovat vše sami ke sdílení odpovědnosti se společností Microsoft.

Dalším významným rozdílem mezi PaaS a tradičními místními nasazeními je nové zobrazení toho, co definuje obvod primárního zabezpečení. Historicky primární obvod zabezpečení v místním prostředí byla vaše síť a většina místních návrhů zabezpečení používá síť jako primární pivot zabezpečení. Pro nasazení PaaS, jsou lépe obsluhovány tím, že považujete identitu za primární obvod zabezpečení.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Přijmout zásadu identity jako primárního bezpečnostního obvodu
Jednou z pěti základních charakteristik cloud computingu je široký přístup k síti, díky čemuž je myšlení zaměřené na síť méně relevantní. Cílem velké části cloud computingu je umožnit uživatelům přístup k prostředkům bez ohledu na umístění. Pro většinu uživatelů, jejich umístění bude někde na internetu.

Následující obrázek znázorňuje, jak se obvod zabezpečení vyvinul z obvodu sítě na obvod identity. Zabezpečení se stává méně o obraně vaší sítě a více o obraně dat, stejně jako o správě zabezpečení vašich aplikací a uživatelů. Hlavní rozdíl je v tom, že chcete posunout zabezpečení blíže k tomu, co je pro vaši společnost důležité.

![Identita jako nový bezpečnostní obvod](./media/paas-deployments/identity-perimeter.png)

Zpočátku služby Azure PaaS (například webové role a Azure SQL) za předpokladu, malé nebo žádné tradiční ochrany obvodu sítě. Bylo zřejmé, že účelem prvku bylo být vystaven internetu (webová role) a že ověřování poskytuje nový obvod (například BLOB nebo Azure SQL).

Moderní bezpečnostní postupy předpokládají, že protivník prolomil obvod sítě. Proto se moderní obranné postupy přesunuly k identitě. Organizace musí vytvořit obvod zabezpečení založené na identitě se silnou hygienou ověřování a autorizace (doporučené postupy).

Principy a vzory pro obvod sítě jsou k dispozici po celá desetiletí. Naproti tomu průmysl má relativně méně zkušeností s používáním identity jako primárního bezpečnostního obvodu. S tím řekl, jsme nashromáždili dostatek zkušeností, aby některé obecné doporučení, které jsou prokázány v této oblasti a vztahují se na téměř všechny služby PaaS.

Následují doporučené postupy pro správu obvodu identity.

**Osvědčený postup:** Zabezpečte své klíče a přihlašovací údaje k zabezpečení nasazení PaaS.   
**Podrobnosti**: Ztráta klíčů a přihlašovacích údajů je běžný problém. Můžete použít centralizované řešení, kde klíče a tajné klíče mohou být uloženy v modulech hardwarového zabezpečení (HSM). [Azure Key Vault](../../key-vault/key-vault-overview.md) chrání vaše klíče a tajné klíče šifrováním ověřovacích klíčů, klíčů účtů úložiště, šifrovacích klíčů dat, souborů Pfx a hesel pomocí klíčů chráněných bezpečnostními počítači.

**Osvědčený postup**: Nevložte přihlašovací údaje a další tajné klíče do zdrojového kódu nebo GitHubu.   
**Detail**: Jediná věc horší než ztráta klíčů a přihlašovacích údajů je mít neoprávněnou stranu získat přístup k nim. Útočníci mohou využít technologie bot k vyhledání klíčů a tajných kódů uložených v úložištích kódu, jako je GitHub. Nevložte klíč a tajné klíče do těchto úložišť veřejného kódu.

**Osvědčený postup**: Chraňte rozhraní pro správu virtuálních počítačů v hybridních službách PaaS a IaaS pomocí rozhraní pro správu, které umožňuje přímo vzdálenou správu těchto virtuálních počítačů.   
**Podrobnosti:** Lze použít protokoly vzdálené správy, jako jsou [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607)a [vzdálená komunikace prostředí PowerShell.](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) Obecně doporučujeme nepovolit přímý vzdálený přístup k virtuálním uživatelům z internetu.

Pokud je to možné, použijte alternativní přístupy, jako je použití virtuálníprivátních sítí ve virtuální síti Azure. Pokud alternativní přístupy nejsou k dispozici, ujistěte se, že používáte komplexní přístupová hesla a dvoufaktorové ověřování (například [Azure Multi-Factor Authentication).](/azure/active-directory/authentication/multi-factor-authentication)

**Osvědčený postup**: Používejte silné platformy pro ověřování a autorizaci.   
**Podrobnosti**: Používejte federované identity ve službě Azure AD namísto vlastních uživatelských úložišť. Při použití federovaných identit můžete využít přístup založený na platformě a delegovat správu autorizovaných identit na své partnery. Federovaný přístup identity je obzvláště důležitý, když jsou zaměstnanci ukončeni a že informace musí být zohledněny prostřednictvím více systémů identity a autorizace.

Místo vlastního kódu používejte mechanismy ověřování a autorizace dodané platformou. Důvodem je, že vývoj vlastního ověřovacího kódu může být náchylný k chybám. Většina vašich vývojářů nejsou bezpečnostní odborníci a je nepravděpodobné, že by si byli vědomi jemností a nejnovějšího vývoje v ověřování a autorizaci. Komerční kód (například od společnosti Microsoft) je často rozsáhle kontrolována zabezpečení.

Použijte dvoufaktorové ověřování. Dvoufaktorové ověřování je aktuální standard pro ověřování a autorizaci, protože se vyhýbá slabým místům zabezpečení, které jsou vlastní typům uživatelského jména a hesla ověřování. Přístup k rozhraním Azure management (portal/remote PowerShell) a službám orientovaným na zákazníky by měl být navržen a nakonfigurován tak, aby používal [azure multifaktorové ověřování](/azure/active-directory/authentication/multi-factor-authentication).

Používejte standardní ověřovací protokoly, například OAuth2 a Kerberos. Tyto protokoly byly rozsáhle recenzovány a jsou pravděpodobně implementovány jako součást knihovny platformy pro ověřování a autorizaci.

## <a name="use-threat-modeling-during-application-design"></a>Použití modelování hrozeb při návrhu aplikace
Životní [cyklus vývoje zabezpečení](https://www.microsoft.com/en-us/sdl) společnosti Microsoft určuje, že týmy by se měly zapojit do procesu nazývaného modelování hrozeb během fáze návrhu. Pro usnadnění tohoto procesu společnost Microsoft vytvořila [nástroj Pro modelování hrozeb SDL](/azure/security/azure-security-threat-modeling-tool). Modelování návrhu aplikace a výčet kroků [krok](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) hrozeb přes všechny hranice důvěryhodnosti můžete zachytit chyby návrhu brzy.

V následující tabulce jsou uvedeny hrozby STRIDE a některé příklady skutečnosti snižující závažnost rizika, které používají funkce Azure. Tato skutečnosti snižující závažnost rizika nebudou fungovat v každé situaci.

| Hrozba | Vlastnost zabezpečení | Potenciální skutečnosti snižující závažnost rizika platformy Azure |
| --- | --- | --- |
| Falšování identity | Authentication | Vyžadovat připojení HTTPS. |
| Falšování | Integrita | Ověřte certifikáty TLS/SSL. |
| Odmítnutí | Neodvolatelnost | Povolte [monitorování a diagnostiku](/azure/architecture/best-practices/monitoring)Azure . |
| Zveřejňování informací | Důvěrnost | Šifrování citlivých dat v klidovém stavu pomocí [servisních certifikátů](/rest/api/appservice/certificates). |
| Odmítnutí služby | Dostupnost | Sledujte metriky výkonu pro potenciální podmínky odmítnutí služby. Implementujte filtry připojení. |
| Zvýšení oprávnění | Autorizace | Používejte [správu privilegovaných identit](/azure/active-directory/privileged-identity-management/subscription-requirements). |

## <a name="develop-on-azure-app-service"></a>Vývoj ve službě Azure App Service
[Azure App Service](/azure/app-service/overview) je nabídka PaaS, která umožňuje vytvářet webové a mobilní aplikace pro jakoukoli platformu nebo zařízení a připojovat se k datům kdekoli, v cloudu nebo místně. Služba App Service zahrnuje webové a mobilní funkce, které byly dříve doručovány samostatně jako Weby Azure a Mobilní služby Azure. Obsahuje také nové možnosti pro automatizaci obchodních procesů a hostování cloudových rozhraní API. Jako jediná integrovaná služba přináší app service bohatou sadu funkcí pro webové, mobilní a integrační scénáře.

Níže jsou uvedeny doporučené postupy pro používání služby App Service.

**Osvědčený postup:** [Ověření pomocí služby Azure Active Directory](/azure/app-service/overview-authentication-authorization).   
**Podrobnosti**: Služba App Service poskytuje službě OAuth 2.0 pro vašeho poskytovatele identity. OAuth 2.0 se zaměřuje na jednoduchost vývojářů klientů a zároveň poskytuje specifické autorizační toky pro webové aplikace, desktopové aplikace a mobilní telefony. Azure AD používá OAuth 2.0, který vám umožní autorizovat přístup k mobilním a webovým aplikacím.

**Osvědčený postup**: Omezte přístup na základě zásad zabezpečení s potřebou znát a co nejméně oprávnění.   
**Podrobnosti**: Omezení přístupu je nezbytné pro organizace, které chtějí vynutit zásady zabezpečení pro přístup k datům. RBAC můžete použít k přiřazení oprávnění uživatelům, skupinám a aplikacím v určitém oboru. Další informace o udělení přístupu uživatelům k aplikacím najdete v tématu [Začínáme se správou přístupu](/azure/role-based-access-control/overview).

**Osvědčený postup:** Chraňte své klíče.   
**Podrobnosti:** Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče, které používají cloudové aplikace a služby. Pomocí trezoru klíčů můžete šifrovat klíče a tajné klíče (například ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat, . PFX soubory a hesla) pomocí klíčů, které jsou chráněny moduly hardwarového zabezpečení (HSM). Pro zvýšené bezpečí můžete klíče importovat nebo generovat v modulech HSM. Další informace najdete v [tématu Azure Key Vault.](/azure/key-vault/key-vault-overview) Trezor klíčů můžete také použít ke správě certifikátů TLS s automatickým obnovením.

**Osvědčený postup**: Omezení příchozích zdrojových adres IP.   
**Podrobnosti**: [Prostředí Služby App Service](/azure/app-service/environment/intro) má funkci integrace virtuální sítě, která vám pomůže omezit příchozí zdrojové IP adresy prostřednictvím skupin zabezpečení sítě. Virtuální sítě umožňují umístit prostředky Azure do neinternetové, směrovatelné sítě, ke které řídíte přístup. Další informace najdete v [tématu Integrace aplikace s virtuální sítí Azure](/azure/app-service/web-sites-integrate-with-vnet).

**Osvědčený postup**: Sledujte stav zabezpečení prostředí služby App Service.   
**Podrobnosti:** Pomocí Azure Security Center můžete monitorovat prostředí služby App Service. Pokud Security Center identifikuje potenciální slabá místa zabezpečení, vytvoří [doporučení,](../../security-center/security-center-virtual-machine-protection.md) která vás provedou procesem konfigurace potřebných ovládacích prvků.

> [!NOTE]
> Monitorování služby App Service je ve verzi preview a je k dispozici pouze na [úrovni Standard](/azure/security-center/security-center-pricing) security center.
>
>

## <a name="install-a-web-application-firewall"></a>Instalace brány firewall webové aplikace
Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Mezi tyto běžné typy zneužití patří mimo jiné například útoky prostřednictvím injektáže SQL nebo skriptování mezi weby. Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování mnoha vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

[Brána firewall webových aplikací (WAF)](/azure/frontdoor/waf-overview) je funkce application gateway, která poskytuje centralizovanou ochranu webových aplikací před běžnými zneužitími a chybami zabezpečení. WAF je založen na pravidlech z [hlavních sad pravidel Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 nebo 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Sledování výkonu aplikací
Monitorování je akt shromažďování a analýzy dat k určení výkonu, stavu a dostupnosti vaší aplikace. Efektivní strategie monitorování vám pomůže porozumět provozu jednotlivých komponent vaší aplikace. Pomáhá vám zvýšit dobu pohotovosti tím, že vás upozorní na kritické problémy, abyste je mohli vyřešit dříve, než se stanou problémy. Také pomáhá detekovat anomálie, které mohou souviset se zabezpečením.

Azure [Application Insights](https://azure.microsoft.com/documentation/services/application-insights) můžete monitorovat dostupnost, výkon a využití vaší aplikace, ať už je hostovaná v cloudu nebo místně. Pomocí Application Insights můžete rychle identifikovat a diagnostikovat chyby ve vaší aplikaci bez čekání na uživatele, aby je nahlásil. Na základě shromažďovaných informací můžete informovaně rozhodovat o údržbě a vylepšení vaší aplikace.

Application Insights obsahuje řadu nástrojů pro interakci se shromažďovanými daty. Application Insights ukládá svoje data do běžného úložiště. Pomocí dotazovacího jazyka Kusto může využívat sdílené funkce, jako jsou výstrahy, řídicí panely a hloubková analýza.

## <a name="perform-security-penetration-testing"></a>Provedení testování průniku zabezpečení
Ověření ochrany zabezpečení je stejně důležité jako testování jiných funkcí. Nastavení [penetračního testování](pen-testing.md) jako standardní součást procesu sestavení a nasazení. Naplánujte pravidelné testy zabezpečení a prohledávání chyb zabezpečení v nasazených aplikacích a sledujte otevřené porty, koncové body a útoky.

Testování Fuzz je metoda pro zjištění selhání programu (chyby kódu) poskytnutím poškozených vstupních dat do rozhraní programu (vstupníbody), které analyzují a spotřebovávají tato data. [Microsoft Security Risk Detection](https://www.microsoft.com/en-us/security-risk-detection/) je cloudový nástroj, který můžete použít k vyhledání chyb a dalších chyb zabezpečení ve vašem softwaru před nasazením do Azure. Nástroj je navržen tak, aby zachytil chyby zabezpečení před nasazením softwaru, takže nemusíte opravovat chybu, řešit pády nebo reagovat na útok po vydání softwaru.


## <a name="next-steps"></a>Další kroky
V tomto článku jsme se zaměřili na výhody zabezpečení nasazení Azure PaaS a doporučené postupy zabezpečení pro cloudové aplikace. Dále se dozvíte doporučené postupy pro zabezpečení webových a mobilních řešení PaaS pomocí konkrétních služeb Azure. Začneme s Azure App Service, Azure SQL Database a Azure SQL Data Warehouse a Azure Storage. Články o doporučených postupech pro jiné služby Azure budou dostupné, odkazy budou uvedeny v následujícím seznamu:

- [Azure App Service](paas-applications-using-app-services.md)
- [Azure SQL Database a Azure SQL Data Warehouse](paas-applications-using-sql.md)
- [Azure Storage](paas-applications-using-storage.md)
- Azure Cache for Redis
- Azure Service Bus
- Brány firewall webových aplikací

Viz [Vývoj zabezpečených aplikací v Azure](abstract-develop-secure-apps.md) pro otázky zabezpečení a ovládací prvky, které byste měli zvážit v každé fázi životního cyklu vývoje softwaru při vývoji aplikací pro cloud.

Podívejte se na [osvědčené postupy a vzory zabezpečení Azure, kde](best-practices-and-patterns.md) najdete další doporučené postupy zabezpečení, které se používají při navrhování, nasazování a správě cloudových řešení pomocí Azure.

K dispozici jsou následující prostředky, které poskytují obecnější informace o zabezpečení Azure a souvisejících službách Microsoftu:
* [Blog týmu zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovějších informacích o azure securityu
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – kde lze nahlásit chyby zabezpečení společnosti Microsoft, včetně problémů s Azure, nebo e-mailemsecure@microsoft.com
