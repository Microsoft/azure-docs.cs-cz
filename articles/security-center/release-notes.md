---
title: Poznámky k verzi pro Azure Security Center
description: Popis toho, co je nového a co se změnilo v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2020
ms.author: memildin
ms.openlocfilehash: 79dcc645ecff00b3189dc90dcf34e042a78ed318
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949322"
---
# <a name="whats-new-in-azure-security-center"></a>Co je nového v Azure Security Center?

Security Center je v aktivním vývoji a průběžně přijímá vylepšení. Tato stránka vám poskytne informace o nových funkcích, opravách chyb a zazastaralých funkcích, abyste měli přehled o nejnovějším vývoji.

Tato stránka se často aktualizuje, takže ji můžete často znovu navštěvovat. 

Další informace o *plánovaných* změnách, které už brzy Security Center, najdete v tématu [důležité nadcházející změny Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Pokud hledáte položky starší než šest měsíců, najdete je v archivu, kde najdete novinky [v Azure Security Center](release-notes-archive.md).


## <a name="november-2020"></a>Listopad 2020

Aktualizace v listopadu zahrnují:

- [29 doporučení verze Preview pro zvýšení pokrytí srovnávacích testů zabezpečení Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 přidáno do řídicího panelu dodržování předpisů pro Security Center](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Seznam doporučení nyní obsahuje filtry](#recommendations-list-now-includes-filters)
- [Vylepšené a rozšířené prostředí pro Automatické zřizování](#auto-provisioning-experience-improved-and-expanded)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 doporučení verze Preview pro zvýšení pokrytí srovnávacích testů zabezpečení Azure

Srovnávací test zabezpečení Azure je Microsoftem vytvořená sada pokynů pro zabezpečení a osvědčené postupy na základě běžných architektur dodržování předpisů, které jsou specifické pro Azure. [Další informace o srovnávacím testu zabezpečení Azure](../security/benchmarks/introduction.md)

Následující 29 doporučení pro verzi Preview jsme přidali do Security Center, abyste zvýšili pokrytí tohoto srovnávacího testu.

Doporučení pro verzi Preview negenerují prostředek v pořádku a nejsou zahrnutá do výpočtů vašeho zabezpečeného skóre. Opravte je všude, kde je to možné, takže až do doby, kdy období Preview skončí, přispějete k vašemu skóre. Přečtěte si další informace o tom, jak na tato doporučení reagovat v tématu o [opravách doporučení v Azure Security Center](security-center-remediate-recommendations.md).

| Řízení zabezpečení                     | Nová doporučení                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Šifrovat data při přenosu              | – Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL.<br>– Pro databázové servery MySQL by mělo být povoleno připojení SSL.<br>-TLS by se měl aktualizovat na nejnovější verzi vaší aplikace API<br>-TLS by se měl aktualizovat na nejnovější verzi vaší aplikace Function App<br>-Protokol TLS by se měl aktualizovat na nejnovější verzi vaší webové aplikace.<br>– FTPS by mělo být vyžadováno v aplikaci API<br>– FTPS by mělo být vyžadováno ve vaší aplikaci Function App<br>– FTPS by mělo být vyžadováno ve vaší webové aplikaci                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Správa přístupu a oprávnění        | – Webové aplikace by měly požádat o certifikát SSL pro všechny příchozí požadavky<br>-V aplikaci API by se měla použít spravovaná identita.<br>– Ve vaší aplikaci Function app by se měla použít spravovaná identita<br>– Ve vaší webové aplikaci by se měla použít spravovaná identita.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Omezit neautorizovaný přístup k síti | – Privátní koncový bod by měl být povolený pro PostgreSQL servery.<br>– Privátní koncový bod by měl být povolený pro MariaDB servery.<br>– Privátní koncový bod by měl být povolený pro servery MySQL.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Povolit auditování a protokolování          | -Protokoly diagnostiky v App Services by měly být povolené                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementace osvědčených postupů zabezpečení    | -Azure Backup by měly být povolené pro virtuální počítače<br>– Geograficky redundantní záloha by měla být povolená pro Azure Database for MariaDB<br>– Geograficky redundantní záloha by měla být povolená pro Azure Database for MySQL<br>– Geograficky redundantní záloha by měla být povolená pro Azure Database for PostgreSQL<br>-PHP by se mělo aktualizovat na nejnovější verzi vaší aplikace API<br>– PHP by se mělo aktualizovat na nejnovější verzi vaší webové aplikace<br>– Java by se měl aktualizovat na nejnovější verzi vaší aplikace API<br>– Java by se měl aktualizovat na nejnovější verzi aplikace Function App<br>– Java by se měl aktualizovat na nejnovější verzi vaší webové aplikace<br>– Python by se měl aktualizovat na nejnovější verzi vaší aplikace API<br>– Python by se měl aktualizovat na nejnovější verzi vaší aplikace Function App<br>– Python by se měl aktualizovat na nejnovější verzi vaší webové aplikace<br>– Uchovávání auditu pro SQL servery by mělo být nastavené aspoň na 90 dní. |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Související odkazy:

- [Další informace o testu zabezpečení Azure](../security/benchmarks/introduction.md)
- [Další informace o Azure API Apps](../app-service/app-service-web-tutorial-rest-api.md)
- [Další informace o aplikacích Azure Function App](../azure-functions/functions-overview.md)
- [Další informace o službě Azure Web Apps](../app-service/overview.md)
- [Další informace o Azure Database for MariaDB](../mariadb/overview.md)
- [Další informace o Azure Database for MySQL](../mysql/overview.md)
- [Další informace o Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 přidáno do řídicího panelu dodržování předpisů pro Security Center

Standard NIST SP 800-171 R2 je teď k dispozici jako integrovaná iniciativa pro použití s řídicím panelem dodržování předpisů Azure Security Center. Mapování pro ovládací prvky jsou popsána v [podrobnostech o integrované iniciativě NIST SP 800-171 R2 dodržování předpisů](../governance/policy/samples/nist-sp-800-171-r2.md). 

Pokud chcete použít standard pro vaše předplatná a průběžně monitorovat svůj stav dodržování předpisů, postupujte podle pokynů v tématu [přizpůsobení sady standardů na řídicím panelu dodržování předpisů](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="NIST SP 800 171 R2 Standard ve Security Center řídicím panelu dodržování předpisů v legislativě":::

Další informace o tomto standardu dodržování předpisů najdete v tématu [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>Seznam doporučení nyní obsahuje filtry

Seznam doporučení zabezpečení teď můžete filtrovat podle rozsahu kritérií. V následujícím příkladu je seznam doporučení filtrovaný tak, aby zobrazoval doporučení, která:

- jsou **všeobecně dostupné** (tj. není ve verzi Preview)
- jsou pro **účty úložiště**
- podpora **opravy rychlých oprav**

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filtry pro seznam doporučení":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Vylepšené a rozšířené prostředí pro Automatické zřizování

Funkce automatického zřizování pomáhá snižovat režijní náklady díky instalaci požadovaných rozšíření na nové a stávající virtuální počítače Azure, aby mohla těžit z ochrany Security Center. 

Jak Azure Security Center roste, vyvinuly se další rozšíření a Security Center můžou monitorovat větší seznam typů prostředků. Nástroje pro Automatické zřizování se teď rozšířily tak, aby podporovaly další rozšíření a typy prostředků, a to díky využití možností Azure Policy.

Teď můžete nakonfigurovat automatické zřizování pro:

- Agent Log Analytics
- New Doplněk Azure Policy pro Kubernetes
- New Microsoft Dependency Agent

Další informace o [automatických zřizováních agentů a rozšíření od Azure Security Center](security-center-enable-data-collection.md).

## <a name="october-2020"></a>Říjen 2020

Aktualizace v říjnu zahrnují:
- [Posouzení ohrožení zabezpečení pro místní a více cloudových počítačů (Preview)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Přidala se Azure Firewall doporučení (Preview).](#azure-firewall-recommendation-added-preview)
- [Pro doporučení služby Kubernetes Services, které se aktualizovaly pomocí rychlé opravy, by se měly definovat povolené rozsahy IP](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Řídicí panel dodržování předpisů teď obsahuje možnost odebrání standardů.](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Tabulka Microsoft. Security/securityStatuses se odebrala z Azure Resource graphu (ARG).](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Posouzení ohrožení zabezpečení pro místní a více cloudových počítačů (Preview)

Skener pro posouzení ohrožení zabezpečení v [Azure Defenderu pro servery](defender-for-servers-introduction.md)(s technologií Qualys) teď prohledává servery s podporou ARC Azure.

Pokud jste povolili Azure ARC na počítačích mimo Azure, Security Center se bude nabízet k nasazení integrovaného skeneru ohrožení zabezpečení ručně a v rámci škálování.

V této aktualizaci si můžete vysílat výkon **Azure Defenderu pro servery** a konsolidovat svůj program pro správu ohrožení zabezpečení ve všech vašich prostředcích Azure i mimo Azure.

Hlavní možnosti:

- Monitorování stavu zřizování skeneru VA (zranitelnost) na počítačích ARC Azure
- Zřizování integrovaného agenta s rozhraním VA pro nechráněné počítače s Windows a Linuxem na platformě Azure ARC (ručně a v rozsahu)
- Přijímání a analýza zjištěných slabých míst z nasazených agentů (ručně a v rozsahu)
- Jednotné prostředí pro virtuální počítače Azure a počítače s obloukem Azure ARC

[Přečtěte si další informace o nasazení integrovaného skeneru ohrožení zabezpečení do hybridních počítačů](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Přečtěte si další informace o serverech s podporou ARC Azure](https://docs.microsoft.com/azure/azure-arc/servers/).


### <a name="azure-firewall-recommendation-added-preview"></a>Přidala se Azure Firewall doporučení (Preview).

Bylo přidáno nové doporučení pro ochranu všech virtuálních sítí pomocí Azure Firewall.

Doporučujeme, aby se **virtuální sítě chránily pomocí Azure firewall** radit s cílem omezit přístup k virtuálním sítím a zabránit potenciálním hrozbám pomocí Azure firewall.

Přečtěte si další informace o [Azure firewall](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Pro doporučení služby Kubernetes Services, které se aktualizovaly pomocí rychlé opravy, by se měly definovat povolené rozsahy IP

**Povolené rozsahy IP adres pro doporučení by měly být definované v Kubernetes Services** nyní mají možnost Rychlá oprava.

Další podrobnosti o tomto doporučení a všech dalších Security Center doporučeních najdete v tématu [doporučení zabezpečení – referenční příručka](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Pro doporučení služby Kubernetes Services s možností Rychlá oprava by se měly definovat rozsahy autorizovaných IP adres.":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Řídicí panel dodržování předpisů teď obsahuje možnost odebrání standardů.

Řídicí panel dodržování legislativních předpisů Security Center poskytuje přehledy o dodržování předpisů v závislosti na tom, jak splňujete konkrétní kontrolu a požadavky na dodržování předpisů.

Řídicí panel obsahuje výchozí sadu regulativních standardů. Pokud některé z uvedených standardů nejsou pro vaši organizaci důležité, je teď jednoduchý proces, který je jednoduše odebral z uživatelského rozhraní pro předplatné. Standardy je možné odebrat jenom na úrovni *předplatného* . Nejedná se o obor skupiny pro správu.

Další informace najdete v [Odebrání standardu z řídicího panelu](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Tabulka Microsoft. Security/securityStatuses se odebrala z Azure Resource graphu (ARG).

Azure Resource Graph je služba v Azure, která je navržená tak, aby poskytovala efektivní průzkum prostředků s možností škálování v rámci dané sady předplatných, abyste mohli efektivně řídit vaše prostředí. 

Pro Azure Security Center můžete použít ARG a [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) k dotazování široké škály dat stav zabezpečení. Například:

- Využití inventáře prostředků (ARG)
- Popsali jsme vzorový ARGický dotaz pro [identifikaci účtů bez povoleného vícefaktorového ověřování (MFA)](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled) .

V ARG existují tabulky dat, které můžete použít ve svých dotazech.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Průzkumník Azure Resource Graph a dostupné tabulky":::

> [!TIP]
> V dokumentaci ARG najdete seznam všech dostupných tabulek v [tabulce Azure Resource Graph a odkaz na typ prostředku](../governance/resource-graph/reference/supported-tables-resources.md).

Z této aktualizace byla odstraněna tabulka **Microsoft. Security/securityStatuses** . Rozhraní securityStatuses API je stále k dispozici.

Nahrazení dat může být použito v tabulce Microsoft. Security/Assessments.

Hlavním rozdílem mezi Microsoft. Security/securityStatuses a Microsoft. Security/Assessments je, že při první zobrazení agregace posouzení obsahuje sekund jeden záznam pro každý.

Například Microsoft. Security/securityStatuses by vrátil výsledek s polem dvou policyAssessments:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
To znamená, že Microsoft. Security/Assessments bude obsahovat záznam pro každé vyhodnocení zásad, a to následujícím způsobem:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Příklad převodu existujícího dotazu ARG pomocí securityStatuses pro teď použití tabulky Assessments:**

Dotaz, který odkazuje na SecurityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Náhradní dotaz pro tabulku posouzení:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Další informace najdete na následujících odkazech:
- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md)
- [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)


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

Podpora pro [soubory](../storage/files/storage-files-introduction.md) a [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) Azure je teď všeobecně dostupná.

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

Doporučení pro verzi Preview "pod zásadou zabezpečení by se měly definovat na Kubernetes Services", jak je popsáno v dokumentaci ke [službě Azure Kubernetes](../aks/use-pod-security-policies.md) .

Funkce zásady zabezpečení pod (Preview) je nastavena pro vyřazení a nebude již k dispozici po 15. říjnu 2020 ve prospěch Azure Policy pro AKS.

Po použití zásady zabezpečení (Preview) je zastaralá. tuto funkci je třeba zakázat na všech stávajících clusterech pomocí zastaralé funkce, aby se prováděly budoucí upgrady clusteru a zůstaly v rámci podpory Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Vylepšená e-mailová oznámení z Azure Security Center

Vylepšili jsme následující oblasti e-mailů týkajících se výstrah zabezpečení: 

- Přidání možnosti odesílání e-mailových oznámení o výstrahách pro všechny úrovně závažnosti
- Přidali jsme možnost informovat uživatele o různých rolích Azure v rámci předplatného.
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

Security Center přidaná plná podpora pro [výchozí nastavení zabezpečení](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), ochrana zabezpečení identit od Microsoftu je bezplatná.

Výchozí hodnoty zabezpečení poskytují předem nakonfigurovaná nastavení zabezpečení identity, která chrání vaši organizaci před běžnými útoky souvisejícími s identitou. Výchozí hodnoty zabezpečení již chrání více než 5 000 000 klientů. klienti 50 000 jsou chráněni také pomocí Security Center.

Security Center teď poskytuje doporučení zabezpečení vždy, když identifikuje předplatné Azure bez povolených výchozích hodnot zabezpečení. Až do této chvíle Security Center doporučeno povolit vícefaktorové ověřování pomocí podmíněného přístupu, který je součástí licence Azure Active Directory (AD) Premium. Pro zákazníky, kteří používají Azure AD Free, teď doporučujeme povolit výchozí nastavení zabezpečení. 

Naším cílem je povzbudit větší počet zákazníků k zabezpečení svých cloudových prostředí pomocí vícefaktorového ověřování a zmírnit jedno z nejvyšších rizik, která jsou také největší dopadem na vaše [zabezpečené skóre](secure-score-security-controls.md).

Přečtěte si další informace o [výchozích hodnotách zabezpečení](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Přidalo se doporučení instančních objektů.

Bylo přidáno nové doporučení, které Security Center zákazníky, kteří používají certifikáty pro správu ke správě předplatných, přepínat na instanční objekty.

K **ochraně vašich předplatných, namísto certifikátů pro správu** , doporučujeme použít instanční objekty nebo Azure Resource Manager pro bezpečnější správu předplatných. 

Další informace o [aplikacích a instančních objektech služby v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


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

|Doporučení|Rozsah|
|----|:----|
|**Povolení integrovaného řešení posouzení ohrožení zabezpečení na virtuálních počítačích (používá se Qualys)**<br>Klíč: 550e890b-e652-4d22-8274-60b3bdb24c63|Integrované|
|**Náprava ohrožení zabezpečení zjištěná na vašich virtuálních počítačích (používá se Qualys)**<br>Klíč: 1195afff-c881-495E-9bc5-1486211ae03f|Integrované|
|**Řešení posouzení ohrožení zabezpečení by se mělo nainstalovat na virtuální počítače.**<br>Klíč: 01b1ed4c-B733-4FEE-b145-f23236e70cf3|BYOL|
|**Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.**<br>Klíč: 71992a2a-D168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Zásady|Rozsah|
|----|:----|
|**Na virtuálních počítačích by mělo být povolené posouzení ohrožení zabezpečení**<br>ID zásady: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Integrované|
|**Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.**<br>ID zásady: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Od srpna 2020

|Doporučení|Rozsah|
|----|:----|
|**Na virtuálních počítačích by mělo být povolené řešení posouzení ohrožení zabezpečení.**<br>Klíč: ffff0522-1e88-47fc-8382-2a80ba848f5d|Předdefinované + BYOL|
|**Ohrožení zabezpečení ve vašich virtuálních počítačích by se mělo opravit.**<br>Klíč: 1195afff-c881-495E-9bc5-1486211ae03f|Předdefinované + BYOL|
||||

|Zásady|Rozsah|
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

I když teď můžete nasadit integrované rozšíření posouzení ohrožení zabezpečení (s technologií Qualys) na mnoho dalších počítačů, podpora je dostupná jenom v případě, že používáte operační systém, který je uvedený v [části nasazení integrovaného skeneru zabezpečení na virtuální počítače úrovně Standard](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines) .

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

Tato nová doporučení patří do nastavení Povolit řízení zabezpečení v **Azure Defenderu** .

Doporučení také zahrnují funkci rychlé opravy. 

> [!IMPORTANT]
> Oprava některé z těchto doporučení bude mít za následek poplatky za ochranu relevantních prostředků. Tyto poplatky začnou platit okamžitě, pokud máte související prostředky v aktuálním předplatném. Případně můžete v budoucnu přidat data později.
> 
> Například pokud ve vašem předplatném nemáte žádné clustery služby Azure Kubernetes a povolíte ochranu před hrozbami, neúčtují se žádné poplatky. Pokud v budoucnu přidáte cluster do stejného předplatného, bude automaticky chráněn a v tomto čase začnou platit poplatky.

Přečtěte si další informace o každé z těchto informací na [referenční stránce doporučení zabezpečení](recommendations-reference.md).

Přečtěte si další informace o [ochraně před hrozbami v Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Vylepšení zabezpečení kontejnerů – rychlejší prohledávání registru a aktualizace dokumentace

V rámci kontinuálního investování v doméně zabezpečení kontejneru máme na začátku sdílení významného zlepšení výkonu Security Center dynamické kontroly imagí kontejnerů uložených v Azure Container Registry. Kontroly se teď většinou dokončí přibližně po dvou minutách. V některých případech může trvat až 15 minut.

Abychom vylepšili přehlednost a pokyny týkající se schopností zabezpečení kontejneru Azure Security Center, Aktualizovali jsme také stránky dokumentace k zabezpečení kontejnerů. 

Další informace o zabezpečení kontejneru Security Center najdete v následujících článcích:

- [Přehled funkcí zabezpečení kontejneru Security Center](container-security.md)
- [Podrobnosti o integraci s Azure Container Registry](defender-for-container-registries-introduction.md)
- [Podrobnosti o integraci se službou Azure Kubernetes](defender-for-kubernetes-introduction.md)
- [Jak kontrolovat registry a posílit svoje hostitele Docker](container-security.md)
- [Výstrahy zabezpečení z funkcí ochrany před hrozbami pro clustery služby Azure Kubernetes](alerts-reference.md#alerts-akscluster)
- [Výstrahy zabezpečení z funkcí ochrany před hrozbami pro hostitele služeb Azure Kubernetes](alerts-reference.md#alerts-containerhost)
- [Doporučení zabezpečení pro kontejnery](recommendations-reference.md#recs-containers)



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

Přečtěte si další informace o [předdefinovaných zásadách](./policy-reference.md).





## <a name="june-2020"></a>Červen 2020

Aktualizace v červnu zahrnují:
- [Rozhraní API pro hodnocení zabezpečení (Preview)](#secure-score-api-preview)
- [Pokročilé zabezpečení dat pro počítače s SQL (Azure, ostatní cloudy a Prem) (Preview)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Dvě nová doporučení pro nasazení agenta Log Analytics do počítačů ARC v Azure (Preview)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nové zásady pro vytváření nepřetržitého exportu a konfigurace automatizace pracovních postupů ve velkém měřítku](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nové doporučení pro použití skupin zabezpečení sítě k ochraně virtuálních počítačů, které nejsou přístupné pro Internet](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nové zásady pro povolení ochrany před hrozbami a pokročilé zabezpečení dat](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Rozhraní API pro hodnocení zabezpečení (Preview)

K vašemu skóre teď můžete přistupovat prostřednictvím [rozhraní API pro zabezpečené skóre](/rest/api/securitycenter/securescores/) (aktuálně ve verzi Preview). Metody rozhraní API poskytují flexibilitu pro dotazování na data a vytváření vlastních mechanismů generování sestav v rámci vašich zabezpečených výsledků v průběhu času. Můžete například použít rozhraní API pro **zabezpečení skóre** k získání skóre pro konkrétní předplatné. Kromě toho můžete použít rozhraní API **ovládacích prvků bezpečného řízení skóre** k vypsání ovládacích prvků zabezpečení a aktuálního skóre vašich předplatných.

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

Přidali jsme dvě nová doporučení, která vám pomůžou nasadit [agenta Log Analytics](../azure-monitor/platform/log-analytics-agent.md) do vašich počítačů s obloukem Azure ARC a zajistit, aby byly chráněné pomocí Azure Security Center:

- **Agent Log Analytics musí být nainstalovaný na počítačích se systémem Windows Azure ARC (Preview).**
- **Agent Log Analytics musí být nainstalovaný na počítačích se systémem Linux (Preview).**

Tato nová doporučení se zobrazí ve stejných čtyřech kontrolních prvcích zabezpečení jako stávající (související) doporučení, **Agent monitorování by měl být nainstalovaný na vašich počítačích**: opravit konfigurace zabezpečení, použít adaptivní řízení aplikací, použít aktualizace systému a povolit službu Endpoint Protection.

Doporučení také obsahují možnosti rychlé opravy, které vám pomůžou urychlit proces nasazení. 

Další informace o těchto dvou nových doporučeních najdete v tabulce s [doporučeními pro výpočty a aplikace](recommendations-reference.md#recs-computeapp) .

Přečtěte si další informace o tom, jak Azure Security Center používá agenta v tématu [co je agent Log Analytics?](faq-data-collection-agents.md#what-is-the-log-analytics-agent).

Přečtěte si další informace o [rozšířeních pro počítače s obloukem Azure ARC](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nové zásady pro vytváření nepřetržitého exportu a konfigurace automatizace pracovních postupů ve velkém měřítku

Automatizace procesů monitorování a reakce na incidenty vaší organizace může výrazně zlepšit dobu potřebnou k prošetření a zmírnění incidentů zabezpečení.

K nasazení konfigurací automatizace napříč vaší organizací použijte tyto integrované zásady Azure DeployIfdNotExist a vytvořte a nakonfigurujte postupy automatizace pro [průběžné exportování](continuous-export.md) a zpracování [pracovního postupu](workflow-automation.md) :

Zásady najdete v části zásady Azure:


|Cíl  |Zásady  |ID zásady  |
|---------|---------|---------|
|Průběžný export do centra událostí|[Nasazení exportu do centra událostí pro upozornění a doporučení služby Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Průběžný export do pracovního prostoru Log Analytics|[Nasazení exportu do pracovního prostoru služby Log Analytics pro upozornění a doporučení služby Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Automatizace pracovního postupu pro výstrahy zabezpečení|[Nasazení automatizace pracovních postupů pro upozornění služby Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatizace pracovního postupu pro doporučení zabezpečení|[Nasazení automatizace pracovních postupů pro doporučení služby Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Začínáme se [šablonami automatizace pracovních postupů](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Přečtěte si další informace o použití těchto dvou zásad exportu v [části Konfigurace automatizace pracovního postupu ve velkém měřítku pomocí zadaných zásad](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) a [Nastavení průběžného exportu](continuous-export.md#set-up-a-continuous-export).


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

Přečtěte si další informace o [ochraně před hrozbami v Azure Security Center](azure-defender.md).
