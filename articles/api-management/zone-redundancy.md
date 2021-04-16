---
title: Podpora zón dostupnosti pro Azure API Management
description: Naučte se zlepšit odolnost instance služby Azure API Management v oblasti tím, že povolíte redundanci zóny.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 817aebab6af8de59071b5d767b24d15cf46d59d9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559048"
---
# <a name="availability-zone-support-for-azure-api-management"></a>Podpora zón dostupnosti pro Azure API Management 

Tento článek popisuje, jak povolit redundanci zóny pro vaši instanci API Management pomocí Azure Portal. [Redundance zóny](../availability-zones/az-overview.md#availability-zones) zajišťuje odolnost a vysokou dostupnost instance služby v konkrétní oblasti Azure (umístění). V případě redundance zóny se brána a řídicí plocha vaší instance API Management (rozhraní API pro správu, portál pro vývojáře, konfigurace Gitu) replikují napříč datovými centry v fyzicky oddělených zónách, což je odolné vůči selhání zóny. 

API Management podporuje i [nasazení ve více oblastech](api-management-howto-deploy-multi-region.md), což pomáhá snižovat latenci žádosti vnímaný geograficky distribuovanými spotřebiteli rozhraní API a vylepšuje dostupnost komponenty brány, pokud je jedna oblast offline. Kombinace zón dostupnosti pro redundanci v rámci oblasti a nasazení ve více oblastech pro zlepšení dostupnosti brány v případě, že dojde k oblastnímu výpadku, pomáhá zvýšit spolehlivost a výkon vaší API Management instance.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>Podporované oblasti

Konfigurace API Management pro redundanci zóny se v současnosti podporuje v následujících oblastech Azure.

* Austrálie – východ
* Brazílie – jih
* Střední Kanada
* Indie – střed
* East US
* USA – východ 2
* Francie – střed
* Japonsko – východ
* Středojižní USA
* Southeast Asia
* Spojené království – jih
* Západní USA 2
* Západní USA 3

## <a name="prerequisites"></a>Požadavky

* Pokud jste ještě nevytvořili instanci služby API Management, přečtěte si téma [vytvoření instance služby API Management](get-started-create-service-instance.md). Vyberte úroveň služby Premium.
* Pokud je vaše instance API Management nasazená ve [virtuální síti](api-management-using-with-vnet.md), ujistěte se, že jste nastavili virtuální síť, podsíť a veřejnou IP adresu v jakémkoli novém umístění, kde chcete povolit redundanci zóny.

## <a name="enable-zone-redundancy---portal"></a>Povolit redundanci zóny – portál

Na portálu můžete volitelně povolit redundanci zóny, když přidáte umístění do služby API Management nebo aktualizujete konfiguraci existujícího umístění.

1. V Azure Portal přejděte do služby API Management a v nabídce vyberte **umístění** .
1. Vyberte existující umístění nebo v horním panelu vyberte **+ Přidat** . Umístění musí [podporovat zóny dostupnosti](#supported-regions).
1. Vyberte počet **[jednotek](upgrade-and-scale.md)** škálování v umístění.
1. V **oblasti dostupnosti** vyberte jednu nebo více zón. Počet vybraných jednotek se musí rovnoměrně rozmístit napříč zónami dostupnosti. Pokud jste například vybrali 3 jednotky, vyberte 3 zóny, aby každá zóna byla hostitelem jedné jednotky.
1. Pokud je instance API Management nasazena ve [virtuální síti](api-management-using-with-vnet.md), nakonfigurujte nastavení virtuální sítě v umístění. Vyberte existující virtuální síť, podsíť a veřejnou IP adresu, která je k dispozici v umístění.
1. Vyberte **použít** a pak vyberte **Uložit**.

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="Povolení zónové redundance":::

> [!IMPORTANT]
> Veřejná IP adresa v umístění se změní, když povolíte, přidáte nebo odeberete zóny dostupnosti. Při aktualizaci zón dostupnosti v oblasti s nastavením sítě je nutné nakonfigurovat jiný prostředek veřejné IP adresy, než který jste nastavili dříve.

> [!NOTE]
> Použití změny instance API Management může trvat 15 až 45 minut.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [nasazení instance služby Azure API Management do více oblastí Azure](api-management-howto-deploy-multi-region.md).
* Redundanci zóny můžete taky povolit pomocí [šablony Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-api-management-simple-zones).
* Přečtěte si další informace o [službách Azure, které podporují zóny dostupnosti](../availability-zones/az-region.md).
* Přečtěte si další informace o sestavování a [spolehlivosti](/azure/architecture/framework/resiliency/overview) v Azure.