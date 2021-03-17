---
title: Nasazení SAP IDES EHP7 SP3 pro SAP ERP 6,0 v Azure | Microsoft Docs
description: Nasazení SAP IDES EHP7 SP3 pro SAP ERP 6,0 v Azure
author: hermanndms
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 5184df38b6a6b2757a5d5f7c0970258657cf5c0f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671006"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Nasazení SAP IDES EHP7 SP3 pro SAP ERP 6,0 v Azure
Tento článek popisuje, jak nasadit systém SAP v systému SAP se systémem SQL Server a operačním systémem Windows v Azure pomocí knihovny cloudových zařízení SAP (SAP CAL) 3,0. Snímky obrazovky ukazují podrobný proces. Pokud chcete nasadit jiné řešení, použijte stejný postup.

Pokud chcete začít se službou SAP CAL, přečtěte si web [Knihovna cloudových zařízení SAP](https://cal.sap.com/) . SAP má také blog o nové [knihovně cloudových zařízení SAP 3,0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> Od 29. května 2017 můžete k nasazení SAP CAL použít i model nasazení Azure Resource Manager společně s méně upřednostňovaným modelem nasazení Classic. Doporučujeme použít nový model nasazení Správce prostředků a ignorovat model nasazení Classic.

Pokud jste už vytvořili účet SAP CAL, který používá klasický model, *budete muset vytvořit jiný účet SAP CAL*. Tento účet se musí nasadit do Azure výhradně pomocí modelu Správce prostředků.

Až se přihlásíte ke službě SAP CAL, první stránka obvykle vás provede na stránku **řešení** . Řešení, která jsou nabízená v rámci SAP CAL, se stále zvyšují, takže možná budete muset posouvat poměrně trochu, abyste našli řešení, které chcete. Zvýrazněné řešení SAP v systému Windows, které je dostupné výhradně v Azure, předvádí proces nasazení:

![Řešení SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Vytvoření účtu v rámci SAP CAL
1. K prvnímu přihlášení ke službě SAP CAL použijte svého uživatele SAP S nebo jiného uživatele zaregistrovaného v SAP. Pak definujte účet SAP CAL, který je používán službou SAP CAL k nasazení zařízení v Azure. V definici účtu potřebujete:

    a. Vyberte model nasazení v Azure (Správce prostředků nebo Classic).

    b. Zadejte své předplatné Azure. Účet SAP CAL se dá přiřadit jenom k jednomu předplatnému. Pokud potřebujete více než jedno předplatné, budete muset vytvořit jiný účet SAP CAL.
    
    c. Udělte oprávnění SAP CAL k nasazení do vašeho předplatného Azure.

   > [!NOTE]
   >  Další kroky ukazují, jak vytvořit účet SAP CAL pro nasazení Správce prostředků. Pokud už máte účet SAP CAL, který je propojený s modelem nasazení Classic, je *potřeba* pomocí těchto kroků vytvořit nový účet SAP CAL. Nový účet SAP CAL musí být nasazený v modelu Správce prostředků.

1. Pokud chcete vytvořit nový účet SAP CAL, zobrazí se na stránce **účty** dvě možnosti pro Azure: 

    a. **Microsoft Azure (Classic)** je model nasazení Classic a již není upřednostňovaný.

    b. **Microsoft Azure** je nový model nasazení Správce prostředků.

    ![Snímek obrazovky se zobrazí S účty CAL P s Microsoft Azureem, které jsou vyvolány.](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Chcete-li nasadit v modelu Správce prostředků, vyberte možnost **Microsoft Azure**.

    ![Snímek obrazovky se zobrazí S účty licencí CAL A P.](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Zadejte **ID předplatného** Azure, které najdete na Azure Portal. 

    ![ID předplatného SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Kliknutím na **autorizovat** autorizujete službu SAP CAL k nasazení do vámi definovaného předplatného Azure. Na kartě prohlížeč se zobrazí následující stránka:

    ![Přihlášení ke cloudovým službám v Internet Exploreru](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Pokud je v seznamu uvedeno více uživatelů, vyberte účet Microsoft propojených se správcem předplatného Azure, které jste vybrali. Na kartě prohlížeč se zobrazí následující stránka:

    ![Potvrzení Cloud Services v Internet Exploreru](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Klikněte na **Přijmout**. Pokud je autorizace úspěšná, znovu se zobrazí definice účtu SAP CAL. Po krátké době zpráva potvrdí, že proces autorizace byl úspěšný.

1. Pokud chcete uživateli přiřadit nově vytvořený účet SAP CAL, zadejte své **ID uživatele** do textového pole vpravo a klikněte na **Přidat**. 

    ![Účet pro přidružení uživatele](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Chcete-li přidružit svůj účet k uživateli, který používáte k přihlášení k SAP CAL, klikněte na tlačítko **zkontrolovat**. 

1. Pokud chcete vytvořit přidružení mezi vaším uživatelem a nově vytvořeným účtem SAP CAL, klikněte na **vytvořit**.

    ![Přidružení uživatele k účtu](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Úspěšně jste vytvořili účet SAP CAL, který může:

- Použijte model nasazení Resource Manager.
- Nasaďte systémy SAP do svého předplatného Azure.

> [!NOTE]
> Než budete moct nasadit řešení SAP IDES založené na Windows a SQL Server, možná budete muset zaregistrovat předplatné SAP CAL. V opačném případě se řešení může na stránce Přehled zobrazit jako **uzamčené** .

### <a name="deploy-a-solution"></a>Nasazení řešení
1. Po nastavení účtu SAP CAL vyberte **řešení SAP v systému Windows a SQL Server** řešení. Klikněte na **vytvořit instanci** a potvrďte podmínky použití a podmínky. 

1. Na stránce **Základní režim: vytvořit instanci** budete potřebovat:

    a. Zadejte **název** instance.

    b. Vyberte **oblast** Azure. Abyste mohli získat více oblastí Azure, budete možná potřebovat předplatné SAP CAL.

    c.  Zadejte hlavní **heslo** pro řešení, jak je znázorněno níže:

    ![Základní režim SAP CAL: vytvořit instanci](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Klikněte na **Vytvořit**. Po určité době v závislosti na velikosti a složitosti řešení (odhad licencí SAP CAL poskytuje odhad) se stav zobrazuje jako aktivní a připravený k použití: 

    ![Instance SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Pokud chcete najít skupinu prostředků a všechny její objekty, které vytvořila služba SAP CAL, navštivte Azure Portal. Virtuální počítač se dá najít počínaje stejným názvem instance, který byl zadaný v rámci SAP CAL.

    ![Objekty skupiny prostředků](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. Na portálu CAL pro SAP přejděte do části nasazené instance a klikněte na **připojit**. Zobrazí se následující automaticky otevírané okno: 

    ![Připojit k instanci](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Než budete moct použít jednu z možností pro připojení k nasazeným systémům, klikněte na **Začínáme Průvodce**. Dokumentace pro každou metodu připojení pojmenuje uživatele. Hesla pro tyto uživatele jsou nastavena na heslo hlavního serveru, které jste definovali na začátku procesu nasazení. V dokumentaci jsou další funkční uživatelé uvedeni s hesly, které můžete použít k přihlášení do nasazeného systému.

    ![Úvodní dokumentace SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Během několika hodin se v Azure nasadí dobrý systém pro platformu SAP.

Pokud jste si koupili předplatné SAP CAL, SAP plně podporuje nasazení prostřednictvím SAP CAL v Azure. Fronta podpory je BC-VCM-CAL.

