---
ms.assetid: ''
title: Trezor klíčů koncové body služby virtuální sítě Azure | Dokumentace Microsoftu
description: Přehled koncových bodů služby virtuální sítě pro službu Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 08/31/2018
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.openlocfilehash: fd3a16841e9d3c9b8bfd9b0f53c42ad1e08b3d80
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345127"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Koncové body služeb virtuální sítě pro Azure Key Vault

Virtuální sítě koncové body služby Key Vault umožňují omezit přístup k zadané virtuální sítě a/nebo na seznam rozsahů adres IPv4 (Internet Protocol verze 4). Jakýkoli volající připojení k vašemu trezoru klíčů z mimo tyto zdroje bude odepřen přístup. Pokud zákazník má povoleno v povolit "Důvěryhodné služby", jako je Office 365 Exchange Online, Office 365 SharePoint Online, Azure compute, Azure Resource Manageru, Azure Backup atd., umožní připojení z těchto služeb přes bránu firewall. Samozřejmě takové volající potřebují, předložit platný token AAD a musí mít oprávnění (konfigurovaná jako zásad přístupu) k provedení požadované operace. Přečtěte si další technické podrobnosti o [koncové body služeb virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scénáře použití

Můžete nakonfigurovat [virtuální sítí a bran firewall služby Key Vault](key-vault-network-security.md) k odepření přístupu k provozu ze všech sítí (včetně přenosy z Internetu) ve výchozím nastavení. Můžete udělit přístup provoz z konkrétní virtuálními sítěmi Azure a/nebo veřejným Internetem IP adresy rozsahů, vám umožní vytvářet zabezpečené ohraničení sítě pro vaše aplikace.

> [!NOTE]
> Key Vault brány firewall a pravidla virtuální sítě platí jenom pro trezor klíčů [rovina dat](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control). Operace roviny řízení služby Key Vault (například služby key vault vytvořit, odstranit, upravit operace, nastavování zásad přístupu, nastavení brány firewall a pravidla virtuální sítě) nejsou ovlivněny brány firewall a pravidla virtuální sítě.

Například:
* Pokud používáte služby Key Vault k ukládání šifrovacích klíčů, tajných klíčů aplikací, certifikátů a chcete zablokovat přístup k vašemu trezoru klíčů z veřejného Internetu.
* Chcete-li zamezit přístup k trezoru klíčů tak, aby jenom vaše aplikace nebo krátký seznam určené hostitele můžete připojit k vašemu trezoru klíčů
* Máte aplikaci spuštěnou ve službě Azure virtual network (VNET) a této virtuální sítě je uzamčena pro veškerý příchozí a odchozí provoz. Vaše aplikace stále potřebuje pro připojení k trezoru klíčů, aby buď načíst tajné kódy a certifikáty, nebo použijte kryptografických klíčů.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Konfigurace bran firewall služby Key Vault a virtuální sítě

Tady jsou kroky potřebnými ke konfiguraci brány firewall a virtuální sítě. Tyto kroky zůstávají stejné bez ohledu na to, jaká rozhraní (portál Azure Powershellu, rozhraní příkazového řádku,), kterou použijete k nastavení brány firewall a pravidla virtuální sítě.
1. Volitelné, ale důrazně doporučené: Povolit [protokolování v key vault](key-vault-logging.md) zobrazíte podrobné zobrazení protokolů. To vám pomůže v diagnostice při brány firewall a pravidla virtuální sítě zabránit přístupu k trezoru klíčů.
2. Povolit pro cílový virtuální sítě a podsítě 'koncové body pro službu key vault.
3. Nastavení brány firewall a pravidla virtuální sítě pro službu key vault k omezení přístupu k této služby key vault z konkrétní virtuální sítě, podsítě a rozsahy adres IPv4.
4. Pokud tento trezor klíčů musí být přístupné pro všechny důvěryhodné služby Microsoftu, je potřeba povolit tuto možnost a povolíte "důvěryhodné Azure Services' pro připojení k trezoru klíčů.

Odkazovat na [virtuálních sítí a konfigurace Azure Key Vault firewallů](key-vault-network-security.md) podrobné pokyny.

> [!IMPORTANT]
> Jakmile se pravidla brány firewall jsou v podstatě všechny služby Key Vault [rovina dat](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) operace lze provádět pouze, když volající požadavky pocházejí z povolených virtuálních sítí nebo rozsahy adres IPV4. To platí i pro přístup k trezoru klíčů z webu Azure portal. Když uživatel může prohlížeče do trezoru klíčů z webu Azure portal, se nebudou moct seznam klíčů/tajných kódů a certifikátů Pokud klientském počítači se nenachází v seznamu povolených aplikací. To ovlivňuje také výběr trezoru klíč pomocí dalších služeb Azure. Uživatelé moci zobrazit seznam trezorů klíčů, ale v seznamu klíčů, je-li zabránit pravidla brány firewall na klientském počítači.


> [!NOTE]
> * Jsou povoleny maximální 127 pravidla virtuální sítě a 127 pravidla IPv4. 
> * Rozsahy malé adres pomocí "/ 31" nebo "/ 32" předpony velikosti se nepodporují. Tyto rozsahy musí být nakonfigurovaný pomocí jednotlivých pravidel IP adres.
> * Pravidla síťových IP jsou povoleny pouze pro veřejné IP adresy. Rozsahy IP adres vyhrazené pro privátní sítě (jak jsou definovány v dokumentu RFC 1918) nejsou povoleny v pravidlech IP. Privátní sítě zahrnují adresy začínající hodnotami * maska 10.* *, * 172.16. **, a * 192.168. **. 
> * V současnosti jsou podporované jenom adresy IPv4.

## <a name="trusted-services"></a>Důvěryhodné služby
Tady je seznam důvěryhodných služeb, které můžou přistupovat k trezoru klíčů, pokud je povolená možnost "Povolit důvěryhodné služby".

|Důvěryhodná služba|Scénáře použití|
| --- | --- |
|Služba nasazení Azure Virtual Machines|[Nasazení certifikátů do virtuálních počítačů z trezoru klíčů spravovaných zákazníkem](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)|
|Služba nasazení šablon Azure Resource Manager|[Předání zabezpečených hodnot během nasazení](../azure-resource-manager/resource-manager-keyvault-parameter.md)|
|Služba šifrování svazků Azure Disk Encryption|Povolit přístup k klíč nástroje BitLocker (virtuální počítač Windows) nebo heslo DM (virtuální počítač s Linuxem) a šifrovací klíč klíče při nasazení virtuálního počítače na povolení [Azure Disk Encryption](../security/azure-security-disk-encryption.md)|
|Azure Backup|Povolit zálohování a obnovení relevantní klíčů a tajných kódů při zálohování virtuálních počítačů Azure pomocí [Azure Backup](../backup/backup-introduction-to-azure-backup.md)|
|Exchange Online a SharePoint Online|Povolit přístup ke klíči zákazníka pro šifrování služby s [klíč zákazníka](https://support.office.com/en-us/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Povolit přístup ke klíči tenanta pro [služby Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|App Services|[Nasazení certifikátu Azure Web App přes Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)|
|Azure SQL|[Transparentní šifrování dat s podporou vlastního klíče pro Azure SQL Database a Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)|
|Azure Storage|[Šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md)|
|Azure Data Lake Store|[Šifrování dat v Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) zákazníkem spravované klíče|



> [!NOTE]
> Zásady přístupu relevantní služby key vault musí nastavit tak, aby odpovídající službami a získávat přístup k trezoru klíčů.

## <a name="next-steps"></a>Další postup

* [Zabezpečení trezoru klíčů](key-vault-secure-your-key-vault.md)
* [Konfigurace bran firewall služby Azure Key Vault a virtuální sítě](key-vault-network-security.md)