---
title: Pracovní postup Azure Attestation
description: Pracovní postup Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 240b27f897d8e7a34026701cf7fdc844eb9d4086
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89237238"
---
# <a name="workflow"></a>Pracovní postup

Microsoft Azure ověření identity přijímá legitimaci od enclavess a vyhodnocuje legitimaci v rámci standardních zásad a zabezpečení Azure. Po úspěšném ověření vygeneruje Azure Attestation token ověření identity pro potvrzení důvěryhodnosti enklávy.

Následující objekty actor jsou zapojené do pracovního postupu Azure Attestation:

- **Předávající strana**: komponenta, která spoléhá na ověření identity Azure, k ověření enklávy platnosti. 
- **Klient**: komponenta, která shromažďuje informace z enklávy a odesílá požadavky do ověření identity Azure. 
- **Ověření identity Azure**: komponenta, která přijímá enklávy legitimace od klienta, ověří ji a vrátí token ověření klienta.


## <a name="enclave-validation-work-flow"></a>Pracovní postup ověření enklávy

Tady je obecný postup v typickém pracovním postupu SGX enklávy Attestation (pomocí ověření identity Azure):

1. Klient shromažďuje legitimaci z enklávy. Legitimace jsou informace o prostředí enklávy a klientské knihovně spuštěné v enklávy.
1. Klient má identifikátor URI, který odkazuje na instanci Azure Attestation. Klient se ověří v Azure AD a získá přístupový token.
1. Klient odesílá legitimaci do Azure Attestation společně s přístupovým tokenem. Přesné informace, které jsou odeslány poskytovateli, závisí na typu enklávy.
1. Azure Attestation ověří odeslané informace a vyhodnotí je proti nakonfigurovaným zásadám. Pokud je ověření úspěšné, Azure Attestation vydá token ověření identity a vrátí ho klientovi. Pokud tento krok neproběhne úspěšně, oznámí Azure ověření klienta chybu klientovi. 
1. Klient odešle token ověření do předávající strany. Předávající strana volá koncový bod metadat veřejného klíče Azure Attestation, aby mohl načíst podpisové certifikáty. Předávající strana potom ověří signaturu tokenu ověření identity a zaručí enklávy věrohodnosti. 

![Tok ověřování enklávy](./media/validation-flow.png)


## <a name="next-steps"></a>Další kroky
- [Jak vytvářet a podepisovat zásady ověření identity](author-sign-policy.md)
- [Nastavení ověření Azure pomocí PowerShellu](quickstart-powershell.md)
