---
title: Pracovní postup Azure Attestation
description: Pracovní postup Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 27a97ceb2ca9a7b58df7200930e4e47d89c9ae89
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762165"
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

## <a name="trusted-platform-module-tpm-enclave-validation-work-flow"></a>Pracovní postup ověření enklávy čipu TPM (Trusted Platform Module)

Tady je obecný postup v typickém pracovním postupu ověření enklávy TPM (pomocí ověření identity Azure):

1.  Při spuštění zařízení nebo platformy se v různých spouštěcích modulech a spouštěcích službách měří události, které jsou zajištěné čipem TPM a jsou bezpečně uložené (protokol TCG).
2.  Klient shromažďuje protokoly TCG ze zařízení a z nabídky čipu TPM, který zpracovává legitimaci pro ověření identity.
3.  Klient má identifikátor URI, který odkazuje na instanci Azure Attestation. Klient odesílá legitimaci do Azure Attestation. Přesné informace, které jsou odeslány poskytovateli, závisí na platformě.
4.  Azure Attestation ověří odeslané informace a vyhodnotí je proti nakonfigurovaným zásadám. Pokud je ověření úspěšné, Azure Attestation vydá token ověření identity a vrátí ho klientovi. Pokud tento krok neproběhne úspěšně, oznámí Azure ověření klienta chybu klientovi. Komunikaci mezi klientem a službou ověření identity vychází z protokolu TPM Azure Attestation.
5.  Klient pak pošle token ověření předávající straně. Předávající strana volá koncový bod metadat veřejného klíče Azure Attestation, aby mohl načíst podpisové certifikáty. Předávající strana potom ověří podpis tokenu ověření identity a zajistí důvěryhodnost platforem.

![Tok ověřování TPM](./media/tpm-validation-flow.png)

## <a name="next-steps"></a>Další kroky
- [Jak vytvářet a podepisovat zásady ověření identity](author-sign-policy.md)
- [Nastavení ověření Azure pomocí PowerShellu](quickstart-powershell.md)
