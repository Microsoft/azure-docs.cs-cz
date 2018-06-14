---
title: 'Vytvoření a úprava okruhu ExpressRoute: portálu Azure | Microsoft Docs'
description: Tento článek popisuje, jak vytvořit, zřízení, ověřte, aktualizovat, odstranit a zrušit jejich zřízení okruhu ExpressRoute.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 68d59d59-ed4d-482f-9cbc-534ebb090613
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2017
ms.author: cherylmc;ganesr
ms.openlocfilehash: be97f441d6eb2e97d85d598b0ed66718aec97b14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29389508"
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Vytvoření a úprava okruhu ExpressRoute
> [!div class="op_single_selector"]
> * [portál Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video – portál Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Tento článek popisuje postup vytvoření okruhu Azure ExpressRoute pomocí webu Azure portal a modelu nasazení Azure Resource Manager. Následující kroky také ukazují zkontrolujte stav okruhu, aktualizaci, nebo odstranit a zrušit jejich zřízení se.


## <a name="before-you-begin"></a>Než začnete
* Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních](expressroute-workflows.md) před zahájením konfigurace.
* Zajistěte, abyste měli přístup k [portál Azure](https://portal.azure.com).
* Ujistěte se, zda máte oprávnění k vytvoření nových síťových prostředků. Pokud nemáte správná oprávnění, obraťte se na správce účtu.
* Můžete [zobrazit video](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) před zahájením pro lepší pochopení kroků.

## <a name="create"></a>Vytvořit a zřídit okruhu ExpressRoute
### <a name="1-sign-in-to-the-azure-portal"></a>1. Přihlášení k webu Azure Portal
V prohlížeči přejděte na web [Azure Portal](http://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Vytvořit nový okruh ExpressRoute.
> [!IMPORTANT]
> Váš okruh ExpressRoute je účtován od okamžiku, kdy se objeví klíč služby. Ujistěte se, při provádění této operace, pokud poskytovatel připojení je připraven ke zřízení okruhu.
> 
> 

1. Okruh ExpressRoute můžete vytvořit tak, že vyberete možnost vytvořit nový prostředek. Klikněte na tlačítko **vytvořit prostředek** > **sítě** > **ExpressRoute**, jak je znázorněno na následujícím obrázku:

  ![Vytvoření okruhu ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Po kliknutí na tlačítko **ExpressRoute**, uvidíte **okruh ExpressRoute vytvořit** stránky. Když jste vyplnění hodnoty na této stránce, ujistěte se, že jste zadali správný SKU vrstvy (Standard nebo Premium) a data měření fakturační model (neomezený nebo Metered).

  ![Konfigurace SKU vrstvy a měření dat.](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

  * **Úroveň** Určuje, zda je povoleno ExpressRoute standard nebo doplněk ExpressRoute premium. Můžete zadat **standardní** získat standardní SKU nebo **Premium** pro doplněk premium.
  * **Měření dat** Určuje typ fakturace. Můžete zadat **Metered** pro plán měření podle objemu dat a **neomezený** pro plán neomezená data na úrovni. Můžete změnit typ fakturace z **Metered** k **neomezený**, ale nemůžete změnit typ z **neomezený** k **Metered**.
  * **Umístění partnerského vztahu** je fyzického umístění, kde jste partnerského vztahu se společností Microsoft.

    > [!IMPORTANT]
    > Určuje umístění partnerského vztahu [fyzické umístění](expressroute-locations.md) kde jste partnerského vztahu se společností Microsoft. Toto je **není** propojené s vlastností "Umístění", která odkazuje na geography, kde se nachází na poskytovatele síťové prostředků Azure. Při nesouvisí, je vhodné vybrat poskytovatele síťových prostředků geograficky v blízkosti umístění partnerského vztahu okruhu.
    >
    >

### <a name="3-view-the-circuits-and-properties"></a>3. Zobrazit okruhy a vlastnosti
**Zobrazit všechny okruhy**

Můžete zobrazit všechny okruhů, které jste vytvořili výběrem **všechny prostředky** v levé nabídce.

![Okruhy zobrazení](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Zobrazit vlastnosti**

Vlastnosti okruhu můžete zobrazit výběrem. Na **přehled** stránky pro váš okruh klíče služby se zobrazí v poli klíče služby. Musíte zkopírovat klíč služby pro váš okruh a předejte ji do poskytovatele služeb k dokončení procesu zřizování. Klíč služby okruhů je specifická pro váš okruh.

![Zobrazení vlastností](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Klíč služby poslat svého poskytovatele připojení pro zřizování
Na této stránce **stav zprostředkovatele** poskytuje informace o aktuálním stavu zřizování na straně poskytovatele služeb. **Stav okruhu** poskytuje stav na straně společnosti Microsoft. Další informace o zřizování stavy okruhu najdete v tématu [pracovních](expressroute-workflows.md#expressroute-circuit-provisioning-states) článku.

Když vytvoříte nový okruh ExpressRoute, okruh je v následujícím stavu:

Stav zprostředkovatele: není zajišťováno<BR>
Stav okruhu: povoleno

![Zahájení procesu zřizování](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Okruh změní na následující stav po probíhá povolení můžete poskytovatele připojení:

Stav zprostředkovatele: zřizování<BR>
Stav okruhu: povoleno

Abyste mohli používat okruh ExpressRoute musí být v následujícím stavu:

Stav zprostředkovatele: zřídit<BR>
Stav okruhu: povoleno

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Pravidelně kontrolovat stav a stav okruhu klíče
Můžete zobrazit vlastnosti okruhu, který máte zájem výběrem. Zkontrolujte **stav zprostředkovatele** a ujistěte se, že se přesunul na **zajištěno** než budete pokračovat.

![Stav okruhu a zprostředkovatele](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Vytvořte vlastní konfiguraci směrování
Podrobné pokyny najdete v části [konfigurace směrování pro okruh ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) článek k vytvoření a úpravám partnerských vztahů pro okruh.

> [!IMPORTANT]
> Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb, které nabízejí vrstvy 2 připojení služby. Pokud používáte poskytovatele služeb, který nabízí spravované vrstvy 3 služby (obvykle virtuální privátní síť IP, např. MPLS), poskytovatel připojení konfiguruje a spravuje směrování.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Propojení virtuální sítě k okruhu ExpressRoute
V dalším kroku propojení virtuální sítě k okruhu ExpressRoute. Použití [propojování virtuálních sítí pro okruhy ExpressRoute](expressroute-howto-linkvnet-arm.md) článek při práci s modelem nasazení Resource Manager.

## <a name="status"></a>Získávání stav okruhu ExpressRoute
Stav okruhu můžete zobrazit tak, že ho vyberete a zobrazení stránky přehled. 

## <a name="modify"></a>Úprava okruhu ExpressRoute
Bez dopadu na připojení můžete upravit některé vlastnosti okruhu ExpressRoute. Můžete upravit fakturační model šířky pásma, SKU a povolit klasické operace na **konfigurace** stránky. Informace o omezení a omezení, najdete v článku [ExpressRoute – nejčastější dotazy](expressroute-faqs.md). 

Můžete provádět následující úlohy bez výpadků:

* Povolit nebo zakázat doplněk ExpressRoute Premium pro váš okruh ExpressRoute.
* Zadaný na tomto portu je dostupná kapacita, zvětšete šířku pásma okruhu ExpressRoute. Přechod na starší verzi šířku pásma okruhu není podporována. 
* Změnit plán měření z *– měření podle objemu dat* k *neomezená Data na úrovni*. Změna plánu měření z neomezená Data – měření podle objemu dat není podporována.
* Můžete povolit nebo zakázat *povolit klasické operace*.

> [!IMPORTANT]
> Možná budete muset znovu vytvořit okruh ExpressRoute, pokud je nedostatečné kapacity na existující port. Pokud v tomto umístění není k dispozici žádné další kapacitu, nelze upgradovat okruh.
>
> I když můžete plynule upgradovat šířku pásma, nejde snížit šířku pásma okruhu ExpressRoute bez přerušení. Přechod na starší verzi šířky pásma vyžaduje zrušení zřízení okruh ExpressRoute a pak znova nezajistíte nové okruh ExpressRoute.
> 
> Zákaz operace rozšíření Premium může selhat, pokud používáte prostředky, které jsou větší než co je povoleno pro standardní okruh.
> 
> 

Chcete-li upravit okruh ExpressRoute, klikněte na tlačítko **konfigurace**.

![Úprava okruhu](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)




## <a name="delete"></a>Zrušení zřízení a odstraňování okruhu ExpressRoute
Váš okruh ExpressRoute můžete odstranit výběrem **odstranit** ikonu. Vezměte na vědomí následující informace:

* Je nutné zrušit všechny virtuální sítě od okruhu ExpressRoute. Pokud se tato operace nezdaří, zkontrolujte, zda jsou všechny virtuální sítě propojené ke okruh.
* Pokud je poskytovatel služby okruh ExpressRoute Stav zřizování **zřizování** nebo **zajištěno** , musíte pracovat se svým poskytovatelem služeb pro zrušení zřízení okruhu na jejich straně. Abychom mohli pokračovat a rezervovat prostředky vám účtovat, dokud poskytovatele služeb dokončení zrušení okruhu a upozorní nás.
* Pokud má poskytovatel služeb zrušit okruhu (poskytovatele služeb Stav zřizování je nastavena na **není zajišťováno**), můžete odstranit okruh. To zastaví fakturace pro okruh.

## <a name="next-steps"></a>Další postup
Po vytvoření okruhu, pokračujte následujícími další kroky:

* [Vytvoření a úprava směrování pro okruhu ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-arm.md)
