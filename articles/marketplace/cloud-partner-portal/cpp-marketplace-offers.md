---
title: Nabídky Azure a AppSource Marketplace
description: Vytváření a Správa nabídek Azure a AppSource Marketplace
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: dsindona
ms.openlocfilehash: 7f6fd723355426a49cff032d51da0e09f13e295d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278480"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Nabídky Azure a AppSource Marketplace

V této první části této části se seznámíte s obecnými operacemi, které se používají k vytváření a správě nabídek pro Azure a AppSource Marketplace.  Tato část poskytuje pozadí, které potřebujete pochopit pro správu konkrétních typů nabídek, a také technické informace, které jsou společné pro všechny typy nabídek.  Většina této části obsahuje podrobné pokyny k vytvoření a správě konkrétních typů nabídek.  

Následující video přináší různé možnosti a různé typy nabídek, které jsou k dispozici v Azure Marketplace nebo AppSource.  Zahrnuje také důležité technické a obchodní aspekty publikování aplikace nebo služby na těchto tržištích.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Sestavování aplikací a služeb pro Azure Marketplace a AppSource-Build 2018**

Další informace o těchto tržištích najdete v článku [Průvodce publikováním Azure Marketplace a AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Běžné operace nabídky

Proces vytvoření nové nabídky se výrazně liší v různých typech nabídek, například mezi [nabídkou aplikací Azure](./azure-applications/cpp-azure-app-offer.md) a [nabídkou konzultační služby](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  Naproti tomu mnoho dalších operací, které provádíte na nabídce v [portál partnerů cloudu](https://cloudpartner.azure.com) , jsou poměrně standardizované napříč různými typy nabídek.  Tyto běžné operace – včetně publikování, zobrazení stavu, aktualizace a odstranění – jsou uvedené v části [Správa nabídek](./manage-offers/cpp-manage-offers.md) .


## <a name="test-drive"></a>Testovací verze

*Test Drive* je funkce Marketplace, která zákazníkům poskytuje možnost "vyzkoušet si", jak si můžete koupit pro každou nabídku.  Funkce testovacích jednotek je omezená na následující podmnožinu typů nabídek: [aplikace Azure](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), [Dynamics 365 for customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md), [Dynamics 365 pro finance a provoz](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [aplikace SaaS](./saas-app/cpp-saas-offer.md)a [virtuální počítače](./virtual-machine/cpp-virtual-machine-offer.md).  Tato funkce vyžaduje, aby Vydavatel vytvořil šablonu testovacího disku přizpůsobenou pro svou nabídku.  Další informace najdete v části [testovací jednotka](./test-drive/what-is-test-drive.md).

Pomocí [filtru testovacích jednotek](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive)můžete procházet stávající nabídky na webu Marketplace, které obsahují ukázky zkušebních jednotek. 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Typy nabídek Azure Marketplace a AppSource

V následující tabulce jsou uvedeny typy aktuálních nabídek, které [portál partnerů cloudu](https://cloudpartner.azure.com)podporuje.  Pro každý typ nabídky se zobrazí seznam webů, na které se nabídka dá uvést, a také obecný popis technologie řešení nabídky.

|                Typ nabídky                |  Marketplace  |   Popis                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Aplikace Azure](./azure-applications/cpp-azure-app-offer.md) | Azure | Řešení se skládá z jednoho nebo více virtuálních počítačů (VM), volitelného vlastního kódu Azure nasazeného pomocí šablony Azure Resource Manageru.  Nasazení může provést buď zákazník prostřednictvím šablony řešení, nebo spravovaná vydavatelem. Tento typ slouží k poskytnutí větší flexibility než poskytnutého typu nabídky virtuálního počítače.  |
| [Konzultační služba](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | protokoly | Konzultanti kvalifikované pro společnost Microsoft mohou vypsat své služby specifické pro doménu buď v Azure Marketplace nebo AppSource.  Jejich odborné znalosti pomáhají zákazníkům vyhodnotit své problémy a vytvářet a nasazovat správná řešení pro splnění jejich obchodních cílů.  |
| [Kontejner](./containers/cpp-containers-offer.md)  | Azure | Řešení je image kontejneru Docker zřízená jako služba založená na Kubernetes nebo služby Azure Container Instances. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Balíček, který rozšiřuje toto plánování podnikových zdrojů (ERP) a systému pro správu podnikových prostředků. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Balíček, který rozšiřuje tento systém správy zákaznických prostředků (CRM) prostřednictvím svých modulů Sales, Service, Project Service a Field Service  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Balíček, který rozšiřuje tuto službu pro plánování podnikových zdrojů (ERP), která podporuje pokročilé finanční operace, provoz, výrobní prostředky a řízení dodavatelských řetězců |
| [Modul IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Kontejner kompatibilní s Docker, který běží na zařízení IoT Edge.  Skládá se z malých výpočetních modulů, které používají kombinaci vlastního kódu, dalších služeb Azure a služeb třetích stran. |
| [Aplikace Power BI](./power-bi/cpp-power-bi-offer.md) | AppSource | Aplikace Power BI, která zabalí přizpůsobitelný Power BI obsah, včetně datových sad, sestav a řídicích panelů |
| [Aplikace SaaS](./saas-app/cpp-saas-offer.md) | Azure | Řešení je předplatné typu software jako služba, které spravuje Vydavatel a kteří se přihlásí pomocí přizpůsobeného rozhraní, které používá Azure Active Directory. |
| [Virtuální počítač](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Řešení je obsaženo v jednom virtuálním počítači nasazeném do předplatného zákazníka.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Další informace najdete v tématu [Průvodce publikováním podle typu nabídky](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Další kroky

Dozvíte se o obecných operacích, které můžete provádět na nabídkách Marketplace a jejich běžných technických atributech a prostředcích v článku [Správa nabídek](./manage-offers/cpp-manage-offers.md).
