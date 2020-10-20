---
title: Základní hodnoty zabezpečení Azure pro Cloud Shell
description: Základní Cloud Shell zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 463bbe637eee26ab098d1531976a18999497d12f
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209804"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>Základní hodnoty zabezpečení Azure pro Cloud Shell

Tato základní hodnota zabezpečení platí pro Cloud Shell pokynů od [zabezpečení Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Srovnávací test zabezpečení Azure poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure.
Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Cloud Shell. **Ovládací prvky** , které se nevztahují k Cloud Shell byly vyloučeny.

 
Pokud chcete zjistit, jak Cloud Shell kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Cloud Shell Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: zákazníci mohou nasadit Azure Cloud Shell do Virtual Network vlastněných zákazníky.

Když nasadíte Azure Cloud Shell do Virtual Network ve vlastnictví zákazníka, musíte vytvořit nebo použít stávající virtuální síť. Zajistěte, aby zvolená virtuální síť měla skupinu zabezpečení sítě použitou pro své podsítě a řízení přístupu k síti nakonfigurovaná specificky pro důvěryhodné porty a zdroje vaší aplikace.

- [Nasazení Cloud Shell do služby Azure Virtual Network](private-vnet.md)

- [Jak vytvořit skupinu zabezpečení sítě s pravidly zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak nasadit a nakonfigurovat Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné**materiály: Azure Cloud Shell je prostředí příkazového řádku založené na prohlížeči, které používá stejnou autorizaci, která se používá pro přístup k Azure Portal, v tomto případě se v případě jednotného přihlašování k Azure Portal ověříte taky pomocí Cloud Shell. 

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Pokyny**: Azure Cloud Shell je prostředí příkazového řádku založené na prohlížeči, které používá stejnou autorizaci, která se používá pro přístup k Azure Portal. v takovém případě se pro Cloud Shell vyžaduje i vícefaktorové ověřování, které se vyžaduje pro připojení k Azure Portal. 

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné**materiály: Azure Cloud Shell je prostředí příkazového řádku založené na prohlížeči, které se používá pro interaktivní správu cloudových prostředků.  Každý kontejner zákazníka je dočasný. pro každou relaci se používá nový kontejner.  Image kontejneru se monitoruje a aktualizuje týmem Cloud Shell.

Azure Cloud Shell umožňuje zákazníkům nainstalovat vlastní nástroje nebo software do své vlastní image podle potřeb organizace.

Zákazníci zodpovídají za spouštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení na softwaru, který běží v daném prostředí.  

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizované řešení pro správu oprav pro softwarové tituly třetích stran

**Doprovodné**materiály: nepoužitelné; Azure Cloud Shell je prostředí příkazového řádku založené na prohlížeči, které se používá pro interaktivní správu cloudových prostředků.  Každý kontejner zákazníka je dočasný. pro každou relaci se používá nový kontejner.  Image kontejneru se monitoruje a aktualizuje týmem Cloud Shell.

Azure Cloud Shell umožňuje zákazníkům nainstalovat vlastní nástroje nebo software do své vlastní image podle potřeb organizace.

Zákazníci zodpovídají za správu oprav softwaru spuštěnou ve svém prostředí.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné**materiály: Azure Cloud Shell je prostředí příkazového řádku založené na prohlížeči, které se používá pro interaktivní správu cloudových prostředků.  Každý kontejner zákazníka je dočasný. pro každou relaci se používá nový kontejner.  Image kontejneru se monitoruje a aktualizuje týmem Cloud Shell.

Azure Cloud Shell umožňuje zákazníkům nainstalovat vlastní nástroje nebo software do své vlastní image podle potřeb organizace.

Zákazníci zodpovídají za napravení ohrožení zabezpečení zjištěného prostřednictvím kontroly ohrožení zabezpečení softwaru. Exportovat výsledky kontroly v konzistentních intervalech a porovnat výsledky s předchozími kontrolami a ověřit tak, že chyby zabezpečení byly opraveny. Pokud používáte doporučení ke správě ohrožení zabezpečení, kterou navrhla Azure Security Center, můžete se na portál vybraného řešení překlopit a zobrazit historická data kontroly.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné**materiály: Azure Cloud Shell je prostředí příkazového řádku založené na prohlížeči, které se používá pro interaktivní správu cloudových prostředků.  Každý kontejner zákazníka je dočasný. pro každou relaci se používá nový kontejner.  Image kontejneru se monitoruje a aktualizuje týmem Cloud Shell.

Azure Cloud Shell umožňuje zákazníkům nainstalovat vlastní nástroje nebo software do své vlastní image podle potřeb organizace.

Zákazníci zodpovídají za napravení ohrožení zabezpečení zjištěného prostřednictvím kontroly ohrožení zabezpečení softwaru.  Použijte běžný program pro vyhodnocování rizik (například běžný systém vyhodnocování chyb zabezpečení) nebo výchozí hodnocení rizik poskytované skenovacím nástrojem třetí strany. 

- [Publikování v NIST – běžný systém vyhodnocování ohrožení zabezpečení](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné**materiály: Azure Cloud Shell je prostředí příkazového řádku založené na prohlížeči, které se používá pro interaktivní správu cloudových prostředků.  Každý kontejner zákazníka je dočasný. pro každou relaci se používá nový kontejner.  Image a nástroje kontejneru jsou monitorované a aktualizované týmem Cloud Shell.  Zákazník může nainstalovat vlastní nástroje ve své vlastní imagi podle potřeb organizace a nástroje `sudo` během instalace nevyžadují oprávnění.

Zákazníkům se doporučuje vytvořit inventarizaci schváleného softwaru, který je nainstalovaný prostřednictvím Azure Cloud Shell podle potřeb vaší organizace.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné**materiály: Azure Cloud Shell je bezplatná služba, která nemá žádné prostředky vlastněné zákazníkem.  Image a nástroje kontejneru jsou monitorované a aktualizované týmem Cloud Shell. 

Azure Cloud Shell umožňuje zákazníkům nainstalovat vlastní nástroje nebo software do své vlastní image podle potřeb organizace.

Zákazníci zodpovídají za monitorování softwarových aplikací spuštěných v daném prostředí, aby se ujistili, že jsou schválené podle zásad organizace.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné**materiály: Azure Cloud Shell je bezplatná služba, která nemá žádné prostředky vlastněné zákazníkem.  Image a nástroje kontejneru jsou monitorované a aktualizované týmem Cloud Shell. 

Azure Cloud Shell umožňuje zákazníkům nainstalovat vlastní nástroje nebo software do své vlastní image podle potřeb organizace.

Zákazníci zodpovídají za monitorování softwarových aplikací spuštěných v daném prostředí, aby se zajistilo, že se neschválený software spravuje na základě zásad organizace.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné**materiály: Azure Cloud Shell je bezplatná služba, která nemá žádné prostředky vlastněné zákazníkem.  Image a nástroje kontejneru jsou monitorované a aktualizované týmem Cloud Shell.  Konkrétní nástroje nemusí odebrat zákazník.

Azure Cloud Shell umožňuje zákazníkům nainstalovat vlastní nástroje nebo aplikace do své vlastní image podle potřeb organizace.

Zákazníci zodpovídají za monitorování aplikací spuštěných v daném prostředí, aby se ujistili, že jsou schválené podle zásad organizace.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné**materiály: nepoužitelné; Azure Cloud Shell je prostředí příkazového řádku založené na prohlížeči, které se používá pro interaktivní správu cloudových prostředků.  Každý kontejner zákazníka je dočasný. pro každou relaci se používá nový kontejner.  Image kontejneru se monitoruje a aktualizuje týmem Cloud Shell.

Azure Cloud Shell umožňuje zákazníkům nainstalovat vlastní nástroje nebo software do své vlastní image podle potřeb organizace.

Zákazníci zodpovídají za udržování inventáře schváleného softwaru spuštěného v prostředí, aby se ujistili, že se jedná o schválený software podle zásad organizace.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné**materiály: Azure Cloud Shell je prostředí příkazového řádku založené na prohlížeči, které se používá pro interaktivní správu cloudových prostředků.  Akce, které se provedou v rámci funkce Cloud Shell, jsou stejné jako akce provedené ze stejných nástrojů nebo jazyků spouštěných v místním prostředí.  Akce z jednotlivých nástrojů a jazyků by měly být omezené, zákazníci nemůžou omezit přístup k Cloud Shell nebo omezovat, co je k dispozici pro uživatele.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné**materiály: Azure Cloud Shell můžou být izolované ve virtuální síti zákazníka.

- [Nasazení Cloud Shell do služby Azure Virtual Network](private-vnet.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: Cloud Shell umožňuje, aby se skripty spouštěly v, vytvořily a nahrály do Cloud Shell prostředí.  Přesunutí přihlašovacích údajů do Azure Key Vault je naše doporučení.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Používejte centrálně spravovaný antimalwarový software

**Doprovodné**materiály: Azure Cloud Shell je prostředí příkazového řádku založené na prohlížeči, které se používá pro interaktivní správu cloudových prostředků.  Každý kontejner zákazníka je dočasný. pro každou relaci se používá nový kontejner.  Image a nástroje kontejneru jsou monitorované a aktualizované týmem Cloud Shell.  Zákazník může nainstalovat vlastní nástroje ve své vlastní imagi podle potřeb organizace a nástroje `sudo` během instalace nevyžadují oprávnění.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované

**Doprovodné**materiály: Azure Cloud Shell je prostředí příkazového řádku založené na prohlížeči, které se používá pro interaktivní správu cloudových prostředků.  Každý kontejner zákazníka je dočasný. pro každou relaci se používá nový kontejner.  Image a nástroje kontejneru jsou monitorované a aktualizované týmem Cloud Shell.  Zákazník může nainstalovat vlastní nástroje ve své vlastní imagi podle potřeb organizace a nástroje `sudo` během instalace nevyžadují oprávnění.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: Vytvoření Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů z detekce až po přezkoumání po jednotlivých událostech.
- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 
- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) 

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné**materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.
Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.
- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md) 
- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md) 

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.
- [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf) 

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.
- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md) 

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné**materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat ověřovací data výstrah.
- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md) 
- [Jak streamovat výstrahy do Azure Sentinel](../sentinel/connect-azure-security-center.md) 

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné**materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.
- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné**materiály: postupujte podle pravidel pro testování průniku Microsoft Cloud, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft.
- [Pravidla testování průniku pro zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 
- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) 

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](/azure/security/benchmarks/overview)
- Další informace o [plánech zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
