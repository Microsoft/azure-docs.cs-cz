---
title: Spravované identity
description: Media Services lze použít se spravovanými identitami Azure.
keywords: ''
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 0bbfb54d6ba7483e96633bdf05bb580e5517d216
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277738"
---
# <a name="managed-identities"></a>Spravované identity

Běžnou výzvou pro vývojáře je Správa tajných klíčů a přihlašovacích údajů pro zabezpečení komunikace mezi různými službami. V Azure spravované identity eliminují nutnost vývojářů spravovat přihlašovací údaje tím, že poskytuje identitu prostředku Azure ve službě Azure AD a použije ho k získání tokenů Azure Active Directory (Azure AD).

V současné době existují dva scénáře, kdy lze spravované identity použít s Media Services:

- Pro přístup k účtům úložiště použijte spravovanou identitu účtu Media Services.

- Pomocí spravované identity účtu Media Services můžete získat přístup k Key Vault přístup k klíčům zákazníka.

Následující dvě části popisují kroky dvou scénářů.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Použití spravované identity účtu Media Services k přístupu k účtům úložiště

1. Vytvořte účet Media Services se spravovanou identitou.
1. Udělte spravovanému hlavnímu objektu identity přístup k účtu úložiště, který vlastníte.
1. Služba Media Services potom může k účtu úložiště přistupovat za použití spravované identity.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Key Vault přístup k klíčům zákazníka získáte pomocí spravované identity účtu Media Services.

1. Vytvořte účet Media Services se spravovanou identitou.
1. Udělte spravovanému hlavnímu objektu identity přístup k Key Vault, který vlastníte.
1. Nakonfigurujte účet Media Services, aby používal šifrování účtu na základě klíčového zákazníka.
1. Pomocí spravované identity Media Services přistupuje k Key Vault vaším jménem.

Další informace o zákaznických klíčích a Key Vault najdete v tématu [použití vlastních klíčů (klíčů spravovaných zákazníkem) s Media Services](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>Kurzy

Tyto kurzy zahrnují jak výše uvedené scénáře.

- [Použití Azure Portal k použití klíčů spravovaných zákazníkem nebo BYOK s Media Services](security-customer-managed-keys-portal-tutorial.md)
- [Použití klíčů spravovaných zákazníkem nebo BYOK s využitím Media Services REST API](security-customer-managed-keys-rest-postman-tutorial.md).

## <a name="next-steps"></a>Další kroky

Další informace o tom, jaké spravované identity můžou dělat pro vás a vaše aplikace Azure, najdete v tématu [spravované identity Azure AD](../../active-directory/managed-identities-azure-resources/overview.md).
