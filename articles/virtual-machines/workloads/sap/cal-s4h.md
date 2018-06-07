---
title: Nasazení SAP S nebo 4HANA nebo BW/4HANA ve virtuálním počítači Azure | Microsoft Docs
description: Nasazení SAP S nebo 4HANA nebo BW/4HANA ve virtuálním počítači Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 10c5116afa46817a42834e0350937fde7ae0b927
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657338"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Nasazení SAP S nebo 4HANA nebo BW/4HANA v Azure
Tento článek popisuje, jak nasadit S nebo 4HANA v Azure pomocí knihovny zařízení cloudu SAP (SAP CAL) 3.0. Pokud chcete nasadit jiných řešení na základě SAP HANA, jako je například BW/4HANA, použijte stejný postup.

> [!NOTE]
Další informace o SAP CAL, přejděte na [knihovny zařízení cloudu SAP](https://cal.sap.com/) webu. SAP blog, který má také o [SAP cloudu zařízení knihovna 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

> [!NOTE]
Od 29. květen 2017 můžete v modelu nasazení Azure Resource Manager kromě modelu nasazení classic méně než upřednostňovaný nasazení SAP CAL. Doporučujeme použít nový model nasazení Resource Manager a modelu nasazení classic ignorovat.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Podrobný postup k nasazení řešení.

Následující posloupnosti snímky obrazovky ukazuje, jak nasadit S nebo 4HANA v Azure pomocí SAP CAL. Proces funguje stejným způsobem jako u jiných řešení, jako je například BW/4HANA.

**Řešení** stránky jsou uvedeny některé z dostupných na základě SAP CAL HANA řešení v Azure. **SAP S nebo 4HANA 1610 FPS01, Fully-Activated zařízení** se v prostředním řádku:

![Řešení CAL SAP](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Vytvoření účtu na SAP CAL
1. Přihlaste se k SAP CAL poprvé, použijte S SAP-uživatele nebo jiný uživatel zaregistrován u služby SAP. Poté definujte SAP CAL účtu, který je používán SAP CAL nasazení zařízení v Azure. V definici účtu budete muset:

    a. Vyberte model nasazení v Azure (Resource Manager nebo classic).

    b. Zadejte předplatné Azure. Účet SAP CAL lze přiřadit pouze jedno předplatné. Pokud potřebujete více než jedno předplatné, musíte vytvořit jiný účet SAP CAL.

    c. Udělte oprávnění SAP CAL k nasazení do vašeho předplatného Azure.

    > [!NOTE]
    Další kroky ukazují, jak vytvořit účet SAP CAL pro nasazení Resource Manager. Pokud již máte účet SAP CAL, který je propojený s modelem nasazení classic budete *potřebovat* postupovat podle těchto kroků můžete vytvořit nový účet SAP CAL. Nový účet SAP CAL je potřeba nasadit v modelu Resource Manager.

2. Vytvořte nový účet SAP CAL. **Účty** stránka zobrazuje tři možnosti pro Azure: 

    a. **Microsoft Azure (klasický)** je modelu nasazení classic a již není upřednostňovaný.

    b. **Microsoft Azure** je nový model nasazení Resource Manager.

    c. **Windows Azure je provozována společností 21Vianet** je možnost v Číně, který používá model nasazení classic.

    Chcete-li nasadit v modelu Resource Manager, vyberte **Microsoft Azure**.

    ![Podrobnosti účtu CAL SAP](./media/cal-s4h/s4h-pic-2a.png)

3. Zadejte Azure **ID předplatného** , naleznete na portálu Azure.

   ![Účty CAL SAP](./media/cal-s4h/s4h-pic3c.png)

4. K autorizaci CAL SAP k nasazení do předplatné Azure, které jste definovali, klikněte na tlačítko **Authorize**. Na následující stránce se zobrazí na záložce prohlížeče:

   ![Internet Explorer cloudových služeb přihlášení](./media/cal-s4h/s4h-pic4c.png)

5. Pokud je uveden více než jeden uživatel, zvolte účet Microsoft, který je propojený jako spolusprávce předplatného Azure, které jste vybrali. Na následující stránce se zobrazí na záložce prohlížeče:

   ![Internet Explorer cloudové služby potvrzení](./media/cal-s4h/s4h-pic5a.png)

6. Klikněte na tlačítko **přijmout**. Pokud ověřování úspěšné, zobrazí definici účtu SAP CAL znovu. Po krátkou dobu zprávu potvrdí, že proces autorizace bylo úspěšné.

7. Chcete-li přiřadit nově vytvořený účet SAP CAL pro vaše uživatele, zadejte vaše **ID uživatele** do textového pole na doprava a klikněte na **přidat**.

   ![Účet k přidružení uživatele](./media/cal-s4h/s4h-pic8a.png)

8. Chcete-li přidružit uživatele, který používáte pro přihlášení k prostředí SAP CAL váš účet, klikněte na tlačítko **zkontrolujte**. 
 
9. Chcete-li vytvořit přidružení mezi uživateli a nově vytvořený účet SAP CAL, klikněte na tlačítko **vytvořit**.

   ![Uživatele k přidružení účtu SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Úspěšně jste vytvořili účet SAP CAL, aby bylo možné:

- Pomocí modelu nasazení Resource Manager.
- Nasazení systémů SAP do vašeho předplatného Azure.

Teď můžete začít S nebo 4HANA nasadit do předplatného uživatele v Azure.

> [!NOTE]
Než budete pokračovat, určete, jestli máte Azure virtuální procesor kvóty pro virtuální počítače Azure H-Series. V tuto chvíli používá SAP CAL H-Series virtuální počítače Azure k nasazení některé z řešení založená na SAP HANA. Vaše předplatné Azure nemusí mít žádné kvóty virtuální procesor H-Series pro H-Series. Pokud ano, budete muset požádejte podporu Azure o získání kvótu alespoň 16 Vcpu H-Series.

> [!NOTE]
Když nasadíte řešení v Azure v SAP CAL, můžete zjistit, které můžete zvolit pouze jedna oblast Azure. Pokud chcete nasadit do Azure oblastí, než jeden navrhovaná službou SAP CAL, budete muset zakoupit předplatné CAL ze SAP. Můžete také chtít otevřít zprávu s SAP povolené pro doručení do Azure oblastí, než ty, které původně navržený CAL uživatelského účtu.

### <a name="deploy-a-solution"></a>Nasazení řešení

Umožňuje nasadit řešení z **řešení** stránky SAP CAL. SAP CAL má dva sekvence pro nasazení:

- Základní pořadí, které používá jednu stránku k definování nasazení systému
- Pokročilé pořadí, který vám dává některé možnosti na velikosti virtuálních počítačů 

Ukážeme základní cesta k nasazení v tomto poli.

1. Na **Podrobnosti účtu** stránky, je třeba:

    a. Vyberte účet SAP CAL. (Použít účet, který je přidružen k nasazení pomocí modelu nasazení Resource Manager.)

    b. Zadejte instance **název**.

    c. Vyberte Azure **oblast**. SAP CAL navrhuje oblast. Pokud potřebujete jinou oblast Azure a nemáte předplatné SAP CAL, budete muset pořadí CAL předplatné s SAP.

    d. Zadejte hlavní **heslo** řešení osm nebo devět znaků. Heslo se používá pro správce různé součásti.

   ![SAP CAL Basic režim: Vytvoření Instance](./media/cal-s4h/s4h-pic10a.png)

2. Klikněte na tlačítko **vytvořit**a v dialogovém okně se zobrazí, klikněte na **OK**.

   ![SAP CAL podporované velikosti virtuálních počítačů](./media/cal-s4h/s4h-pic10b.png)

3. V **privátní klíč** dialogové okno, klikněte na tlačítko **ukládání** k uložení privátního klíče v SAP CAL. Chcete-li použít ochranu heslem pro privátní klíč, klikněte na tlačítko **Stáhnout**. 

   ![SAP CAL privátní klíč](./media/cal-s4h/s4h-pic10c.png)

4. Přečtěte si SAP CAL **upozornění** zpráva a klikněte na tlačítko **OK**.

   ![Upozornění CAL SAP](./media/cal-s4h/s4h-pic10d.png)

    Nyní probíhá nasazení. Po určité době, v závislosti na velikost a složitost řešení (SAP CAL poskytuje odhad), je stav zobrazen jako aktivní a připravena k použití.

5. Virtuální počítače shromážděné pomocí jiné související prostředky v jedné skupině prostředků, naleznete na portálu Azure: 

   ![Nasazené na portálu nové objekty SAP CAL](./media/cal-s4h/sapcaldeplyment_portalview.png)

6. Na portálu SAP CAL, zobrazí se stav jako **Active**. Chcete-li se připojit k řešení, klikněte na tlačítko **Connect**. Různé možnosti pro připojení k různé součásti jsou nasazeny v rámci tohoto řešení.

   ![Instance SAP Kalendáře](./media/cal-s4h/active_solution.png)

7. Než použijete jednu z možností pro připojení k nasazené systémy, klikněte na tlačítko **– Příručka Začínáme**. 

   ![Připojte se k instanci](./media/cal-s4h/connect_to_solution.png)

    V dokumentaci názvy uživatelů pro každou metodu připojení. Hesla pro tyto uživatele jsou nastaveny na hlavní heslo, které jste definovali na začátku procesu nasazení. V dokumentaci jsou uvedeny další více funkční uživatelé s svá hesla, které můžete použít k přihlášení k systému nasazené. 

    Například pokud použijete grafické uživatelské rozhraní SAP, který je předinstalován v počítači Windows vzdálené plochy, S/4 systému může vypadat například takto:

   ![SM50 v grafickém Uživatelském rozhraní předinstalované SAP](./media/cal-s4h/gui_sm50.png)

    Nebo pokud chcete použít DBACockpit, instance může vypadat například takto:

   ![SM50 v grafickém Uživatelském rozhraní DBACockpit SAP](./media/cal-s4h/dbacockpit.png)

V rámci několik hodin je v pořádku zařízení SAP S/4 nasazené v Azure.

Pokud jste si zakoupili předplatné SAP CAL, SAP plně podporuje nasazení SAP CAL na Azure. Podpora fronta je BC. VCM CAL.







