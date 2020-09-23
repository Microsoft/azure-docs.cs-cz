---
title: Ověřování enclaves v Azure
description: Zjistěte, jak můžete pomocí ověřování identity ověřit, jestli je vaše důvěrné výpočetní prostředí v bezpečí.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: 70a17aacde67744eae74ca263200f2c65fbd300a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995849"
---
# <a name="attesting-sgx-enclaves"></a>Ověřování SGX Enclaves

Důvěrné výpočetní prostředí v Azure nabízí virtuální počítače založené na Intel SGX, které mohou izolovat část kódu nebo dat. Při práci s těmito [enclaves](confidential-computing-enclaves.md)budete chtít získat ověření a ověření, že je vaše důvěryhodné prostředí zabezpečené. Toto ověření je proces ověření identity. 

## <a name="overview"></a>Přehled 

Ověřování identity umožňuje předávající straně zvýšit jistotu, že jejich software je (1) spuštěný v enklávy a (2) že enklávy je aktuální a zabezpečený. Například enklávy požádá o základní hardware, aby vygeneroval přihlašovací údaje, které obsahují důkaz o tom, že enklávy na platformě existuje. Tuto sestavu je pak možné udělit druhému enklávy, který ověří, že se sestava vygenerovala na stejné platformě.

![ověření kódu v enklávy](media/attestation/attestation.png)



Ověření identity se musí implementovat pomocí služby zabezpečeného ověřování identity, která je kompatibilní se systémovým softwarem a silikonem. Mezi příklady služeb, které můžete použít, patří

- [Ověření identity Microsoft Azure (Preview)](https://docs.microsoft.com/azure/attestation/overview) nebo
- [Ověření a služby zřizování Intel](https://software.intel.com/sgx/attestation-services)


které jsou kompatibilní s infrastrukturou Azure důvěrného výpočetních prostředků Intel SGX. 

## <a name="next-steps"></a>Další kroky
Vyzkoušejte si [ukázky Microsoft Azure ověření identity pro aplikace s podporou enklávy](https://docs.microsoft.com/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/).