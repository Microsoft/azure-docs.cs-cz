---
title: Porovnání služby Azure Stack a global Azure | Dokumentace Microsoftu
description: Zjistěte, jak společnost Microsoft poskytuje Azure a Azure Stack řady služeb v ekosystému Azure
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 5e871d467fec476f1dac77fb879d180ea2322c80
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650079"
---
# <a name="differences-between-global-azure-azure-stack-and-azure-stack-hci"></a>Rozdíly mezi globální Azure, Azure Stack a Azure Stack HCL

Společnost Microsoft poskytuje Azure a Azure Stack řady služeb v ekosystému Azure. Použijte stejný aplikační model, samoobslužné portály a rozhraní API pomocí Azure Resource Manageru k zajištění cloudové možnosti, jestli vaší firmě používá globální Azure nebo místním prostředkům.

Tento článek popisuje globální funkce Azure, Azure Stack a Azure Stack HCL a poskytuje běžné scénáře doporučení, která vám pomůže zajistit nejlepší volbou pro doručování cloudových služeb Microsoftu pro vaši organizaci.

![Přehled ekosystému Azure](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>Globální Azure

Microsoft Azure je neustále se rozšiřující sada cloudových služeb, které pomáhají vaší organizaci překonávat překážky v podnikání. Nabízejí svobodu při sestavování, správě a nasazování aplikací v rozsáhlé globální síti pomocí oblíbených nástrojů a architektur.

Global Azure nabízí více než 100 služeb dostupných v 54 oblastech po celém světě. Nejaktuálnější seznam globálních služeb Azure, najdete v článku [ *dostupné produkty v jednotlivých oblastech*](https://azure.microsoft.com/regions/services). Služby jsou dostupné v Azure jsou uvedené podle kategorie, také určuje, zda jsou obecně dostupné nebo jsou k dispozici ve verzi preview.

Další informace o globální služby Azure najdete v tématu [Začínáme s Azure](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1).

## <a name="azure-stack"></a>Azure Stack

Azure Stack je rozšířením Azure, které přináší flexibilitu a inovace cloud computingu do místního prostředí. Nasazená místně, Azure Stack slouží k poskytování služeb Azure konzistentní vzhledem k aplikacím, které buď připojené k Internetu (a Azure) nebo v odpojených prostředích bez připojení k Internetu. Azure Stack používá stejný základní technologie jako globální Azure, která zahrnuje základní komponenty infrastruktury as-a-Service (IaaS), Software-as-a-Service (SaaS), a volitelné funkce Platform-as-a-Service (PaaS), včetně:

- Virtuální počítače Azure pro Windows a Linux
- Azure Web Apps a Functions
- Azure Key Vault
- Azure Resource Manager
- Azure Marketplace
- Containers
- Azure IoT Hub a Event Hubs
- Nástroje pro správu (plány, nabídky, RBAC, atd.)

Možnosti modelu PaaS služby Azure Stack jsou volitelné, protože Azure Stack není provozované společností Microsoft – je provozována společností naše zákazníky. To znamená, že můžete nabídnout libovolné služby PaaS chcete koncovým uživatelům, pokud jste připraveni abstraktní základní infrastrukturu a procesy od koncového uživatele. Azure Stack však zahrnuje několik volitelných poskytovatelů služeb PaaS včetně služby App Service, databází SQL a databáze MySQL. Toto jsou dodané jako poskytovatele prostředků, takže jsou připravená více tenantů, aktualizované v čase standardu, který aktualizace služby Azure Stack, nezobrazuje na portálu Azure Stack a dobře integrovaná s Azure Stack.

Kromě poskytovatelů prostředků je popsáno výše, jsou k dispozici další služby PaaS k dispozici a otestované jako [řešení založené na šablonách Azure Resource Manageru](https://github.com/Azure/AzureStack-QuickStart-Templates) , na kterých běží v IaaS, ale jako může operátor Azure Stack nabízí je jako Služby PaaS vašim uživatelům, včetně:

- Service Fabric
- Služby kontejnerů Kubernetes
- IoT Hub a centra událostí
- Etherium Blockchain
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack"></a>Vzorové případy použití pro službu Azure Stack:

- Finanční modelování
- Lékařské a deklarace dat.
- Analýzy zařízení IoT
- Optimalizace sortiment maloobchodního prodeje
- Optimalizace dodavatelský řetězec
- Průmyslové IoT
- Prediktivní údržba
- Inteligentní Město
- Angažovanosti občanů

Další informace o službě Azure Stack na [co je Azure Stack](azure-stack-overview.md).

## <a name="azure-stack-hci"></a>HCI služby Azure Stack 

Řešení Azure Stack HCL umožňují spouštět virtuální počítače v místním a snadno připojit k Azure pomocí hyperkonvergovaného řešení infrastruktury (HCL). Sestavujte a spouštějte cloudové aplikace s využitím konzistentních služeb Azure v místním prostředí a zajistěte splnění legislativních a technických požadavků. Kromě spuštěné virtualizované aplikace místně umožňuje Azure Stack HCL nahradit a konsolidovat stárnoucích serverové infrastruktury a připojení k Azure pro cloudové služby pomocí Windows Admin Center.

Azure Stack HCL poskytuje ověřené řešení HCL používá technologii Hyper-V a prostory úložiště – přímé s Windows serverem. 2019 softwarově definované datového centra (SDDC). Windows Admin Center se používá ke správě a integrovaný přístup ke službám Azure, jako:

- Azure Backup
- Azure Site Recovery
- Azure Monitor a aktualizace

Aktualizovaný seznam Azure služeb, které se můžete připojit HCL Azure Stack, přejděte k části [připojení Windows serveru do služby Azure hybrid](https://docs.microsoft.com/windows-server/azure-hybrid-services/index).

### <a name="example-use-cases-for-azure-stack-hci"></a>Vzorové případy použití pro Azure Stack HCL
- Vzdálené nebo pobočkové systémy office
- Konsolidace datových center
- Virtual desktop Infrastructure
- Důležité obchodní informace infrastruktury
- Nízkonákladové úložiště
- Vysoká dostupnost a zotavení po havárii v cloudu
- Podnikové aplikace, jako je SQL Server

Přejděte [HCL Azure Stack web](https://azure.microsoft.com/overview/azure-stack/hci/) zobrazíte 70 Azure Stack HCL řešení aktuálně k dispozici od partnerů Microsoftu.

## <a name="next-steps"></a>Další postup

[Základy správy Azure Stack](azure-stack-manage-basics.md)

[Rychlý start: použití portálu pro správu služby Azure Stack](azure-stack-manage-portals.md)
