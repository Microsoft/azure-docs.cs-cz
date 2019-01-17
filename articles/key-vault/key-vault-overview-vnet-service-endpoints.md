---
ms.assetid: ''
title: Koncové body služeb virtuální sítě pro Azure Key Vault – Azure Key Vault | Dokumentace Microsoftu
description: Přehled koncových bodů služby virtuální sítě pro službu Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 01/02/2019
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.openlocfilehash: 2c035b7c6bbd687ca2922d5f52e2ddaa0c96e50c
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351420"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Koncové body služeb virtuální sítě pro Azure Key Vault

Koncové body služeb virtuální sítě pro Azure Key Vault umožňují omezit přístup k zadané virtuální sítě. Koncové body umožňují omezit přístup k seznamu rozsahů adres IPv4 (internet protocol verze 4). Každý uživatel, připojení k vašemu trezoru klíčů z mimo tyto zdroje je odepřen přístup.

Existuje jedna důležitá výjimka tohoto omezení. Pokud se uživatel přihlásí k povolit důvěryhodným službám Microsoftu, se přes bránu firewall umožňují připojení z těchto služeb. Například tyto služby patří služby Office 365 Exchange Online, Office 365 SharePoint Online, Azure compute, Azure Resource Manageru a Azure Backup. Stále potřeba, předložit platný token Azure Active Directory a musí mít oprávnění (konfigurovaná jako zásad přístupu) k provedení požadované operace. Další informace najdete v tématu [koncové body služeb virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scénáře použití

Můžete nakonfigurovat [virtuální sítí a bran firewall služby Key Vault](key-vault-network-security.md) k odepření přístupu k provozu ze všech sítí (včetně přenosů z Internetu) ve výchozím nastavení. Můžete udělit přístup provoz z konkrétní služby Azure virtual networks a veřejným Internetem IP adresy rozsahů, což umožňuje vytvořit zabezpečené ohraničení sítě pro vaše aplikace.

> [!NOTE]
> Key Vault brány firewall a pravidla virtuální sítě platí jenom pro [rovina dat](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) služby Key Vault. Operace roviny řízení služby Key Vault (například vytváření, odstraňování a změny operace, nastavení zásad přístupu, nastavení brány firewall a pravidla virtuální sítě) nejsou ovlivněny brány firewall a pravidla virtuální sítě.

Tady je několik příkladů použití koncových bodů služby:

* Používáte služby Key Vault pro ukládání šifrovacích klíčů, tajných kódů aplikace a certifikáty, a chcete zablokovat přístup k vašemu trezoru klíčů z veřejného Internetu.
* Chcete zamezit přístup k trezoru klíčů tak, aby pouze svou aplikaci nebo krátký seznam určených hostitelů, můžete připojit k vašemu trezoru klíčů.
* Máte aplikaci spuštěnou ve službě Azure virtual network a této virtuální sítě je uzamčena pro veškerý příchozí a odchozí provoz. Vaše aplikace stále potřebuje k připojení do služby Key Vault načíst tajné kódy a certifikáty, nebo použít kryptografických klíčů.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Konfigurace bran firewall služby Key Vault a virtuální sítě

Tady jsou kroky potřebnými ke konfiguraci brány firewall a virtuální sítě. Tento postup platí, ať už používáte PowerShell, rozhraní příkazového řádku Azure nebo na webu Azure portal.

1. Povolit [protokolování v Key Vault](key-vault-logging.md) zobrazíte podrobné zobrazení protokolů. To pomáhá v diagnostice, když brány firewall a pravidla virtuální sítě zabránit přístupu k trezoru klíčů. (Tento krok je volitelný, ale důrazně doporučujeme.)
2. Povolit **koncové body pro trezor klíčů služby** pro cílový virtuální sítě a podsítě.
3. Nastavení brány firewall a pravidla virtuální sítě pro službu key vault k omezení přístupu k této služby key vault z konkrétní virtuální sítě, podsítě a rozsahy adres IPv4.
4. Pokud tento trezor klíčů musí být přístupné pro všechny důvěryhodné služby Microsoftu, povolte tuto možnost a povolíte **důvěryhodným službám Azure** k připojení do služby Key Vault.

Další informace najdete v tématu [virtuální sítí a bran firewall nakonfigurovat služby Azure Key Vault](key-vault-network-security.md).

> [!IMPORTANT]
> Po pravidla brány firewall jsou aktivní, uživatelé mohou provádět pouze služby Key Vault [rovina dat](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) povolené operace při jejich požadavky pocházejí z virtuální sítě nebo rozsahy adres IPv4. To platí i pro přístup k trezoru klíčů z portálu Azure portal. I když můžou uživatelé procházet k trezoru klíčů z portálu Azure portal, se nebudou moct vypsat klíče, tajné kódy nebo certifikáty Pokud klientském počítači se nenachází v seznamu povolených aplikací. To ovlivňuje také výběr Key Vault pomocí dalších služeb Azure. Uživatelé možná půjde zobrazit seznam trezorů klíčů, ale v seznamu klíčů, je-li zabránit pravidla brány firewall na klientském počítači.


> [!NOTE]
> Mějte na paměti následující omezení konfigurace:
> * Jsou povoleny maximálně 127 pravidla virtuální sítě a 127 pravidla IPv4. 
> * Rozsahy, které využívají malé adres "/ 31" nebo "/ 32" předpony velikosti se nepodporují. Místo toho nakonfigurujte tyto rozsahy pomocí jednotlivých pravidel IP adresu.
> * Pravidla síťových IP jsou povoleny pouze pro veřejné IP adresy. Rozsahy IP adres vyhrazené pro privátní sítě (jak jsou definovány v dokumentu RFC 1918) nejsou povoleny v pravidlech IP. Privátní sítě zahrnují adresy začínající hodnotami **10.**, **172.16.**, a **192.168.**. 
> * V současnosti jsou podporované jenom adresy IPv4.

## <a name="trusted-services"></a>Důvěryhodné služby

Tady je seznam důvěryhodných služeb, které můžou přistupovat k trezoru klíčů, pokud **povolit důvěryhodným službám** je povolená možnost.

|Důvěryhodná služba|Scénáře použití|
| --- | --- |
|Služba nasazení Azure Virtual Machines|[Nasazení certifikátů do virtuálních počítačů z trezoru klíčů spravovaných zákazníkem](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Služba nasazení šablon Azure Resource Manager|[Předání zabezpečených hodnot během nasazení](../azure-resource-manager/resource-manager-keyvault-parameter.md).|
|Služba šifrování svazků Azure Disk Encryption|Povolit přístup k klíč nástroje BitLocker (virtuální počítač Windows) nebo heslo DM (virtuální počítač s Linuxem) a šifrovací klíč klíče, během nasazení virtuálních počítačů. Díky tomu [Azure Disk Encryption](../security/azure-security-disk-encryption.md).|
|Azure Backup|Povolit zálohování a obnovení relevantní klíčů a tajných kódů při zálohování virtuálních počítačů Azure s použitím [Azure Backup](../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online a SharePoint Online|Povolit přístup k klíč zákazníka pro šifrování služby Azure Storage s [klíč zákazníka](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Povolit přístup ke klíči tenanta pro [služby Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Nasaďte certifikát pro Azure Web App přes Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/).|
|Azure SQL Database|[Transparentní šifrování dat s podporou vlastního klíče pro Azure SQL Database a Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Šifrování dat v Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) pomocí klíče spravovaného zákazníkem.|
|Azure databricks|[Rychlá, snadná a spolupráci založené na Apache Sparku analytická služba](../azure-databricks/what-is-azure-databricks.md)|



> [!NOTE]
> Musíte nastavit příslušné zásady přístupu trezoru klíčů, aby odpovídající služby, chcete-li získat přístup k trezoru klíčů.

## <a name="next-steps"></a>Další postup

* [Zabezpečení trezoru klíčů](key-vault-secure-your-key-vault.md)
* [Konfigurace bran firewall služby Azure Key Vault a virtuální sítě](key-vault-network-security.md)
