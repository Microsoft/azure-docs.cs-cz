---
title: Nabídky Azure a AppSource Marketplace
description: Vytváření a Správa nabídek Azure a AppSource Marketplace
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: pabutler
ms.openlocfilehash: 746b1b51d593b21bdf85bca4eeb75c135196093a
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962857"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Nabídky Azure a AppSource Marketplace

V této první části této části se seznámíte s obecnými operacemi, které se používají k vytváření a správě nabídek pro Azure a AppSource Marketplace.  Tato část poskytuje pozadí, které potřebujete pochopit pro správu konkrétních typů nabídek, a také technické informace, které jsou společné pro všechny typy nabídek.  Většina této části obsahuje podrobné pokyny k vytvoření a správě konkrétních typů nabídek.  

Následující video přináší různé možnosti a různé typy nabídek, které jsou k dispozici v Azure Marketplace nebo AppSource.  Zahrnuje také důležité technické a obchodní aspekty publikování aplikace nebo služby na těchto tržištích.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Sestavování aplikací a služeb pro Azure Marketplace a AppSource-Build 2018**

Další informace o těchto tržištích najdete v článku [Průvodce publikováním Azure Marketplace a AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Běžné operace nabídky

Proces vytvoření nové nabídky se výrazně liší v různých typech nabídek, například mezi [nabídkou aplikací Azure](./azure-applications/cpp-azure-app-offer.md) a [nabídkou konzultační služby](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  Naproti tomu mnoho dalších operací, které provádíte na nabídce v [portál partnerů cloudu](https://cloudpartner.azure.com) , jsou poměrně standardizované napříč různými typy nabídek.  Tyto běžné operace – včetně publikování, zobrazení stavu, aktualizace a odstranění – jsou uvedené v části [Správa nabídek](./manage-offers/cpp-manage-offers.md) .


## <a name="test-drive"></a>Test Drive

*Test Drive* je funkce Marketplace, která zákazníkům poskytuje možnost "vyzkoušet si", jak si můžete koupit pro každou nabídku.  Funkce testovacích jednotek je omezená na následující podmnožinu typů nabídek: [Aplikace Azure](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), [Dynamics 365 for customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md), [Dynamics 365 pro finance a provoz](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [SaaS aplikace](./saas-app/cpp-saas-offer.md)a [virtuální počítače](./virtual-machine/cpp-virtual-machine-offer.md).  Tato funkce vyžaduje, aby Vydavatel vytvořil šablonu testovacího disku přizpůsobenou pro svou nabídku.  Další informace najdete v části [testovací jednotka](./test-drive/what-is-test-drive.md).

Pomocí [filtru testovacích jednotek](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive)můžete procházet stávající nabídky na webu Marketplace, které obsahují ukázky zkušebních jednotek. 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Typy nabídek Azure Marketplace a AppSource

V následující tabulce jsou uvedeny typy aktuálních nabídek, které [portál partnerů cloudu](https://cloudpartner.azure.com)podporuje.  Pro každý typ nabídky se zobrazí seznam webů, na které se nabídka dá uvést, a také obecný popis technologie řešení nabídky.

|                Typ nabídky                |  Marketplace  |   Popis                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Aplikace Azure](./azure-applications/cpp-azure-app-offer.md) | Azure | Řešení se skládá z jednoho nebo více virtuálních počítačů (VM), volitelného vlastního kódu Azure nasazeného pomocí šablony Azure Resource Manageru.  Nasazení může provést buď zákazník prostřednictvím šablony řešení, nebo spravovaná vydavatelem. Tento typ slouží k poskytnutí větší flexibility než poskytnutého typu nabídky virtuálního počítače.  |
| [Konzultační služba](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | protokoly | Konzultanti kvalifikované pro společnost Microsoft mohou vypsat své služby specifické pro doménu buď v Azure Marketplace nebo AppSource.  Jejich odborné znalosti pomáhají zákazníkům vyhodnotit své problémy a vytvářet a nasazovat správná řešení pro splnění jejich obchodních cílů.  |
| [Kontejner](./containers/cpp-containers-offer.md)  | Azure | Řešení je image kontejneru Docker zřízená jako služba založená na Kubernetes nebo služby Azure Container Instances. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Balíček, který rozšiřuje toto plánování podnikových zdrojů (ERP) a systému pro správu podnikových prostředků. |
| [Dynamics 365 pro zákaznickou zapojení](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Balíček, který rozšiřuje tento systém správy zákaznických prostředků (CRM) prostřednictvím svých modulů Sales, Service, Project Service a Field Service  |
| [Dynamics 365 pro finance a operace](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Balíček, který rozšiřuje tuto službu pro plánování podnikových zdrojů (ERP), která podporuje pokročilé finanční operace, provoz, výrobní prostředky a řízení dodavatelských řetězců |
| [Modul IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Kontejner kompatibilní s Docker, který běží na zařízení IoT Edge.  Skládá se z malých výpočetních modulů, které používají kombinaci vlastního kódu, dalších služeb Azure a služeb třetích stran. |
| [Aplikace Power BI](./power-bi/cpp-power-bi-offer.md) | AppSource | Aplikace Power BI, která zabalí přizpůsobitelný Power BI obsah, včetně datových sad, sestav a řídicích panelů |
| [Aplikace SaaS](./saas-app/cpp-saas-offer.md) | Azure | Řešení je předplatné typu software jako služba, které spravuje Vydavatel a kteří se přihlásí pomocí přizpůsobeného rozhraní, které používá Azure Active Directory. |
| [Virtuální počítač](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Řešení je obsaženo v jednom virtuálním počítači nasazeném do předplatného zákazníka.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Další informace najdete v tématu [Průvodce publikováním podle typu nabídky](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Další kroky

Dozvíte se o obecných operacích, které můžete provádět na nabídkách Marketplace a jejich běžných technických atributech a prostředcích v článku [Správa nabídek](./manage-offers/cpp-manage-offers.md).
