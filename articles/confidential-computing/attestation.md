---
title: Ověřování enclaves v Azure
description: Zjistěte, jak můžete pomocí ověřování identity ověřit, jestli je vaše důvěrné výpočetní prostředí v bezpečí.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: a7b0ca65329016b0a73f612115d8caba43dfbe2a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551347"
---
# <a name="attesting-sgx-enclaves"></a>Ověřování SGX Enclaves

Důvěrné výpočetní prostředí v Azure nabízí virtuální počítače založené na Intel SGX, které mohou izolovat část kódu nebo dat. Při práci s těmito [enclaves](confidential-computing-enclaves.md)budete chtít získat ověření a ověření, že je vaše důvěryhodné prostředí zabezpečené. Toto ověření je proces ověření identity. 

## <a name="overview"></a>Přehled 

Ověřování identity umožňuje předávající straně zvýšit jistotu, že jejich software je (1) spuštěný v enklávy a (2) že enklávy je aktuální a zabezpečený. Například enklávy požádá o základní hardware, aby vygeneroval přihlašovací údaje, které obsahují důkaz o tom, že enklávy na platformě existuje. Tuto sestavu je pak možné udělit druhému enklávy, který ověří, že se sestava vygenerovala na stejné platformě.

![ověření kódu v enklávy](media/attestation/attestation.png)



Ověření identity se musí implementovat pomocí služby zabezpečeného ověřování identity, která je kompatibilní se systémovým softwarem a silikonem. Mezi příklady služeb, které můžete použít, patří

- [Ověření identity Microsoft Azure (Preview)](../attestation/overview.md) nebo
- [Ověření a služby zřizování Intel](https://software.intel.com/sgx/attestation-services)


které jsou kompatibilní s infrastrukturou Azure důvěrného výpočetních prostředků Intel SGX. 

## <a name="next-steps"></a>Další kroky
Vyzkoušejte si [ukázky Microsoft Azure ověření identity pro aplikace s podporou enklávy](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/).