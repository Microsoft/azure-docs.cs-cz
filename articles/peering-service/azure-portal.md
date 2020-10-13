---
title: Registrovat službu Azure peering Service – Azure Portal
description: Naučte se registrovat službu Azure peering Service pomocí Azure Portal
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 62210105ba4ba84cff4b246c5b7cf8cc794fb5e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534943"
---
# <a name="register-peering-service-by-using-the-azure-portal"></a>Registrace služby partnerského vztahu pomocí Azure Portal

Azure peering Service je síťová služba, která vylepšuje připojení zákazníků ke cloudovým službám Microsoftu, jako jsou Microsoft 365, Dynamics 365, služby software jako služba (SaaS), Azure nebo jakékoli služby Microsoftu přístupné prostřednictvím veřejného Internetu.

V tomto článku se dozvíte, jak zaregistrovat připojení ke službě peering Service pomocí Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) hned teď.

> 

## <a name="prerequisites"></a>Požadavky

Musíte mít následující:

### <a name="azure-account"></a>Účet Azure

Musíte mít platný a aktivní účet Microsoft Azure. Tento účet je nutný k nastavení připojení služby partnerského vztahu. Peering Service je prostředek v rámci předplatných Azure. 

### <a name="connectivity-provider"></a>Poskytovatel připojení

Můžete pracovat s partnerem poskytovatele internetových služeb nebo s internetovým Exchangem a získat službu peering Service pro připojení vaší sítě k síti Microsoftu.

Ujistěte se, že jsou [poskytovatelé připojení](location-partners.md) partneři s Microsoftem.



## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V prohlížeči přejdete na Azure Portal a přihlásíte se pomocí svého účtu Azure.

## <a name="register-a-peering-service-connection"></a>Registrace připojení služby Peering Service

1. Pokud chcete zaregistrovat připojení ke službě peering Service, vyberte **vytvořit službu pro vytváření**  >  **partnerských vztahů**k prostředkům.

    ![Registrace služby Peering Service](./media/peering-service-portal/peering-servicecreate.png)
1. Na kartě **základy** na stránce **vytvořit připojení ke službě peering Service** zadejte následující podrobnosti.

 
1. Vyberte předplatné a skupinu prostředků, která je přidružená k předplatnému.

   ![Registrovat kartu Basic partnerského vztahu](./media/peering-service-portal/peering-servicebasics.png)

1. Zadejte **název** , na který by měla být zaregistrovaná instance služby partnerského vztahu.
 
1. Nyní vyberte tlačítko **Další: Konfigurace** v dolní části stránky. Zobrazí se stránka **Konfigurace** .

## <a name="configure-the-peering-service-connection"></a>Konfigurace připojení služby peering Service

1. Na stránce **Konfigurace** vyberte umístění, do kterého musí být služba partnerského vztahu povolená, a to tak, že vyberete stejnou možnost z rozevíracího seznamu **umístění služby partnerského vztahu** .

1. Vyberte poskytovatele služeb, ze kterého se má služba partnerského vztahu získat, a to tak, že v rozevíracím seznamu **poskytovatele partnerské služby** vyberete název poskytovatele.
 
1. V dolní části oddílu **předpony** vyberte **vytvořit novou předponu** a zobrazí se textová pole. Nyní zadejte název prostředku předpony a předpony, které jsou přidruženy k poskytovateli služeb.

1. Vyberte **klíč předpony** a přidejte klíč předpony, který vám dal poskytovatel (ISP nebo IXP). Tento klíč umožňuje společnosti Microsoft ověřit předponu a poskytovatele, kteří mají přidělenou předponu IP.
   > ![Snímek obrazovky se zobrazí karta konfigurace na stránce vytvořit připojení ke službě partnerského vztahu, kde můžete zadat klíč předpony.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. V levém dolním rohu stránky vyberte tlačítko **Revize + vytvořit** . Zobrazí se stránka **Revize + vytvořit** a Azure ověří vaši konfiguraci.
    

1. Až se zobrazí zpráva s **potvrzením úspěšného ověření** , vyberte **vytvořit**.

   > ![Snímek obrazovky s kartou pro vytváření připojení ke službě peering Service se zobrazí na stránce pro kontrolu a vytvoření.](./media/peering-service-portal/peering-service-prefix.png)


1. Po registraci připojení ke službě peering Service se pro zahrnuté předpony provede další ověřování. Stav ověření můžete zkontrolovat v části **předpony** názvu prostředku. Pokud se ověření nepovede, zobrazí se jedna z následujících chybových zpráv:

   - Neplatná předpona služby partnerského vztahu, předpona by měla být platný formát, podporuje se jenom předpona IPv4.
   - Od poskytovatele služby partnerského vztahu nebyla přijata předpona.
   - Oznámení předpony nemá platnou komunitu BGP a poskytovatele služby partnerského partnerského vztahu.
   - Trasa pro zálohování se nenašla, kontaktujte poskytovatele služby partnerského vztahu.
   - Předpona přijata s delším jako cesta, poskytovatel služby partnerského vztahu se obrátí.
   - Předpona přijatá s soukromou jako v cestě, od poskytovatele služby partnerského vztahu.

### <a name="add-or-remove-a-prefix"></a>Přidání nebo odebrání předpony

Vyberte **Přidat předpony** na stránce **předpony** a přidejte tak předpony.

Vyberte tři tečky (...) vedle uvedené předpony a vyberte možnost **Odstranit** .

### <a name="delete-a-peering-service-connection"></a>Odstranění připojení ke službě peering Service

Na stránce **všechny prostředky** zaškrtněte políčko u služby partnerského vztahu a v horní části stránky vyberte možnost **Odstranit** .

> [!NOTE]
> Existující předponu nelze upravit.
>

## <a name="next-steps"></a>Další kroky

- Další informace o připojení ke službě peering Service najdete v tématu [připojení ke službě peering Service](connection.md).
- Další informace o telemetrie připojení ke službě peering Service najdete v tématu [telemetrie připojení ke službě peering Service](connection-telemetry.md).
- Měření telemetrie najdete v tématu [měření telemetrie připojení](measure-connection-telemetry.md).
- Pokud chcete připojení zaregistrovat pomocí Azure PowerShell, přečtěte si téma [registrace připojení služby peering Service-Azure PowerShell](powershell.md).
- Informace o registraci připojení pomocí rozhraní příkazového řádku Azure najdete v tématu [registrace připojení ke službě peering Service – Azure CLI](cli.md).
