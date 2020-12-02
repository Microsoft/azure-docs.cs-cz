---
title: Průvodce publikováním podle typu nabídky – komerční tržiště Microsoftu
description: Tento článek popisuje typy nabídek, které jsou k dispozici na komerčním webu Microsoft Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 10/06/2020
ms.openlocfilehash: 2191a4df5b319ec16a4a6116aa99cfac50c87d9b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462919"
---
# <a name="publishing-guide-by-offer-type"></a>Průvodce publikováním podle typu nabídky

Tento článek popisuje typy nabídek, které jsou k dispozici na komerčním webu Marketplace. *Typ nabídky* definuje strukturu nabídky, která zahrnuje metadata, artefakty a další obsah prezentované na komerčním webu Marketplace.

Až se [rozhodnete pro možnost publikování](determine-your-listing-type.md), musíte před zahájením vytváření nabídky v partnerském centru zvolit typ nabídky. Typ nabídky bude odpovídat typu řešení, aplikace nebo nabídky služeb, které chcete publikovat, a také jeho zarovnání na produkty a služby společnosti Microsoft.

Jeden typ nabídky můžete nakonfigurovat různými způsoby, jak povolit různé možnosti publikování, možnosti výpisu, zřizování nebo ceny. Možnost publikování a konfigurace typu nabídky také odpovídají nárokům na nabídku a technické požadavky.

Před vytvořením vaší nabídky si přečtěte požadavky na online obchod a typ nabídky a požadavky na technickou podporu publikování.

## <a name="list-of-offer-types"></a>Seznam typů nabídek

V následující tabulce jsou uvedeny typy nabídek z komerčního tržiště v partnerském centru.

| **Typ nabídky**    | **Popis**  |
| :------------------- | :-------------------|
| [**Azure Application**](plan-azure-application-offer.md) | Existují dva druhy plánů aplikací Azure: _Šablona řešení_ a _spravovaná aplikace_. Oba typy plánů podporují automatizaci nasazení a konfigurace řešení mimo jeden virtuální počítač (VM). Proces poskytování více prostředků, včetně virtuálních počítačů, sítí a prostředků úložiště, můžete automatizovat, abyste mohli poskytovat složitá řešení, například IaaS řešení. Oba typy plánů můžou využívat mnoho různých druhů prostředků Azure, mimo jiné jenom virtuální počítače.<ul><li>Plány **šablon řešení** jsou jedním z hlavních způsobů publikování řešení na komerčním webu Marketplace. Plány šablon řešení se nedají v komerčním tržišti použít, ale můžou se používat k nasazení placených nabídek virtuálních počítačů, které se účtují prostřednictvím komerčního tržiště. Typ plánu šablony řešení použijte, když zákazník bude spravovat řešení a transakce se účtují prostřednictvím jiného plánu.</li><br><li>Plány **spravovaných aplikací** umožňují snadno sestavovat a doručovat plně spravované aplikace klíč pro vaše zákazníky. Mají stejné možnosti jako plány šablon řešení s některými klíčovými rozdíly:</li><ul><li> Prostředky se nasazují do skupiny prostředků a spravují se vydavatelem aplikace. Skupina prostředků se nachází v rámci předplatného zákazníka, ale má k ní přístup určitá identita v rámci tenanta vydavatele.</li><li>Jako vydavatel zadáte náklady na průběžnou podporu řešení a transakce se podporují prostřednictvím komerčního tržiště.</li></ul>Typ plánu spravované aplikace použijte, když vy nebo váš zákazník požadujete, aby bylo řešení spravované partnerem, nebo když nasadíte řešení založené na předplatném.</ul> |
| [**Kontejner Azure**](marketplace-containers.md) | Pokud je vaše řešení image kontejneru Docker zřízená jako Kubernetes Azure Container Service, použijte typ nabídky kontejneru Azure. |
| [**Virtuální počítač Azure**](marketplace-virtual-machines.md) | Typ nabídky virtuálního počítače použijte, když nasadíte virtuální zařízení do předplatného přidruženého k vašemu zákazníkovi. |
| [**Konzultační služba**](./plan-consulting-service-offer.md) | Konzultační služby usnadňují propojení zákazníků se službami pro podporu a rozšiřování jejich používání služeb Azure, Dynamics 365 nebo Power Suite.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | Můžete publikovat nabídky AppSource, které sestavují nebo rozšířily Dynamics 365 Business Central, Dynamics 365 Customer Engagement, Power Apps a finance a provozní aplikace.|
| [**Modul IoT Edge**](iot-edge-module.md) | Azure IoT Edge moduly jsou nejmenší výpočetní jednotky spravované IoT Edge a můžou obsahovat služby Microsoftu (například Azure Stream Analytics), služby třetích stran nebo vlastní kód specifický pro řešení. |
| [**Spravovaná služba**](partner-center-portal/create-new-managed-service-offer.md) | Můžete vytvářet nabídky spravované služby a spravovat předplatné nebo skupiny prostředků delegované zákazníky prostřednictvím [Azure Lighthouse](../lighthouse/overview.md).|
| [**Aplikace Power BI** <br/> **Microsoft 365**](appsource-offer-publishing-guide.md) | Můžete publikovat AppSource nabídky, které sestaví nebo rozšířily Power BI a Microsoft 365.|
| [**Software jako služba**](plan-saas-offer.md) | Pomocí typu nabídky software jako služba (SaaS) můžete zákazníkům umožnit, aby si koupili své technické řešení založené na SaaS jako předplatné. Informace o požadavcích na jednotné přihlašování pro nabídky SaaS najdete v tématu [Azure AD a SaaS nabídky na komerčním webu Marketplace](azure-ad-saas.md). |


## <a name="next-steps"></a>Další kroky

- Pokud chcete dokončit výběr a konfiguraci vaší nabídky, Projděte si požadavky na způsobilost v příslušném článku pro váš typ nabídky.
- Seznamte se se vzorem publikování pro každé online úložiště, kde najdete příklady, jak vaše řešení mapuje typ a konfiguraci nabídky.