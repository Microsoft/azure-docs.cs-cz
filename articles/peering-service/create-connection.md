---
title: 'Služba partnerského vztahu Azure: vytvořit '
description: V tomto kurzu se dozvíte, jak zaregistrovat službu partnerského vztahu Azure a předponu.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/2/2020
ms.author: derekol
Customer intent: With Azure Peering service enhancing the customer connectivity to Microsoft cloud services .
ms.openlocfilehash: f8f0ee71a8dd00498d868bb6dc11b3fa083df766
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530208"
---
# <a name="tutorial-create-a-peering-service-connection"></a>Kurz: vytvoření připojení ke službě peering Service

V tomto kurzu se dozvíte, jak vytvořit prostředek služby partnerského vztahu a jak nakonfigurovat připojení ke službě peering Service. 

1. Pokud chcete zaregistrovat připojení ke službě peering Service, vyberte **vytvořit službu pro vytváření**  >  **partnerských vztahů**k prostředkům.

 
    ![Registrace služby Peering Service](./media/peering-service-portal/peering-servicecreate.png)

2. Na kartě **základy** na stránce **vytvořit připojení ke službě peering Service** zadejte následující podrobnosti.
 
3. Vyberte předplatné a skupinu prostředků, která je přidružená k předplatnému.

    ![Registrovat kartu pro základní službu peering Service](./media/peering-service-portal/peering-servicebasics.png)

4. Zadejte **název** , na který by měla být zaregistrovaná instance služby partnerského vztahu.

5. Nyní vyberte tlačítko **Další: Konfigurace** v dolní části stránky. Zobrazí se stránka **Konfigurace** .
## <a name="configure-the-peering-service-connection"></a>Konfigurace připojení služby peering Service

1. Na stránce **Konfigurace** vyberte umístění, do kterého musí být služba partnerského vztahu povolená, a to tak, že vyberete stejnou možnost z rozevíracího seznamu **umístění služby partnerského vztahu** .

1. Vyberte poskytovatele služeb, ze kterého se má služba partnerského vztahu získat, a to tak, že v rozevíracím seznamu **poskytovatele partnerské služby** vyberete název poskytovatele.
 
1. V dolní části oddílu **předpony** vyberte **vytvořit novou předponu** a zobrazí se textová pole. Nyní zadejte název prostředku předpony a předpony, které jsou přidruženy k poskytovateli služeb.

1. Vyberte **klíč předpony** a přidejte klíč předpony, který vám dal poskytovatel (ISP nebo IXP). Tento klíč umožňuje společnosti Microsoft ověřit předponu a poskytovatele, kteří mají přidělenou předponu IP.

    ![Snímek obrazovky se zobrazí karta konfigurace na stránce vytvořit připojení ke službě partnerského vztahu, kde můžete zadat klíč předpony.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. V levém dolním rohu stránky vyberte tlačítko **Revize + vytvořit** . Zobrazí se stránka **Revize + vytvořit** a Azure ověří vaši konfiguraci.

 1. Až se zobrazí zpráva s **potvrzením úspěšného ověření** , vyberte **vytvořit**.

> ![Snímek obrazovky s kartou pro vytváření připojení ke službě peering Service se zobrazí na stránce pro kontrolu a vytvoření.](./media/peering-service-portal/peering-service-prefix.png)

1. Po registraci připojení ke službě peering Service se pro zahrnuté předpony provede další ověřování. Stav ověření můžete zkontrolovat v části **předpony** názvu prostředku. Pokud se ověření nepovede, zobrazí se jedna z následujících chybových zpráv:

   - Neplatná předpona služby partnerského vztahu, předpona by měla být platný formát, podporuje se jenom předpona IPv4.
   - Od poskytovatele služby partnerského vztahu nebyla přijata předpona.
   - Oznámení předpony nemá platnou komunitu BGP, kontaktujte prosím poskytovatele partnerské služby.
   - Trasa pro zálohování se nenašla, kontaktujte prosím poskytovatele partnerské služby.
   - Předpona se přijala s delším jako cesta, kontaktujte prosím poskytovatele partnerské služby.
   - Předpona přijatá s privátní jako v cestě, kontaktujte prosím poskytovatele partnerské služby.

### <a name="add-or-remove-a-prefix"></a>Přidání nebo odebrání předpony

Vyberte **Přidat předpony** na stránce **předpony** a přidejte tak předpony.

Vyberte tři tečky (...) vedle uvedené předpony a vyberte možnost **Odstranit** .

### <a name="delete-a-peering-service-connection"></a>Odstranění připojení ke službě peering Service

Na stránce **všechny prostředky** zaškrtněte políčko u služby partnerského vztahu a v horní části stránky vyberte možnost **Odstranit** .
## <a name="next-steps"></a>Další kroky

- Další informace o připojení ke službě peering Service najdete v tématu [připojení ke službě peering Service](connection.md).
- Další informace o telemetrie připojení ke službě peering Service najdete v tématu [telemetrie připojení ke službě peering Service](connection-telemetry.md).
- Měření telemetrie najdete v tématu [měření telemetrie připojení](measure-connection-telemetry.md).
- Pokud chcete připojení zaregistrovat pomocí Azure PowerShell, přečtěte si téma [registrace připojení služby peering Service-Azure PowerShell](powershell.md).
- Informace o registraci připojení pomocí rozhraní příkazového řádku Azure najdete v tématu [registrace připojení ke službě peering Service – Azure CLI](cli.md).