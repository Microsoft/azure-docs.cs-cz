---
title: Azure Disk Encryption v izolované síti
description: V tomto článku se seznámíte s tipy pro řešení potíží pro Microsoft Azure šifrování disků na virtuálních počítačích se systémem Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: 1157a18954c30e1f1d93aa33dbea6104491b5353
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91396447"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Azure Disk Encryption v izolované síti

Pokud je připojení omezeno pomocí brány firewall, požadavku serveru proxy nebo skupiny zabezpečení sítě (NSG), může dojít k přerušení možnosti rozšíření k provedení potřebných úkolů. Příčinou tohoto narušení může být stavové zprávy, například "stav rozšíření není na virtuálním počítači k dispozici".

## <a name="package-management"></a>Správa balíčků

Azure Disk Encryption závisí na řadě komponent, které se obvykle instalují jako součást povolení ADE, pokud ještě není k dispozici. Pokud je za bránou firewall nebo jinak izolovaná od Internetu, musí být tyto balíčky předem nainstalované nebo dostupné místně.

Tady jsou balíčky nutné pro každou distribuci. Úplný seznam podporovaných distribuce a typů svazků najdete v tématu [podporované virtuální počítače a operační systémy](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14,04, 16,04, 18,04**: lsscsi, psmisc, at, cryptsetup-bin, Python – parted, Python-6, procps
- **CentOS 7,2-7,7**: lsscsi, psmisc, LVM2, UUID, at, patch, cryptsetup, cryptsetup-reencrypter, pyparted, procps-NG, util – Linux
- **CentOS 6,8**: lsscsi, psmisc, LVM2, UUID, at, cryptsetup-reencrypted, pyparted, Python-šest
- **RedHat 7,2-7,7**: lsscsi, psmisc, LVM2, UUID, at, patch, cryptsetup, cryptsetup-reencrypt, procps-NG, util-Linux
- **RedHat 6,8**: lsscsi, psmisc, LVM2, UUID, at, patch, cryptsetup – reencrypting
- **openSUSE 42,3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

Pokud je v Red Hat vyžadován proxy server, je nutné zajistit, aby byly správně nastaveny Správce předplatného a Yumu. Další informace najdete v tématu řešení potíží se [správcem předplatného a Yumu](https://access.redhat.com/solutions/189533).  

Pokud jsou balíčky nainstalovány ručně, je nutné je také ručně upgradovat při vydání nových verzí.

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě
Všechna použitá nastavení skupiny zabezpečení sítě musí stále umožňovat, aby koncový bod splňoval popsané požadavky na konfiguraci sítě pro šifrování disků.  Viz [Azure Disk Encryption: požadavky na síť](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption s Azure AD (předchozí verze)

Pokud používáte [Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-overview-aad.md), bude nutné ručně nainstalovat [knihovnu Azure Active Directory](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) pro všechny distribuce (kromě balíčků odpovídajících distribuce, jak je [uvedeno výše](#package-management)).

Když se povolí šifrování s [přihlašovacími údaji Azure AD](disk-encryption-linux-aad.md), cílový virtuální počítač musí umožňovat připojení ke koncovým bodům Azure Active Directory i k Key Vault koncovým bodům. Aktuální Azure Active Directory koncové body ověřování jsou uchovávány v oddílech 56 a 59 [Microsoft 365 adres URL a v dokumentaci rozsahů IP adres](/microsoft-365/enterprise/urls-and-ip-address-ranges) . Pokyny pro Key Vault najdete v dokumentaci týkající se [přístupu Azure Key Vault za bránou firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 

Virtuální počítač musí mít přístup ke koncovému bodu [služby metadat instance Azure](instance-metadata-service.md) , který používá dobře známou IP adresu Nesměrovatelné IP adresy ( `169.254.169.254` ), ke které se dá přistupovat jenom v rámci virtuálního počítače.  Konfigurace proxy serveru, které mění místní přenos HTTP na tuto adresu (například přidání řádku s přesměrováním X), nejsou podporovány.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další kroky pro [řešení potíží s Azure Disk Encryption](disk-encryption-troubleshooting.md) .
- [Šifrování neaktivních uložených dat v Azure](../../security/fundamentals/encryption-atrest.md)
