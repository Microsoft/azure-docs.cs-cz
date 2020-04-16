---
title: Koncové body služby Virtuální síť pro azure key vault – azure key vault | Dokumenty společnosti Microsoft
description: Přehled koncových bodů služby virtuální sítě pro trezor klíčů
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 2a68a50a5d15b9f38407c19494a39a14abfa0a5a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432070"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Koncové body služby Virtuální síť pro Azure Key Vault

Koncové body služby virtuální sítě pro Azure Key Vault umožňují omezit přístup k zadané virtuální síti. Koncové body také umožňují omezit přístup k seznamu rozsahů adres IPv4 (internetový protokol verze 4). Všem uživatelům, kteří se připojují k trezoru klíčů mimo tyto zdroje, je odepřen přístup.

Existuje jedna důležitá výjimka z tohoto omezení. Pokud se uživatel přihlásil k povolení důvěryhodných služeb společnosti Microsoft, připojení z těchto služeb jsou pronajímána bránou firewall. Mezi tyto služby patří například Office 365 Exchange Online, Office 365 SharePoint Online, Výpočetní prostředky Azure, Správce prostředků Azure a Azure Backup. Tito uživatelé stále potřebují předložit platný token služby Azure Active Directory a musí mít oprávnění (nakonfigurovaná jako zásady přístupu) k provedení požadované operace. Další informace naleznete v tématu [Koncové body služby virtuální sítě](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scénáře použití

Brány [firewall trezoru klíčů a virtuální sítě](network-security.md) můžete ve výchozím nastavení nakonfigurovat tak, aby odepřely přístup k provozu ze všech sítí (včetně internetového provozu). Přístup k provozu můžete udělit z konkrétních virtuálních sítí Azure a rozsahů veřejných internetových IP adres, což vám umožní vytvořit zabezpečenou hranici sítě pro vaše aplikace.

> [!NOTE]
> Brány firewall úložiště klíčů a pravidla virtuální sítě se vztahují pouze na [rovinu dat](secure-your-key-vault.md#data-plane-access-control) trezoru klíčů. Operace řídicí roviny úložiště klíčů (například operace vytváření, odstraňování a úprav, nastavení zásad přístupu, nastavení bran firewall a pravidel virtuální sítě) nejsou ovlivněny bránami firewall a pravidly virtuální sítě.

Tady je několik příkladů, jak můžete používat koncové body služby:

* Trezor klíčů používáte k ukládání šifrovacích klíčů, tajných kódů aplikací a certifikátů a chcete blokovat přístup k trezoru klíčů z veřejného Internetu.
* Chcete uzamknout přístup k trezoru klíčů tak, aby pouze vaše aplikace, nebo krátký seznam určených hostitelů, se může připojit k trezoru klíčů.
* Máte aplikaci spuštěnou ve vaší virtuální síti Azure a tato virtuální síť je uzamčená pro všechny příchozí a odchozí přenosy. Aplikace se stále potřebuje připojit k trezoru klíčů, aby načítala tajné klíče nebo certifikáty, nebo použít kryptografické klíče.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Konfigurace bran firewall a virtuálních sítí pro Key Vault

Tady jsou kroky potřebné ke konfiguraci bran firewall a virtuálních sítí. Tyto kroky platí bez ohledu na to, zda používáte PowerShell, Azure CLI nebo portál Azure.

1. Povolit [protokolování trezoru klíčů](logging.md)) zobrazíte podrobné protokoly přístupu. To pomáhá při diagnostice, když brány firewall a pravidla virtuální sítě brání přístupu k trezoru klíčů. (Tento krok je volitelný, ale důrazně doporučujeme.)
2. Povolte **koncové body služby pro trezor klíčů** pro cílové virtuální sítě a podsítě.
3. Nastavte brány firewall a pravidla virtuální sítě pro trezor klíčů, abyste omezili přístup k tomuto trezoru klíčů z konkrétních virtuálních sítí, podsítí a rozsahů adres IPv4.
4. Pokud tento trezor klíčů musí být přístupný všem důvěryhodným službám Společnosti Microsoft, povolte možnost povolit **služby Trusted Azure Services** pro připojení k trezoru klíčů.

Další informace naleznete v [tématu Konfigurace bran firewall azure key vaultu a virtuálních sítí](network-security.md).

> [!IMPORTANT]
> Po použití pravidel brány firewall mohou uživatelé provádět operace [s rovinou dat úložiště](secure-your-key-vault.md#data-plane-access-control) klíčů pouze v případě, že jejich požadavky pocházejí z povolených virtuálních sítí nebo rozsahů adres IPv4. To platí také pro přístup k trezoru klíčů z webu Azure Portal. Přestože uživatelé mohou přejít do trezoru klíčů z portálu Azure, nemusí být schopni seznam klíčů, tajných kódů nebo certifikátů, pokud jejich klientský počítač není v seznamu povolených. To má také vliv na výběr trezoru klíčů jinými službami Azure. Uživatelé mohou zobrazit seznam trezorů klíčů, ale ne klíče seznamu, pokud pravidla brány firewall brání jejich klientském počítači.


> [!NOTE]
> Mějte na paměti následující omezení konfigurace:
> * Maximálně 127 pravidel virtuální sítě a 127 pravidel IPv4 jsou povoleny. 
> * Malé rozsahy adres, které používají velikosti předpony "/31" nebo "/32", nejsou podporovány. Místo toho nakonfigurujte tyto rozsahy pomocí jednotlivých pravidel IP adresy.
> * Pravidla sítě IP jsou povolena pouze pro veřejné IP adresy. Rozsahy IP adres vyhrazené pro privátní sítě (podle definice v rfc 1918) nejsou v pravidlech protokolu IP povoleny. Soukromé sítě zahrnují adresy začínající **na 10.**, **172.16-31**a **192.168.**. 
> * V současné době jsou podporovány pouze adresy IPv4.

## <a name="trusted-services"></a>Důvěryhodné služby

Zde je seznam důvěryhodných služeb, které mají přístup k trezoru klíčů, pokud je povolena možnost **Povolit důvěryhodné služby.**

|Důvěryhodná služba|Podporované scénáře použití|
| --- | --- |
|Služba nasazení virtuálních počítačů Azure|[Nasazujte certifikáty do virtuálních aplikací z trezoru klíčů spravovaného zákazníkem](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Služba nasazení šablon Azure Resource Manager|[Předajte zabezpečené hodnoty během nasazení](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Služba šifrování svazku azure diskového šifrování|Povolit přístup k bitlocker klíč (Windows VM) nebo Přístupové heslo DM (Linux VM) a klíč šifrovací klíč, během nasazení virtuálního počítače. To povolí [azure disk encryption](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|Povolte zálohování a obnovení příslušných klíčů a tajných kódů během zálohování virtuálních počítačů Azure pomocí [služby Azure Backup](../../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online & SharePointu Online|Povolit přístup k klíči zákazníka pro šifrování služby Azure Storage Service s [klíčem zákazníka](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Povolte přístup ke klíči klienta pro [Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Nasazení certifikátu Azure Web App prostřednictvím trezoru klíčů](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparentní šifrování dat s podporou vlastních klíčů pro Azure SQL Database a Datový sklad](../../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Šifrování služby storage service pomocí klíčů spravovaných zákazníkem v trezoru klíčů Azure](../../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Šifrování dat v Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) pomocí klíče spravovaného zákazníkem.|
|Azure Databricks|[Rychlá, snadná a kolaborativní analytická služba založená na Apache Spark](../../azure-databricks/what-is-azure-databricks.md)|
|Azure API Management|[Nasazení certifikátů pro vlastní doménu z trezoru klíčů pomocí msi](../../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|
|Azure Data Factory|[Načtení přihlašovacích údajů úložiště dat v trezoru klíčů z datové továrny](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Povolit přístup k trezoru klíčů pro scénář klíčů spravovaných zákazníkem](https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key)|
|Azure Service Bus|[Povolit přístup k trezoru klíčů pro scénář klíčů spravovaných zákazníkem](https://docs.microsoft.com/azure/service-bus-messaging/configure-customer-managed-key)|
|Azure Import/Export| [Použití klíčů spravovaných zákazníkem ve službě Azure Key Vault for Import/Export](https://docs.microsoft.com/azure/storage/common/storage-import-export-encryption-key-portal)

> [!NOTE]
> Je nutné nastavit příslušné zásady přístupu trezoru klíčů, aby odpovídající služby mohly získat přístup k trezoru klíčů.

## <a name="next-steps"></a>Další kroky

* [Zabezpečte trezor klíčů](secure-your-key-vault.md))
* [Konfigurace bran azure key vault a virtuálních sítí](network-security.md)
