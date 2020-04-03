---
title: Azure Security Center a Azure Container Registry
description: Informace o integraci Azure Security Center s Azure Container Registry
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 1c1b48d3715d838827f88f99fc0849d25677fdcc
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585741"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Integrace registru kontejnerů Azure s Centrem zabezpečení

Azure Container Registry (ACR) je spravovaná soukromá služba registru Dockeru, která ukládá a spravuje ibi kontejnerů pro nasazení Azure v centrálním registru. Je založen na open source registru Docker 2.0.

Pokud jste na standardní úrovni Azure Security Center, můžete přidat balíček registrů kontejnerů. Tato volitelná funkce přináší hlubší přehled o zranitelnosti obrázků ve vašich registrech založených na ARM. Povolte nebo zakažte balíček na úrovni předplatného tak, aby pokrýval všechny registry v předplatném. Tato funkce se účtuje za obrázek, jak je znázorněno na [stránce s cenami](security-center-pricing.md). Povolením balíčku registrů kontejnerů zajistíte, že Centrum zabezpečení je připraveno ke skenování bitových kopií, které se dostanou do registru. 

Při každém zasunutí bitové kopie do registru centrum zabezpečení tuto bitovou kopii automaticky prohledá. Chcete-li spustit prohledávací snímek obrázku, zatlačte jej do úložiště.

Po dokončení skenování (obvykle po přibližně 10 minutách) jsou zjištění k dispozici v doporučeních Centra zabezpečení, jako je tato:

[![Ukázkové doporučení Centra zabezpečení Azure týkající se chyb zabezpečení zjištěných v hostované bitové kopii registru kontejnerů Azure (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Výhody integrace

Security Center identifikuje registry ACR založené na ARM ve vašem předplatném a bezproblémově poskytuje:

* **Azure-nativní vyhledávání zranitelnosti** pro všechny nabízené bitové kopie Linuxu. Security Center skenuje obrázek pomocí skeneru od předního dodavatele pro hledáčky zranitelnosti, Qualys. Toto nativní řešení je ve výchozím nastavení bezproblémově integrováno.

* **Bezpečnostní doporučení** pro bitové kopie Linuxu se známými chybami zabezpečení. Centrum zabezpečení obsahuje podrobnosti o každé hlášené chybě zabezpečení a klasifikaci závažnosti. Kromě toho poskytuje pokyny, jak napravit konkrétní chyby zabezpečení nalezené na každé bitové kopii tlačil do registru.

![Přehled na vysoké úrovni Azure Security Center a Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení kontejnerů centra Security Center najdete v tématu:

* [Azure Security Center a zabezpečení kontejnerů](container-security.md)

* [Integrace se službou Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Ochrana virtuálního počítače](security-center-virtual-machine-protection.md) – popisuje doporučení Centra zabezpečení.