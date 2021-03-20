---
title: Konfigurace předávání DNS pro soubory Azure | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat předávání DNS pro soubory Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9abe306668a4b20e42e45c498bf85b540dfaaee5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94630188"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Konfigurace přesměrování DNS pro Azure Files
Služba soubory Azure umožňuje vytvářet soukromé koncové body pro účty úložiště obsahující sdílené složky. I když je to užitečné pro mnoho různých aplikací, soukromé koncové body jsou obzvláště užitečné pro připojení ke sdíleným složkám Azure ze své místní sítě pomocí připojení VPN nebo ExpressRoute pomocí privátního partnerského vztahu. 

Aby připojení k vašemu účtu úložiště mohla přecházet přes vaše síťové tunelové propojení, plně kvalifikovaný název domény (FQDN) účtu úložiště se musí přeložit na privátní IP adresu privátního koncového bodu. Abyste to dosáhli, musíte přeslat příponu koncového bodu úložiště ( `core.windows.net` pro oblasti veřejného cloudu) do privátní služby DNS Azure přístupné z vaší virtuální sítě. V této příručce se dozvíte, jak nastavit a nakonfigurovat předávání DNS pro správné přeložení na IP adresu privátního koncového bodu účtu úložiště.

Důrazně doporučujeme, abyste si před dokončením kroků popsaných v tomto článku načetli téma [plánování pro nasazení souborů Azure](storage-files-planning.md) a [Azure Files](storage-files-networking-overview.md) .

## <a name="overview"></a>Přehled
Soubory Azure poskytují dva hlavní typy koncových bodů pro přístup ke sdíleným složkám Azure: 
- Veřejné koncové body, které mají veřejnou IP adresu a jsou přístupné odkudkoli na světě.
- Privátní koncové body, které existují v rámci virtuální sítě a které mají privátní IP adresu v adresním prostoru virtuální sítě.

V účtu služby Azure Storage existují veřejné a privátní koncové body. Účet úložiště je konstrukce správy, která představuje sdílený fond úložiště, ve kterém můžete nasadit více sdílených složek a další prostředky úložiště, jako jsou kontejnery nebo fronty objektů BLOB.

Každý účet úložiště má plně kvalifikovaný název domény (FQDN). Pro oblasti veřejného cloudu tento plně kvalifikovaný název domény řídí vzor, `storageaccount.file.core.windows.net` kde `storageaccount` je název účtu úložiště. Když provedete žádosti s tímto názvem, například připojením sdílené složky na pracovní stanici pomocí protokolu SMB, váš operační systém provede vyhledávání DNS a přeloží plně kvalifikovaný název domény na IP adresu, kterou může použít k odeslání požadavků SMB do.

Ve výchozím nastavení se `storageaccount.file.core.windows.net` přeloží na IP adresu veřejného koncového bodu. Veřejný koncový bod pro účet úložiště je hostovaný v clusteru Azure Storage, který hostuje mnoho dalších veřejných koncových bodů účtů úložiště. Při vytváření privátního koncového bodu je privátní zóna DNS propojená s virtuální sítí, do které byla přidána, pomocí mapování záznamů CNAME `storageaccount.file.core.windows.net` na záznam a pro privátní IP adresu privátního koncového bodu vašeho účtu úložiště. To vám umožňuje používat `storageaccount.file.core.windows.net` plně kvalifikovaný název domény v rámci virtuální sítě a překládat ji na IP adresu privátního koncového bodu.

Vzhledem k tomu, že naším cílem je přistupovat ke sdíleným složkám Azure hostovaným v účtu úložiště z místního počítače pomocí tunelového propojení sítě, jako je připojení VPN nebo ExpressRoute, musíte nakonfigurovat místní servery DNS tak, aby přenesly požadavky na službu soubory Azure do privátní služby DNS Azure. K tomu je potřeba nastavit *podmíněné předávání* `*.core.windows.net` (nebo odpovídající příponu koncového bodu úložiště pro státní správu USA, Německo nebo Čínu) na server DNS hostovaný v rámci vaší virtuální sítě Azure. Tento server DNS pak rekurzivně přepošle požadavek na privátní službu DNS Azure, která bude přeložit plně kvalifikovaný název domény účtu úložiště na příslušnou privátní IP adresu.

Konfigurace předávání DNS pro soubory Azure vyžaduje, aby virtuální počítač mohl hostovat server DNS, aby předával požadavky, ale toto je jednorázový krok pro všechny sdílené složky Azure hostované ve vaší virtuální síti. Kromě toho se nejedná o výhradní požadavek na soubory Azure – jakákoli služba Azure, která podporuje privátní koncové body, ke kterým chcete získat přístup z místního prostředí, může používat předávání DNS, které budete konfigurovat v této příručce: Azure Blob Storage, SQL Azure, Cosmos DB atd. 

V této příručce se dozvíte, jak nakonfigurovat předávání DNS pro koncový bod služby Azure Storage, takže kromě souborů Azure se požadavky na překlad názvů DNS pro všechny ostatní služby Azure Storage (Azure Blob Storage, Azure Table Storage, Azure Queue Storage atd.) předají do privátní služby DNS Azure. Další koncové body pro ostatní služby Azure je možné přidat i v případě potřeby. Přesměrování DNS zpátky na místní servery DNS se taky nakonfigurují a povolí cloudové prostředky ve vaší virtuální síti (třeba serveru DFS-N) k překladu místních názvů počítačů. 

## <a name="prerequisites"></a>Předpoklady
Než budete moct nastavit předávání DNS do služby soubory Azure, musíte provést následující kroky:

- Účet úložiště, který obsahuje sdílenou složku Azure, se chcete připojit. Informace o tom, jak vytvořit účet úložiště a sdílenou složku Azure, najdete v tématu [Vytvoření sdílené složky Azure](storage-how-to-create-file-share.md).
- Privátní koncový bod pro účet úložiště. Informace o tom, jak vytvořit privátní koncový bod pro soubory Azure, najdete v tématu [Vytvoření privátního koncového bodu](storage-files-networking-endpoints.md#create-a-private-endpoint).
- [Nejnovější verze](/powershell/azure/install-az-ps) modulu Azure PowerShell.

> [!Important]  
> V tomto průvodci se předpokládá, že používáte server DNS v systému Windows Server v místním prostředí. Všechny kroky popsané v této příručce jsou možné u libovolného serveru DNS, nikoli jenom ze serveru DNS se systémem Windows.

## <a name="manually-configuring-dns-forwarding"></a>Ruční konfigurace předávání DNS
Pokud už máte servery DNS v rámci služby Azure Virtual Network, nebo pokud jednoduše dáváte přednost nasazení vlastních virtuálních počítačů na servery DNS podle jakékoli metodologie, kterou vaše organizace používá, můžete DNS nakonfigurovat ručně pomocí integrovaných rutin PowerShell serveru DNS.

Na místních serverech DNS vytvořte podmíněný předávací server pomocí `Add-DnsServerConditionalForwarderZone` . Aby bylo možné zajistit správné předávání provozu do Azure, musí být tento podmíněný předávání nasazený na všech místních serverech DNS. Nezapomeňte nahradit `<azure-dns-server-ip>` odpovídajícími IP adresami pro vaše prostředí.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Na serverech DNS v rámci služby Azure Virtual Network budete muset místo toho umístit službu pro přesměrování tak, aby požadavky na zónu DNS účtu úložiště byly směrovány na privátní službu DNS Azure, která je frontou rezervované IP adresy `168.63.129.16` . (Nezapomeňte naplnit, `$storageAccountEndpoint` Pokud spouštíte příkazy v rámci jiné relace PowerShellu.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Konfigurace předávání DNS pomocí hybridního modulu souborů Azure
Aby bylo možné co nejrychleji nakonfigurovat předávání DNS, poskytujeme automatizaci v modulu Azure Files Hybrid. Rutiny poskytované pro manipulaci s DNS v tomto modulu vám pomůžou nasadit servery DNS ve službě Azure Virtual Network a aktualizovat místní servery DNS tak, aby se do nich předaly. 

Pokud jste nikdy nepoužívali hybridní modul služby soubory Azure, musíte ho nejdřív nainstalovat na pracovní stanici. Stáhněte si [nejnovější verzi](https://github.com/Azure-Samples/azure-files-samples/releases) modulu Azure Files Hybrid PowerShell:

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

Nasazení řešení předávání DNS má dva kroky, vytvoření sady pravidel předávání DNS, která definuje služby Azure, na které chcete předávat požadavky, a skutečné nasazení služeb předávání DNS. 

V následujícím příkladu jsou předávány požadavky na účet úložiště, včetně požadavků na soubory Azure, úložiště objektů BLOB v Azure, úložiště tabulek Azure a Azure Queue Storage. V případě potřeby můžete přidat přesměrování pro další službu Azure na pravidlo prostřednictvím `-AzureEndpoints` parametru `New-AzDnsForwardingRuleSet` rutiny. Nezapomeňte nahradit `<virtual-network-resource-group>` , `<virtual-network-name>` a `<subnet-name>` s odpovídajícími hodnotami pro vaše prostředí.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Může vám také být užitečné, abyste mohli dodat několik dalších parametrů:

| Název parametru | Typ | Description |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Ve výchozím nastavení se servery DNS nasadí do stejné skupiny prostředků jako virtuální síť. Pokud to není žádoucí, tento parametr vám umožní vybrat alternativní skupinu prostředků, do které se mají nasadit. |
| `DnsForwarderRootName` | `string` | Ve výchozím nastavení mají servery DNS, které jsou nasazené v Azure `DnsFwder-*` , názvy, kde je hvězdička naplněna iterátorem. Tento parametr změní kořen daného názvu (tj. `DnsFwder` ). |
| `VmTemporaryPassword` | `SecureString` | Ve výchozím nastavení se pro dočasný výchozí účet vybere náhodné heslo, které má virtuální počítač předtím, než se připojí k doméně. Po připojení k doméně je výchozí účet zakázaný. |
| `DomainToJoin` | `string` | Doména, ke které se připojí virtuální počítače DNS pro připojení. Ve výchozím nastavení se tato doména vybere v závislosti na doméně počítače, na kterém spouštíte rutiny. |
| `DnsForwarderRedundancyCount` | `int` | Počet virtuálních počítačů DNS pro nasazení pro virtuální síť. Ve výchozím nastavení `New-AzDnsForwarder` nasadí dva servery DNS ve virtuální síti Azure ve skupině dostupnosti, aby se zajistila redundance. Toto číslo může být podle potřeby upraveno. |
| `OnPremDnsHostNames` | `HashSet<string>` | Ručně zadaný seznam místních názvů hostitelů DNS, na kterých se mají vytvořit servery pro přeposílání. Tento parametr je užitečný, když nechcete používat servery pro směrování na všech místních serverech DNS, například když máte celou řadu klientů s ručně zadanými názvy DNS. |
| `Credential` | `PSCredential` | Přihlašovací údaje, které se mají použít při aktualizaci serverů DNS. To je užitečné v případě, že uživatelský účet, ke kterému jste se přihlásili, nemá oprávnění upravovat nastavení DNS. |
| `SkipParentDomain` | `SwitchParameter` | Služby DNS pro přeposílání jsou ve výchozím nastavení aplikovány na doménu nejvyšší úrovně, která existuje ve vašem prostředí. Pokud `northamerica.corp.contoso.com` je například podřízenou doménou, bude `corp.contoso.com` pro servery DNS přidružené k serveru vytvořena služba pro vytváření `corp.contoso.com` . Tento parametr způsobí, že se pro přeposílání vytvoří v `northamerica.corp.contoso.com` . |

## <a name="confirm-dns-forwarders"></a>Potvrdit servery DNS pro přeposílání
Před testováním ověřte, jestli se služby DNS pro přeposílání úspěšně nastavily, doporučujeme vyprázdnit mezipaměť DNS na místní pracovní stanici pomocí `Clear-DnsClientCache` . Pokud chcete zjistit, jestli můžete úspěšně přeložit plně kvalifikovaný název domény svého účtu úložiště, použijte `Resolve-DnsName` nebo `nslookup` .

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Pokud je překlad adres úspěšný, měla by se zobrazit přeložená IP adresa odpovídat IP adrese vašeho účtu úložiště.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

Pokud jste už nastavili připojení VPN nebo ExpressRoute, můžete také použít `Test-NetConnection` k zobrazení, že se k vašemu účtu úložiště dá úspěšně vytvořit připojení TCP.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Viz také
- [Plánování nasazení Azure Files](storage-files-planning.md)
- [Požadavky na síť pro Azure Files](storage-files-networking-overview.md)
- [Konfigurace koncových bodů sítě služby soubory Azure](storage-files-networking-endpoints.md)