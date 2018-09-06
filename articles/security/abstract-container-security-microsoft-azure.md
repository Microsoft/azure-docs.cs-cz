---
title: Abstraktní kontejneru zabezpečení v Microsoft Azure
description: Abstrakce pro zabezpečení kontejnerů v dokumentu white paper Microsoft Azure.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 2ce3aec3b7a588076584ebdf400f8cafcdeb02fe
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "43842923"
---
# <a name="container-security-in-microsoft-azure"></a>Zabezpečení kontejneru ve službě Microsoft Azure
## <a name="abstract"></a>Abstraktní

Technologie kontejnerů je způsobí strukturální změnu ve světě cloud computingu. Kontejnery umožňují spouštět více instancí aplikace do jedné instance operačního systému, tím více efektivní využívání prostředků. Kontejnery poskytnout organizace konzistence a flexibilita. Umožňují průběžné nasazování, protože aplikace vyvinuté na pracovní ploše, otestovat ve virtuálním počítači a pak nasadí pro produkční prostředí v cloudu. Kontejnery poskytuje flexibilitu, efektivnější provoz, škálovatelnost a snížit tak náklady z důvodu optimalizace prostředků.

Protože je relativně nové technologie kontejnerů, mají mnoho Odborníci v oblasti IT zabezpečením chybějící viditelnost a využití v produkčním prostředí. Vývojové týmy se často vědět o osvědčené postupy zabezpečení. Tento dokument white paper může pomoct zabezpečení provozní týmy a vývojáři při výběru přístupy k zabezpečení kontejnerů vývoj a nasazení na platformě Microsoft Azure.

Tento dokument popisuje, kontejnery, nasazování kontejnerů a správy a služeb pro nativní platformy. Také popisuje problémy se zabezpečením modulu runtime, které vznikají s využitím kontejnerů na platformě Azure. V obrázcích a příklady, tento dokument se zaměřuje na Dockeru jako kontejner modelu a Kubernetes ve formě orchestrátor kontejnerů. Většina doporučení zabezpečení platí také pro jiné modelů kontejnerů od partnerů Microsoftu na platformě Azure.

[Stáhněte si dokument white paper](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)