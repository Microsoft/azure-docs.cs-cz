---
title: Nabídky Azure a AppSource Marketplace
description: Vytváření a správa nabídek azure a appsource tržiště
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: dsindona
ms.openlocfilehash: 7f6fd723355426a49cff032d51da0e09f13e295d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278480"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Nabídky Azure a AppSource Marketplace

Tato první část této části představuje obecné operace používané k vytváření a správě nabídek pro Azure a AppSource Marketplaces.  Tato část obsahuje pozadí, které potřebujete pochopit ke správě konkrétních typů nabídek, stejně jako technické informace, které jsou společné pro všechny typy nabídek.  Většina této části obsahuje podrobné pokyny, jak vytvořit a spravovat konkrétní typy nabídek.  

Následující video představuje různé možnosti a různé typy nabídek, které jsou dostupné na Azure Marketplace nebo AppSource.  Zahrnuje také důležité technické a obchodní aspekty publikování aplikace nebo služby na těchto tržištích.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Vytváření aplikací a služeb pro Azure Marketplace a AppSource – sestavení 2018**

Další informace o těchto tržištích najdete v [tématu Azure Marketplace a AppSource publikování průvodce](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Operace společné nabídky

Proces vytváření nové nabídky se v jednotlivých typech nabídek značně liší, například mezi [nabídkou aplikací Azure](./azure-applications/cpp-azure-app-offer.md) a [nabídkou konzultačních služeb](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  Naproti tomu mnoho dalších operací, které provádíte v nabídce na [portálu partnerů cloudu,](https://cloudpartner.azure.com) je napříč typy nabídek poměrně standardizováno.  Tyto běžné operace – včetně publikování, stavu zobrazení, aktualizace a odstranění – jsou zahrnuty v části [Správa nabídek.](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Testovací verze

*Test Drive* je funkce marketplace, která poskytuje zákazníkům možnost předvádění před zakoupením pro každou takto povolenou nabídku.  Funkce Test Drive je omezena na následující podmnožinu typů nabídek: [aplikace Azure](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), Dynamics [365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md), Dynamics [365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [SaaS aplikace](./saas-app/cpp-saas-offer.md)a virtuální [počítače](./virtual-machine/cpp-virtual-machine-offer.md).  Tato funkce vyžaduje, aby vydavatel vytvořil šablonu testovací jednotky přizpůsobenou pro svou nabídku.  Další informace naleznete v části [Test Drive](./test-drive/what-is-test-drive.md).

Můžete procházet existující nabídky marketplace, které mají ukázky test drive použitím [filtru testovací jednotky](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive). 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Typy nabídek Azure Marketplace a AppSource

V následující tabulce jsou uvedeny aktuální typy nabídek podporované [portálem partnerů cloudu](https://cloudpartner.azure.com).  Pro každý typ nabídky uvádí tržiště, kde může být nabídka uvedena, a také obecný popis technologie řešení nabídky.

|                Typ nabídky                |  Marketplace  |   Popis                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Aplikace Azure](./azure-applications/cpp-azure-app-offer.md) | Azure | Řešení se skládá z jednoho nebo více virtuálních počítačů (VM), volitelného vlastního kódu Azure, nasazeného prostřednictvím šablony Azure Resource Manger.  Nasazení může být buď zákazníkem prostřednictvím šablony řešení nebo spravované vydavatelem. Tento typ se používá k zajištění větší flexibility než za předpokladu, že typ nabídky virtuálního počítače.  |
| [Konzultační servis](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | oba | Konzultanti s kvalifikací microsoftu můžou uvádět své služby specifické pro doménu na Azure Marketplace nebo AppSource.  Jejich odbornost pomáhá zákazníkům posoudit jejich problémy a vytvářet a nasazovat správná řešení pro splnění jejich obchodních cílů.  |
| [Kontejner](./containers/cpp-containers-offer.md)  | Azure | Řešení je image kontejneru Dockeru zřízená jako služba založená na Kubernetes nebo azure container instance. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Balíček, který rozšiřuje tento plán zdrojů organizace (ERP) a systém řízení podniku. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Balíček, který rozšiřuje tento systém správy zdrojů zákazníka (CRM) prostřednictvím modulů prodeje, služeb, projektových služeb a servisních služeb v terénu  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Balíček, který rozšiřuje tuto službu plánování podnikových zdrojů (ERP), která podporuje pokročilé financování, provoz, výrobu a řízení dodavatelského řetězce |
| [Modul IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Kontejner kompatibilní s Dockerem, který běží na zařízení IoT Edge.  Skládá se z malých výpočetních modulů, které používají kombinaci vlastního kódu, dalších služeb Azure a služeb třetích stran. |
| [Aplikace Power BI](./power-bi/cpp-power-bi-offer.md) | AppSource | Aplikace Power BI, která balí přizpůsobitelný obsah Power BI včetně datových sad, sestav a řídicích panelů |
| [Aplikace SaaS](./saas-app/cpp-saas-offer.md) | Azure | Řešení je předplatné softwaru jako služby spravované vydavatelem, které uživatelé přihlašují prostřednictvím přizpůsobeného rozhraní, které používá službu Azure Active Directory. |
| [Virtuální počítač](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Řešení je obsaženo v rámci jednoho virtuálního počítače nasazeného do předplatného zákazníka.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Další informace naleznete v [tématu Publishing guide by offer type](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Další kroky

O obecných operacích, které můžete provádět na tržištích, se dozvíte v článku [Správa nabídek](./manage-offers/cpp-manage-offers.md).
