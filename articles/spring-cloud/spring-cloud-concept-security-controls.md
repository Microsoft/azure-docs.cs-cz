---
title: Koncepce – řízení zabezpečení pro jarní cloudovou službu Azure
description: Používejte bezpečnostní mechanismy, které jsou integrované v Azure jaře Cloud Service.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 5b459ef57d0e8a22ce1cd53f56c44d31e53c7c93
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594980"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Ovládací prvky zabezpečení pro jarní cloudovou službu Azure
Ovládací prvky zabezpečení jsou integrované do služby Azure jaře Cloud Service.

Řízení zabezpečení je kvalita nebo funkce služby Azure, která přispívá ke schopnostem služby zabránit, zjišťovat a reagovat na ohrožení zabezpečení.  Pro každý ovládací prvek používáme *Ano* nebo *ne* k označení, zda je v tuto chvíli pro službu nastavená.  Pro ovládací prvek, který není použitelný pro službu, používáme *N/a* . 

**Ovládací prvky zabezpečení ochrany dat**

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|:-------------|:-------|:-------------------------------|:----------------------|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano | Uživatel nahrál zdroje a artefakty, nastavení konfiguračního serveru, nastavení aplikace a data v trvalém úložišti jsou uložená v Azure Storage, která automaticky zašifruje obsah v klidovém umístění.<br><br>Mezipaměť serveru konfigurace, běhové binární soubory sestavené z nahraného zdroje a protokoly aplikací během životnosti aplikace se ukládají do disku spravovaného Azure, který automaticky šifruje obsah v klidovém režimu.<br><br>Image kontejnerů sestavené ze zdroje odeslaného uživatelem jsou uloženy v Azure Container Registry, který automaticky šifruje obsah image v klidovém umístění. | [Šifrování služby Azure Storage pro neaktivní uložená data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Šifrování na straně serveru Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Úložiště imagí kontejneru v Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Šifrování v přechodném případě | Ano | Veřejné koncové body uživatelské aplikace používají k příchozímu přenosu standardně HTTPS. |  |
| Zašifrovaná volání rozhraní API | Ano | Volání správy ke konfiguraci jarní cloudové služby Azure nastávají prostřednictvím Azure Resource Manager volání přes protokol HTTPS. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

