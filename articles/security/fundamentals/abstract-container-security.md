---
title: Zabezpečení z abstraktního kontejneru v Microsoft Azure
description: Abstrakce pro zabezpečení kontejneru v Microsoft Azure dokument White Paper.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.openlocfilehash: b70744f403c483448a844d3f3decf6ce26f48f06
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727785"
---
# <a name="container-security-in-microsoft-azure"></a>Zabezpečení kontejneru v Microsoft Azure
## <a name="abstract"></a>Abstraktní

Technologie kontejnerů způsobuje strukturální změnu na světě cloudového computingu. Kontejnery umožňují spouštět více instancí aplikace v jediné instanci operačního systému, a to efektivněji pomocí prostředků. Kontejnery poskytují konzistenci a flexibilitu organizací. Umožňují průběžné nasazování, protože aplikaci je možné vyvíjet na ploše, testovat na virtuálním počítači a potom nasadit pro produkční prostředí v cloudu. Kontejnery poskytují flexibilitu, zjednodušené operace, škálovatelnost a snížení nákladů z důvodu optimalizace prostředků.

Vzhledem k tomu, že je technologie kontejneru relativně novinkou, mnoho IT specialistů má obavy z hlediska zabezpečení při nedostatku viditelnosti a využití v produkčním prostředí. Vývojové týmy často neznají osvědčené postupy zabezpečení. Tento dokument White Paper vám může pomáhat týmům a vývojářům v provozu při výběru přístupů k zabezpečení vývoje a nasazení kontejnerů na platformě Microsoft Azure.

Tento dokument popisuje kontejnery, nasazování a správu kontejnerů a služby nativních platforem. Popisuje také problémy zabezpečení modulu runtime, které vznikají při použití kontejnerů na platformě Azure. V obrázcích a příkladech se tento dokument zaměřuje na Docker jako model kontejneru a Kubernetes jako na kontejner Orchestrator. Většina doporučení týkajících se zabezpečení se vztahuje také na jiné modely kontejnerů od partnerů Microsoftu na platformě Azure.

[Stáhnout dokument White Paper](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)