---
title: Šifrování disku Azure v izolované síti
description: Tento článek obsahuje tipy pro řešení potíží s microsoft azure šifrování disků pro virtuální počítače s Linuxem.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: aa0dc204a017e2d40eb3952a9ede0755127f8de2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970666"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Šifrování disku Azure v izolované síti

Pokud je připojení omezeno nastavením brány firewall, požadavku na server proxy nebo skupiny zabezpečení sítě (NSG), může být schopnost rozšíření provádět potřebné úlohy narušena. Toto narušení může mít za následek stavové zprávy, jako je například "Stav rozšíření není k dispozici na virtuálním počítači."

## <a name="package-management"></a>Správa balíčků

Azure Disk Encryption závisí na počtu součástí, které se obvykle instalují jako součást povolení ADE, pokud již nejsou k dispozici. Pokud jsou tyto balíčky za bránou firewall nebo jinak izolovány od Internetu, musí být předinstalované nebo dostupné místně.

Zde jsou balíčky potřebné pro každou distribuci. Úplný seznam podporovaných distribucí a typů svazků najdete v [tématu podporované virtuální počítače a operační systémy](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-six, procps
- **CentOS 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencrypt, pyparted, python-six
- **RedHat 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux
- **RedHat 6,8**: lsscsi, psmisc, lvm2, uuid, na, patch, cryptsetup-reencrypt
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

Na Red Hat, když je vyžadován proxy, musíte se ujistit, že správce předplatného a yum jsou správně nastaveny. Další informace naleznete v [tématu Řešení potíží se správcem předplatného a mlazemi](https://access.redhat.com/solutions/189533).  

Při instalaci balíčků ručně, musí být také ručně upgradovány jako nové verze jsou uvolněny.

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě
Všechna použitá nastavení skupiny zabezpečení sítě musí koncovému bodu stále umožňovat splnění předpokladů pro konfiguraci sítě pro šifrování disku.  Viz [Šifrování disku Azure: Požadavky na sítě](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Šifrování disku Azure pomocí Azure AD (předchozí verze)

Pokud [používáte Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-overview-aad.md), bude nutné [knihovnu Azure Active Directory](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) nainstalovat ručně pro všechny distribuce (kromě balíčků vhodných pro distro, jak [je uvedeno výše).](#package-management)

Když je šifrování povoleno s [přihlašovacími údaji Azure AD](disk-encryption-linux-aad.md), cílový virtuální počítač musí povolit připojení ke koncovým bodům služby Azure Active Directory i koncovým bodům trezoru klíčů. Aktuální koncové body ověřování služby Azure Active Directory se udržují v oddílech 56 a 59 adres [URL office 365 a dokumentaci k rozsahům IP adres.](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) Pokyny k trezoru klíčů jsou k dispozici v dokumentaci o tom, jak získat přístup k [trezoru klíčů Azure za bránou firewall](../../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Služba metadat instance Azure 

Virtuální počítač musí mít přístup ke koncovému bodu [služby Metadat instance Azure,](instance-metadata-service.md) který používá`169.254.169.254`známou nesměrovatelnou IP adresu ( ), ke které lze přistupovat pouze z virtuálního počítače.  Konfigurace serveru proxy, které mění místní přenos protokolu HTTP na tuto adresu (například přidání hlavičky X-Forwarded-For), nejsou podporovány.

## <a name="next-steps"></a>Další kroky

- Další kroky pro [řešení potíží s šifrováním disku Azure](disk-encryption-troubleshooting.md)
- [Šifrování dat Azure v klidovém stavu](../../security/fundamentals/encryption-atrest.md)
