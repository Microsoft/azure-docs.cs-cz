---
title: Spravované identity a důvěryhodné úložiště pomocí Media Services
description: Media Services lze použít se spravovanými identitami k povolení důvěryhodného úložiště.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: inhenkel
ms.openlocfilehash: a0ded2a1ad5cd590ab5715edebde2ab19e399e8d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686732"
---
# <a name="managed-identities-and-trusted-storage-with-media-services"></a>Spravované identity a důvěryhodné úložiště pomocí Media Services

Media Services lze použít se [spravovanými identitami](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) k povolení důvěryhodného úložiště. Když vytváříte účet Media Services, musíte ho přidružit k účtu úložiště. Media Services mají přístup k tomuto účtu úložiště pomocí ověřování systému. Media Services ověří, že účet Media Services a účet úložiště jsou ve stejném předplatném, a ověří, že uživatel přidávající přidružení má přístup k účtu úložiště s Azure Resource Manager RBAC.

## <a name="trusted-storage"></a>Důvěryhodné úložiště

Pokud ale chcete použít bránu firewall k zabezpečení svého účtu úložiště, musíte použít spravované ověřování identity. Umožňuje Media Services získat přístup k účtu úložiště, který byl nakonfigurován s bránou firewall nebo omezením virtuální sítě prostřednictvím důvěryhodného přístupu k úložišti.  Další informace o důvěryhodných službách Microsoftu najdete v tématu [konfigurace Azure Storage bran firewall a virtuálních sítí](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services).

## <a name="media-services-managed-identity-scenarios"></a>Spravované scénáře identity služby Media Services

V současné době existují dva scénáře, ve kterých lze použít spravovanou identitu s Media Services:

- Pro přístup k účtům úložiště použijte spravovanou identitu účtu Media Services.

- Pomocí spravované identity účtu Media Services můžete získat přístup k Key Vault přístup k klíčům zákazníka.

V dalších dvou částech jsou popsány rozdíly ve dvou scénářích.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Použití spravované identity účtu Media Services k přístupu k účtům úložiště

1. Vytvořte účet Media Services se spravovanou identitou.
1. Udělte spravovanému hlavnímu objektu identity přístup k účtu úložiště, který vlastníte.
1. Služba Media Services potom může k účtu úložiště přistupovat za použití spravované identity.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Key Vault přístup k klíčům zákazníka získáte pomocí spravované identity účtu Media Services.

1. Vytvořte účet Media Services se spravovanou identitou.
1. Udělte spravovanému hlavnímu objektu identity přístup k Key Vault, který vlastníte.
1. Nakonfigurujte účet Media Services, aby používal šifrování účtu na základě klíčového zákazníka.
1. Pomocí spravované identity Media Services přistupuje k Key Vault vaším jménem.

Další informace o zákaznických klíčích a Key Vault najdete v tématu [použití vlastních klíčů (klíčů spravovaných zákazníkem) s Media Services](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>Kurzy

Tyto kurzy zahrnují jak výše uvedené scénáře.

- [Použití Azure Portal k použití klíčů spravovaných zákazníkem nebo BYOK s Media Services](tutorial-byok-portal.md)
- [Použití klíčů spravovaných zákazníkem nebo BYOK s využitím Media Services REST API](tutorial-byok-postman.md).

## <a name="next-steps"></a>Další kroky

Další informace o tom, jaké spravované identity můžou dělat pro vás a vaše aplikace Azure, najdete v tématu [spravované identity Azure AD](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
