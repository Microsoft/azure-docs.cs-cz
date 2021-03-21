---
title: 'Rychlý Start: směrování na sdílené služby pomocí šablony ARM'
titleSuffix: Azure Virtual WAN
description: V tomto rychlém startu se dozvíte, jak nastavit trasy pro přístup k virtuální síti s sdílenou službou s úlohou, ke které má každá virtuální síť a větev přístup pomocí šablony Azure Resource Manager (šablona ARM).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: subject-armqs
ms.openlocfilehash: f325dd445e778bf03049d2c9e2e00fed7a427ccf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443942"
---
# <a name="quickstart-route-to-shared-services-vnets-using-an-arm-template"></a>Rychlý Start: směrování na sdílené služby virtuální sítě pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak použít šablonu Azure Resource Manager (šablonu ARM) k nastavení tras pro přístup ke sdílené virtuální síti služby s úlohami, ke kterým chcete každou virtuální síť a větev (VPN/ER/P2S) získat přístup. Příklady těchto sdílených úloh můžou zahrnovat virtuální počítače se službami, jako jsou řadiče domény nebo sdílené složky, nebo služby Azure exponované interně prostřednictvím [privátního koncového bodu Azure](../private-link/private-endpoint-overview.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f301-virtual-wan-with-route-tables%2fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* Pro tuto konfiguraci se vyžadují data certifikátu veřejného klíče. Ukázková data jsou uvedená v článku. Ukázková data jsou však k dispozici pouze pro splnění požadavků šablony, aby bylo možné vytvořit bránu P2S. Po dokončení šablony a nasazení prostředků musíte aktualizovat toto pole vlastními daty certifikátu, aby konfigurace fungovala. Viz [certifikáty VPN uživatele](certificates-point-to-site.md#cer).

## <a name="review-the-template"></a><a name="review"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/301-virtual-wan-with-route-tables). Šablona pro tento článek je moc dlouhá, takže se tady nedá zobrazit. Chcete-li zobrazit šablonu, přečtěte si téma [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/301-virtual-wan-with-route-tables/azuredeploy.json).

V tomto rychlém startu vytvoříte nasazení Azure Virtual WAN s více rozbočovači, včetně všech bran a připojení virtuální sítě. Seznam vstupních parametrů byl pro určitý účel uchováván minimálně. Schéma přidělování IP adres lze změnit úpravou proměnných uvnitř šablony. Scénář je podrobněji vysvětlen v článku [scénář: virtuální síť sdílené služby](scenario-shared-services-vnet.md) .

:::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Architektura nasazení" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

Tato šablona vytvoří plně funkční prostředí Azure Virtual WAN s následujícími prostředky:

* 2 odlišná centra v různých oblastech.
* 4 virtuální sítě Azure (VNet).
* 2 připojení k virtuální síti pro každé centrum VWan
* Jedna brána sítě VPN typu Point-to-Site (P2S) v každém centru.
* 1 brána sítě VPN typu Site-to-Site (S2S) v každém centru.
* 1 brána ExpressRoute v každém centru.
* Vlastní směrovací tabulky RT_SHARED v každém centru.
* LBL_RT_SHARED popisku pro seskupení RT_SHARED směrovacích tabulek.

V šabloně je definováno víc prostředků Azure:

* [**Microsoft. Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Microsoft. Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Microsoft. Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/hubvirtualnetworkconnections**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Microsoft. Network/hubroutetables**](/azure/templates/microsoft.network/virtualhubs/hubRouteTables)
* [**Microsoft. Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Microsoft. Network/vpngateways**](/azure/templates/microsoft.network/vpngateways)
* [**Microsoft. Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Microsoft. Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnServerConfigurations)

>[!NOTE]
> Tato šablona ARM nevytváří prostředky na straně zákazníka vyžadované pro hybridní připojení. Po nasazení šablony stále potřebujete vytvořit a nakonfigurovat klienty VPN P2S, větve sítě VPN (místní lokality) a připojení okruhů ExpressRoute.
>

Další šablony najdete v tématu [šablony rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a><a name="deploy"></a>Nasazení šablony

Chcete-li tuto šablonu nasadit správně, je nutné použít tlačítko k nasazení do Azure Button a Azure Portal místo jiných metod, a to z následujících důvodů:

* Aby bylo možné vytvořit konfiguraci P2S, je třeba nahrát data kořenového certifikátu. Datové pole nepřijímá data certifikátu při použití PowerShellu nebo rozhraní příkazového řádku.
* Tato šablona nefunguje správně pomocí Cloud Shell z důvodu odeslání dat certifikátu.
* Kromě toho můžete snadno upravit šablonu a parametry na portálu tak, aby vyhovovaly rozsahům IP adres a dalším hodnotám.

1. Klikněte na **Nasadit do Azure**.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f301-virtual-wan-with-route-tables%2fazuredeploy.json)
1. Chcete-li zobrazit šablonu, klikněte na tlačítko **Upravit šablonu**. Na této stránce můžete upravit některé z hodnot, jako je adresní prostor nebo název určitých prostředků. **Uložením** uložte změny, nebo **zahoďte** změny.
1. Na stránce Šablona zadejte hodnoty. Pro tuto šablonu se vyžadují data veřejného certifikátu P2S. Pokud tento článek používáte jako cvičení, můžete jako ukázková data pro obě centra použít následující data z tohoto souboru. cer. Po spuštění šablony a dokončení nasazení, aby bylo možné použít konfiguraci P2S, je nutné nahradit tyto informace [daty certifikátu](certificates-point-to-site.md#cer) veřejného klíče pro vaše vlastní nasazení.

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. Po dokončení zadávání hodnot vyberte **zkontrolovat + vytvořit**.
1. Na stránce **Revize + vytvořit** klikněte po úspěšném ověření na **vytvořit**.
1. Dokončení nasazení trvá přibližně 75 minut. Průběh můžete zobrazit na stránce **Přehled** šablon. Při zavření portálu bude nasazení pokračovat.

   :::image type="content" source="./media/quickstart-route-shared-services-template/template.png" alt-text="Příklad nasazení je dokončený.":::

## <a name="validate-the-deployment"></a><a name="validate"></a>Ověření nasazení

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V levém podokně vyberte **skupiny prostředků** .
1. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Na stránce **Přehled** se zobrazí podobný příklad jako v tomto příkladu: :::image type="content" source="./media/quickstart-route-shared-services-template/resources.png" alt-text="Příklad prostředků" lightbox="./media/quickstart-route-shared-services-template/resources.png":::

1. Kliknutím na virtuální síť WAN zobrazíte centra. Na stránce virtuální síť WAN klikněte na každé centrum, abyste zobrazili připojení a další informace o centru.
   :::image type="content" source="./media/quickstart-route-shared-services-template/hub.png" alt-text="Příklad Center" lightbox="./media/quickstart-route-shared-services-template/hub.png":::

## <a name="complete-the-hybrid-configuration"></a><a name="complete"></a>Dokončení hybridní konfigurace

Šablona nekonfiguruje všechna nastavení potřebná pro hybridní síť. V závislosti na vašich požadavcích musíte dokončit následující konfigurace a nastavení.

* [Konfigurace větví sítě VPN – místní lokality](virtual-wan-site-to-site-portal.md#site)
* [Dokončete konfiguraci sítě VPN P2S.](virtual-wan-point-to-site-portal.md)
* [Připojit okruhy ExpressRoute](virtual-wan-expressroute-portal.md)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili, odstraňte je. Některé virtuální prostředky sítě WAN je potřeba z důvodu závislostí odstranit v určitém pořadí. Dokončení odstranění může trvat přibližně 30 minut.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Dokončete konfiguraci sítě VPN P2S.](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [Konfigurace větví sítě VPN – místní lokality](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [Připojit okruhy ExpressRoute](virtual-wan-expressroute-portal.md)
