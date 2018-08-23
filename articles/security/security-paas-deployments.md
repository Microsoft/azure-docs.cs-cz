---
title: Zabezpečení nasazení PaaS | Dokumentace Microsoftu
description: " Vysvětlení zabezpečení výhody PaaS a další modely služeb v cloudu a přečtěte si doporučené postupy pro zabezpečení vašeho nasazení Azure PaaS. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: techlake
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: da5d59aaaea8e6186609eb5f3419fba5e67d4279
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "42054016"
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

## <a name="identity-as-the-primary-security-perimeter"></a>Identita jako primárního obvodu zabezpečení
Jeden z pět základních vlastností cloud computing je široký přístup k síti, takže zaměřené na síti uvažujete méně závažné. Cílem velkou část cloud computingu je umožnit uživatelům přístup k prostředkům bez ohledu na umístění. Pro většinu uživatelů jejich umístění bude někde na Internetu.

Následující obrázek ukazuje, jak se vyvinula obvodu zabezpečení z hraniční sítě do identity hraniční. Zabezpečení bude méně o ochraně sítě a další informace o ochrana vašich dat, jakož i Správa zabezpečení aplikací a uživatelů. Klíčovým rozdílem je, že chcete zapsat zabezpečení blíže na to, co je důležité pro vaši společnost.

![Identity jako nové bezpečnostní hraniční sítě][4]

Na začátku služeb Azure PaaS (například webové role a Azure SQL) k dispozici žádné nebo téměř žádné tradiční sítě hraniční obrana. Předpokládá se, že účelem prvku bylo být zpřístupněné Internetu (webové role) a ověření poskytuje nové hraniční síti (například objekt BLOB nebo Azure SQL).

Moderní bezpečnostní postupy předpokládají, že nežádoucí osoba prolomil hranici sítě. Proto postupů moderní defense přesunuli do identity. Organizace musí vytvořit hraniční zabezpečení na základě identit pomocí silného ověřování a autorizace lékařských (doporučené).

## <a name="recommendations-for-managing-the-identity-perimeter"></a>Doporučení pro správu identit hraniční síti

Principy a modely pro hraniční síť byly k dispozici desítky let. Naproti tomu odvětví má poměrně méně zkušenosti s používáním identity jako primárního obvodu zabezpečení. To ale nutné dodat jsme nashromáždili dostatek zkušeností poskytnout některé obecná doporučení, které jsou prověřené v poli a použít na téměř všechny služby PaaS.

Následující možnost shrne obecné osvědčené postupy přistupovat ke správě perimetr identity.

- **Neztraťte klíče nebo přihlašovací údaje** zabezpečení klíčů a přihlašovacích údajů je velmi důležité zabezpečení nasazení PaaS. Došlo ke ztrátě klíčů a přihlašovacích údajů je běžný problém. Jeden dobrým řešením je použití centralizovaného řešení, kde klíče a tajné klíče mohou být uloženy v modulech hardwarového zabezpečení (HSM). Azure poskytuje v cloudu pomocí modulu hardwarového zabezpečení [Azure Key Vault](../key-vault/key-vault-whatis.md).
- **Neumisťujte pověření a dalších tajných kódů do zdrojového kódu nebo GitHub** jediné, co horší ztráty klíčů a přihlašovacích údajů, je, aniž by bylo neoprávněným stran získat přístup k nim. Útočníci mohou využít výhod bot technologie k vyhledání klíče a tajné kódy uložené v úložištích kódu, jako je například GitHub. Neumisťujte klíče a tajné klíče v těchto veřejných úložišť zdrojového kódu.
- **Ochrana vašeho rozhraní pro správu virtuálních počítačů v hybridním službám PaaS a IaaS** služby IaaS a PaaS spouštět na virtuálních počítačích (VM). Podle toho, jaké služby jsou k dispozici několik rozhraní pro správu této povolit, můžete vzdáleně spravovat tyto virtuální počítače přímo. Protokoly pro vzdálenou správu jako [protokolu Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell), [protokolu RDP (Remote Desktop)](https://support.microsoft.com/kb/186607), a [vzdáleného prostředí PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) lze použít. Obecně platí doporučujeme vám, že nepovolíte vzdálený přímý přístup k virtuálním počítačům z Internetu. Pokud je k dispozici, měli byste použít alternativní přístupy, například pomocí virtuální privátní sítě do virtuální sítě Azure. Pokud nejsou k dispozici alternativní přístupy, ujistěte se, že používáte komplexní hesla a pokud je k dispozici, dvojúrovňového ověřování (například [ověřování Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)).
- **Použijte silné ověřování a autorizace platformy**

  - Pomocí federované identity ve službě Azure AD namísto vlastními úložišti uživatelů. Při použití federované identity využít výhod přístupu podle platformy a delegovat správu identit oprávněným partnerům. Federovanou identitu přístup je zvlášť důležité ve scénářích, když zaměstnanci budou ukončeny a informace se musí být neprojeví prostřednictvím více systémů identit a autorizace.
  - Mechanismy ověřování a autorizace platformy nahrazujícím vlastní kód. Důvodem je, že vývoj vlastního ověřovacího kódu mohou být náchylné k chybám. Většina vašich vývojářů nejsou odborníci na zabezpečení a je nepravděpodobné, že je potřeba vědět odlišnosti a nejnovějším vývoji v ověřování a autorizace. Komerční kód (například od Microsoftu) je často výrazně revizi zabezpečení.
  - Použití služby Multi-Factor authentication. Ověřování službou Multi-Factor Authentication je aktuálním standardem pro ověřování a autorizaci, protože se eliminuje slabá místa zabezpečení vyplývajících z uživatelského jména a hesla typy ověřování. Přístup k rozhraní Azure Správa (portál/vzdáleného prostředí PowerShell) a služeb určených pro zákazníky by navržené a nakonfigurovány pro použití [Azure Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md).
  - Použijte standardní ověřovací protokoly, jako je například OAuth2 nebo protokolu Kerberos. Tyto protokoly se hojně přezkoumány a pravděpodobně jsou implementovány jako součást vaší knihovny platformy pro ověřování a autorizaci.

## <a name="next-steps"></a>Další postup
V tomto článku jsme se zaměřili na výhody zabezpečení nasazení služby Azure PaaS. Dále se naučíte doporučené postupy pro zabezpečení PaaS webové a mobilní řešení. Začneme s Azure App Service, Azure SQL Database a Azure SQL Data Warehouse. Jakmile budou k dispozici články týkající se doporučených postupů pro ostatní služby Azure, poskytneme vám odkazy v následujícím seznamu:

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Azure SQL Database a Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- [Azure Storage](security-paas-applications-using-storage.md)
- Azure REDIS Cache
- Azure Service Bus
- Brány firewall webových aplikací

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
