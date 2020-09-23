---
title: Poznámky k verzi pro Azure Security Center
description: Popis toho, co je nového a co se změnilo v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 3cb35cdf217d497b612dee12aedb869b0583e464
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986686"
---
# <a name="whats-new-in-azure-security-center"></a>Co je nového v Azure Security Center?

Zabezpečení Azure je v aktivním vývoji a průběžně přijímá vylepšení. Tato stránka vám poskytne informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nové funkce
- Opravy chyb
- Zastaralé funkce

Tato stránka se pravidelně aktualizuje, takže ji můžete často znovu navštěvovat. Pokud hledáte položky starší než šest měsíců, najdete je v archivu, kde najdete novinky [v Azure Security Center](release-notes-archive.md).


## <a name="september-2020"></a>Září 2020

Aktualizace v září zahrnují:
- [Security Center získá nový vzhled.](#security-center-gets-a-new-look)
- [Vydaný Azure Defender](#azure-defender-released)
- [Azure Defender pro Key Vault je všeobecně dostupný](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender pro ochranu úložiště souborů a ADLS Gen2 je všeobecně dostupný](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Nástroje inventáře prostředků jsou teď všeobecně dostupné.](#asset-inventory-tools-are-now-generally-available)
- [Zakázání konkrétní chyby zabezpečení při hledání registrů kontejnerů a virtuálních počítačů](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Vyloučení prostředku z doporučení](#exempt-a-resource-from-a-recommendation)
- [Konektory AWS a GCP v Security Center přinášejí prostředí pro více cloudů](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Sada doporučení pro ochranu úloh Kubernetes](#kubernetes-workload-protection-recommendation-bundle)
- [Vylepšení ochrany IoT Threat v Azure Defenderu pro IoT](#iot-threat-protection-enhancements-in-azure-defender-for-iot)
- [Výsledky posouzení ohrožení zabezpečení jsou nyní k dispozici při průběžném exportu](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Zabránit neoprávněným konfiguracím zabezpečení vynucováním doporučení při vytváření nových prostředků](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Vylepšení doporučení skupiny zabezpečení sítě](#network-security-group-recommendations-improved)
- [Doporučení AKS inverze Preview "pod" zásady zabezpečení by měly být definované v Kubernetes Services "](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Vylepšená e-mailová oznámení z Azure Security Center](#email-notifications-from-azure-security-center-improved)
- [Zabezpečené skóre nezahrnuje doporučení pro verzi Preview.](#secure-score-doesnt-include-preview-recommendations)
- [Doporučení nyní obsahují indikátor závažnosti a interval aktuálnosti.](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center získá nový vzhled.

Vydali jsme aktualizované uživatelské rozhraní pro stránky portálu Security Center. Nové stránky obsahují novou stránku Přehled a také řídicí panely pro bezpečné skóre, inventář prostředků a Azure Defender.

Stránka s přehledem přepracování teď obsahuje dlaždici pro přístup k zabezpečenému skóre, inventáři prostředků a řídicím panelům Azure Defenderu. Obsahuje také dlaždici, která odkazuje na řídicí panel dodržování předpisů.

Přečtěte si další informace o [stránce Přehled](overview-page.md).


### <a name="azure-defender-released"></a>Vydaný Azure Defender

**Azure Defender** je platforma ochrany zatížení cloudu (CWPP) integrovaná v rámci Security Center pro pokročilou, inteligentní a chráněnou službu Azure a hybridní úlohy. Nahradí možnost standardní cenové úrovně v Security Center. 

Pokud povolíte Azure Defender z oblasti **ceny a nastavení** Azure Security Center, jsou všechny tyto plány Defenderu povolené současně a poskytují komplexní ochranu pro výpočetní, datovou a provozní vrstvu vašeho prostředí:

- [Azure Defender pro servery](defender-for-servers-introduction.md)
- [Azure Defender pro App Service](defender-for-app-service-introduction.md)
- [Azure Defender pro úložiště](defender-for-storage-introduction.md)
- [Azure Defender pro SQL](defender-for-sql-introduction.md)
- [Azure Defender for IoT](defender-for-iot-introduction.md)
- [Azure Defender pro trezor klíčů](defender-for-key-vault-introduction.md)
- [Azure Defender pro Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender pro registry kontejnerů](defender-for-container-registries-introduction.md)

Každý z těchto plánů je vysvětlen samostatně v dokumentaci Security Center.

Díky jeho vyhrazenému řídicímu panelu poskytuje Azure Defender výstrahy zabezpečení a pokročilou ochranu před internetovými útoky pro virtuální počítače, databáze SQL, kontejnery, webové aplikace, síť a další.

[Další informace o Azure Defenderu](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender pro Key Vault je všeobecně dostupný

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné kódy, jako jsou certifikáty, připojovací řetězce a hesla. 

**Azure Defender pro Key Vault** poskytuje rozšířenou ochranu před internetovými útoky azure pro Azure Key Vault a poskytuje další vrstvu funkcí Security Intelligence. V důsledku rozšíření používá Azure Defender pro Key Vault ochranu mnoha prostředků závislých na vašich Key Vault účtech.

Volitelný plán je teď GA. Tato funkce je ve verzi Preview jako rozšířená ochrana před internetovými útoky pro Azure Key Vault.

Key Vault stránky v Azure Portal nyní obsahují vyhrazenou **bezpečnostní** stránku pro **Security Center** doporučení a upozornění.

Další informace najdete v tomto [Key Vault v Azure Defenderu](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender pro ochranu úložiště souborů a ADLS Gen2 je všeobecně dostupný 

**Azure Defender pro úložiště** detekuje na vašich Azure Storage účtech potenciálně škodlivé aktivity. Data je možné chránit bez ohledu na to, jestli jsou uložená jako kontejnery objektů blob, sdílené složky nebo datová jezera.

Podpora pro [soubory](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) a [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) Azure je teď všeobecně dostupná.

Od 1. října 2020 začneme účtovat za ochranu prostředků u těchto služeb.

Další informace najdete v [Azure Defenderu pro úložiště](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Nástroje inventáře prostředků jsou teď všeobecně dostupné.

Stránka inventáře assetů Azure Security Center poskytuje jednu stránku pro zobrazení stav zabezpečení prostředků, ke kterým jste se připojili Security Center.

Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure, aby se identifikovaly potenciální ohrožení zabezpečení. Pak vám poskytne doporučení k nápravě těchto chyb zabezpečení.

Pokud některý z prostředků obsahuje nevyřízená doporučení, zobrazí se v inventáři.

Další informace najdete v informacích o [prozkoumání a správě vašich prostředků pomocí inventáře a nástrojů pro správu](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Zakázání konkrétní chyby zabezpečení při hledání registrů kontejnerů a virtuálních počítačů

Azure Defender obsahuje skenery ohrožení zabezpečení pro skenování imagí ve vašich Azure Container Registry a virtuálních počítačích.

Pokud máte organizaci, kterou je třeba ignorovat, místo toho, aby ji bylo možné opravit, můžete ji případně zakázat. Zakázané závěry neovlivňují vaše zabezpečené skóre nebo generují nežádoucí hluk.

Pokud hledání odpovídá kritériím, která jste definovali v pravidlech vypnutí, nezobrazí se v seznamu zjištění.

Tato možnost je k dispozici na stránkách s podrobnostmi doporučení pro:

- **Ohrožení zabezpečení v Azure Container Registrych imagí by mělo být opraveno**
- **Ohrožení zabezpečení ve vašich virtuálních počítačích by se mělo opravit.**

Další informace najdete v [zakažte konkrétní zjištění imagí kontejneru](defender-for-container-registries-usage.md#disable-specific-findings-preview) a [zakažte konkrétní zjištění pro vaše virtuální počítače](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Vyloučení prostředku z doporučení

V některých případech se prostředek bude zobrazovat jako špatný v souvislosti s konkrétním doporučením (a tím se sníží vaše zabezpečené skóre), i když byste to ještě neměli. Mohl by být opraven procesem, který není sledován nástrojem Security Center. Nebo možná vaše organizace rozhodla přijmout riziko pro konkrétní prostředek. 

V takových případech můžete vytvořit pravidlo výjimky a zajistit, aby prostředek nebyl v budoucnu uvedený v seznamu prostředků, které nejsou v pořádku. Tato pravidla mohou zahrnovat popsaná odůvodnění, jak je popsáno níže.

Další informace najdete v informacích [o vyloučení prostředků z doporučení a zabezpečeného skóre](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Konektory AWS a GCP v Security Center přinášejí prostředí pro více cloudů

Cloudové úlohy běžně pokrývá několik cloudových platforem, ale cloudové služby zabezpečení musí provádět stejné.

Azure Security Center nyní chrání úlohy v Azure, Amazon Web Services (AWS) a Google Cloud Platform (GCP).

Při připojování účtů AWS a GCP do Security Center se integruje AWS Security hub, GCP Security a Azure Security Center. 

Další informace najdete v informacích o [připojení účtů AWS k Azure Security Center](quickstart-onboard-aws.md) a [připojení účtů GCP k Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Sada doporučení pro ochranu úloh Kubernetes

Aby se zajistilo, že Kubernetes úlohy jsou zabezpečené ve výchozím nastavení, Security Center přidávají doporučení pro posílení úrovně Kubernetes, včetně možností vynucení s řízením přístupu Kubernetes.

Když jste nainstalovali doplněk Azure Policy pro Kubernetes do clusteru AKS, všechny požadavky na server rozhraní Kubernetes API se budou monitorovat proti předdefinované sadě osvědčených postupů předtím, než se trvale uloží do clusteru. Pak můžete nakonfigurovat, aby vynutil osvědčené postupy a nakonfigurovaly je pro budoucí úlohy.

Můžete například stanovit, že by se neměly vytvářet privilegované kontejnery a všechny budoucí požadavky tak budou zablokovány.

Další informace najdete v článku [osvědčené postupy ochrany úloh pomocí řízení přístupu Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="iot-threat-protection-enhancements-in-azure-defender-for-iot"></a>Vylepšení ochrany IoT Threat v Azure Defenderu pro IoT

Azure Defender pro IoT přináší více funkcí ochrany před hrozbami díky zahrnutí technologie bez agentů CyberX. To přináší ochranu zabezpečení pro nespravovaná zařízení brownfield používaná v prostředích provozní technologie, jako je například výroba, vytváření systémů pro správu (BMS), věd o životní prostředí, energetické a vodní pomůcky, ropný & plyn a logistika.

Další informace najdete v [úvodu k Azure Defenderu pro IoT](defender-for-iot-introduction.md).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Výsledky posouzení ohrožení zabezpečení jsou nyní k dispozici při průběžném exportu

Používejte průběžný export ke streamování výstrah a doporučení v reálném čase do služby Azure Event Hubs, Log Analytics pracovních prostorů nebo Azure Monitor. Odtud můžete tato data integrovat s systémů Siem (například Azure Sentinel, Power BI, Azure Průzkumník dat a dalšími.

Nástroje pro posouzení ohrožení zabezpečení Security Center v rámci "nadřazených" doporučení vracet informace o vašich prostředcích jako užitečná doporučení, jako je třeba ohrožení zabezpečení ve virtuálních počítačích. 

Výsledky zabezpečení jsou nyní k dispozici pro export prostřednictvím průběžného exportu, když vyberete možnost doporučení a povolíte možnost **Zahrnout zjištění zabezpečení** .

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Zahrnout zjištění zabezpečení – přepnout do konfigurace průběžného exportu" :::

Související stránky:

- [Integrované řešení posouzení ohrožení zabezpečení Security Center pro virtuální počítače Azure](deploy-vulnerability-assessment-vm.md)
- [Integrované řešení posouzení ohrožení zabezpečení Security Center pro bitové kopie Azure Container Registry](defender-for-container-registries-usage.md)
- [Průběžný export](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Zabránit neoprávněným konfiguracím zabezpečení vynucováním doporučení při vytváření nových prostředků

Nezabezpečené konfigurace zabezpečení představují hlavní příčinu incidentů zabezpečení. Security Center nyní mají schopnost *zabránit* v neúspěšně konfiguracích nových prostředků s ohledem na konkrétní doporučení. 

Tato funkce vám může přispět k zabezpečení vašich úloh a ke stabilizaci zabezpečeného skóre.

Vynucování zabezpečené konfigurace na základě konkrétního doporučení se nabízí ve dvou režimech:

- Pomocí efektu **odepření** Azure Policy můžete zastavit vytváření špatných prostředků.

- Pomocí možnosti **Vynutilit** můžete využít **DeployIfNotExist** efektu zásad Azure a automaticky opravovat prostředky, které nedodržují předpisy při tvorbě.
 
Tato možnost je k dispozici pro vybraná doporučení zabezpečení a nachází se v horní části stránky s podrobnostmi o prostředku.

Další informace o tom, jak [zabránit v neoprávněných konfiguracích s doporučeními pro vymáhání](prevent-misconfigurations.md)

###  <a name="network-security-group-recommendations-improved"></a>Vylepšení doporučení skupiny zabezpečení sítě

Byla vylepšena následující doporučení zabezpečení související se skupinami zabezpečení sítě, aby se snížily některé výskyty falešně pozitivních hodnot.

- Všechny síťové porty by se měly omezit na NSG přidružených k vašemu VIRTUÁLNÍmu počítači.
- Porty pro správu by měly být uzavřeny na virtuálních počítačích
- Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě
- Podsítě by měly být přidružené ke skupině zabezpečení sítě.


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Doporučení AKS inverze Preview "pod" zásady zabezpečení by měly být definované v Kubernetes Services "

Doporučení pro verzi Preview "pod zásadou zabezpečení by se měly definovat na Kubernetes Services", jak je popsáno v dokumentaci ke [službě Azure Kubernetes](https://docs.microsoft.com/azure/aks/use-pod-security-policies) .

Funkce zásady zabezpečení pod (Preview) je nastavena pro vyřazení a nebude již k dispozici po 15. říjnu 2020 ve prospěch Azure Policy pro AKS.

Po použití zásady zabezpečení (Preview) je zastaralá. tuto funkci je třeba zakázat na všech stávajících clusterech pomocí zastaralé funkce, aby se prováděly budoucí upgrady clusteru a zůstaly v rámci podpory Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Vylepšená e-mailová oznámení z Azure Security Center

Vylepšili jsme následující oblasti e-mailů týkajících se výstrah zabezpečení: 

- Přidání možnosti odesílání e-mailových oznámení o výstrahách pro všechny úrovně závažnosti
- Přidali jsme možnost informovat uživatele o různých rolích RBAC v předplatném.
- Ve výchozím nastavení oznamujeme, že vlastníci předplatného budou upozorňováni na výstrahy s vysokou závažností (s vysokou pravděpodobností, že se jedná o pravé porušení předpisů).
- Odebrali jsme pole telefonní číslo ze stránky Konfigurace e-mailových oznámení.

Další informace najdete v [Nastavení e-mailových oznámení pro výstrahy zabezpečení](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Zabezpečené skóre nezahrnuje doporučení pro verzi Preview. 

Security Center průběžně vyhodnocuje vaše prostředky, odběry a organizace pro problémy se zabezpečením. Pak agreguje všechna zjištění do jediného skóre, abyste na první pohled mohli sdělit aktuální situaci zabezpečení: čím vyšší je skóre, tím se sníží zjištěná úroveň rizika.

Nově zjištěné hrozby jsou v Security Center k dispozici nové Rady zabezpečení prostřednictvím nových doporučení. Pokud se chcete vyhnout nedarovaným změnám v zabezpečeném skóre a poskytnout období odkladu, ve kterém můžete prozkoumat nová doporučení předtím, než budou mít dopad na vaše skóre, doporučení označená jako **verze Preview** už nejsou zahrnutá do výpočtů zabezpečeného skóre. Měly by se pořád opravit všude, kde je to možné, takže po skončení období Preview budou přispět ke svému skóre.

Doporučení **verze Preview** navíc nevykreslují prostředek "není v pořádku".

Příklad doporučení verze Preview:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Doporučení s příznakem Preview":::

[Přečtěte si další informace o zabezpečeném skóre](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Doporučení nyní obsahují indikátor závažnosti a interval aktuálnosti.

Stránka s podrobnostmi pro doporučení nyní obsahuje indikátor intervalu aktuálnosti (kdykoli je to relevantní) a jasné zobrazení závažnosti doporučení.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Stránka s doporučeními ukazující aktuálnost a závažnost":::



## <a name="august-2020"></a>Srpen 2020

Aktualizace v srpnu zahrnují:

- [Inventář assetů – výkonné nové zobrazení stav zabezpečení vašich prostředků](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Přidání podpory pro výchozí nastavení zabezpečení Azure Active Directory (pro Multi-Factor Authentication)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Přidalo se doporučení instančních objektů.](#service-principals-recommendation-added)
- [Posouzení ohrožení zabezpečení na virtuálních počítačích – konsolidovaná doporučení a zásady](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Nové zásady zabezpečení AKS přidané do ASC_default iniciativy – jenom pro zákazníky s privátní verzí Preview](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventář assetů – výkonné nové zobrazení stav zabezpečení vašich prostředků

Inventář assetů Security Center (aktuálně ve verzi Preview) poskytuje způsob, jak zobrazit stav zabezpečení prostředků, ke kterým jste se připojili Security Center.

Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure, aby se identifikovaly potenciální ohrožení zabezpečení. Pak vám poskytne doporučení k nápravě těchto chyb zabezpečení. Pokud některý z prostředků obsahuje nevyřízená doporučení, zobrazí se v inventáři.

Můžete použít zobrazení a jeho filtry k prozkoumání dat stav zabezpečení a provádět další akce na základě vašich výsledků.

Přečtěte si další informace o [inventáři prostředků](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Přidání podpory pro výchozí nastavení zabezpečení Azure Active Directory (pro Multi-Factor Authentication)

Security Center přidaná plná podpora pro [výchozí nastavení zabezpečení](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), ochrana zabezpečení identit od Microsoftu je bezplatná.

Výchozí hodnoty zabezpečení poskytují předem nakonfigurovaná nastavení zabezpečení identity, která chrání vaši organizaci před běžnými útoky souvisejícími s identitou. Výchozí hodnoty zabezpečení již chrání více než 5 000 000 klientů. klienti 50 000 jsou chráněni také pomocí Security Center.

Security Center teď poskytuje doporučení zabezpečení vždy, když identifikuje předplatné Azure bez povolených výchozích hodnot zabezpečení. Až do této chvíle Security Center doporučeno povolit vícefaktorové ověřování pomocí podmíněného přístupu, který je součástí licence Azure Active Directory (AD) Premium. Pro zákazníky, kteří používají Azure AD Free, teď doporučujeme povolit výchozí nastavení zabezpečení. 

Naším cílem je povzbudit větší počet zákazníků k zabezpečení svých cloudových prostředí pomocí vícefaktorového ověřování a zmírnit jedno z nejvyšších rizik, která jsou také největší dopadem na vaše [zabezpečené skóre](https://docs.microsoft.com/azure/security-center/secure-score-security-controls).

Přečtěte si další informace o [výchozích hodnotách zabezpečení](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults).


### <a name="service-principals-recommendation-added"></a>Přidalo se doporučení instančních objektů.

Bylo přidáno nové doporučení, které Security Center zákazníky, kteří používají certifikáty pro správu ke správě předplatných, přepínat na instanční objekty.

K **ochraně vašich předplatných, namísto certifikátů pro správu** , doporučujeme použít instanční objekty nebo Azure Resource Manager pro bezpečnější správu předplatných. 

Další informace o [aplikacích a instančních objektech služby v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Posouzení ohrožení zabezpečení na virtuálních počítačích – konsolidovaná doporučení a zásady

Security Center zkontroluje vaše virtuální počítače a zjistí, jestli neběží na řešení pro posouzení ohrožení zabezpečení. Pokud se nenajde žádné řešení posouzení ohrožení zabezpečení, Security Center poskytne doporučení ke zjednodušení nasazení.

Po nalezení ohrožení zabezpečení Security Center poskytuje doporučení k tomu, abyste zjistili, co je potřeba k prozkoumání a nápravě v případě potřeby.

Abychom zajistili konzistentní prostředí pro všechny uživatele, bez ohledu na typ skeneru, který používají, jsme sjednoceni čtyřmi doporučeními v následujících dvou příkladech:

|Sjednocené doporučení|Popis změny|
|----|:----|
|**Na virtuálních počítačích by mělo být povolené řešení posouzení ohrožení zabezpečení.**|Nahrazuje následující dvě doporučení:<br> **•** Povolit integrované řešení posouzení ohrožení zabezpečení na virtuálních počítačích (s využitím Qualys (teď zastaralé) (zahrnuté do úrovně Standard)<br> **•** Řešení posouzení ohrožení zabezpečení by se mělo nainstalovat na vaše virtuální počítače (teď zastaralé) (úrovně Standard a Free).|
|**Ohrožení zabezpečení ve vašich virtuálních počítačích by se mělo opravit.**|Nahrazuje následující dvě doporučení:<br>**•** Oprava ohrožení zabezpečení zjištěných na virtuálních počítačích (s technologií Qualys) (nyní zastaralé)<br>**•** Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení (teď zastaralé).|
|||

Nyní použijete stejné doporučení k nasazení rozšíření posouzení ohrožení zabezpečení Security Center nebo soukromě Licencovanému řešení ("BYOL") od partnera, jako je Qualys nebo Rapid7.

V případě, že dojde k nalezení a nahlášení ohrožení zabezpečení Security Center, se vám zobrazí jedno doporučení bez ohledu na řešení posouzení ohrožení zabezpečení, které je identifikovalo.

#### <a name="updating-dependencies"></a>Aktualizace závislostí

Pokud máte skripty, dotazy nebo automatizace odkazující na předchozí doporučení nebo klíče zásad nebo názvy, použijte následující tabulky k aktualizaci odkazů:

##### <a name="before-august-2020"></a>Před srpna 2020

|Doporučení|Obor|
|----|:----|
|**Povolení integrovaného řešení posouzení ohrožení zabezpečení na virtuálních počítačích (používá se Qualys)**<br>Klíč: 550e890b-e652-4d22-8274-60b3bdb24c63|Integrované|
|**Náprava ohrožení zabezpečení zjištěná na vašich virtuálních počítačích (používá se Qualys)**<br>Klíč: 1195afff-c881-495E-9bc5-1486211ae03f|Integrované|
|**Řešení posouzení ohrožení zabezpečení by se mělo nainstalovat na virtuální počítače.**<br>Klíč: 01b1ed4c-B733-4FEE-b145-f23236e70cf3|BYOL|
|**Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.**<br>Klíč: 71992a2a-D168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Zásady|Obor|
|----|:----|
|**Na virtuálních počítačích by mělo být povolené posouzení ohrožení zabezpečení**<br>ID zásady: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Integrované|
|**Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.**<br>ID zásady: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Od srpna 2020

|Doporučení|Obor|
|----|:----|
|**Na virtuálních počítačích by mělo být povolené řešení posouzení ohrožení zabezpečení.**<br>Klíč: ffff0522-1e88-47fc-8382-2a80ba848f5d|Předdefinované + BYOL|
|**Ohrožení zabezpečení ve vašich virtuálních počítačích by se mělo opravit.**<br>Klíč: 1195afff-c881-495E-9bc5-1486211ae03f|Předdefinované + BYOL|
||||

|Zásady|Obor|
|----|:----|
|[**Na virtuálních počítačích by mělo být povolené posouzení ohrožení zabezpečení**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ID zásady: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Předdefinované + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nové zásady zabezpečení AKS přidané do ASC_default iniciativy – jenom pro zákazníky s privátní verzí Preview

Aby se zajistilo, že Kubernetes úlohy jsou zabezpečené ve výchozím nastavení, Security Center přidávají zásady Kubernetes úrovně a posílení doporučení, včetně možností vynucení pomocí řízení přístupu Kubernetes.

Úvodní fáze tohoto projektu zahrnuje soukromou verzi Preview a přidání nových (zakázaných ve výchozím) zásadách pro ASC_default iniciativu.

Tyto zásady můžete bezpečně ignorovat a nebude to mít žádný vliv na vaše prostředí. Pokud je chcete povolit, zaregistrujte si verzi Preview na adrese https://aka.ms/SecurityPrP a vyberte si z těchto možností:

1. **Single Preview** – pro připojení pouze k této privátní verzi Preview. Jako náhled, ke kterému se chcete připojit, výslovně uveďte "ASC průběžné prověřování".
1. **Probíhající program** – přidaný do tohoto a budoucího privátního náhledu. Budete muset dokončit profil a smlouvu o ochraně osobních údajů.


## <a name="july-2020"></a>Červenec 2020

Aktualizace v červenci zahrnují:
- [Pro Image mimo Marketplace je teď k dispozici posouzení ohrožení zabezpečení pro virtuální počítače.](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Ochrana před hrozbami pro Azure Storage rozšířena tak, aby zahrnovala soubory Azure a Azure Data Lake Storage Gen2 (Preview)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Osm nových doporučení pro povolení funkcí ochrany před hrozbami](#eight-new-recommendations-to-enable-threat-protection-features)
- [Vylepšení zabezpečení kontejnerů – rychlejší prohledávání registru a aktualizace dokumentace](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Adaptivní řízení aplikací je aktualizované o nové doporučení a podporu zástupných znaků v pravidlech cesty.](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Šest zásad pro zabezpečení rozšířených dat SQL zastaralé](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Pro Image mimo Marketplace je teď k dispozici posouzení ohrožení zabezpečení pro virtuální počítače.

Při nasazení řešení posouzení ohrožení zabezpečení Security Center před nasazením dříve provedla kontrolu ověření. Kontrola měla potvrdit SKLADOVOU položku Marketplace cílového virtuálního počítače. 

Z této aktualizace jste odebrali kontrolu a teď můžete nasadit nástroje pro posouzení ohrožení zabezpečení do vlastních počítačů se systémem Windows a Linux. Vlastní image jsou ty, které jste změnili ve výchozím nastavení webu Marketplace.

I když teď můžete nasadit integrované rozšíření posouzení ohrožení zabezpečení (s technologií Qualys) na mnoho dalších počítačů, podpora je dostupná jenom v případě, že používáte operační systém, který je uvedený v [části nasazení integrovaného skeneru zabezpečení na virtuální počítače úrovně Standard](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-vulnerability-scanner-to-your-vms) .

Přečtěte si další informace o [integrovaném skeneru ohrožení zabezpečení pro virtuální počítače (vyžaduje Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Přečtěte si další informace o používání vlastního řešení posouzení ohrožení zabezpečení v soukromém prostředí z Qualys nebo Rapid7 v [nasazení řešení pro kontrolu ohrožení zabezpečení partnera](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Ochrana před hrozbami pro Azure Storage rozšířena tak, aby zahrnovala soubory Azure a Azure Data Lake Storage Gen2 (Preview)

Ochrana před hrozbami pro Azure Storage detekuje na vašich Azure Storage účtech potenciálně škodlivé aktivity. Security Center zobrazuje výstrahy, když detekuje pokusy o přístup k účtům úložiště nebo jejich zneužití. 

Data je možné chránit bez ohledu na to, jestli jsou uložená jako kontejnery objektů blob, sdílené složky nebo datová jezera.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Osm nových doporučení pro povolení funkcí ochrany před hrozbami

Přidali jsme osm nových doporučení, která poskytují jednoduchý způsob, jak povolit funkce ochrany před internetovými útoky v Azure Security Center pro následující typy prostředků: virtuální počítače, App Service plány, Azure SQL Database servery, SQL servery na počítačích, účty Azure Storage, clustery služby Azure Kubernetes, Azure Container Registry Registry a Azure Key Vault trezory.

Nová doporučení:

- **V Azure SQL Databasech serverech by mělo být povolené rozšířené zabezpečení dat.**
- **Rozšířené zabezpečení dat by mělo být povoleno na serverech SQL na počítačích**
- **V plánech Azure App Service by měla být povolena rozšířená ochrana před internetovými útoky.**
- **V Azure Container Registry Registry by měla být povolena rozšířená ochrana před internetovými útoky.**
- **V trezorech Azure Key Vault by měla být povolena rozšířená ochrana před internetovými útoky.**
- **V clusterech služby Azure Kubernetes by měla být povolena rozšířená ochrana před internetovými útoky.**
- **Pro účty Azure Storage by měla být povolena rozšířená ochrana před internetovými útoky.**
- **Na virtuálních počítačích by měla být povolena rozšířená ochrana před internetovými útoky.**

Tato nová doporučení patří k řízení zabezpečení **Povolit pokročilou ochranu před internetovými útoky** .

Doporučení také zahrnují funkci rychlé opravy. 

> [!IMPORTANT]
> Oprava některé z těchto doporučení bude mít za následek poplatky za ochranu relevantních prostředků. Tyto poplatky začnou platit okamžitě, pokud máte související prostředky v aktuálním předplatném. Případně můžete v budoucnu přidat data později.
> 
> Například pokud ve vašem předplatném nemáte žádné clustery služby Azure Kubernetes a povolíte ochranu před hrozbami, neúčtují se žádné poplatky. Pokud v budoucnu přidáte cluster do stejného předplatného, bude automaticky chráněn a v tomto čase začnou platit poplatky.

Přečtěte si další informace o každé z těchto informací na [referenční stránce doporučení zabezpečení](recommendations-reference.md).

Přečtěte si další informace o [ochraně před hrozbami v Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Vylepšení zabezpečení kontejnerů – rychlejší prohledávání registru a aktualizace dokumentace

V rámci kontinuálního investování v doméně zabezpečení kontejneru máme na začátku sdílení významného zlepšení výkonu Security Center dynamické kontroly imagí kontejnerů uložených v Azure Container Registry. Kontroly se teď většinou dokončí přibližně po dvou minutách. V některých případech může trvat až 15 minut.

Abychom vylepšili přehlednost a pokyny týkající se schopností zabezpečení kontejneru Azure Security Center, Aktualizovali jsme také stránky dokumentace k zabezpečení kontejnerů. 

Další informace o zabezpečení kontejneru Security Center najdete v následujících článcích:

- [Přehled funkcí zabezpečení kontejneru Security Center](https://docs.microsoft.com/azure/security-center/container-security)
- [Podrobnosti o integraci s Azure Container Registry](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)
- [Podrobnosti o integraci se službou Azure Kubernetes](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)
- [Jak kontrolovat registry a posílit svoje hostitele Docker](https://docs.microsoft.com/azure/security-center/monitor-container-security)
- [Výstrahy zabezpečení z funkcí ochrany před hrozbami pro clustery služby Azure Kubernetes](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)
- [Výstrahy zabezpečení z funkcí ochrany před hrozbami pro hostitele služeb Azure Kubernetes](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)
- [Doporučení zabezpečení pro kontejnery](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Adaptivní řízení aplikací je aktualizované o nové doporučení a podporu zástupných znaků v pravidlech cesty.

Funkce Adaptivní řízení aplikací obdržela dvě důležité aktualizace:

* Nové doporučení identifikuje potenciálně legitimní chování, které nebylo dříve povoleno. Nové doporučení, **pravidla povolených v zásadách adaptivního řízení aplikací by se měla aktualizovat**, vyzve vás k přidání nových pravidel do stávajících zásad, aby se snížil počet falešně pozitivních výstrah v rámci adaptivních upozornění na porušení řízení aplikací.

* Pravidla cesty teď podporují zástupné znaky. Z této aktualizace můžete nakonfigurovat pravidla povolených cest pomocí zástupných znaků. Existují dva podporované scénáře:

    * Použití zástupného znaku na konci cesty pro povolení všech spustitelných souborů v této složce a podsložkách

    * Použití zástupných znaků uprostřed cesty k povolení známého názvu spustitelného souboru s názvem složky (například osobní složky uživatele se známým spustitelným souborem, automaticky generované názvy složek atd.).


[Přečtěte si další informace o adaptivních řízeních aplikací](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Šest zásad pro zabezpečení rozšířených dat SQL zastaralé

Šest zásad souvisejících s pokročilým zabezpečením dat pro počítače SQL je zastaralé:

- V rozšířených nastaveních zabezpečení dat spravované instance SQL by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.
- V rozšířených nastaveních zabezpečení dat SQL serveru by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.
- Pokročilá nastavení zabezpečení dat pro spravovanou instanci SQL by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.
- Pokročilá nastavení zabezpečení dat pro SQL Server by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.
- E-mailová oznámení správcům a vlastníkům předplatného by se měla povolit v rozšířených nastaveních zabezpečení dat spravované instance SQL
- E-mailová oznámení správcům a vlastníkům předplatného by měla být povolená v nastavení SQL Server Advanced Data Security

Přečtěte si další informace o [předdefinovaných zásadách](security-center-policy-definitions.md).





## <a name="june-2020"></a>Červen 2020

Aktualizace v červnu zahrnují:
- [Rozhraní API pro hodnocení zabezpečení (Preview)](#secure-score-api-preview)
- [Pokročilé zabezpečení dat pro počítače s SQL (Azure, ostatní cloudy a Prem) (Preview)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Dvě nová doporučení pro nasazení agenta Log Analytics do počítačů ARC v Azure (Preview)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nové zásady pro vytváření nepřetržitého exportu a konfigurace automatizace pracovních postupů ve velkém měřítku](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nové doporučení pro použití skupin zabezpečení sítě k ochraně virtuálních počítačů, které nejsou přístupné pro Internet](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nové zásady pro povolení ochrany před hrozbami a pokročilé zabezpečení dat](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Rozhraní API pro hodnocení zabezpečení (Preview)

K vašemu skóre teď můžete přistupovat prostřednictvím [rozhraní API pro zabezpečené skóre](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (aktuálně ve verzi Preview). Metody rozhraní API poskytují flexibilitu pro dotazování na data a vytváření vlastních mechanismů generování sestav v rámci vašich zabezpečených výsledků v průběhu času. Můžete například použít rozhraní API pro **zabezpečení skóre** k získání skóre pro konkrétní předplatné. Kromě toho můžete použít rozhraní API **ovládacích prvků bezpečného řízení skóre** k vypsání ovládacích prvků zabezpečení a aktuálního skóre vašich předplatných.

Příklady externích nástrojů, které jsou dostupné s rozhraním API pro zabezpečené skóre, najdete v části [bezpečné skóre naší komunity GitHubu](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Přečtěte si další informace o [zabezpečeném řízení hodnocení a zabezpečení v Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Pokročilé zabezpečení dat pro počítače s SQL (Azure, ostatní cloudy a Prem) (Preview)

Azure Security Center pokročilé zabezpečení dat pro počítače SQL teď chrání SQL servery hostované v Azure, v jiných cloudových prostředích i v místních počítačích. Tím se rozšiřuje ochrana vašich serverů SQL Azure Native tak, aby plně podporovala hybridní prostředí.

Rozšířené zabezpečení dat poskytuje posouzení ohrožení zabezpečení a rozšířenou ochranu před internetovými útoky pro vaše počítače SQL bez ohledu na jejich umístění.

Nastavení se skládá ze dvou kroků:

1. Nasazení agenta Log Analytics do hostitelského počítače SQL Server, aby se zajistilo připojení k účtu Azure.

1. Povolení volitelného balíčku na stránce ceny a nastavení Security Center.

Přečtěte si další informace o [pokročilém zabezpečení dat pro počítače s SQL](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Dvě nová doporučení pro nasazení agenta Log Analytics do počítačů ARC v Azure (Preview)

Přidali jsme dvě nová doporučení, která vám pomůžou nasadit [agenta Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) do vašich počítačů s obloukem Azure ARC a zajistit, aby byly chráněné pomocí Azure Security Center:

- **Agent Log Analytics musí být nainstalovaný na počítačích se systémem Windows Azure ARC (Preview).**
- **Agent Log Analytics musí být nainstalovaný na počítačích se systémem Linux (Preview).**

Tato nová doporučení se zobrazí ve stejných čtyřech kontrolních prvcích zabezpečení jako stávající (související) doporučení, **Agent monitorování by měl být nainstalovaný na vašich počítačích**: opravit konfigurace zabezpečení, použít adaptivní řízení aplikací, použít aktualizace systému a povolit službu Endpoint Protection.

Doporučení také obsahují možnosti rychlé opravy, které vám pomůžou urychlit proces nasazení. 

Další informace o těchto dvou nových doporučeních najdete v tabulce s [doporučeními pro výpočty a aplikace](recommendations-reference.md#recs-computeapp) .

Přečtěte si další informace o tom, jak Azure Security Center používá agenta v tématu [co je agent Log Analytics?](https://docs.microsoft.com/azure/security-center/faq-data-collection-agents#what-is-the-log-analytics-agent).

Přečtěte si další informace o [rozšířeních pro počítače s obloukem Azure ARC](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).



### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nové zásady pro vytváření nepřetržitého exportu a konfigurace automatizace pracovních postupů ve velkém měřítku

Automatizace procesů monitorování a reakce na incidenty vaší organizace může výrazně zlepšit dobu potřebnou k prošetření a zmírnění incidentů zabezpečení.

K nasazení konfigurací automatizace napříč vaší organizací použijte tyto integrované zásady Azure DeployIfdNotExist a vytvořte a nakonfigurujte postupy automatizace pro [průběžné exportování](continuous-export.md) a zpracování [pracovního postupu](workflow-automation.md) :

Zásady najdete v části zásady Azure:


|Cíl  |Zásady  |ID zásady  |
|---------|---------|---------|
|Průběžný export do centra událostí|[Nasazení exportu do centra událostí pro Azure Security Center výstrahy a doporučení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Průběžný export do pracovního prostoru Log Analytics|[Nasazení exportu do Log Analytics pracovního prostoru pro Azure Security Center výstrahy a doporučení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Automatizace pracovního postupu pro výstrahy zabezpečení|[Nasazení automatizace pracovních postupů pro Azure Security Center výstrahy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatizace pracovního postupu pro doporučení zabezpečení|[Nasazení automatizace pracovních postupů pro Azure Security Center doporučení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Začínáme se [šablonami automatizace pracovních postupů](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Přečtěte si další informace o tom, jak pomocí těchto dvou zásad exportu [průběžně exportovat Azure Security Center upozornění a doporučení prostřednictvím zásad](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nové doporučení pro použití skupin zabezpečení sítě k ochraně virtuálních počítačů, které nejsou přístupné pro Internet

Kontrola zabezpečení "implementovat osvědčené postupy zabezpečení" teď obsahuje následující nové doporučení:

- **Virtuální počítače, které nejsou přístupné z Internetu, by měly být chráněné pomocí skupin zabezpečení sítě**

Stávající doporučení: **internetové virtuální počítače by měly být chráněné pomocí skupin zabezpečení sítě**, nerozlišují se mezi internetovými a neinternetovými virtuálními počítači. U obou se vygenerovalo doporučení s vysokou závažností, pokud se virtuální počítač nepřiřadil do skupiny zabezpečení sítě. Toto nové doporučení odděluje nepřístupné počítače k Internetu, aby se snížily falešně pozitivní a vyhnuli se zbytečným výstrahám s vysokou závažností.

Další informace najdete v tabulce [doporučení sítě](recommendations-reference.md#recs-network) .




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nové zásady pro povolení ochrany před hrozbami a pokročilé zabezpečení dat

Nové zásady níže byly přidány do výchozí iniciativy ASC a jsou určeny pro pomoc s povolením ochrany před hrozbami nebo pokročilým zabezpečením dat pro příslušné typy prostředků.

Zásady najdete v části zásady Azure:


| Zásady                                                                                                                                                                                                                                                                | ID zásady                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [V Azure SQL Databasech serverech by mělo být povolené rozšířené zabezpečení dat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Rozšířené zabezpečení dat by mělo být povoleno na serverech SQL na počítačích](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Pro účty Azure Storage by měla být povolena rozšířená ochrana před internetovými útoky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [V trezorech Azure Key Vault by měla být povolena rozšířená ochrana před internetovými útoky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [V plánech Azure App Service by měla být povolena rozšířená ochrana před internetovými útoky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [V Azure Container Registry Registry by měla být povolena rozšířená ochrana před internetovými útoky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [V clusterech služby Azure Kubernetes by měla být povolena rozšířená ochrana před internetovými útoky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [V Virtual Machines by měla být povolena rozšířená ochrana před internetovými útoky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Přečtěte si další informace o [ochraně před hrozbami v Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection).





## <a name="may-2020"></a>Květen 2020

Aktualizace v nástroji mohou zahrnovat:
- [Pravidla pro potlačení výstrah (Preview)](#alert-suppression-rules-preview)
- [Posouzení ohrožení zabezpečení virtuálního počítače je teď všeobecně dostupné](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Změny přístupu k virtuálnímu počítači JIT (just-in-time)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Vlastní doporučení se přesunula do samostatného řízení zabezpečení.](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Přepínač přidaný pro zobrazení doporučení v ovládacích prvcích nebo jako nestrukturovaný seznam](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Rozšířené řízení zabezpečení "implementovat osvědčené postupy zabezpečení"](#expanded-security-control-implement-security-best-practices)
- [Vlastní zásady s vlastními metadaty jsou teď všeobecně dostupné.](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Funkce analýzy výpisu stavu systému při migraci na detekci útoku typu soubor](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Pravidla pro potlačení výstrah (Preview)

Tato nová funkce (aktuálně ve verzi Preview) pomáhá snižovat únavu výstrah. Pomocí pravidel můžete automaticky skrýt výstrahy, které se neškodného nebo které souvisejí s běžnými aktivitami ve vaší organizaci. To vám umožní soustředit se na nejrelevantnější hrozby. 

Budou se vygenerovat i výstrahy, které odpovídají povoleným pravidlům potlačení, ale jejich stav se nastaví na zastaveno. Můžete zobrazit stav v Azure Portal, nebo máte přístup k výstrahám zabezpečení Security Center.

Pravidla potlačení definují kritéria, pro která se mají automaticky odkládat výstrahy. Obvykle byste pravidlo potlačení používali k těmto akcím:

- potlačit výstrahy, které jste identifikovali jako falešně pozitivní

- potlačit výstrahy, které se spouštějí příliš často, aby byly užitečné

Přečtěte si další informace o [potlačení výstrah z ochrany před hrozbami v Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Posouzení ohrožení zabezpečení virtuálního počítače je teď všeobecně dostupné

Úroveň Standard Security Center nyní zahrnuje integrované posouzení ohrožení zabezpečení pro virtuální počítače bez dalších poplatků. Toto rozšíření používá Qualys, ale oznamuje své závěry přímo zpátky do Security Center. Nepotřebujete licenci Qualys ani účet Qualys – všechno se bez problémů zpracovává v Security Center.

Nové řešení může průběžně kontrolovat vaše virtuální počítače a odhalit tak ohrožení zabezpečení a prezentovat zjištění v Security Center. 

K nasazení řešení použijte nové doporučení zabezpečení:

"Povolte integrované řešení posouzení ohrožení zabezpečení na virtuálních počítačích (s technologií Qualys)."

Přečtěte si další informace o [integrovaném posouzení ohrožení zabezpečení Security Center pro virtuální počítače](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Změny přístupu k virtuálnímu počítači JIT (just-in-time)

Security Center obsahuje volitelnou funkci pro ochranu portů pro správu vašich virtuálních počítačů. To zajišťuje ochranu před nejběžnějšími formami útoků hrubou silou.

Tato aktualizace přináší následující změny této funkce:

- Doporučení, které doporučuje povolit JIT na virtuálním počítači, bylo přejmenováno. Dřív je potřeba na virtuálních počítačích použít řízení přístupu k síti za běhu: porty pro správu virtuálních počítačů by se měly chránit pomocí řízení přístupu k síti za běhu.

- Doporučení se aktivuje jenom v případě, že existují otevřené porty pro správu.

Přečtěte si další informace o [funkci přístupu JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Vlastní doporučení se přesunula do samostatného řízení zabezpečení.

Jeden ovládací prvek zabezpečení představený pomocí zvýšeného zabezpečeného skóre byl "implementovat osvědčené postupy zabezpečení". Všechna vlastní doporučení vytvořená pro vaše předplatná se automaticky umístila do tohoto ovládacího prvku. 

Aby bylo snazší najít vaše vlastní doporučení, přesunuli jsme je do vyhrazeného řízení zabezpečení "Custom doporučení". Tento ovládací prvek nemá žádný vliv na vaše zabezpečené skóre.

Přečtěte si další informace o ovládacích prvcích zabezpečení v [rozšířeném zabezpečeném skóre (Preview) v Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Přepínač přidaný pro zobrazení doporučení v ovládacích prvcích nebo jako nestrukturovaný seznam

Řízení zabezpečení jsou logické skupiny souvisejících doporučení zabezpečení. Odrážejí vaše zranitelné plochy pro útok. Ovládací prvek je sada doporučení zabezpečení s pokyny, které vám pomůžou implementovat tato doporučení.

Pokud chcete hned zjistit, jak dobře vaše organizace zabezpečuje jednotlivé plochy pro útok, Projděte si skóre pro jednotlivé ovládací prvky zabezpečení.

Ve výchozím nastavení se vaše doporučení zobrazují v ovládacích prvcích zabezpečení. Z této aktualizace je můžete zobrazit také jako seznam. Pokud je chcete zobrazit jako jednoduchý seznam seřazený podle stavu ovlivněných prostředků, použijte nový přepínač seskupit podle ovládacích prvků. Přepínač je nad seznamem na portálu.

Ovládací prvky zabezpečení – a tento přepínač jsou součástí nového prostředí pro zajištění skóre. Nezapomeňte nám poslat svůj názor přímo z portálu.

Přečtěte si další informace o ovládacích prvcích zabezpečení v [rozšířeném zabezpečeném skóre (Preview) v Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Přepínací tlačítko Seskupit podle ovládacích prvků pro doporučení":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Rozšířené řízení zabezpečení "implementovat osvědčené postupy zabezpečení" 

Jeden ovládací prvek zabezpečení představený pomocí rozšířeného zabezpečeného skóre je "implementace osvědčených postupů zabezpečení". Pokud je v tomto ovládacím prvku doporučení, nemá to vliv na zabezpečené skóre. 

V této aktualizaci se tři doporučení přesunuly mimo ovládací prvky, ve kterých byly původně umístěné, a do tohoto osvědčeného postupu. Provedli jsme tento krok, protože jsme zjistili, že riziko těchto tří doporučení je nižší, než bylo původně v myšlenkě.

Kromě toho byla do tohoto ovládacího prvku zavedena dvě nová doporučení a přidána.

Existují tři doporučení, která jste přesunuli:

- **Pro účty s oprávněním ke čtení v předplatném** (původně v ovládacím prvku povolit MFA) by měla být povolená MFA.
- **Externí účty s oprávněním ke čtení by se měly z předplatného odebrat** (původně v ovládacím prvku Správa přístupu a oprávnění).
- **Pro vaše předplatné by se měla určit maximálně 3 vlastníci** (v ovládacím prvku Správa přístupu a oprávnění).

K ovládacímu prvku jsou přidána dvě nová doporučení:

- **Rozšíření konfigurace hosta by se mělo nainstalovat na virtuální počítače s Windows (verze Preview)** – pomocí [Azure Policy konfigurace hosta](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) poskytuje viditelnost ve virtuálních počítačích s nastavením serveru a aplikací (jenom Windows).

- **Na vašich počítačích by mělo být povolené ochrany před zneužitím v programu Windows Defender (Preview)** – ochrana před zneužitím v programu Windows defender využívá Azure Policyho agenta konfigurace hostů. Ochrana před zneužitím má čtyři komponenty, které jsou navržené tak, aby se zařízení zamkly na širokou škálu útoků, a to v případě, že se v případě malwaru povoluje vyrovnávat rizika zabezpečení a požadavky na produktivitu (jenom Windows).

Další informace o systému Windows Defender zneužití Guard v [vytváření a nasazení zásad pro ochranu před zneužitím](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Přečtěte si další informace o ovládacích prvcích zabezpečení ve [zvýšeném zabezpečeném skóre (Preview)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Vlastní zásady s vlastními metadaty jsou teď všeobecně dostupné.

Vlastní zásady jsou teď součástí prostředí Security Center doporučení, bezpečného skóre a řídicího panelu standardů dodržování předpisů regulativních předpisů. Tato funkce je teď všeobecně dostupná a umožňuje rozšíření pokrytí posouzení zabezpečení vaší organizace v Security Center. 

Vytvořte vlastní iniciativu v zásadách Azure, přidejte do ní zásady a připojte je k Azure Security Center a vizualizujte je jako doporučení.

Nyní jsme také přidali možnost Upravit metadata vlastního doporučení. Mezi možnosti metadat patří závažnost, nápravný postup, informace o hrozbách a další.  

Přečtěte si další [informace o vylepšení vlastních doporučení s podrobnými informacemi](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Funkce analýzy výpisu stavu systému při migraci na detekci útoku typu soubor 

Do [detekce útoku typu soubor](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless)se integruje možnosti detekce CDA (Windows Crash Analysis). Analýza detekce útoků bez souborů přináší vylepšené verze těchto výstrah zabezpečení pro počítače s Windows: zjištěné injektáže kódu, zjištěná maskující modul Windows, zjištěná skrytý spustitelný kód a zjištěn podezřelý segment kódu.

Některé z výhod tohoto přechodu:

- **Proaktivní a včasné zjišťování malwaru** – přístup k diskovým diskům (CDA) se zabývá tím, že se nevyskytne chyba a následně se spustí analýza, aby bylo možné najít škodlivé Při použití detekce útoků pomocí souborů dojde k proaktivní identifikaci hrozeb v paměti, které jsou spuštěny. 

- **Obohacené výstrahy** – výstrahy zabezpečení z detekce útoků bez souborů zahrnují rozšíření, která nejsou k dispozici ze služby CDA, například informace o aktivních síťových připojeních. 

- **Agregace výstrah** – Pokud disk CDA zjistil více vzorů útoku v rámci jednoho výpisu stavu systému, aktivovalo se několik výstrah zabezpečení. Detekce útoků bez souborů kombinuje všechny identifikované vzory útoků ze stejného procesu do jediné výstrahy. tím se eliminuje nutnost korelace více výstrah.

- **Omezené požadavky na pracovní prostor Log Analytics** – výpisy stavu systému obsahující potenciálně citlivá data se už nebudou nahrávat do vašeho pracovního prostoru Log Analytics.



## <a name="april-2020"></a>Duben 2020

Aktualizace v dubnu zahrnují:
- [Dynamické balíčky pro dodržování předpisů jsou teď všeobecně dostupné.](#dynamic-compliance-packages-are-now-generally-available)
- [Doporučení identity teď jsou součástí Azure Security Center úrovně Free.](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamické balíčky pro dodržování předpisů jsou teď všeobecně dostupné.

Řídicí panel dodržování předpisů Azure Security Center nyní zahrnuje **dynamické balíčky kompatibility** (teď všeobecně dostupné) ke sledování dalších odvětví a regulativních standardů.

Dynamické balíčky pro dodržování předpisů můžete přidat do svého předplatného nebo skupiny pro správu ze stránky Security Center zásady zabezpečení. Po zprovoznění standardního nebo srovnávacího testu se standardně zobrazí na řídicím panelu dodržování předpisů, kde jsou všechna přidružená data o dodržování předpisů namapovaná jako posouzení. Zobrazí se souhrnná zpráva o všech standardech, které jsou k dispozici ke stažení.

Nyní můžete přidat standardy jako:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **Oficiální Velká Británie a Velká Británie NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nový)** (což je ucelenější reprezentace Azure CIS 1.1.0)

Kromě toho jsme nedávno přidali **srovnávací testy zabezpečení Azure**, které jsou určené pro zabezpečení a osvědčené postupy pro dodržování předpisů v závislosti na běžných architekturách dodržování předpisů, a pokyny specifické pro Azure. Další standardy budou na řídicím panelu podporovány, jakmile budou k dispozici.  
 
Přečtěte si další informace o [přizpůsobení sady standardů na řídicím panelu dodržování legislativních předpisů](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Doporučení identity teď jsou součástí Azure Security Center úrovně Free.

Doporučení zabezpečení pro identitu a přístup na úrovni Free Azure Security Center jsou teď všeobecně dostupná. Tato funkce je součástí snahy o zpřístupnění funkcí CSPM (Cloud Security stav Management) zdarma. Až do této chvíle byly tato doporučení dostupná jenom na cenové úrovni Standard.

Příklady doporučení pro identitu a přístup zahrnují:

- U účtů s oprávněním vlastníka v předplatném by mělo být povoleno vícefaktorové ověřování.
- "Pro vaše předplatné by se měla určit maximálně tři vlastníci."
- "Zastaralé účty by se měly odebrat z vašeho předplatného".

Pokud máte předplatné na cenové úrovni Free, jejich zabezpečená skóre budou ovlivněna touto změnou, protože nebyly nikdy posuzovány pro jejich identitu a zabezpečení přístupu.

Další informace o [identitě a doporučeních pro přístup](recommendations-reference.md#recs-identity).

Přečtěte si další informace o [identitě a přístupu k monitorování](security-center-identity-access.md).