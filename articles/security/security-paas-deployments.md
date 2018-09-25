---
title: Zabezpečení nasazení PaaS | Dokumentace Microsoftu
description: " Vysvětlení zabezpečení výhody PaaS a další modely služeb v cloudu a přečtěte si doporučené postupy pro zabezpečení vašeho nasazení Azure PaaS. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: techlake
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: terrylan
ms.openlocfilehash: 35650eec65fa9181d035c52e6b466985b483500c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036503"
---
# <a name="securing-paas-deployments"></a>Zabezpečení nasazení PaaS

Tento článek obsahuje informace, které vám pomůžou:

- Jaké výhody zabezpečení hostování aplikací v cloudu
- Vyhodnocení výhody zabezpečení platforma jako služba (PaaS) a další modely cloudové služby
- Změnit váš výběr zabezpečení z zaměřené na síť na hraniční zaměřená na identitu zabezpečení přístupu
- Implementace obecné PaaS osvědčené postupy doporučení pro zabezpečení

## <a name="cloud-security-advantages"></a>Výhody zabezpečení cloudu
Existují výhody zabezpečení v cloudu. V místním prostředí, organizace pravděpodobně mají dosud nevyřešených zodpovědnosti a omezené prostředky, které jsou k dispozici investice do zabezpečení, který vytvoří prostředí, kde se útočníci zneužít slabá místa na všech úrovních.

![Výhody zabezpečení cloudovou éru][1]

Organizace můžou zvýšit jejich detekce hrozeb a doby odezvy pomocí možnosti poskytovatele cloudového zabezpečení a inteligentní cloudové funkce.  Přepínáním odpovědnosti poskytovatelem cloudových služeb organizace můžete získat další zabezpečení pokrytí, odkud můžou přidělit jinému uživateli zabezpečení prostředků a rozpočet na jiných obchodních priorit.

## <a name="division-of-responsibility"></a>Dělení zodpovědnosti
Je důležité pochopit, dělení zodpovědnosti mezi vámi a společností Microsoft. On-premises celý balík, které vlastníte, ale při přesunu do cloudu určité podmínky přenos do společnosti Microsoft. Následující matici zodpovědnosti ukazuje oblasti zásobníku v nasazení SaaS, PaaS a IaaS, která je zodpovědná za a Microsoft je zodpovědný za.

![Odpovědnosti zóny][2]

Pro všechny typy nasazení cloudu které vlastníte vašich dat a identity. Zodpovídáte za chránit bezpečnost vašich dat a identity, místní prostředky a cloudové komponenty je ovládací prvek (který se liší podle typu služby).

Odpovědnosti, které jsou vždy zachovány vámi, bez ohledu na typ nasazení, jsou:

- Data
- Koncové body
- Účet
- Správa přístupu

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Zabezpečení výhody paas v cloudu modelu služby
Pomocí stejného matice odpovědnosti, Podívejme se na výhody zabezpečení oproti místnímu nasazení služby Azure PaaS.

![Zabezpečení výhody PaaS][3]

Spouští se v dolní části zásobníku fyzické infrastruktury, Microsoft zmírňuje běžné rizika a odpovědnosti. Protože cloud od Microsoftu je průběžně monitorovat microsoftem, je obtížné vůči útokům. Nemá smysl pro útočníka zneužít cloudu společnosti Microsoft jako cíl. Pokud má útočník spoustu peněz a prostředky, útočník by mohla přesunout na jiný cíl.  

Uprostřed zásobníku není žádný rozdíl mezi nasazení PaaS a místních. Na aplikační vrstvě a vrstvě správy účtu a přístup budete mít podobný rizika. V části Další kroky v tomto článku provedeme vás k osvědčeným postupům pro odstranění nebo minimalizovat těchto rizik.

V horní části zásobníku, zásady správného řízení dat a rights management je provést jeden rizika, která se dají zmírnit správu klíčů. (Správa klíčů je zahrnuté v osvědčené postupy.) Správa klíčů je další odpovědnosti, máte oblastí v nasazení PaaS, které už muset spravovat, takže můžete přesunout prostředky na správu klíčů.

Platforma Azure také nabízí silnou ochranu před útoky DDoS pomocí různých technologií založené na síti. Všechny typy založené na síti metody ochrany před útoky DDoS však mají jejich omezení na základě na propojení a za datacenter. Pokud chcete vyhnout dopad velkých útoků DDoS, můžete využít Azure základní cloudové možnosti povolování můžete rychle a automaticky horizontálního navýšení kapacity se chránit před útoky DDoS. Budeme věnovat podrobněji na jak to udělat v článcích doporučených postupů.

## <a name="modernizing-the-defenders-mindset"></a>Modernizace defender způsob myšlení
Nasazení modelu paas Přijďte shift v celkovým přístupem na zabezpečení. Posunete před nutností řídit všechno sami sdílení odpovědnost s Microsoftem.

Další z největších rozdílů mezi PaaS a tradičních místních nasazení, je nové zobrazení co definuje primárního obvodu zabezpečení. V minulosti obvodu zabezpečení místní byl vaší sítě a většina návrhy zabezpečení místních používat síť jako její primární zabezpečení pivot. U nasazení PaaS jsou lépe vyhovovat vycházet identity jako primárního obvodu zabezpečení.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Přijmout zásady identity jako primárního obvodu zabezpečení
Jeden z pět základních vlastností cloud computing je široký přístup k síti, takže zaměřené na síti uvažujete méně závažné. Cílem velkou část cloud computingu je umožnit uživatelům přístup k prostředkům bez ohledu na umístění. Pro většinu uživatelů jejich umístění bude někde na Internetu.

Následující obrázek ukazuje, jak se vyvinula obvodu zabezpečení z hraniční sítě do identity hraniční. Zabezpečení bude méně o ochraně sítě a další informace o ochrana vašich dat, jakož i Správa zabezpečení aplikací a uživatelů. Klíčovým rozdílem je, že chcete zapsat zabezpečení blíže na to, co je důležité pro vaši společnost.

![Identity jako nové bezpečnostní hraniční sítě][4]

Na začátku služeb Azure PaaS (například webové role a Azure SQL) k dispozici žádné nebo téměř žádné tradiční sítě hraniční obrana. Předpokládá se, že účelem prvku bylo být zpřístupněné Internetu (webové role) a ověření poskytuje nové hraniční síti (například objekt BLOB nebo Azure SQL).

Moderní bezpečnostní postupy předpokládají, že nežádoucí osoba prolomil hranici sítě. Proto postupů moderní defense přesunuli do identity. Organizace musí vytvořit hraniční zabezpečení na základě identit pomocí silného ověřování a autorizace lékařských (doporučené).

Principy a modely pro hraniční síť byly k dispozici desítky let. Naproti tomu odvětví má poměrně méně zkušenosti s používáním identity jako primárního obvodu zabezpečení. To ale nutné dodat jsme nashromáždili dostatek zkušeností poskytnout některé obecná doporučení, které jsou prověřené v poli a použít na téměř všechny služby PaaS.

Níže jsou doporučené postupy pro správu identit hraniční síti.

**Osvědčený postup**: zabezpečení vašich klíčů a přihlašovacích údajů k zabezpečení nasazení PaaS.   
**Podrobnosti o**: ztráty klíčů a přihlašovacích údajů je běžný problém. Můžete použít centralizované řešení, kde klíče a tajné klíče mohou být uloženy v modulech hardwarového zabezpečení. Azure poskytuje v cloudu pomocí modulu hardwarového zabezpečení [Azure Key Vault](../key-vault/key-vault-whatis.md).

**Osvědčený postup**: neumisťujte pověření a dalších tajných kódů v zdrojový kód nebo GitHub.   
**Podrobnosti o**: pouze jednu horší ztráty klíčů a přihlašovacích údajů, je, aniž by bylo neoprávněným stran získat přístup k nim. Útočníci mohou využít výhod bot technologie k vyhledání klíče a tajné kódy uložené v úložištích kódu, jako je například GitHub. Nevkládejte do těchto úložišť kódu veřejné klíče a tajné kódy.

**Osvědčený postup**: ochrana vašeho rozhraní pro správu virtuálních počítačů na hybridní PaaS a IaaS služby pomocí rozhraní pro správu, která vám umožní vzdáleně spravovat tyto virtuální počítače přímo.   
**Podrobnosti o**: Vzdálená správa protokoly jako [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607), a [vzdálené komunikace Powershellu](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) lze použít. Obecně platí doporučujeme vám, že nepovolíte vzdálený přímý přístup k virtuálním počítačům z Internetu.

Pokud je to možné použijte alternativní přístupy, jako je ve virtuální síti Azure pomocí virtuální privátní sítě. Pokud nejsou k dispozici alternativní přístupy, ujistěte se, že používáte komplexní hesla a dvojúrovňového ověřování (například [ověřování Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)).

**Osvědčený postup**: použijte silné ověřování a autorizace platformy.   
**Podrobnosti o**: pomocí federované identity ve službě Azure AD namísto vlastními úložišti uživatelů. Při použití federované identity využít výhod přístupu podle platformy a delegovat správu identit oprávněným partnerům. Federované identity přístup je zvlášť důležité při zaměstnanci budou ukončeny a informace se musí být neprojeví prostřednictvím více systémů identit a autorizace.

Mechanismy ověřování a autorizace, které byly zadané platformy nahrazujícím vlastní kód. Důvodem je, že vývoj vlastního ověřovacího kódu mohou být náchylné k chybám. Většina vašich vývojářů nejsou odborníci na zabezpečení a je nepravděpodobné, že je potřeba vědět odlišnosti a nejnovějším vývoji v ověřování a autorizace. Komerční kódu (např. z Microsoft) je často výrazně revizi zabezpečení.

Pomocí dvojúrovňového ověřování. Dvoufaktorové ověřování je aktuálním standardem pro ověřování a autorizaci, protože se eliminuje slabá místa zabezpečení vyplývajících z uživatelského jména a hesla typy ověřování. Přístup k rozhraní správy Azure (portál/vzdáleného prostředí PowerShell) a služeb určených pro zákazníky by navržené a nakonfigurovány pro použití [ověřování Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md).

Použijte standardní ověřovací protokoly, jako je například OAuth2 nebo protokolu Kerberos. Tyto protokoly se hojně přezkoumány a pravděpodobně jsou implementovány jako součást vaší knihovny platformy pro ověřování a autorizaci.

## <a name="use-threat-modeling-during-application-design"></a>Použijte při návrhu aplikace modelování hrozeb
Microsoft [Security Development Lifecycle](https://www.microsoft.com/en-us/sdl) Určuje, že týmy by měly zapojit do procesu nazývaného hrozeb modelování ve fázi návrhu. Abychom to mohli provést, společnost Microsoft vytvořila [nástroj pro modelování hrozeb SDL](../security/azure-security-threat-modeling-tool.md). Modelování návrhu aplikace a vytváření výčtů [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) hrozeb napříč všechny vztahu důvěryhodnosti může zachytit hranice návrhu chyby raném stádiu.

Následující tabulka uvádí hrozby ROZTEČ a poskytuje některá příklad zmírnění rizik, které používají funkce Azure. Tyto způsoby zmírnění rizik nebudou fungovat ve všech situacích.

| Před internetovými útoky | Vlastnost zabezpečení | Potenciální migrace platformy Azure |
| --- | --- | --- |
| Falšování identity | Authentication | Vyžadovat připojení prostřednictvím protokolu HTTPS. |
| Manipulace | Integrita | Ověření certifikátů SSL. |
| odmítnutí | Neodvolatelnost | Povolení služby Azure [monitorováním a diagnostikou](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). |
| Zpřístupnění informací | Důvěrnost | Šifrovat citlivá data v klidovém stavu pomocí [služby certifikáty](https://docs.microsoft.com/rest/api/appservice/certificates). |
| Útok DoS | Dostupnost | Monitorovat metriky výkonu pro potenciální útok na dostupnost služby podmínky. Implementace připojení filtry. |
| Zvýšení úrovně oprávnění | Autorizace | Použití [Privileged Identity Management](../active-directory/privileged-identity-management/subscription-requirements.md). |

## <a name="develop-on-azure-app-service"></a>Vývoj pro služby Azure App Service
[Azure App Service](../app-service/app-service-web-overview.md) je PaaS nabídka, která umožňuje vytvářet webové a mobilní aplikace pro jakoukoliv platformu nebo zařízení a připojení k datům bez ohledu na, v cloudu nebo místně. App Service zahrnuje webové a mobilní funkce, které byly dříve nabízeli samostatně jako weby Azure a Azure Mobile Services. Obsahuje také nové možnosti pro automatizaci obchodních procesů a hostování cloudových rozhraní API. Jako jediná integrovaná služba App Service přináší bohatou sadu možností pro webové, mobilní a integrační scénáře.

Toto jsou osvědčené postupy při používání služby App Service.

**Osvědčený postup**: [ověřit prostřednictvím Azure Active Directory](../app-service/app-service-authentication-overview.md).   
**Podrobnosti o**: Služba App Service poskytuje službu OAuth 2.0 pro zprostředkovatele identity. OAuth 2.0, zaměřuje na jednoduchost klienta pro vývojáře poskytují konkrétní povolení tocích pro webové aplikace, aplikací klasické pracovní plochy a mobilní telefony. Azure AD používá OAuth 2.0 k umožnění autorizace přístupu k mobilních a webových aplikací.

**Osvědčený postup**: omezení přístupu na základě potřeba znát a principů zabezpečení nejnižších oprávnění.   
**Podrobnosti o**: omezení přístupu je nezbytné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům. RBAC můžete přiřadit oprávnění pro uživatele, skupiny nebo aplikace v určitém rozsahu. Další informace o uživatelům udělíte přístup k aplikacím, najdete v článku [Začínáme se správou přístupu](../role-based-access-control/overview.md).

**Osvědčený postup**: chrání vaše klíče.   
**Podrobnosti o**: Azure Key Vault pomáhá chránit kryptografické klíče a tajné kódy, které cloudové aplikace a služby používat. Se službou Key Vault můžete šifrovat klíče a tajné klíče (např. ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat. Soubory PFX a hesla) pomocí klíčů chráněných moduly hardwarového zabezpečení (HSM). Pro zvýšené bezpečí můžete klíče importovat nebo generovat v modulech HSM. Zobrazit [Azure Key Vault](../key-vault/key-vault-whatis.md) Další informace. Key Vault můžete také spravovat své certifikáty protokolu TLS se automatické obnovení.

**Osvědčený postup**: omezit příchozí zdrojové IP adresy.   
**Podrobnosti o**: [služby App Service Environment](../app-service/environment/intro.md) má funkci integrace virtuální sítě, která pomáhá omezit příchozí zdrojové IP adresy pomocí skupin zabezpečení sítě. Virtuální sítě umožňují umístit prostředky Azure v Internetu jiných výrobců, směrovatelné síti, které řídí přístup k. Další informace najdete v tématu [integrujte svou aplikaci s Azure virtual network](../app-service/web-sites-integrate-with-vnet.md).

**Osvědčený postup**: monitorování stavu zabezpečení služby App Service Environment.   
**Podrobnosti o**: použití služby Azure Security Center k monitorování služby App Service Environment. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří [doporučení](../security-center/security-center-virtual-machine-recommendations.md) , který vás provede procesem konfigurace potřebných kontrol.

> [!NOTE]
> Monitorování služby App Service je ve verzi preview a je k dispozici pouze na [úrovně Standard](../security-center/security-center-pricing.md) služby Security Center.
>
>

## <a name="install-a-web-application-firewall"></a>Instalace firewallu webových aplikací
Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Mezi tyto běžné typy zneužití patří mimo jiné například útoky prostřednictvím injektáže SQL nebo skriptování mezi weby. Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování mnoha vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

[Firewall webových aplikací (WAF)](../application-gateway/waf-overview.md) je funkce služby Application Gateway poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožení zabezpečení. WAF je na základě pravidel ze [základní sady pravidel Application Open Web projektu zabezpečení (OWASP)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 nebo 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Monitorování výkonu aplikací
Monitorování je shromažďování a analýza dat s cílem určit výkon, stav a dostupnost vaší aplikace. Efektivní strategie monitorování vám pomůže porozumět provozu jednotlivých komponent vaší aplikace. Pomůže vám prodloužit dobu provozu podle vás upozorní na kritické problémy tak, aby je mohli vyřešit předtím, než začnou způsobovat problémy. Také pomáhá detekovat anomálie, které může být týkající se zabezpečení.

Použití [Azure Application Insights](http://azure.microsoft.com/documentation/services/application-insights) monitorovat dostupnost, výkon a využití vaší aplikace, jestli je hostovaný v cloudu nebo místně. S využitím Application Insights, můžete rychle identifikovat a diagnostikovat chyby ve vaší aplikaci bez čekání na jejich nahlášení uživatelem. Na základě shromažďovaných informací můžete informovaně rozhodovat o údržbě a vylepšení vaší aplikace.

Application Insights obsahuje řadu nástrojů pro interakci se shromažďovanými daty. Application Insights ukládá svoje data do běžného úložiště. Může využívat sdílené funkce, například upozornění, řídicí panely a hloubkovou analýzu pomocí dotazovacího jazyka Log Analytics.



## <a name="next-steps"></a>Další postup
V tomto článku jsme se zaměřili na výhody zabezpečení nasazení služby Azure PaaS a osvědčené postupy zabezpečení pro cloudové aplikace. Dále se naučíte doporučené postupy pro zabezpečení PaaS webové a mobilní řešení využívající konkrétních služeb Azure. Začneme s Azure App Service, Azure SQL Database a Azure SQL Data Warehouse a Azure Storage. Jakmile budou k dispozici články týkající se doporučených postupů pro ostatní služby Azure, poskytneme vám odkazy v následujícím seznamu:

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Azure SQL Database a Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- [Azure Storage](security-paas-applications-using-storage.md)
- Azure REDIS Cache
- Azure Service Bus
- Brány firewall webových aplikací

Zobrazit [osvědčené postupy zabezpečení Azure a vzory](security-best-practices-and-patterns.md) pro další doporučené postupy zabezpečení, mají použít, když jste návrhu, nasazení a správa cloudových řešení pomocí služby Azure.

Jsou následující prostředky vám poskytnou další obecné informace o zabezpečení Azure a související služby Microsoftu:
* [Blog týmu Azure zabezpečení](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovější vydání v Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – tam, kde mohou být hlášeny chyby zabezpečení společnosti Microsoft, včetně problémů s Azure, nebo prostřednictvím e-mailu secure@microsoft.com

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
