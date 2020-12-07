---
title: Pracovní postup Azure Attestation
description: Pracovní postup Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 09d793f3d8ed544a386a362677f24be6d18673d7
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748721"
---
# <a name="workflow"></a>Pracovní postup

Microsoft Azure ověření identity přijímá legitimaci od enclaves a vyhodnocuje legitimaci v rámci standardních zásad a zabezpečení Azure. Po úspěšném ověření vygeneruje Azure Attestation token ověření identity, aby bylo možné potvrdit důvěryhodnost enklávy.

Následující objekty actor jsou zapojené do pracovního postupu Azure Attestation:

- **Předávající strana**: komponenta, která spoléhá na ověření identity Azure, k ověření enklávy platnosti. 
- **Klient**: komponenta, která shromažďuje informace z enklávy a odesílá požadavky do ověření identity Azure. 
- **Ověření identity Azure**: komponenta, která přijímá enklávy legitimace od klienta, ověří ji a vrátí token ověření klienta.


## <a name="intel-software-guard-extensions-sgx-enclave-validation-work-flow"></a>Pracovní postup ověření enklávy pro rozšíření Intel® software Guard (SGX)

Tady je obecný postup v typickém pracovním postupu SGX enklávy Attestation (pomocí ověření identity Azure):

1. Klient shromažďuje legitimaci z enklávy. Legitimace jsou informace o prostředí enklávy a klientské knihovně spuštěné v enklávy.
1. Klient má identifikátor URI, který odkazuje na instanci Azure Attestation. Klient odesílá legitimaci do Azure Attestation. Přesné informace, které jsou odeslány poskytovateli, závisí na typu enklávy.
1. Azure Attestation ověří odeslané informace a vyhodnotí je proti nakonfigurovaným zásadám. Pokud je ověření úspěšné, Azure Attestation vydá token ověření identity a vrátí ho klientovi. Pokud tento krok neproběhne úspěšně, oznámí Azure ověření klienta chybu klientovi. 
1. Klient odešle token ověření do předávající strany. Předávající strana volá koncový bod metadat veřejného klíče Azure Attestation, aby mohl načíst podpisové certifikáty. Předávající strana potom ověří signaturu tokenu ověření identity a zaručí enklávy věrohodnosti. 

![Tok ověřování SGX enklávy](./media/sgx-validation-flow.png)

> [!Note]
> Když posíláte žádosti o ověření ve verzi rozhraní API [2018-09-01-Preview](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/attestation/data-plane/Microsoft.Attestation/stable/2018-09-01-preview) , musí klient odesílat legitimaci do Azure Attestation spolu s přístupovým tokenem Azure AD.

## <a name="next-steps"></a>Další kroky
- [Jak vytvářet a podepisovat zásady ověření identity](author-sign-policy.md)
- [Nastavení ověření Azure pomocí PowerShellu](quickstart-powershell.md)
