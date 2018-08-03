---
title: Nasazení SAP IDES EHP7 SP3 pro SAP ERP 6.0 v Azure | Dokumentace Microsoftu
description: Nasazení SAP IDES EHP7 SP3 pro SAP ERP 6.0 v Azure
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 93824c8f0e7667fcb58fd6b8292cddfa2b4a482a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441455"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Nasazení SAP IDES EHP7 SP3 pro SAP ERP 6.0 v Azure
Tento článek popisuje, jak nasadit systém SAP IDES provozu s využitím SQL serveru a operačního systému Windows v Azure prostřednictvím SAP Cloud Appliance Library (SAP CAL) 3.0. Snímky obrazovky zobrazit podrobný proces. Pokud chcete nasadit jiné řešení, postupujte podle stejných kroků.

Pokud chcete začít se SAP CAL, přejděte na [SAP Cloud Appliance Library](https://cal.sap.com/) webu. SAP má také blogu o nové [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
Od 29. května 2017 můžete kromě modelu nasazení classic méně než upřednostňovaný model nasazení Azure Resource Manageru pro nasazení SAP CAL. Doporučujeme, abyste pomocí nového modelu nasazení Resource Manager a modelu nasazení classic ignorovat.

Pokud jste již vytvořili SAP CAL účtu, který se používá v případě klasického modelu *musíte vytvořit jiný účet SAP CAL*. Tento účet musí výhradně pomocí modelu Resource Manageru nasadit do Azure.

Po přihlášení k SAP CAL na první stránce obvykle povede k **řešení** stránky. Řešení nabídnutých v řešení SAP CAL neustále roste, takže možná budete muset posunout odlišují najít řešení, které chcete. Zvýrazněný založené na Windows SAP IDES řešení, která je dostupná výhradně na Azure znázorňuje proces nasazení:

![Řešení SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Vytvoření účtu ve službě SAP CAL
1. Přihlaste se k SAP CAL poprvé, použijte S SAP – uživatel nebo jiný uživatel zaregistrován s řešením SAP. Potom definujte SAP CAL účtu, který používá SAP CAL pro nasazení zařízení v Azure. V definici účtu budete muset:

    a. Vyberte model nasazení v Azure (Resource Manager nebo classic).

    b. Zadejte předplatné Azure. Pouze k jednomu předplatnému můžete přiřadit účet SAP CAL. Pokud potřebujete více než jedno předplatné, musíte vytvořit jiný účet SAP CAL.
    
    c. Udělit oprávnění SAP CAL pro nasazení do vašeho předplatného Azure.

    > [!NOTE]
    Následující kroky ukazují, jak vytvořit účet SAP CAL pro nasazení Resource Manager. Pokud už máte účet SAP CAL, který je propojený s modelem nasazení classic můžete *potřebovat* postupovat podle těchto kroků a vytvořte nový účet SAP CAL. Nový účet SAP CAL je potřeba nasadit v modelu Resource Manager.

1. Chcete-li vytvořit nový účet SAP CAL **účty** stránky zobrazí dvě možnosti pro Azure: 

    a. **Microsoft Azure (klasické)** je model nasazení classic a už není upřednostňovaný.

    b. **Microsoft Azure** je nový model nasazení Resource Manageru.

    ![Účty SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Pokud chcete nasadit v modelu Resource Manager, vyberte **Microsoft Azure**.

    ![Účty SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Zadejte Azure **ID předplatného** , který najdete na webu Azure portal. 

    ![ID předplatného SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. K autorizaci SAP CAL pro nasazení do předplatného Azure, kterou jste definovali, klikněte na tlačítko **Authorize**. Na záložce prohlížeče, zobrazí se následující stránka:

    ![Aplikace Internet Explorer cloudových služeb přihlášení](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Pokud je uveden více než jednoho uživatele, zvolte účet Microsoft, který je propojen se spolusprávce předplatného Azure, které jste vybrali. Na záložce prohlížeče, zobrazí se následující stránka:

    ![Potvrzení aplikace Internet Explorer cloud services](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Klikněte na tlačítko **přijmout**. Pokud je ověřování úspěšné, znovu zobrazí definici účet SAP CAL. Po krátkou dobu zobrazí se zpráva potvrzující, že proces autorizace byla úspěšná.

1. Chcete-li přiřadit nově vytvořený účet SAP CAL pro vaše uživatele, zadejte vaše **ID uživatele** do textového pole vpravo a klikněte na tlačítko **přidat**. 

    ![Účet pro přidružení uživatelů](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Pokud chcete přidružit k účtu uživatele, který používáte pro přihlášení k SAP CAL, klikněte na tlačítko **revize**. 

1. Vytvoření přidružení mezi uživateli a nově vytvořený účet SAP CAL, klikněte na tlačítko **vytvořit**.

    ![Účet přiřazení uživatele k](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Úspěšně jste vytvořili účet SAP CAL, aby bylo možné:

- Pomocí modelu nasazení Resource Manager.
- Nasazení systémů SAP do vašeho předplatného Azure.

> [!NOTE]
Před nasazením SAP IDES řešení založené na Windows a SQL Server, můžete si zaregistrovat předplatné SAP CAL. V opačném případě může být řešení zobrazí jako **uzamčené** na stránce Přehled.

### <a name="deploy-a-solution"></a>Nasazení řešení
1. Jakmile nastavíte účet SAP CAL, vyberte **řešení SAP IDES na Windows a SQL Server** řešení. Klikněte na tlačítko **vytvořit instanci**a zkontrolujte využití a podmínky podmínky. 

1. Na **základní režim: vytvořit instanci** stránce, budete muset:

    a. Zadejte instanci **název**.

    b. Vyberte Azure **oblasti**. Potřebujete předplatné SAP CAL zobrazíte víc oblastí Azure nabízí.

    c.  Zadejte hlavní **heslo** pro řešení, jak je znázorněno:

    ![SAP CAL Basic režim: Vytvoření Instance](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Klikněte na možnost **Vytvořit**. Zopakovat později v závislosti na velikosti a složitosti řešení (SAP CAL poskytuje odhad), stav se zobrazí jako aktivní a připravena k použití: 

    ![Instance SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Najít skupinu prostředků a všechny objekty, které byly vytvořeny podle SAP CAL, přejděte na web Azure Portal. Virtuální počítač můžete najít spuštění s názvem instance, zda byl zadán v SAP CAL.

    ![Objekty skupiny prostředků](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. Na portálu pro SAP CAL, přejděte do nasazené instance a klikněte na tlačítko **připojit**. Zobrazí se následující automaticky otevírané okno: 

    ![Připojte se k instanci](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Než použijete jednu z možností pro připojení k nasazené systémy, klikněte na tlačítko **– Příručka Začínáme**. V dokumentaci k názvy uživatelů pro každou z metod připojení. Hesla pro uživatele, jsou nastaveny na hlavní heslo, které jste definovali na začátku procesu nasazení. V dokumentaci jsou uvedeny ostatním uživatelům více funkcí pomocí hesla, které můžete použít k přihlášení k systému nasazené.

    ![Vítejte v dokumentaci k SAPU](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Během pár hodin stavu systému SAP IDES nasazené v Azure.

Pokud jste si zakoupili předplatné SAP CAL, SAP v Azure plně podporuje nasazení SAP CAL. Podpora fronta je BC. VCM CAL.

