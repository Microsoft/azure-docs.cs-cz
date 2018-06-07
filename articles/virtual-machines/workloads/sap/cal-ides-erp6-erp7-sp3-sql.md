---
title: Nasazení SAP integrovaného vývojového prostředí EHP7 SP3 pro SAP ERP 6.0 v Azure | Microsoft Docs
description: Nasazení SAP integrovaného vývojového prostředí EHP7 SP3 pro SAP ERP 6.0 na Azure
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
ms.openlocfilehash: b1895fb1910c5f30cbcff1c16ca66057d31a580b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656478"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Nasazení SAP integrovaného vývojového prostředí EHP7 SP3 pro SAP ERP 6.0 na Azure
Tento článek popisuje postup nasazení SAP integrovaného vývojového prostředí systému spuštěné s SQL serverem a operačním systému Windows v Azure prostřednictvím SAP cloudu zařízení knihoven (SAP CAL) 3.0. Na snímcích obrazovky zobrazit podrobný postup. Pokud chcete nasadit jiné řešení, použijte stejný postup.

Chcete-li začít s SAP CAL, přejděte na [SAP cloudu zařízení knihovny](https://cal.sap.com/) webu. SAP má také blog o nové [SAP cloudu zařízení knihovna 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
Od 29. květen 2017 můžete v modelu nasazení Azure Resource Manager kromě modelu nasazení classic méně než upřednostňovaný nasazení SAP CAL. Doporučujeme použít nový model nasazení Resource Manager a modelu nasazení classic ignorovat.

Pokud jste již vytvořili SAP CAL účet, který používá v případě klasického modelu *musíte vytvořit jiný účet SAP CAL*. Tento účet musí výhradně nasadit do Azure pomocí modelu Resource Manager.

Po přihlášení k prostředí SAP CAL na první stránku obvykle vede k **řešení** stránky. Řešení nabízí na SAP CAL jsou vytrvale zvýšení, takže možná budete muset posunout odlišují najít řešení, které chcete. Zvýrazněných integrovaného vývojového prostředí systému Windows SAP řešení, které je k dispozici výhradně na Azure znázorňuje proces nasazení:

![Řešení CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Vytvoření účtu na SAP CAL
1. Přihlaste se k SAP CAL poprvé, použijte S SAP-uživatele nebo jiný uživatel zaregistrován u služby SAP. Poté definujte SAP CAL účtu, který je používán SAP CAL nasazení zařízení v Azure. V definici účtu budete muset:

    a. Vyberte model nasazení v Azure (Resource Manager nebo classic).

    b. Zadejte předplatné Azure. Účet SAP CAL lze přiřadit pouze jedno předplatné. Pokud potřebujete více než jedno předplatné, musíte vytvořit jiný účet SAP CAL.
    
    c. Udělte oprávnění SAP CAL k nasazení do vašeho předplatného Azure.

    > [!NOTE]
    Další kroky ukazují, jak vytvořit účet SAP CAL pro nasazení Resource Manager. Pokud již máte účet SAP CAL, který je propojený s modelem nasazení classic budete *potřebovat* postupovat podle těchto kroků můžete vytvořit nový účet SAP CAL. Nový účet SAP CAL je potřeba nasadit v modelu Resource Manager.

2. Chcete-li vytvořit nový účet SAP CAL, **účty** stránky zobrazí dvě možnosti pro Azure: 

    a. **Microsoft Azure (klasický)** je modelu nasazení classic a již není upřednostňovaný.

    b. **Microsoft Azure** je nový model nasazení Resource Manager.

    ![Účty CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Chcete-li nasadit v modelu Resource Manager, vyberte **Microsoft Azure**.

    ![Účty CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

3. Zadejte Azure **ID předplatného** , naleznete na portálu Azure. 

    ![ID předplatného CAL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

4. K autorizaci CAL SAP k nasazení do předplatné Azure, které jste definovali, klikněte na tlačítko **Authorize**. Na následující stránce se zobrazí na záložce prohlížeče:

    ![Internet Explorer cloudových služeb přihlášení](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

5. Pokud je uveden více než jeden uživatel, zvolte účet Microsoft, který je propojený jako spolusprávce předplatného Azure, které jste vybrali. Na následující stránce se zobrazí na záložce prohlížeče:

    ![Internet Explorer cloudové služby potvrzení](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

6. Klikněte na tlačítko **přijmout**. Pokud ověřování úspěšné, zobrazí definici účtu SAP CAL znovu. Po krátkou dobu zprávu potvrdí, že proces autorizace bylo úspěšné.

7. Chcete-li přiřadit nově vytvořený účet SAP CAL pro vaše uživatele, zadejte vaše **ID uživatele** do textového pole na doprava a klikněte na **přidat**. 

    ![Účet k přidružení uživatele](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

8. Chcete-li přidružit uživatele, který používáte pro přihlášení k prostředí SAP CAL váš účet, klikněte na tlačítko **zkontrolujte**. 

9. Chcete-li vytvořit přidružení mezi uživateli a nově vytvořený účet SAP CAL, klikněte na tlačítko **vytvořit**.

    ![Uživatele k přidružení účtu](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Úspěšně jste vytvořili účet SAP CAL, aby bylo možné:

- Pomocí modelu nasazení Resource Manager.
- Nasazení systémů SAP do vašeho předplatného Azure.

> [!NOTE]
Před nasazením řešení SAP integrovaného vývojového prostředí na základě systému Windows a SQL Server, možná budete muset zaregistrujte si předplatné SAP CAL. Jinak se možná řešení nezobrazí jako **uzamčen** na stránce Přehled.

### <a name="deploy-a-solution"></a>Nasazení řešení
1. Když nastavíte účet SAP CAL, vyberte **řešení SAP integrovaného vývojového prostředí v systémech Windows a SQL Server** řešení. Klikněte na tlačítko **vytvořit instanci**a potvrďte podmínky použití a podmínky. 

2. Na **základní režim: vytvoření Instance** stránky, potřebujete:

    a. Zadejte instance **název**.

    b. Vyberte Azure **oblast**. Může být nutné předplatné SAP CAL získat několika oblastmi Azure nabízí.

    c.  Zadejte hlavní **heslo** pro řešení, jak je znázorněno:

    ![SAP CAL Basic režim: Vytvoření Instance](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

3. Klikněte na možnost **Vytvořit**. Po určité době, v závislosti na velikost a složitost řešení (SAP CAL poskytuje odhad), je stav zobrazen jako aktivní a připravena k použití: 

    ![Instance SAP Kalendáře](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

4. Chcete-li najít skupinu prostředků a všechny její objekty, které byly vytvořeny SAP CAL, přejděte na portálu Azure. Virtuální počítač můžete najít spuštěním se stejným názvem instance, který byl zadán v SAP CAL.

    ![Objekty skupiny prostředků](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

5. Na portálu SAP CAL, přejděte do nasazené instancí a klikněte na tlačítko **Connect**. Zobrazí se následující automaticky otevírané okno okno: 

    ![Připojte se k instanci](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

6. Než použijete jednu z možností pro připojení k nasazené systémy, klikněte na tlačítko **– Příručka Začínáme**. V dokumentaci názvy uživatelů pro každou metodu připojení. Hesla pro tyto uživatele jsou nastaveny na hlavní heslo, které jste definovali na začátku procesu nasazení. V dokumentaci jsou uvedeny další více funkční uživatelé s svá hesla, které můžete použít k přihlášení k systému nasazené.

    ![Vítejte v dokumentaci k SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

V rámci několik hodin je v pořádku systému integrovaného vývojového prostředí SAP nasazené v Azure.

Pokud jste si zakoupili předplatné SAP CAL, SAP plně podporuje nasazení SAP CAL na Azure. Podpora fronta je BC. VCM CAL.

