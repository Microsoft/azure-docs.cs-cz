---
title: Informace o tom, jak auditovat obsah virtuálních počítačů
description: Přečtěte si, jak Azure Policy používá klienta konfigurace hosta k auditování nastavení v rámci virtuálních počítačů.
ms.date: 01/14/2021
ms.topic: conceptual
ms.openlocfilehash: 6fb3ed3644ccdb5de8f03bedf56943a91570322b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105733022"
---
# <a name="understand-azure-policys-guest-configuration"></a>Vysvětlení konfigurace hosta ve službě Azure Policy


Azure Policy můžou auditovat nastavení v počítači, a to pro počítače běžící v Azure i v [počítačích připojených k Arc](../../../azure-arc/servers/overview.md). Ověřování se provádí pomocí rozšíření Konfigurace hosta a prostřednictvím klienta. Toto rozšíření prostřednictvím klienta ověřuje nastavení, jako například:

- Konfigurace operačního systému
- Konfigurace nebo přítomnost aplikací
- Nastavení prostředí

V současné době většina Azure Policy definice zásad konfigurace hostů jenom auditovat nastavení v rámci počítače. Neaplikují konfigurace. Výjimkou je jedna integrovaná zásada, na [kterou se odkazuje níže](#applying-configurations-using-guest-configuration).

[K dispozici je návod k videu tohoto dokumentu](https://youtu.be/Y6ryD3gTHOs).

## <a name="enable-guest-configuration"></a>Povolit konfiguraci hosta

Pokud chcete auditovat stav počítačů ve vašem prostředí, včetně počítačů v Azure a připojených počítačů ARC, Projděte si následující podrobnosti.

## <a name="resource-provider"></a>Poskytovatel prostředků

Než budete moct použít konfiguraci hosta, musíte zaregistrovat poskytovatele prostředků. Pokud se přiřazení zásady konfigurace hosta provádí prostřednictvím portálu nebo pokud je předplatné zaregistrované v Azure Security Center, poskytovatel prostředků se zaregistruje automaticky. Můžete provést ruční registraci prostřednictvím [portálu](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)nebo rozhraní příkazového [řádku Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Nasazení požadavků pro virtuální počítače Azure

Pokud chcete auditovat nastavení v rámci počítače, je povolená [rozšíření virtuálního počítače](../../../virtual-machines/extensions/overview.md) a počítač musí mít systémově spravovanou identitu. Toto rozšíření stáhne příslušné přiřazení zásad a odpovídající definici konfigurace. Identita se používá k ověření počítače při jeho čtení a zápisu do služby konfigurace hosta. Pro připojené počítače ARC není rozšíření vyžadováno, protože je zahrnuto v agentovi počítače připojeného k ARC.

> [!IMPORTANT]
> K auditování virtuálních počítačů Azure se vyžaduje rozšíření konfigurace hosta a spravovaná identita. Pokud chcete nasadit rozšíření ve velkém měřítku, přiřaďte následující iniciativu zásad:
> 
> `Deploy prerequisites to enable Guest Configuration policies on virtual machines`

### <a name="limits-set-on-the-extension"></a>Omezení nastavená pro rozšíření

Pokud chcete omezit rozšíření proti ovlivnění aplikací spuštěných v počítači, konfigurace hosta nemůže překročit více než 5% procesoru. Toto omezení existuje jak pro předdefinované, tak pro vlastní definice. Totéž platí pro službu konfigurace hosta v agentovi počítače připojeného k ARC.

### <a name="validation-tools"></a>Nástroje ověřování

V počítači používá klient konfigurace Host místní nástroje pro spuštění auditu.

V následující tabulce je uveden seznam místních nástrojů používaných na každém podporovaném operačním systému. U předdefinovaného obsahu obsluhuje konfigurace hosta automatické načítání těchto nástrojů.

|Operační systém|Nástroj pro ověření|Poznámky|
|-|-|-|
|Windows|[Konfigurace požadovaného stavu prostředí PowerShell](/powershell/scripting/dsc/overview/overview) v2| Po straně bylo načteno do složky, kterou používá Azure Policy. Nekoliduje s Windows PowerShell DSC. PowerShell Core není přidaný do systémové cesty.|
|Linux|[Nespec](https://www.chef.io/inspec/)| Nainstaluje 2.2.61 INSPEC verze ve výchozím umístění a přidá se do systémové cesty. Jsou nainstalované i závislosti pro INSPEC Package, včetně Ruby a Pythonu. |

### <a name="validation-frequency"></a>Frekvence ověřování

Klient konfigurace hosta kontroluje nová nebo změněná přiřazení hostů každých 5 minut. Po přijetí přiřazení hostů se nastavení této konfigurace znovu zkontrolují v intervalu 15 minut. Výsledky se odešlou do poskytovatele prostředků konfigurace hosta, až se audit dokončí. Když dojde k [aktivaci vyhodnocení](../how-to/get-compliance-data.md#evaluation-triggers) zásad, stav počítače se zapíše do poskytovatele prostředků konfigurace hosta. Tato aktualizace způsobí Azure Policy vyhodnocení vlastností Azure Resource Manager. Vyhodnocení Azure Policy na vyžádání načte nejnovější hodnotu z poskytovatele prostředků konfigurace hosta. Neaktivuje ale nové auditování konfigurace v rámci počítače. Stav se zapisuje současně do grafu prostředků Azure.

## <a name="supported-client-types"></a>Podporované typy klientů

Definice zásad konfigurace hostů jsou zahrnuté do nových verzí. Starší verze operačních systémů, které jsou k dispozici v Azure Marketplace, jsou vyloučené, pokud není klient konfigurace hosta kompatibilní. Následující tabulka obsahuje seznam podporovaných operačních systémů pro Image Azure:

|Publisher|Name|Verze|
|-|-|-|
|Canonical|Ubuntu Server|14,04 – 20,04|
|Credativ|Debian|8 - 10|
|Microsoft|Windows Server|2012 – 2019|
|Microsoft|Klient Windows|Windows 10|
|OpenLogic|CentOS|7,3 – 8|
|Red Hat|Red Hat Enterprise Linux|7,4 – 8|
|SUSE|SLES|12 SP3 – SP5, 15|

Definice zásad konfigurace hosta podporuje vlastní image virtuálních počítačů, pokud se jedná o jeden z operačních systémů uvedených v tabulce výše.

## <a name="network-requirements"></a>Požadavky sítě

Virtuální počítače v Azure můžou ke komunikaci se službou konfigurace hosta použít buď svůj místní síťový adaptér, nebo privátní odkaz.

Počítače se systémem Azure Arc se připojují pomocí místní síťové infrastruktury, aby se dosáhlo služeb Azure a nahlásily stav dodržování předpisů.

### <a name="communicate-over-virtual-networks-in-azure"></a>Komunikace přes virtuální sítě v Azure

Virtuální počítače, které používají virtuální sítě pro komunikaci, budou vyžadovat odchozí přístup k datacentrům Azure na portu `443` . Pokud používáte privátní virtuální síť v Azure, která neumožňuje odchozí přenosy, nakonfigurujte výjimky s pravidly skupiny zabezpečení sítě. Označení služby "GuestAndHybridManagement" lze použít k odkazování na službu konfigurace hosta.

### <a name="communicate-over-private-link-in-azure"></a>Komunikace prostřednictvím privátního propojení v Azure

Virtuální počítače můžou používat [privátní propojení](../../../private-link/private-link-overview.md) ke komunikaci se službou konfigurace hosta. Pokud chcete povolit tuto funkci, použijte značku s názvem `EnablePrivateNeworkGC` (bez "t" v síti) a hodnotou `TRUE` . Značku lze použít před nebo po použití definic zásad konfigurace hosta pro daný počítač.

Provoz se směruje pomocí [virtuální veřejné IP adresy](../../../virtual-network/what-is-ip-address-168-63-129-16.md) Azure a vytvoří zabezpečený a ověřený kanál s prostředky platformy Azure.

### <a name="azure-arc-connected-machines"></a>Počítače připojené k Azure ARC

Uzly umístěné mimo Azure, které jsou připojené přes Azure ARC, vyžadují připojení ke službě konfigurace hosta. Podrobnosti o požadavcích sítě a proxy serveru, které jsou k dispozici v [dokumentaci k Azure ARC](../../../azure-arc/servers/overview.md).

Aby počítače komunikovaly s poskytovatelem prostředků konfigurace hosta v Azure, vyžadují odchozí přístup k datacentrům Azure na portu **443**. Pokud síť v Azure nepovoluje odchozí přenosy, nakonfigurujte výjimky s pravidly [skupiny zabezpečení sítě](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . [Označení služby](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" lze použít k odkazování na službu konfigurace hosta.

Pro servery připojené k ARC v privátních datových centrech povolte provoz pomocí následujících vzorů:

- Port: pro odchozí přístup k Internetu se vyžaduje jenom TCP 443.
- Globální adresa URL: `*.guestconfiguration.azure.com`

## <a name="managed-identity-requirements"></a>Požadavky na spravovanou identitu

Definice zásad v iniciativě _nasazení požadavků pro povolení zásad konfigurace hostů na virtuálních počítačích_ povolují spravovanou identitu přiřazenou systémem, pokud taková neexistuje. V iniciativě existují dvě definice zásad, které spravují vytváření identit. Podmínky IF v definicích zásad zajišťují správné chování na základě aktuálního stavu prostředku počítače v Azure.

Pokud počítač v tuto chvíli nemá žádné spravované identity, budou platné zásady: [Přidat spravovanou identitu přiřazenou systémem a povolit přiřazení konfigurace hostů na virtuálních počítačích bez identit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) .

Pokud má počítač v současné době uživatelsky přiřazené uživatelské identity, platí tyto zásady: [Přidání spravované identity přiřazené systémem pro povolení přiřazení konfigurace hostů na virtuálních počítačích s identitou přiřazenou uživatelem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) .

## <a name="guest-configuration-definition-requirements"></a>Požadavky na definici konfigurace hosta

Definice zásad konfigurace hostů používají efekt **AuditIfNotExists** . Po přiřazení definice služba back-end automaticky zpracuje životní cyklus všech požadavků v `Microsoft.GuestConfiguration` poskytovateli prostředků Azure.

Definice zásad **AuditIfNotExists** nevrátí výsledky dodržování předpisů, dokud nebudou všechny požadavky splněny v počítači. Požadavky jsou popsané v části [požadavky na nasazení pro virtuální počítače Azure](#deploy-requirements-for-azure-virtual-machines)

> [!IMPORTANT]
> V předchozí verzi konfigurace hosta se vyžadovala iniciativa ke kombinování definicí **DeployIfNoteExists** a **AuditIfNotExists** . Definice **DeployIfNotExists** se už nevyžadují. Definice a iniciativy jsou označeny, `[Deprecated]` ale existující přiřazení budou fungovat i nadále. Informace najdete v blogovém příspěvku: [důležitá změna vydaná pro zásady auditu konfigurace hostů](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316) .

### <a name="what-is-a-guest-assignment"></a>Co je přiřazení hostů?

Pokud je přiřazena Azure Policy, pokud je v kategorii "konfigurace hosta", existují metadata, která jsou obsažena v popisu přiřazení hostů.
Přiřazení hostů si můžete představit jako propojení mezi počítačem a scénářem Azure Policy.
Následující fragment kódu například přidruží konfiguraci standardních hodnot Windows Azure s minimální verzí `1.0.0` na všechny počítače v oboru zásad. Ve výchozím nastavení bude přiřazení hostů provádět jenom audit počítače.

```json
"metadata": {
    "category": "Guest Configuration",
    "guestConfiguration": {
        "name": "AzureWindowsBaseline",
        "version": "1.*"
    }
//additional metadata properties exist
```

Přiřazení hostů se vytváří automaticky pro jednotlivé počítače pomocí služby konfigurace hosta. Typ prostředku je `Microsoft.GuestConfiguration/guestConfigurationAssignments`.
Azure Policy používá vlastnost **complianceStatus** prostředku přiřazení hosta k hlášení stavu dodržování předpisů. Další informace najdete v tématu [získání dat o dodržování předpisů](../how-to/get-compliance-data.md).

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditování nastavení operačního systému po oborových plánech

Jedna z iniciativ v Azure Policy Audituje nastavení operačního systému podle směrného plánu. Definice, _\[ verze Preview \] : počítače s Windows by měly splňovat požadavky na základní hodnoty zabezpečení Azure,_ zahrnuje sadu pravidel založených na službě Active Directory Zásady skupiny.

Většina nastavení je k dispozici jako parametry. Parametry umožňují přizpůsobit, co je auditováno.
Zarovnejte zásady s vašimi požadavky nebo namapujte zásady na informace třetích stran, jako jsou například oborový zákon o standardech.

Některé parametry podporují rozsah celočíselných hodnot. Například nastavení maximálního stáří hesla může auditovat platné nastavení Zásady skupiny. Rozsah "1; 70" by potvrdil, že uživatelé musí měnit hesla alespoň každých 70 dní, ale ne méně než jeden den.

Pokud zásadu přiřadíte pomocí šablony Azure Resource Manager (šablona ARM), použijte k řízení výjimek soubor parametrů. Soubory vraťte se změnami do systému správy verzí, jako je třeba Git. Komentáře k změnám souborů poskytují důkaz o tom, proč je přiřazení výjimkou očekávané hodnoty.

#### <a name="applying-configurations-using-guest-configuration"></a>Použití konfigurace pomocí konfigurace hosta

Pouze definice _, které časové pásmo v počítačích se systémem Windows_ , provádí změny v počítači konfigurací časového pásma. Definice vlastních zásad pro konfiguraci nastavení uvnitř počítačů nejsou podporované.

Při přiřazování definic, které začínají na _Konfigurovat_, musíte také přiřadit _předpoklady nasazení definice a povolit zásadu konfigurace hosta na virtuálních počítačích s Windows_. V případě, že se rozhodnete, můžete tyto definice kombinovat v iniciativě.

> [!NOTE]
> Integrovaná zásada časového pásma je jediná definice, která podporuje konfiguraci nastavení v počítačích a definice vlastních zásad, které konfigurují nastavení v počítačích nejsou podporované.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Přiřazování zásad do počítačů mimo Azure

Definice zásad auditu, které jsou k dispozici pro konfiguraci hosta, zahrnují typ prostředku **Microsoft. HybridCompute/počítače** . Všechny počítače připojené ke [službě Azure ARC pro servery](../../../azure-arc/servers/overview.md) , které jsou v oboru přiřazení zásad, jsou automaticky zahrnuté.

## <a name="troubleshooting-guest-configuration"></a>Řešení potíží s konfigurací hosta

Další informace o řešení potíží s konfigurací hostů najdete v tématu [řešení potíží s Azure Policy](../troubleshoot/general.md).

### <a name="multiple-assignments"></a>Více přiřazení

Definice zásad konfigurace hostů momentálně podporují přiřazování stejného přiřazení hostů jenom jednou pro každý počítač, a to i v případě, že přiřazení zásady používá jiné parametry.

### <a name="client-log-files"></a>Soubory protokolů klienta

Rozšíření konfigurace hosta zapisuje soubory protokolu do následujících umístění:

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux

- Virtuální počítač Azure: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`
- Virtuální počítač Azure: `/var/lib/GuestConfig/arc_policy_logs/gc_agent.log`

### <a name="collecting-logs-remotely"></a>Vzdálené shromažďování protokolů

Prvním krokem při řešení potíží s konfiguracemi konfigurace hosta nebo moduly by se měly používat `Test-GuestConfigurationPackage` rutiny podle kroků, jak [vytvořit vlastní zásady auditu konfigurace hostů pro Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Pokud to neproběhne úspěšně, může shromažďování protokolů klienta pomáhat s diagnostikou problémů.

#### <a name="windows"></a>Windows

Pomocí [příkazu spustit pro virtuální počítače Azure](../../../virtual-machines/windows/run-command.md)Zachyťte informace ze souborů protokolů, což může být užitečné v následujícím ukázkovém skriptu PowerShellu.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Pomocí [příkazu spustit pro virtuální počítače Azure](../../../virtual-machines/linux/run-command.md)Zachyťte informace ze souborů protokolů, což může být užitečné v následujícím ukázkovém skriptu bash.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

### <a name="client-files"></a>Soubory klienta

Klient konfigurace hosta stáhne balíčky obsahu do počítače a extrahuje obsah.
Chcete-li ověřit, jaký obsah byl stažen a uložen, zobrazte níže uvedená umístění složek.

Windows: `c:\programdata\guestconfig\configuration`

Linux: `/var/lib/GuestConfig/Configuration`

## <a name="guest-configuration-samples"></a>Ukázky konfigurace hosta

Ukázky integrovaných zásad konfigurace hosta jsou k dispozici v následujících umístěních:

- [Předdefinované definice zásad – konfigurace hostů](../samples/built-in-policies.md#guest-configuration)
- [Integrované iniciativy – konfigurace hostů](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy Samples – úložiště GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

### <a name="video-overview"></a>Přehled videí

Následující přehled Azure Policy konfigurace hostů je ze ITOps rozhovory 2021.

[Řízení standardních hodnot v prostředích hybridních serverů pomocí Azure Policy konfigurace hostů](https://techcommunity.microsoft.com/t5/itops-talk-blog/ops114-governing-baselines-in-hybrid-server-environments-using/ba-p/2109245)

## <a name="next-steps"></a>Další kroky

- Podívejte se, jak zobrazit podrobnosti jednotlivých nastavení ze [zobrazení dodržování předpisů konfigurace hostů.](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](./definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](./effects.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
