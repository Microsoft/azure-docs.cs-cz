---
title: Nasazení SAP IDES EHP7 SP3 pro SAP ERP 6.0 v Azure | Dokumenty společnosti Microsoft
description: Nasazení SAP IDES EHP7 SP3 pro SAP ERP 6.0 v Azure
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 3efd92226b7c69590f3960458ffec49b63b8364f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616693"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Nasazení SAP IDES EHP7 SP3 pro SAP ERP 6.0 v Azure
Tento článek popisuje, jak nasadit systém SAP IDES se systémem SQL Server a operační systém Windows v Azure prostřednictvím SAP Cloud Appliance Library (SAP CAL) 3.0. Snímky obrazovky ukazují postup ný krok za krokem. Chcete-li nasadit jiné řešení, postupujte stejným způsobem.

Chcete-li začít s SAP CAL, přejděte na web [knihovny cloudových spotřebičů SAP.](https://cal.sap.com/) SAP má také blog o nové [knihovně SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> května 2017 můžete kromě méně upřednostňovaného klasického modelu nasazení k nasazení sap cal použít model nasazení Azure Resource Manageru. Doporučujeme použít nový model nasazení Resource Manager a ignorovat klasický model nasazení.

Pokud jste již vytvořili účet SAP CAL, který používá klasický model, *musíte vytvořit jiný účet SAP CAL*. Tento účet musí být nasazen výhradně do Azure pomocí modelu Správce prostředků.

Po přihlášení k sap cal vás první stránka obvykle přenese na stránku **Řešení.** Řešení nabízená na SAP CAL se neustále zvyšují, takže možná budete muset trochu posunout, abyste našli řešení, které chcete. Zvýrazněné řešení SAP IDES založené na systému Windows, které je dostupné výhradně v Azure, demonstruje proces nasazení:

![Řešení SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Vytvoření účtu v sap cal
1. Chcete-li se poprvé přihlásit k sap cal, použijte SAP S-User nebo jiného uživatele registrovaného u SAP. Pak definujte účet SAP CAL, který používá SAP CAL k nasazení zařízení v Azure. V definici účtu je třeba:

    a. Vyberte model nasazení v Azure (Správce prostředků nebo klasické).

    b. Zadejte předplatné Azure. Účet SAP CAL lze přiřadit pouze k jednomu předplatnému. Pokud potřebujete více než jedno předplatné, musíte vytvořit jiný účet SAP CAL.
    
    c. Udělit SAP CAL oprávnění k nasazení do předplatného Azure.

   > [!NOTE]
   >  Další kroky ukazují, jak vytvořit účet SAP CAL pro nasazení Správce prostředků. Pokud již máte účet SAP CAL, který je propojen s klasickým modelem nasazení, *musíte* vytvořit nový účet SAP CAL následujícím postupem. Nový účet SAP CAL je třeba nasadit v modelu Resource Manager.

1. Chcete-li vytvořit nový účet SAP CAL, stránka **Účty** zobrazuje dvě možnosti pro Azure: 

    a. **Microsoft Azure (klasický)** je klasický model nasazení a už není upřednostňovaný.

    b. **Microsoft Azure** je nový model nasazení Správce prostředků.

    ![Účty SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Pokud chcete nasadit v modelu Správce prostředků, vyberte **Microsoft Azure**.

    ![Účty SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Zadejte **ID předplatného** Azure, které se najdete na webu Azure Portal. 

    ![ID předplatného SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Pokud chcete autorizovat nasazení sap cal do definovaného předplatného Azure, klikněte na **Autorizovat**. Na kartě prohlížeče se zobrazí následující stránka:

    ![Přihlášení ke cloudovým službám Internet Exploreru](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Pokud je v seznamu uvedeno více uživatelů, zvolte účet Microsoft, který je propojený, aby byl spolusprávcem vybraného předplatného Azure. Na kartě prohlížeče se zobrazí následující stránka:

    ![Potvrzení cloudových služeb aplikace Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Klikněte na **Přijmout**. Pokud je autorizace úspěšná, definice účtu SAP CAL se znovu zobrazí. Po krátké době zpráva potvrzuje, že proces autorizace byl úspěšný.

1. Chcete-li uživateli přiřadit nově vytvořený účet SAP CAL, zadejte **své ID uživatele** do textového pole vpravo a klepněte na tlačítko **Přidat**. 

    ![Přidružení účtu k uživateli](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Pokud chcete přidružit svůj účet k uživateli, kterého používáte k přihlášení k programu SAP CAL, klikněte na **Zkontrolovat**. 

1. Chcete-li vytvořit přidružení mezi uživatelem a nově vytvořeným účtem SAP CAL, klepněte na tlačítko **Vytvořit**.

    ![Přidružení uživatele k účtu](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Úspěšně jste vytvořili účet SAP CAL, který je schopen:

- Použijte model nasazení Resource Manager.
- Nasaďte systémy SAP do svého předplatného Azure.

> [!NOTE]
> Před nasazením řešení SAP IDES na základě Windows a SQL Server, možná budete muset zaregistrovat předplatné SAP CAL. V opačném případě se řešení může zobrazit jako **Uzamčeno** na stránce přehledu.

### <a name="deploy-a-solution"></a>Nasazení řešení
1. Po nastavení účtu SAP CAL vyberte řešení SAP IDES v řešení **Windows a SQL Server.** Klikněte na **Vytvořit instanci**a potvrďte použití a podmínky. 

1. V **základním režimu: Vytvořit instanci,** musíte:

    a. Zadejte **název**instance .

    b. Vyberte **oblast**Azure . Možná budete potřebovat předplatné SAP CAL, abyste získali více oblastí Azure nabízených.

    c.  Zadejte hlavní **heslo** pro řešení, jak je znázorněno na obrázku:

    ![SAP CAL Základní režim: Vytvořit instanci](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Klikněte na **Vytvořit**. Po určité době, v závislosti na velikosti a složitosti řešení (SAP CAL poskytuje odhad), je stav zobrazen jako aktivní a připravený k použití: 

    ![Instance SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Pokud chcete najít skupinu prostředků a všechny její objekty, které byly vytvořeny pomocí SAP CAL, přejděte na portál Azure. Virtuální počítač lze nalézt počínaje stejným názvem instance, který byl uveden v SAP CAL.

    ![Objekty skupiny prostředků](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. Na portálu SAP CAL přejděte na nasazené instance a klikněte na **připojit**. Zobrazí se následující vyskakovací okno: 

    ![Připojení k instanci](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Než se budete moci připojit k nasazeným systémům pomocí jedné z možností, klepněte na tlačítko **Příručka Začínáme**. Dokumentace pojmenuje uživatele pro každou metodu připojení. Hesla pro tyto uživatele jsou nastavena na hlavní heslo, které jste definovali na začátku procesu nasazení. V dokumentaci jsou uvedeni další funkční uživatelé se svými hesly, která můžete použít k přihlášení k nasazenému systému.

    ![Uvítací dokumentace SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Během několika hodin se v Azure nasadí zdravý systém SAP IDES.

Pokud jste si zakoupili předplatné SAP CAL, SAP plně podporuje nasazení prostřednictvím SAP CAL v Azure. Fronta podpory je BC-VCM-CAL.

