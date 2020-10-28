---
title: ID adresy modulu Azure Integration Runtime
description: Zjistěte, které IP adresy musíte povolit příchozí provoz z, aby bylo možné správně nakonfigurovat brány firewall pro zabezpečení síťového přístupu k úložištím dat.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: fb83be59a3ccb11ea8bbd88307596937caa2b354
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638291"
---
# <a name="azure-integration-runtime-ip-addresses"></a>ID adresy modulu Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

IP adresy, které Azure Integration Runtime používá, závisí na oblasti, ve které se nachází Azure Integration runtime. *Vše* Prostředí Azure Integration runtime, která jsou ve stejné oblasti, používají stejné rozsahy IP adres.

> [!IMPORTANT]  
> Toky dat a Azure Integration Runtime, které umožňují spravované Virtual Network nepodporují použití pevných rozsahů IP adres.
>
> Tyto rozsahy IP adres můžete použít pro provádění přesunu dat, kanálu a externích aktivit. Tyto rozsahy IP adres se dají použít k filtrování v úložištích dat/skupinách zabezpečení sítě (NSG)/branách firewall pro příchozí přístup z prostředí Azure Integration runtime. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime IP adresy: konkrétní oblasti

Povolte provoz z IP adres uvedených pro Azure Integration runtime v konkrétní oblasti Azure, ve které se vaše prostředky nacházejí. Z [odkazu na stažení rozsahu IP adres značek služby](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)můžete získat seznam ROZSAHŮ IP adres. Pokud je například oblast Azure **AustraliaEast** , můžete získat seznam ROZSAHŮ IP adres z objektu **DataFactory. AustraliaEast** .


## <a name="known-issue-with-azure-storage"></a>Známý problém s Azure Storage

* Při připojování k účtu Azure Storage nemají pravidla sítě IP žádný vliv na požadavky pocházející z prostředí Azure Integration runtime ve stejné oblasti jako účet úložiště. Další podrobnosti najdete v [tomto článku](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range). 

  Místo toho doporučujeme [při připojování k Azure Storage používat důvěryhodné služby](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Další kroky

* [Otázky zabezpečení při přesunu dat v Azure Data Factory](data-movement-security-considerations.md)