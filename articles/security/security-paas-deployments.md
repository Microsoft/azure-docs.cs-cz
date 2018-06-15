---
title: Zabezpečení nasazení PaaS | Microsoft Docs
description: " Pochopit výhody zabezpečení PaaS a dalších cloudu modely služeb a další doporučené postupy pro zabezpečení vašeho nasazení Azure PaaS. "
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
ms.openlocfilehash: f19c52629a997687692eef9bce2e13b2b7894052
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31794731"
---
# <a name="securing-paas-deployments"></a>Zabezpečení nasazení PaaS

Tento článek obsahuje informace, které vám pomůže:

- Pochopit výhody zabezpečení hostování aplikací v cloudu
- Vyhodnocení výhod zabezpečení platforma jako služba (PaaS) a ostatní modely cloudové služby
- Změnit váš výběr zabezpečení z zaměřené na síť na hraniční zaměřená na identitu zabezpečení přístup
- Implementovat obecné PaaS zabezpečení podle doporučení

## <a name="cloud-security-advantages"></a>Výhody zabezpečení cloudu
Existují zvyšuje úroveň zabezpečení je v cloudu. V prostředí místní organizace pravděpodobně mít unmet odpovědnosti a prostředků omezené investovat do zabezpečení, který vytvoří prostředí, kde se útočníci můžou zneužívají ohrožení zabezpečení na všechny vrstvy.

![Výhody zabezpečení letopočtu cloudu][1]

Organizace mohou jejich detekce hrozeb a časy odezvy můžete zlepšit použitím funkce zprostředkovatele zabezpečení založené na cloudu a intelligence cloudu.  Přepínáním odpovědnosti k poskytovateli cloudu organizace můžete získat další pokrytí, zabezpečení, které umožňuje, aby znovu přidělte zabezpečení prostředků a nároky na jiné firmy priority.

## <a name="division-of-responsibility"></a>Dělení zodpovědnosti
Je důležité si uvědomit, dělení zodpovědnosti mezi vámi a společností Microsoft. Místní, vlastní celý zásobník, ale při přechodu do cloudu některé odpovědnosti přenos do společnosti Microsoft. Následující odpovědnost matice zobrazí oblasti zásobníku v SaaS, IaaS a PaaS nasazení, která je zodpovědná za a Microsoft je zodpovědná za.

![Odpovědnost zóny][2]

Pro všechny typy nasazení cloudu které vlastníte dat a identity. Jste zodpovědní za chránit bezpečnost vašich dat a identity, místních prostředků a součástí cloudu můžete řídit, (která se liší podle typu služby).

Odpovědnosti, které jsou vždy zachovány vy, bez ohledu na typ nasazení, jsou:

- Data
- Koncové body
- Účet
- Správa přístupu

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Výhody zabezpečení PaaS cloudu modelu služby
Pomocí stejné matice odpovědnost, podíváme se na výhody zabezpečení nasazení Azure PaaS a místní.

![Výhody zabezpečení PaaS][3]

Spouštění v dolní části zásobníku, fyzické infrastruktuře, Microsoft snižuje běžné rizik a odpovědnosti. Vzhledem k cloudu Microsoftu je průběžně monitorovat společností Microsoft, je obtížné útoku. Nemá smysl pro útočníka vykonávat cloudem Microsoftu jako cíl. Pokud útočník má spoustu peníze a prostředky, útočník je pravděpodobné, chcete-li přesunout jiný cíl.  

Prostřední vrstvy není žádný rozdíl mezi nasazení PaaS a místně. Na aplikační vrstvu a vrstva správy účtu a přístup mají podobné rizika. V části Další kroky v tomto článku provedeme vás k osvědčené postupy pro odstranění nebo minimalizace těchto rizik.

V horní části zásobníku, řízení dat a rights management můžete provést pro jeden riziko, že může být omezeny správy klíčů. (Správa klíčů je popsaná v osvědčených postupů). I správu klíčů je další odpovědnost, máte oblastí v nasazení PaaS, které už muset spravovat, aby prostředky můžete posunutí pro správu klíčů.

Platformy Azure také poskytuje silnou ochranu DDoS pomocí různých technologií založené na síti. Všechny typy metody ochrany založené na síti DDoS však mít jejich omezení na základě na propojení a za datacenter. Abyste se vyhnuli dopad útoků DDoS velké, můžete využít Azure základní cloudu možnost povolení můžete rychle a automaticky škálovat bránit proti útokům DDoS. Budeme věnovat podrobněji na tom, jak to můžete provést v článcích doporučené postupy.

## <a name="modernizing-the-defenders-mindset"></a>Modernizing přistupovat defender
Nasazení PaaS má posun v celkového přístupu k zabezpečení. Můžete posunutí z museli řídit všechno sami sdílení odpovědnost se společností Microsoft.

Další důležité rozdíl mezi PaaS a tradičních místních nasazení, je nové zobrazení co definuje hraniční primární zabezpečení. V minulosti byl primární místní zabezpečení hraniční síti a většina návrhů zabezpečení místní používat síť jako jeho pivot primární zabezpečení. U nasazení PaaS jsou lépe obsloužených zvažování identity jako primární zabezpečení hraniční síti.

## <a name="identity-as-the-primary-security-perimeter"></a>Identity jako primární zabezpečení hraniční síti
Jeden z pět základních vlastností technologie cloud computing je široký přístup k síti, takže je zaměřená na síti myslím méně důležité. Cílem mnohem cloud computing je umožnit uživatelům přístup k prostředkům bez ohledu na umístění. Pro většinu uživatelů jejich umístění bude někde na Internetu.

Následující obrázek ukazuje, jak má hraniční zabezpečení vyvinuly z hraniční sítě do hraniční identity. Zabezpečení se změní na menší o nutné chránit vaši síť a o nutné chránit vaše data, jakož i správu zabezpečení uživatelů a aplikací. Klíčovým rozdílem je, že chcete push blíže zabezpečení na to, co je důležité pro vaši společnost.

![Identity jako nový hraniční zabezpečení][4]

Na začátku služby Azure PaaS (například webové role a Azure SQL) k dispozici žádné nebo téměř žádné tradiční sítě hraniční obrany. Má se za to, že elementu účelem bylo být zpřístupněné Internetu (webové role) a poskytuje nové hraniční síti (například objektů BLOB nebo Azure SQL), ověření.

Postupy moderní zabezpečení předpokládá, že má nežádoucí osoba nedodržení hraniční sítě. Proto moderní obrany postupy přesunutí identitě. Organizace musí vytvořit zabezpečení na základě identity hraniční s silné ověřování a autorizace kontrolu (osvědčených postupů).

## <a name="recommendations-for-managing-the-identity-perimeter"></a>Doporučení pro správu hraniční identity

Principy a vzory pro hraniční síť nejsou k dispozici pro dekád. Naproti tomu odvětví má relativně. méně zkušenosti s používáním identity jako primární zabezpečení hraniční síti. S třídou uvedená jsme shromáždily dostatek zkušeností poskytnout některé obecné doporučení, které jsou ověřené v poli a platí pro téměř všechny PaaS služby.

Následující možnost shrne obecné osvědčené postupy přístup pro správu hraniční vaší identity.

- **Neztraťte klíče nebo přihlašovací údaje** zabezpečení klíčů a přihlašovacích údajů je nezbytné pro zabezpečení nasazení PaaS. Došlo ke ztrátě klíče a přihlašovací údaje jsou běžné potíže. Jeden dobrým řešením je použití centralizovaného řešení, kde klíče a tajné klíče mohou být uložené v modulech hardwarového zabezpečení (HSM). Azure poskytuje modul hardwarového zabezpečení v cloudu s [Azure Key Vault](../key-vault/key-vault-whatis.md).
- **Nevkládejte do zdrojového kódu nebo Githubu přihlašovací údaje a jiné tajné** jediné, co horší než došlo ke ztrátě klíče a přihlašovací údaje má neoprávněná osoba získat přístup k nim. Útočníci mohou využít výhod robota technologie Najít klíče a tajné klíče uložené v kódu úložiště, jako je například Githubu. Neumísťujte klíče a tajné klíče v těchto veřejných úložišť zdrojového kódu.
- **Chránit vaše rozhraní pro správu virtuálních počítačů v hybridním službám PaaS a IaaS** IaaS a PaaS služby spouštět na virtuálních počítačích (VM). V závislosti na typu služby, jsou k dispozici několik rozhraní pro správu této povolit vzdálené správě těchto virtuálních počítačů přímo. Vzdálená správa protokoly, jako [protokolu Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell), [protokol RDP (Remote Desktop)](https://support.microsoft.com/kb/186607), a [vzdáleného prostředí PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) lze použít. Doporučujeme obecně platí, že nepovolíte vzdálený přímý přístup k virtuálním počítačům z Internetu. Pokud je k dispozici, měli byste použít alternativních přístupech například pomocí virtuální privátní síť do virtuální sítě Azure. Pokud nejsou k dispozici alternativní přístupy a pak ověřte, že používáte komplexní přístupová hesla a pokud je k dispozici, dvojúrovňového ověřování (například [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)).
- **Silné ověřování a autorizace platformy**

  - Použijte federované identity ve službě Azure AD místo úložiště vlastní uživatele. Při použití federovaných identit, můžete využít přístupu na základě platformy a delegovat správu oprávnění identit partnerům. Přístup federovaných identit je obzvláště důležité ve scénářích, když zaměstnanci budou ukončeny a informace se musí projevit prostřednictvím více systémy identity a autorizace.
  - Použití platformy zadat mechanismy ověřování a autorizace místo vlastní kód. Důvodem je, že vývoj vlastní ověřovací kód může být chyba náchylné k chybám. Většina vaší vývojáři nejsou odborníky na zabezpečení a pravděpodobně potřeba mít na paměti odlišnosti a nejnovější vývoj ve ověřování a autorizace. Komerční kód (například ze společnosti Microsoft) je často hojně zabezpečení zkontrolovat.
  - Používání služby Multi-Factor authentication. Služba Multi-Factor authentication je aktuální standard pro ověřování a autorizaci, protože při ní nedochází slabá místa zabezpečení vyplývajících z uživatelské jméno a heslo typy ověřování. Přístup k rozhraní správy Azure (portál vzdáleného prostředí PowerShell) a zákazníků, kterým čelí služby by měl určené a nakonfigurovaná pro použití [Azure Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md).
  - Použijte standardní ověřovací protokoly, jako je například OAuth2 a protokolu Kerberos. Tyto protokoly byly hojně sdílené zkontrolovány a pravděpodobně jsou implementované jako součást vaší knihovny platformy pro ověřování a autorizaci.

## <a name="next-steps"></a>Další postup
V tomto článku jsme zaměřuje na výhody zabezpečení nasazení Azure PaaS. Dále se naučíte doporučené postupy pro zabezpečení vašich PaaS webové a mobilní řešení. Začneme s Azure App Service, Azure SQL Database a Azure SQL Data Warehouse. Dostupná články na doporučené postupy pro jinými službami Azure, bude k dispozici odkazy v následujícím seznamu:

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Databáze Azure SQL a Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- Azure Storage
- Mezipaměť REDIS systému Azure
- Azure Service Bus
- Brány firewall webových aplikací

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
