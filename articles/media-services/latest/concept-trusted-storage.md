---
title: Důvěryhodné úložiště pro Media Services
description: Ověřování spravovaných identit umožňuje Media Services získat přístup k účtu úložiště, který byl nakonfigurován s bránou firewall nebo omezením virtuální sítě prostřednictvím důvěryhodného přístupu k úložišti.
keywords: důvěryhodné úložiště, spravované identity
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 59c1eb7936bc113f8935d6fa2ad378c6994c3ca9
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99408490"
---
# <a name="trusted-storage-for-media-services"></a>Důvěryhodné úložiště pro Media Services

Když vytváříte účet Media Services, musíte ho přidružit k účtu úložiště. Media Services mají přístup k tomuto účtu úložiště pomocí ověřování systému. Media Services ověří, že účet Media Services a účet úložiště jsou ve stejném předplatném, a ověří, že uživatel přidávající přidružení má přístup k účtu úložiště s Azure Resource Manager RBAC.

Pokud však chcete použít bránu firewall k zabezpečení účtu úložiště a Povolit důvěryhodné úložiště, je nutné použít ověřování [spravované identity](concept-managed-identities.md) . Umožňuje Media Services získat přístup k účtu úložiště, který byl nakonfigurován s bránou firewall nebo omezením virtuální sítě prostřednictvím důvěryhodného přístupu k úložišti.

Pokud chcete pochopit metody vytváření důvěryhodných úložišť se spravovanými identitami, přečtěte si [spravované identity a Media Services](concept-managed-identities.md).

Další informace o zákaznických klíčích a Key Vault najdete v tématu [použití vlastních klíčů (klíčů spravovaných zákazníkem) s Media Services](concept-use-customer-managed-keys-byok.md)

Další informace o důvěryhodných službách Microsoftu najdete v tématu [konfigurace Azure Storage bran firewall a virtuálních sítí](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="tutorials"></a>Kurzy

Tyto kurzy zahrnují jak výše uvedené scénáře.

- [Použití Azure Portal k použití klíčů spravovaných zákazníkem nebo BYOK s Media Services](tutorial-byok-portal.md)
- [Použití klíčů spravovaných zákazníkem nebo BYOK s využitím Media Services REST API](tutorial-byok-postman.md).

## <a name="next-steps"></a>Další kroky

Další informace o tom, jaké spravované identity můžou dělat pro vás a vaše aplikace Azure, najdete v tématu [spravované identity Azure AD](../../active-directory/managed-identities-azure-resources/overview.md).