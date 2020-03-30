---
title: Konfigurace předávání DNS pro soubory Azure | Dokumenty společnosti Microsoft
description: Přehled možností sítě pro soubory Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 35dfbcb274721049f2160719222ca89038c93356
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082497"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Konfigurace předávání DNS pro soubory Azure
Azure Files umožňuje vytvářet soukromé koncové body pro účty úložiště obsahující sdílené složky. I když jsou užitečné pro mnoho různých aplikací, privátní koncové body jsou užitečné zejména pro připojení ke sdíleným položkám souborů Azure z místní sítě pomocí připojení VPN nebo ExpressRoute pomocí soukromého partnerského vztahu. 

Aby připojení k vašemu účtu úložiště mohla přejít přes tunelový propojení v síti, musí se plně kvalifikovaný název domény (FQDN) vašeho účtu úložiště přeložit na privátní IP adresu vašeho soukromého koncového bodu. K dosažení tohoto cíle je nutné předat příponu koncového bodu úložiště (pro`core.windows.net` oblasti veřejného cloudu) do privátní služby AZURE DNS přístupné z vaší virtuální sítě. Tato příručka vám ukáže, jak nastavit a nakonfigurovat předávání DNS tak, aby správně vyřešilo privátní IP adresu koncového bodu vašeho účtu úložiště.

Důrazně doporučujeme, abyste si před dokončením kroků popsaných v tomto článku přečetli [plánování nasazení souborů Azure](storage-files-planning.md) a síťové [aspekty azure files.](storage-files-networking-overview.md)

## <a name="overview"></a>Přehled
Azure Files poskytuje dva hlavní typy koncových bodů pro přístup ke sdíleným složkám Azure: 
- Veřejné koncové body, které mají veřejnou IP adresu a lze k nim přistupovat z libovolného místa na světě.
- Privátní koncové body, které existují v rámci virtuální sítě a mají privátní IP adresu z v adresním prostoru této virtuální sítě.

Veřejné a soukromé koncové body existují na účtu úložiště Azure. Účet úložiště je konstrukce správy, která představuje sdílený fond úložiště, ve kterém můžete nasadit více sdílených složek, stejně jako další prostředky úložiště, jako jsou kontejnery objektů blob nebo fronty.

Každý účet úložiště má plně kvalifikovaný název domény (FQDN). Pro oblasti veřejného cloudu tento hlavní název `storageaccount.file.core.windows.net` `storageaccount` názvu se řídí vzorem, kde je název účtu úložiště. Pokud provádíte požadavky proti tomuto názvu, jako je například připojení sdílené složky na pracovní stanici pomocí protokolu SMB, provede operační systém vyhledávání DNS, aby se překlad plně kvalifikovaného názvu domény přejmenoval na adresu IP, na kterou může odeslat požadavky SMB.

Ve výchozím `storageaccount.file.core.windows.net` nastavení se překládá na ip adresu veřejného koncového bodu. Veřejný koncový bod pro účet úložiště je hostovaný v clusteru úložiště Azure, který hostuje mnoho veřejných koncových bodů jiných účtů úložiště. Když vytvoříte soukromý koncový bod, soukromá zóna DNS je propojena s virtuální `storageaccount.file.core.windows.net` sítí, do které byla přidána, s mapováním záznamu CNAME na položku záznamu A pro privátní IP adresu privátního koncového bodu vašeho účtu úložiště. To umožňuje používat `storageaccount.file.core.windows.net` hlavní název sítě v rámci virtuální sítě a nechat ji vyřešit na ip adresu privátního koncového bodu.

Vzhledem k tomu, že naším konečným cílem je přístup ke sdíleným složekm Azure hostovaným v rámci účtu úložiště z místního prostředí pomocí síťového tunelového propojení, jako je připojení VPN nebo ExpressRoute, musíte nakonfigurovat místní servery DNS tak, aby předávaly požadavky na Azure. Soubory služby pro privátní službu DNS Azure. K dosažení tohoto cíle je třeba nastavit `*.core.windows.net` *podmíněné předávání* (nebo příslušné koncové hodovací stanice úložiště pro národní cloudy vlády USA, Německa nebo Číny) na server DNS hostovaný v rámci virtuální sítě Azure. Tento server DNS pak bude rekurzivně předávat požadavek na privátní službu DNS Azure, která vyřeší plně kvalifikovaný název domény účtu úložiště na příslušnou privátní IP adresu.

Konfigurace předávání DNS pro soubory Azure bude vyžadovat spuštění virtuálního počítače pro hostování serveru DNS pro předávání požadavků, ale toto je jednorázový krok pro všechny sdílené složky Azure hostované ve vaší virtuální síti. Navíc to není výhradní požadavek na soubory Azure – jakákoli služba Azure, která podporuje privátní koncové body, které chcete získat přístup z místního prostředí můžete využít předávání DNS nakonfigurovali v této příručce: Azure Blob storage, SQL Azure, Cosmos DB, Atd. 

Tato příručka ukazuje postup konfigurace předávání DNS pro koncový bod úložiště Azure, takže kromě souborů Azure budou kromě souborů Azure požadavky na překlad názvů DNS pro všechny ostatní služby úložiště Azure (úložiště Azure Blob, úložiště Azure Table, úložiště fronty Azure atd.) přeposílány soukromé službě DNS Azure. Další koncové body pro jiné služby Azure lze také přidat v případě potřeby. Bude také nakonfigurováno přeposílání DNS zpět na místní servery DNS, což umožní cloudovým prostředkům v rámci vaší virtuální sítě (například serveru DFS-N) vyřešit názvy místních počítačů. 

## <a name="prerequisites"></a>Požadavky
Před nastavením předávání DNS do souborů Azure je nutné provést následující kroky:

- Účet úložiště obsahující sdílenou složku Azure, kterou chcete připojit. Informace o tom, jak vytvořit účet úložiště a sdílenou složku Azure, najdete [v tématu Vytvoření sdílené složky Azure](storage-how-to-create-file-share.md).
- Soukromý koncový bod pro účet úložiště. Informace o tom, jak vytvořit privátní koncový bod pro soubory Azure, najdete v [tématu Vytvoření privátního koncového bodu](storage-files-networking-endpoints.md#create-a-private-endpoint).
- [Nejnovější verze](https://docs.microsoft.com/powershell/azure/install-az-ps) modulu Azure PowerShell.

> [!Important]  
> Tato příručka předpokládá, že používáte server DNS v systému Windows Server v místním prostředí. Všechny kroky popsané v této příručce jsou možné s libovolným serverem DNS, nikoli pouze se serverem Windows DNS Server.

## <a name="manually-configuring-dns-forwarding"></a>Ruční konfigurace předávání DNS
Pokud už máte v rámci virtuální sítě Azure servery DNS nebo pokud jednoduše dáváte přednost nasazení vlastních virtuálních počítačů jako serverů DNS podle jakékoli metodiky, kterou vaše organizace používá, můžete dns nakonfigurovat ručně pomocí integrovaného serveru DNS. Rutiny prostředí PowerShell.

Na místních serverech DNS vytvořte podmíněný `Add-DnsServerConditionalForwarderZone`server pro předávání pomocí aplikace . Tento podmíněný server pro předávání musí být nasazen na všech místních serverech DNS, aby byl efektivní při správném předávání provozu do Azure. Nezapomeňte nahradit `<azure-dns-server-ip>` příslušné IP adresy pro vaše prostředí.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Na serverech DNS v rámci virtuální sítě Azure budete také muset umístit server pro předávání tak, aby požadavky na zónu DNS účtu úložiště `168.63.129.16`byly směrovány na privátní službu AZURE DNS, která je předřízena vyhrazenou IP adresou . (Nezapomeňte naplnit, `$storageAccountEndpoint` pokud používáte příkazy v rámci jiné relace prostředí PowerShell.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Použití hybridního modulu Azure Files ke konfiguraci předávání DNS
Abychom co nejvíce usnadnili konfiguraci předávání DNS, zajistili jsme automatizaci v modulu Azure Files Hybrid. Rutiny poskytované pro manipulaci se službou DNS v tomto modulu vám pomohou nasadit servery DNS ve vaší virtuální síti Azure a aktualizovat místní servery DNS tak, aby jim je předaly. 

Pokud jste nikdy nepoužívali hybridní modul Azure Files, musíte ho nejdřív nainstalovat na pracovní stanici. Stáhněte si [nejnovější verzi](https://github.com/Azure-Samples/azure-files-samples/releases) modulu Hybridní PowerShell Azure Files:

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

Nasazení řešení předávání DNS má dva kroky, vytvoření sady pravidel předávání DNS, která definuje, které služby Azure, kterým chcete předávat požadavky, a skutečné nasazení serverů pro předávání DNS. 

Následující příklad předává požadavky na účet úložiště, včetně požadavků na soubory Azure, úložiště objektů blob Azure, úložiště Azure Table a úložiště Fronty Azure. V případě potřeby můžete přidat předávání pro další službu Azure do pravidla prostřednictvím parametru `-AzureEndpoints` rutiny. `New-AzDnsForwardingRuleSet` Nezapomeňte nahradit `<virtual-network-resource-group>` `<virtual-network-name>`, `<subnet-name>` a příslušné hodnoty pro vaše prostředí.

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

Dále můžete považovat za užitečné / nezbytné zadat několik dalších parametrů:

| Název parametru | Typ | Popis |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Ve výchozím nastavení budou servery DNS nasazeny do stejné skupiny prostředků jako virtuální síť. Pokud to není žádoucí, tento parametr umožňuje vybrat alternativní skupinu prostředků pro jejich nasazení do. |
| `DnsForwarderRootName` | `string` | Ve výchozím nastavení mají servery DNS, které `DnsFwder-*`jsou nasazené v Azure, názvy , kde je hvězdička naplněna iterátorem. Tento parametr změní kořen tohoto názvu (tj. ). `DnsFwder` |
| `VmTemporaryPassword` | `SecureString` | Ve výchozím nastavení je pro dočasný výchozí účet, který má virtuální server před připojenou doménou, vybráno náhodné heslo. Po vstupu do domény je výchozí účet zakázán. |
| `DomainToJoin` | `string` | Doména, která se má připojit k virtuálním virtuálním mům DNS, aby se k nim připojila. Ve výchozím nastavení je tato doména vybrána na základě domény počítače, ve kterém jsou spuštěny rutiny. |
| `DnsForwarderRedundancyCount` | `int` | Počet virtuálních počítačích DNS, které se mají nasadit pro vaši virtuální síť. Ve výchozím `New-AzDnsForwarder` nastavení nasazuje dva servery DNS ve vaší virtuální síti Azure v sadě dostupnosti, aby byla zajištěna redundance. Toto číslo může být upraveno podle potřeby. |
| `OnPremDnsHostNames` | `HashSet<string>` | Ručně zadaný seznam místních názvů hostitelů DNS, na kterých se mají vytvářet servery pro předávání. Tento parametr je užitečný, pokud nechcete použít servery pro předávání na všech místních serverech DNS, například když máte řadu klientů s ručně zadanými názvy DNS. |
| `Credential` | `PSCredential` | Pověření, které se má použít při aktualizaci serverů DNS. To je užitečné v případě, že uživatelský účet, pomocí kterých jste se přihlásili, nemá oprávnění ke změně nastavení DNS. |
| `SkipParentDomain` | `SwitchParameter` | Ve výchozím nastavení jsou servery pro předávání DNS použity na doménu nejvyšší úrovně, která existuje ve vašem prostředí. Pokud `northamerica.corp.contoso.com` je například podřízená doména služby `corp.contoso.com`, bude pro `corp.contoso.com`servery DNS přidružené k aplikaci vytvořeno server pro předávání . Tento parametr způsobí, že předávání, které mají být vytvořeny v aplikaci `northamerica.corp.contoso.com`. |

## <a name="confirm-dns-forwarders"></a>Potvrzení serverů pro předávání DNS
Před testováním, zda byly úspěšně použity servery pro předávání DNS, doporučujeme `Clear-DnsClientCache`vymazat mezipaměť DNS na místní pracovní stanici pomocí aplikace . Chcete-li otestovat, zda můžete úspěšně přeložit plně kvalifikovaný název `Resolve-DnsName` `nslookup`domény účtu úložiště, použijte nebo .

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Pokud je překlad názvů úspěšný, měla by se vyřešená adresa IP shodovat s IP adresou vašeho účtu úložiště.

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

Pokud jste již nastavili připojení VPN nebo ExpressRoute, můžete také použít `Test-NetConnection` k uvidíte, že připojení TCP lze úspěšně provést k účtu úložiště.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Viz také
- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
- [Důležité informace o vytváření sítí Azure Files](storage-files-networking-overview.md)
- [Konfigurace síťových koncových bodů souborů Azure](storage-files-networking-endpoints.md)