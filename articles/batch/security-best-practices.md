---
title: Doporučené postupy zabezpečení a dodržování předpisů v dávce
description: Naučte se osvědčené postupy a užitečné tipy pro zvýšení zabezpečení pomocí Azure Batchch řešení.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: b9732ffb810a1038a6f402a46fa8b809f180f0a8
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802282"
---
# <a name="batch-security-and-compliance-best-practices"></a>Doporučené postupy zabezpečení a dodržování předpisů v dávce

Tento článek poskytuje pokyny a osvědčené postupy pro zvýšení zabezpečení při použití Azure Batch.

Ve výchozím nastavení mají účty Azure Batch veřejný koncový bod a jsou veřejně přístupné. Když se vytvoří fond Azure Batch, fond se zřídí v zadané podsíti služby Azure Virtual Network. Virtuální počítače ve fondu Batch jsou přístupné prostřednictvím veřejných IP adres, které jsou vytvořeny službou Batch. Výpočetní uzly ve fondu můžou vzájemně komunikovat v případě potřeby, například pro spouštění úloh s více instancemi, ale uzly ve fondu nemůžou komunikovat s virtuálními počítači mimo fond.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="Diagram znázorňující typické prostředí Batch":::

K dispozici je mnoho funkcí, které vám pomůžou vytvořit bezpečnější Azure Batch nasazení. Přístup k uzlům můžete omezit a omezit zjistitelnost uzlů z internetu tím, že fond zařadíte [bez veřejných IP adres](batch-pool-no-public-ip-address.md). Výpočetní uzly můžou zabezpečeně komunikovat s ostatními virtuálními počítači nebo s místní sítí tím, že [zřídí fond v podsíti virtuální sítě Azure](batch-virtual-network.md). Můžete ale povolit [privátní přístup z virtuálních sítí](private-connectivity.md) ze služby využívající privátní propojení Azure.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="Diagram znázorňující bezpečnější prostředí Batch.":::

## <a name="general-security-related-best-practices"></a>Obecné osvědčené postupy související se zabezpečením

### <a name="pool-configuration"></a>Konfigurace fondu

Mnoho funkcí zabezpečení je dostupných jenom pro fondy nakonfigurované pomocí [Konfigurace virtuálních počítačů](nodes-and-pools.md#configurations), a ne pro fondy s konfigurací Cloud Services. Doporučujeme použít fondy konfigurace virtuálních počítačů, které využívají [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md), kdykoli to bude možné.

### <a name="batch-account-authentication"></a>Ověřování účtu Batch

Přístup k účtu Batch podporuje dvě metody ověřování: Shared Key and [Azure Active Directory (Azure AD)](batch-aad-auth.md).

Důrazně doporučujeme používat Azure AD pro ověřování účtu Batch. Některé možnosti dávky vyžadují tuto metodu ověřování, včetně řady funkcí souvisejících se zabezpečením, které jsou zde popsané.

### <a name="batch-account-pool-allocation-mode"></a>Režim přidělování fondu účtů Batch

Při vytváření účtu Batch si můžete vybrat mezi dvěma [režimy přidělování fondů](accounts.md#batch-accounts):

- **Služba Batch**: výchozí možnost, kde základní cloudová služba nebo prostředky sady škálování virtuálního počítače použité k přidělení a správě uzlů fondu jsou vytvořeny ve vnitřních předplatných a nejsou přímo viditelné v Azure Portal. Zobrazit se budou jenom fondy a uzly Batch. 
- **Předplatné uživatele**: základní cloudová služba nebo prostředky sady škálování virtuálního počítače se vytvoří ve stejném předplatném jako účet Batch. Tyto prostředky jsou proto v předplatném viditelné, kromě odpovídajících prostředků Batch.

V režimu předplatného uživatele se virtuální počítače a další prostředky služby Batch vytvářejí přímo ve vašem předplatném při vytvoření fondu. Režim předplatného uživatele je nutný, pokud chcete vytvořit fondy Batch pomocí Azure Reserved VM Instances, používat Azure Policy prostředků sady škálování virtuálních počítačů a/nebo spravovat základní kvótu předplatného (sdílené ve všech účtech Batch v rámci předplatného). Pokud chcete vytvořit účet Batch v režimu předplatného uživatele, musíte také zaregistrovat předplatné ve službě Azure Batch a k účtu přidružit službu Azure Key Vault.

## <a name="restrict-network-endpoint-access"></a>Omezení přístupu k síťovému koncovému bodu

### <a name="batch-network-endpoints"></a>Koncové body sítě Batch

Všimněte si, že ve výchozím nastavení jsou koncové body s veřejnými IP adresami používány ke komunikaci s účty Batch, fondy dávek a uzly fondu.

### <a name="batch-account-api"></a>Rozhraní API účtu Batch

 Při vytvoření účtu Batch se vytvoří veřejný koncový bod, který se používá k vyvolání většiny operací pro účet pomocí [REST API](/rest/api/batchservice/). Koncový bod účtu má základní adresu URL ve formátu `https://{account-name}.{region-id}.batch.azure.com` . Přístup k účtu Batch je zabezpečený, přičemž komunikace s koncovým bodem účtu se šifruje pomocí protokolu HTTPS a každý požadavek se ověřil pomocí sdíleného klíče nebo Azure Active Directory (Azure AD) ověřování.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Kromě operací specifických pro účet Batch se [operace správy](/rest/api/batchmanagement/) vztahují na jeden a několik účtů Batch. K těmto operacím správy se dostanete prostřednictvím Azure Resource Manager.

Operace správy služby Batch prostřednictvím Azure Resource Manager jsou šifrované pomocí protokolu HTTPS a každý požadavek se ověřuje pomocí ověřování Azure AD.

### <a name="batch-pool-nodes"></a>Uzly fondu Batch

Služba Batch komunikuje s agentem uzlu služby Batch, který běží na všech uzlech ve fondu. Například služba instruuje agenta uzlu, aby spustil úlohu, zastavila úlohu nebo získala soubory pro úlohu. Komunikace s agentem uzlu je povolena jedním nebo více nástroji pro vyrovnávání zatížení, jejichž počet závisí na počtu uzlů ve fondu. Nástroj pro vyrovnávání zatížení předá komunikaci s požadovaným uzlem s každým uzlem, který je adresován jedinečným číslem portu. Ve výchozím nastavení mají nástroje pro vyrovnávání zatížení přidružené veřejné IP adresy. Pomocí protokolu RDP nebo SSH můžete také vzdáleně přistupovat k uzlům fondu. Tento přístup je ve výchozím nastavení povolený a komunikace prostřednictvím nástrojů pro vyrovnávání zatížení.

### <a name="restricting-access-to-batch-endpoints"></a>Omezení přístupu k koncovým bodům dávky 

K dispozici je několik možností, které omezují přístup k různým koncovým bodům dávky, zejména v případě, že řešení používá virtuální síť. 

#### <a name="use-private-endpoints"></a>Použití privátních koncových bodů

[Privátní propojení Azure](../private-link/private-link-overview.md) umožňuje přístup ke službám Azure PaaS a zákaznickým/partnerským službám Azure hostovaným na zákazníky přes privátní koncový bod ve vaší virtuální síti. Soukromé propojení můžete použít k omezení přístupu k účtu Batch z virtuální sítě nebo z jakékoli partnerské virtuální sítě. Prostředky namapované na soukromé odkazy jsou k dispozici i místně přes privátní partnerský vztah prostřednictvím sítě VPN nebo Azure ExpressRoute.

Aby bylo možné použít privátní koncové body, musí být při vytvoření účet Batch správně nakonfigurován. Konfigurace přístupu k veřejné síti musí být zakázaná. Po vytvoření můžete privátní koncové body vytvořit a přidružit k účtu Batch. Další informace najdete v tématu [použití privátních koncových bodů s účty Azure Batch](private-connectivity.md).

#### <a name="create-pools-in-virtual-networks"></a>Vytváření fondů ve virtuálních sítích

Výpočetní uzly ve fondu Batch můžou vzájemně komunikovat, například spouštět úlohy s více instancemi, aniž by vyžadovaly virtuální síť (VNET). Ve výchozím nastavení ale uzly ve fondu nemůžou komunikovat s virtuálními počítači, které jsou mimo fond ve virtuální síti a mají privátní IP adresy, jako jsou například licenční servery nebo souborové servery.

Pokud chcete, aby výpočetní uzly komunikovaly zabezpečeně s ostatními virtuálními počítači nebo v místní síti, můžete fond nakonfigurovat tak, aby byl v podsíti virtuální sítě Azure.

Pokud mají fondy veřejné IP adresy, podsíť musí povolit příchozí komunikaci ze služby Batch, aby mohla plánovat úlohy a provádět jiné operace na výpočetních uzlech, a odchozí komunikaci ke komunikaci s Azure Storage nebo jinými prostředky podle potřeby vašich úloh. Pro fondy v konfiguraci virtuálního počítače Batch přidá skupiny zabezpečení sítě (skupin zabezpečení sítě) na úrovni síťového rozhraní připojené k výpočetním uzlům. Tato skupin zabezpečení sítě mají pravidla, která umožňují:

- Příchozí provoz TCP z IP adres služby Batch
- Příchozí provoz TCP pro vzdálený přístup
- Odchozí přenosy na jakémkoli portu virtuální sítě (dá se měnit podle pravidel NSG na úrovni podsítě)
- Odchozí přenosy na jakémkoli portu na internetu (může být změněno podle pravidel NSG na úrovni podsítě)

Nemusíte zadávat skupin zabezpečení sítě na úrovni podsítě virtuální sítě, protože Batch konfiguruje vlastní skupin zabezpečení sítě. Pokud máte NSG přidruženou k podsíti, ve které jsou nasazené výpočetní uzly Batch, nebo pokud chcete použít vlastní pravidla NSG k přepsání použitých výchozích hodnot, musíte tento NSG nakonfigurovat aspoň na pravidla zabezpečení příchozích a odchozích připojení, aby se mohla komunikace služby Batch s Azure Storage uzly fondu a komunikace mezi uzly fondu.

Další informace najdete v tématu [Vytvoření fondu Azure Batch ve virtuální síti](batch-virtual-network.md).

#### <a name="create-pools-with-static-public-ip-addresses"></a>Vytváření fondů se statickými veřejnými IP adresami

Ve výchozím nastavení jsou veřejné IP adresy přidružené ke fondům dynamické; jsou vytvořeny při vytvoření fondu a při změně velikosti fondu lze přidat nebo odebrat IP adresy. Když aplikace úlohy spuštěné v uzlech fondu potřebují přístup k externím službám, může být nutné omezit přístup k těmto službám na konkrétní IP adresy.  V takovém případě nebude možné spravovat dynamické IP adresy.

Statické prostředky veřejné IP adresy můžete vytvořit ve stejném předplatném jako účet Batch před vytvořením fondu. Tyto adresy pak můžete zadat při vytváření fondu.

Další informace najdete v tématu [Vytvoření fondu Azure Batch se zadanými veřejnými IP adresami](create-pool-public-ip.md).

#### <a name="create-pools-without-public-ip-addresses"></a>Vytváření fondů bez veřejných IP adres

Ve výchozím nastavení mají všechny výpočetní uzly v Azure Batch fond konfigurací virtuálních počítačů přiřazenou jednu nebo více veřejných IP adres. Služba Batch používá tyto koncové body k naplánování úloh a ke komunikaci s výpočetními uzly, včetně odchozího přístupu k Internetu.

Pokud chcete omezit přístup k těmto uzlům a snížit zjistitelnost těchto uzlů z Internetu, můžete fond zřídit bez veřejných IP adres.

Další informace najdete v tématu [Vytvoření fondu bez veřejných IP adres](batch-pool-no-public-ip-address.md).

#### <a name="limit-remote-access-to-pool-nodes"></a>Omezení vzdáleného přístupu na uzly fondu

Ve výchozím nastavení služba Batch umožňuje uživateli uzlu se síťovým připojením připojit externě k výpočetnímu uzlu ve fondu služby Batch pomocí protokolu RDP nebo SSH.

Chcete-li omezit vzdálený přístup k uzlům, použijte jednu z následujících metod:

- Nakonfigurujte [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) tak, aby odepřel přístup. K fondu bude přidružena příslušná skupina zabezpečení sítě (NSG).
- Vytvořte fond [bez veřejných IP adres](batch-pool-no-public-ip-address.md). Ve výchozím nastavení nejsou tyto fondy dostupné mimo virtuální síť.
- Přidružte NSG k virtuální síti, abyste odepřeli přístup k portům RDP nebo SSH.
- Nevytvářejte žádné uživatele na uzlu. Bez uživatelů uzlů nebude možné vzdálený přístup.

## <a name="encrypt-data"></a>Šifrování dat

### <a name="encrypt-data-in-transit"></a>Šifrovat data při přenosu

Veškerá komunikace s koncovým bodem účtu Batch (nebo přes Azure Resource Manager) musí používat protokol HTTPS. `https://`Při připojování ke službě Batch je nutné použít v adresách URL účtu Batch určených v rozhraních API.

Klienti, kteří komunikují se službou Batch, by se měli nakonfigurovat tak, aby používali TLS (Transport Layer Security) 1,2.

### <a name="encrypt-batch-data-at-rest"></a>Zašifrovat data dávky v klidovém umístění

Některé informace zadané v rozhraních API služby Batch, jako jsou certifikáty účtů, metadata úlohy a úlohy a příkazové řádky úkolu, se při ukládání službou Batch automaticky šifrují. Ve výchozím nastavení se tato data šifrují pomocí Azure Batch klíčů spravovaných platformou, které jsou jedinečné pro každý účet Batch.

Tato data můžete také šifrovat pomocí [klíčů spravovaných zákazníkem](batch-customer-managed-key.md). [Azure Key Vault](../key-vault/general/overview.md) se používá ke generování a uložení klíče s identifikátorem klíče registrovaným pro váš účet Batch.

### <a name="encrypt-compute-node-disks"></a>Šifrování disků výpočetních uzlů

Výpočetní uzly služby Batch mají ve výchozím nastavení dva disky: disk s operačním systémem a místní dočasnou jednotku SSD. [Soubory a adresáře](files-and-directories.md) spravované dávkou jsou umístěné na dočasné SSD, což je výchozí umístění pro soubory, jako jsou výstupní soubory úlohy. Aplikace úlohy Batch můžou používat výchozí umístění na disku SSD nebo na disku s operačním systémem.

Pro zajištění vyššího zabezpečení Zašifrujte tyto disky pomocí některé z těchto funkcí pro Azure Disk Encryption:

- [Spravované šifrování disků v klidovém umístění pomocí klíčů spravovaných platformou](../virtual-machines/windows/disk-encryption.md#platform-managed-keys)
- [Šifrování na hostiteli pomocí klíče spravovaného platformou](../virtual-machines/windows/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>Zabezpečený přístup ke službám z výpočetních uzlů

Uzly služby Batch můžou [bezpečně přistupovat k přihlašovacím údajům a tajným klíčům](credential-access-key-vault.md) uloženým v [Azure Key Vault](../key-vault/general/overview.md), které můžou používat aplikace pro úlohy pro přístup k jiným službám. Certifikát slouží k udělení přístupu uzlům fondu k Key Vault.

## <a name="governance-and-compliance"></a>Zásady správného řízení a dodržování předpisů

### <a name="compliance"></a>Dodržování předpisů

Pro pomoc zákazníkům, kteří splnili vlastní povinnosti dodržování předpisů napříč regulovanými průmyslovými odvětvími a trhy po celém světě, Azure udržuje [velké portfolio nabídek dodržování předpisů](https://azure.microsoft.com/overview/trusted-cloud/compliance). 

Tyto nabídky jsou založené na různých typech zárukou, včetně formálních certifikací, atestů, ověření, autorizací a hodnocení vytvořených nezávislými společnostmi pro audit třetích stran, jakož i smluvními změnami, vlastními posouzeními a zákaznickými dokumenty vytvořenými společností Microsoft. Projděte si [kompletní přehled nabídek dodržování předpisů](https://aka.ms/AzureCompliance) , abyste zjistili, které z nich můžou být relevantní pro vaše řešení Batch.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) pomáhá vyhovět standardům organizace a hodnotit dodržování předpisů ve velkém měřítku. Běžné případy použití pro Azure Policy zahrnují implementaci zásad správného řízení pro konzistenci prostředků, dodržování legislativních předpisů, zabezpečení, náklady a správu.

V závislosti na režimu přidělování fondů a prostředcích, na které by se měly zásady vztahovat, použijte Azure Policy se službou Batch jedním z následujících způsobů:

- Přímo pomocí prostředku Microsoft.Batch/batchAccounts. Dá se použít podmnožina vlastností pro účet Batch. Vaše zásada může například zahrnovat platné oblasti účtu Batch, povolený režim přidělování fondů a to, jestli je pro účty povolená veřejná síť.
- Nepřímo pomocí prostředku Microsoft. COMPUTE/virtualMachineScaleSets. Účty Batch s režimem přidělování fondů předplatných uživatele můžou mít nastavené zásady u prostředků sady škálování virtuálních počítačů, které se vytvářejí v předplatném účtu Batch. Například povolené velikosti virtuálních počítačů a zajišťují, že některá rozšíření se spouštějí na každém uzlu fondu.

## <a name="next-steps"></a>Další kroky

- Projděte si [základní hodnoty zabezpečení Azure pro službu Batch](security-baseline.md).
- Přečtěte si další [osvědčené postupy pro Azure Batch](best-practices.md).
