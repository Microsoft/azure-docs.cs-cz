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
ms.openlocfilehash: 87f52bef07644370c268168c984a452d5ae3f9bd
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279455"
---
# <a name="trusted-storage-for-media-services"></a>Důvěryhodné úložiště pro Media Services

Když vytváříte účet Media Services, musíte ho přidružit k účtu úložiště. Media Services mají přístup k tomuto účtu úložiště pomocí ověřování systému nebo spravovaného ověřování identity. Media Services ověří, že účet Media Services a účet úložiště jsou ve stejném předplatném, a ověří, že uživatel přidávající přidružení má přístup k účtu úložiště s Azure Resource Manager RBAC.

>[!NOTE]
>Důvěryhodné úložiště je k dispozici pouze v rozhraní API a aktuálně není povoleno v Azure Portal.

## <a name="trusted-storage-with-a-firewall"></a>Důvěryhodné úložiště pomocí brány firewall

Pokud ale chcete použít bránu firewall k zabezpečení účtu úložiště a Povolit důvěryhodné úložiště, je upřednostňovanou možností ověřování [spravovaných identit](concept-managed-identities.md) . Umožňuje Media Services získat přístup k účtu úložiště, který byl nakonfigurován s bránou firewall nebo omezením virtuální sítě prostřednictvím důvěryhodného přístupu k úložišti.

## <a name="tutorial"></a>Kurz

Další informace o povolení důvěryhodného úložiště najdete v kurzu [Media Services důvěryhodné úložiště](security-trusted-storage-rest-tutorial.md) .

> [!NOTE]
> Aby Media Services mohl číst a zapisovat do účtu úložiště, je třeba udělit přístup přispěvatele dat objektu BLOB úložiště spravované identity v AMS.  Udělení obecné role přispěvatele nebude fungovat, protože nepovoluje správná oprávnění k rovině dat.

## <a name="further-reading"></a>Další čtení

Pokud chcete pochopit metody vytváření důvěryhodných úložišť se spravovanými identitami, přečtěte si [spravované identity a Media Services](concept-managed-identities.md).

Další informace o důvěryhodných službách Microsoftu najdete v tématu [konfigurace Azure Storage bran firewall a virtuálních sítí](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="next-steps"></a>Další kroky

Další informace o tom, jaké spravované identity můžou dělat pro vás a vaše aplikace Azure, najdete v tématu [spravované identity Azure AD](../../active-directory/managed-identities-azure-resources/overview.md).
