---
title: Nasazení SAP S/4HANA nebo BW/4HANA na virtuální ms Azure | Dokumenty společnosti Microsoft
description: Nasazení SAP S/4HANA nebo BW/4HANA na virtuální ms Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: c110a4e0429ba52e01c472097a2241f91d504cf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616209"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Nasazení SAP S/4HANA nebo BW/4HANA v Azure
Tento článek popisuje, jak nasadit S/4HANA v Azure pomocí knihovny SAP Cloud Appliance Library (SAP CAL) 3.0. Chcete-li nasadit další řešení založená na SAP HANA, jako je například BW/4HANA, postupujte stejným způsobem.

> [!NOTE]
> Další informace o sap cal najdete na webu [knihovny cloudových spotřebičů SAP.](https://cal.sap.com/) SAP má také blog o [KNIHOVNĚ SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> května 2017 můžete kromě méně upřednostňovaného klasického modelu nasazení k nasazení sap cal použít model nasazení Azure Resource Manageru. Doporučujeme použít nový model nasazení Resource Manager a ignorovat klasický model nasazení.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Podrobný proces nasazení řešení

Následující posloupnost snímků obrazovky ukazuje, jak nasadit S/4HANA v Azure pomocí SAP CAL. Tento proces funguje stejným způsobem pro jiná řešení, jako je například BW/4HANA.

Stránka **Řešení** zobrazuje některá řešení založená na SAP CAL HANA dostupná v Azure. **SAP S/4HANA 1610 FPS01, plně aktivovaný přístroj** je ve střední řadě:

![Řešení SAP CAL](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Vytvoření účtu v sap cal
1. Chcete-li se poprvé přihlásit k sap cal, použijte SAP S-User nebo jiného uživatele registrovaného u SAP. Pak definujte účet SAP CAL, který používá SAP CAL k nasazení zařízení v Azure. V definici účtu je třeba:

    a. Vyberte model nasazení v Azure (Správce prostředků nebo klasické).

    b. Zadejte předplatné Azure. Účet SAP CAL lze přiřadit pouze k jednomu předplatnému. Pokud potřebujete více než jedno předplatné, musíte vytvořit jiný účet SAP CAL.

    c. Udělit SAP CAL oprávnění k nasazení do předplatného Azure.

   > [!NOTE]
   >  Další kroky ukazují, jak vytvořit účet SAP CAL pro nasazení Správce prostředků. Pokud již máte účet SAP CAL, který je propojen s klasickým modelem nasazení, *musíte* vytvořit nový účet SAP CAL následujícím postupem. Nový účet SAP CAL je třeba nasadit v modelu Resource Manager.

1. Vytvořte nový účet SAP CAL. Stránka **Účty** zobrazuje tři možnosti pro Azure: 

    a. **Microsoft Azure (klasický)** je klasický model nasazení a už není upřednostňovaný.

    b. **Microsoft Azure** je nový model nasazení Správce prostředků.

    c. **Windows Azure provozovaný společností 21Vianet** je možnost v Číně, která používá klasický model nasazení.

    Pokud chcete nasadit v modelu Správce prostředků, vyberte **Microsoft Azure**.

    ![Podrobnosti o účtu SAP CAL](./media/cal-s4h/s4h-pic-2a.png)

1. Zadejte **ID předplatného** Azure, které se najdete na webu Azure Portal.

   ![Účty SAP CAL](./media/cal-s4h/s4h-pic3c.png)

1. Pokud chcete autorizovat nasazení sap cal do definovaného předplatného Azure, klikněte na **Autorizovat**. Na kartě prohlížeče se zobrazí následující stránka:

   ![Přihlášení ke cloudovým službám Internet Exploreru](./media/cal-s4h/s4h-pic4c.png)

1. Pokud je v seznamu uvedeno více uživatelů, zvolte účet Microsoft, který je propojený, aby byl spolusprávcem vybraného předplatného Azure. Na kartě prohlížeče se zobrazí následující stránka:

   ![Potvrzení cloudových služeb aplikace Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

1. Klikněte na **Přijmout**. Pokud je autorizace úspěšná, definice účtu SAP CAL se znovu zobrazí. Po krátké době zpráva potvrzuje, že proces autorizace byl úspěšný.

1. Chcete-li uživateli přiřadit nově vytvořený účet SAP CAL, zadejte **své ID uživatele** do textového pole vpravo a klepněte na tlačítko **Přidat**.

   ![Přidružení účtu k uživateli](./media/cal-s4h/s4h-pic8a.png)

1. Pokud chcete přidružit svůj účet k uživateli, kterého používáte k přihlášení k programu SAP CAL, klikněte na **Zkontrolovat**. 
 
1. Chcete-li vytvořit přidružení mezi uživatelem a nově vytvořeným účtem SAP CAL, klepněte na tlačítko **Vytvořit**.

   ![Přidružení účtu Uživatel k účtu SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Úspěšně jste vytvořili účet SAP CAL, který je schopen:

- Použijte model nasazení Resource Manager.
- Nasaďte systémy SAP do svého předplatného Azure.

Teď můžete začít nasazovat S/4HANA do svého uživatelského předplatného v Azure.

> [!NOTE]
> Než budete pokračovat, zjistěte, jestli máte kvóty virtuálních procesorů Azure pro virtuální počítače Azure řady H. V současné době SAP CAL používá virtuální počítače řady H azure k nasazení některých řešení založených na SAP HANA. Vaše předplatné Azure nemusí mít žádné kvóty virtuálních procesorů řady H pro řadu H.Your Azure subscription might not have any H-Series vCPU quotas for H-Series. Pokud ano, možná budete muset kontaktovat podporu Azure získat kvótu alespoň 16 H-series virtuálních procesorů.
> 
> [!NOTE]
> Když nasadíte řešení v Azure v SAP CAL, můžete zjistit, že můžete vybrat jenom jednu oblast Azure. Chcete-li nasadit do jiných oblastí Azure, než je ta, kterou navrhuje SAP CAL, musíte si zakoupit předplatné CAL od SAP. Taky možná budete muset otevřít zprávu se SAP, aby byl váš účet CAL povolen pro doručování do jiných oblastí Azure, než které byly původně navrženy.

### <a name="deploy-a-solution"></a>Nasazení řešení

Nasadíme řešení ze stránky **Řešení** sap cal. SAP CAL má dvě sekvence k nasazení:

- Základní sekvence, která používá jednu stránku k definování systému, který má být nasazen
- Pokročilá sekvence, která poskytuje určité možnosti na velikosti virtuálních počítačích 

Zde předvádíme základní cestu k nasazení.

1. Na stránce **Podrobnosti o účtu** je třeba:

    a. Vyberte účet SAP CAL. (Použijte účet, který je přidružen k nasazení s modelem nasazení Resource Manageru.)

    b. Zadejte **název**instance .

    c. Vyberte **oblast**Azure . SAP CAL navrhuje oblast. Pokud potřebujete jinou oblast Azure a nemáte předplatné SAP CAL, musíte si objednat předplatné CAL se sapem.

    d. Zadejte hlavní **heslo** pro řešení osmi nebo devíti znaků. Heslo se používá pro správce různých součástí.

   ![SAP CAL Základní režim: Vytvořit instanci](./media/cal-s4h/s4h-pic10a.png)

1. Klepněte na tlačítko **Vytvořit**a v postovací mase klepněte na tlačítko **OK**.

   ![Sap CAL podporované velikosti virtuálních počítače](./media/cal-s4h/s4h-pic10b.png)

1. V dialogovém okně **Soukromý klíč** klikněte na **Store** a uložte soukromý klíč do služby SAP CAL. Chcete-li použít ochranu heslem pro soukromý klíč, klepněte na tlačítko **Stáhnout**. 

   ![Soukromý klíč SAP CAL](./media/cal-s4h/s4h-pic10c.png)

1. Přečtěte si **výstražnou** zprávu SAP CAL a klepněte na tlačítko **OK**.

   ![Upozornění sap cal](./media/cal-s4h/s4h-pic10d.png)

    Nyní probíhá nasazení. Po určité době, v závislosti na velikosti a složitosti řešení (SAP CAL poskytuje odhad), stav je zobrazen jako aktivní a připravený k použití.

1. Pokud chcete najít virtuální počítače shromážděné s ostatními přidruženými prostředky v jedné skupině prostředků, přejděte na portál Azure: 

   ![Objekty SAP CAL nasazené na novém portálu](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. Na portálu SAP CAL se stav zobrazí jako **Aktivní**. Chcete-li se k řešení připojit, klepněte na tlačítko **Připojit**. V rámci tohoto řešení jsou nasazeny různé možnosti připojení k různým součástem.

   ![Instance SAP CAL](./media/cal-s4h/active_solution.png)

1. Než se budete moci připojit k nasazeným systémům pomocí jedné z možností, klepněte na tlačítko **Příručka Začínáme**. 

   ![Připojení k instanci](./media/cal-s4h/connect_to_solution.png)

    Dokumentace pojmenuje uživatele pro každou metodu připojení. Hesla pro tyto uživatele jsou nastavena na hlavní heslo, které jste definovali na začátku procesu nasazení. V dokumentaci jsou uvedeni další funkční uživatelé se svými hesly, která můžete použít k přihlášení k nasazenému systému. 

    Pokud například používáte grafické uživatelské rozhraní SAP, které je předinstalováno v počítači se vzdálenou plochou systému Windows, může systém S/4 vypadat takto:

   ![SM50 v předinstalovaném SAP GUI](./media/cal-s4h/gui_sm50.png)

    Nebo pokud používáte DBACockpit, instance může vypadat takto:

   ![SM50 v dbacockpit sap GUI](./media/cal-s4h/dbacockpit.png)

Během několika hodin se v Azure nasadí zdravé zařízení SAP S/4.

Pokud jste si zakoupili předplatné SAP CAL, SAP plně podporuje nasazení prostřednictvím SAP CAL v Azure. Fronta podpory je BC-VCM-CAL.







