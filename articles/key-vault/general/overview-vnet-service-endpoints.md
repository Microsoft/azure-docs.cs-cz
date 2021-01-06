---
title: Koncové body služby virtuální sítě pro Azure Key Vault
description: Přečtěte si, jak koncové body služby virtuální sítě pro Azure Key Vault umožňují omezit přístup k zadané virtuální síti, včetně scénářů použití.
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 9dcabe10822fd09c8f7a0da6259d81a089c1a042
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936290"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Koncové body služby virtuální sítě pro Azure Key Vault

Koncové body služby virtuální sítě pro Azure Key Vault umožňují omezit přístup k zadané virtuální síti. Koncové body také umožňují omezit přístup k seznamu rozsahů adres protokolu IPv4 (Internet Protocol verze 4). Přístup všem uživatelům, kteří se připojují k trezoru klíčů mimo tyto zdroje, má odepřený přístup.

Toto omezení má jednu důležitou výjimku. Pokud se uživatel rozhodl Povolit důvěryhodné služby Microsoftu, umožní připojení z těchto služeb přes bránu firewall. Mezi tyto služby patří například Office 365 Exchange Online, Office 365 SharePoint Online, COMPUTE Azure, Azure Resource Manager a Azure Backup. Tito uživatelé ještě potřebují mít platný token Azure Active Directory a k provedení požadované operace musí mít oprávnění (nakonfigurovaná jako zásady přístupu). Další informace najdete v tématu [koncové body služby virtuální sítě](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scénáře použití

Ve výchozím nastavení můžete nakonfigurovat [Key Vault brány firewall a virtuální sítě](network-security.md) tak, aby odepřely přístup k provozu ze všech sítí (včetně internetového provozu). Můžete udělit přístup k provozu z konkrétních virtuálních sítí Azure a rozsahů veřejných IP adres, což vám umožní vytvořit zabezpečenou hranici sítě pro vaše aplikace.

> [!NOTE]
> Brány firewall Key Vault a pravidla virtuální sítě se vztahují jenom na [rovinu dat](secure-your-key-vault.md#data-plane-access-control) Key Vault. Brány firewall a pravidla virtuální sítě neovlivní Key Vault operace roviny řízení (například operace vytvoření, odstranění a úpravy, nastavení zásad přístupu, nastavení brány firewall a pravidel virtuální sítě).

Tady je několik příkladů použití koncových bodů služby:

* Používáte Key Vault k ukládání šifrovacích klíčů, tajných klíčů aplikací a certifikátů a chcete zablokovat přístup k trezoru klíčů z veřejného Internetu.
* Chcete uzamknout přístup k trezoru klíčů, aby se k vašemu trezoru klíčů mohli připojit jenom vaše aplikace nebo krátký seznam určených hostitelů.
* Máte aplikaci spuštěnou ve službě Azure Virtual Network a tato virtuální síť je u všech příchozích a odchozích přenosů uzamčena. Vaše aplikace se pořád potřebuje připojit k Key Vault k načtení tajných klíčů nebo certifikátů nebo k používání kryptografických klíčů.

## <a name="trusted-services"></a>Důvěryhodné služby

Tady je seznam důvěryhodných služeb, které mají povolený přístup k trezoru klíčů, pokud je povolená možnost **Povolit důvěryhodné služby** .

|Důvěryhodná služba|Podporované scénáře použití|
| --- | --- |
|Služba pro nasazení služby Azure Virtual Machines|[Nasazení certifikátů do virtuálních počítačů ze Key Vault spravovaných zákazníky](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Služba nasazení šablon Azure Resource Manager|[Předání zabezpečených hodnot během nasazení](../../azure-resource-manager/templates/key-vault-parameter.md).|
|SKU Azure Application Gateway v2|[Ukončení protokolu TLS s certifikáty Key Vault](../../application-gateway/key-vault-certs.md)|
|Služba šifrování svazků Azure Disk Encryption|Povolte přístup k klíči nástroje BitLocker (Windows VM) nebo k přístupovému heslu DM (virtuální počítač Linux) a šifrovacímu klíči během nasazování virtuálního počítače. To umožňuje [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|Povolí zálohování a obnovení důležitých klíčů a tajných kódů během zálohování Azure Virtual Machines pomocí [Azure Backup](../../backup/backup-overview.md).|
|Exchange Online & SharePoint Online|Povolí přístup ke klíči zákazníka pro šifrování Azure Storage služby s [klíčem zákazníka](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Povolí přístup ke klíči tenanta pro [Azure Information Protection.](/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Nasaďte certifikát webové aplikace Azure prostřednictvím Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparentní šifrování dat s podporou Bring Your Own Key pro Azure SQL Database a Azure synapse Analytics](../../azure-sql/database/transparent-data-encryption-byok-overview.md?view=sql-server-2017&preserve-view=true&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Šifrování služby Storage používání klíčů spravovaných zákazníkem v Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|[Šifrování dat v Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) s klíčem spravovaným zákazníkem.|
|Azure Databricks|[Služba analýzy založená na rychlé, jednoduché a Apache Spark spolupráci](/azure/databricks/scenarios/what-is-azure-databricks)|
|Azure API Management|[Nasazení certifikátů pro vlastní doménu z Key Vault pomocí MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Načíst přihlašovací údaje úložiště dat v Key Vault z Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Povolení přístupu k trezoru klíčů pro základní klíče spravované zákazníkem](../../event-hubs/configure-customer-managed-key.md)|
|Azure Service Bus|[Povolení přístupu k trezoru klíčů pro základní klíče spravované zákazníkem](../../service-bus-messaging/configure-customer-managed-key.md)|
|Azure Import/Export| [Použití klíčů spravovaných zákazníkem v Azure Key Vault pro službu import/export](../../storage/common/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[Šifrování registru pomocí klíčů spravovaných zákazníkem](../../container-registry/container-registry-customer-managed-keys.md)

> [!NOTE]
> Aby mohly příslušné služby získat přístup k Key Vault, musíte nastavit příslušné zásady přístupu Key Vault.

## <a name="next-steps"></a>Další kroky

- Podrobné pokyny najdete v tématu [konfigurace Azure Key Vault bran firewall a virtuálních sítí](network-security.md) .
- Další informace najdete v tématu [Přehled zabezpečení Azure Key Vault](security-overview.md) .
