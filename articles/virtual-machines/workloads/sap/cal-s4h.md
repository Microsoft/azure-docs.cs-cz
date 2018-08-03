---
title: Nasadit řešení SAP S/4HANA nebo BW/4HANA na Virtuálním počítači Azure | Dokumentace Microsoftu
description: Nasadit řešení SAP S/4HANA nebo BW/4HANA na Virtuálním počítači Azure
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
ms.openlocfilehash: a99fb959ae1ac1434bedffd782a7c4e0a302d361
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431403"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Nasadit řešení SAP S/4HANA nebo BW/4HANA v Azure
Tento článek popisuje postup nasazení S/4HANA v Azure s využitím SAP Cloud Appliance Library (SAP CAL) 3.0. Nasazení dalších řešení založených na SAP HANA, jako je například BW/4HANA, postupujte podle stejných kroků.

> [!NOTE]
Další informace o SAP CAL, přejděte [SAP Cloud Appliance Library](https://cal.sap.com/) webu. SAP má také blogu o [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

> [!NOTE]
Od 29. května 2017 můžete kromě modelu nasazení classic méně než upřednostňovaný model nasazení Azure Resource Manageru pro nasazení SAP CAL. Doporučujeme, abyste pomocí nového modelu nasazení Resource Manager a modelu nasazení classic ignorovat.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Podrobný postup nasazení řešení

K následujícímu pořadí snímky obrazovky ukazuje, jak nasadit S/4HANA v Azure pomocí SAP CAL. Tento proces funguje stejně jako pro ostatní řešení, jako je například BW/4HANA.

**Řešení** stránce jsou uvedeny některé řešení založená na SAP CAL HANA k dispozici v Azure. **SAP S/4HANA 1610 FPS01, zařízení Fully-Activated** se v prostředním:

![Řešení SAP CAL](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Vytvoření účtu ve službě SAP CAL
1. Přihlaste se k SAP CAL poprvé, použijte S SAP – uživatel nebo jiný uživatel zaregistrován s řešením SAP. Potom definujte SAP CAL účtu, který používá SAP CAL pro nasazení zařízení v Azure. V definici účtu budete muset:

    a. Vyberte model nasazení v Azure (Resource Manager nebo classic).

    b. Zadejte předplatné Azure. Pouze k jednomu předplatnému můžete přiřadit účet SAP CAL. Pokud potřebujete více než jedno předplatné, musíte vytvořit jiný účet SAP CAL.

    c. Udělit oprávnění SAP CAL pro nasazení do vašeho předplatného Azure.

    > [!NOTE]
    Následující kroky ukazují, jak vytvořit účet SAP CAL pro nasazení Resource Manager. Pokud už máte účet SAP CAL, který je propojený s modelem nasazení classic můžete *potřebovat* postupovat podle těchto kroků a vytvořte nový účet SAP CAL. Nový účet SAP CAL je potřeba nasadit v modelu Resource Manager.

1. Vytvořte nový účet SAP CAL. **Účty** stránka zobrazuje tři možnosti pro Azure: 

    a. **Microsoft Azure (klasické)** je model nasazení classic a už není upřednostňovaný.

    b. **Microsoft Azure** je nový model nasazení Resource Manageru.

    c. **Windows Azure provozovaný společností 21Vianet** je možnost, která v Číně, který používá model nasazení classic.

    Pokud chcete nasadit v modelu Resource Manager, vyberte **Microsoft Azure**.

    ![Podrobnosti o SAP CAL účtu](./media/cal-s4h/s4h-pic-2a.png)

1. Zadejte Azure **ID předplatného** , který najdete na webu Azure portal.

   ![Účty SAP CAL](./media/cal-s4h/s4h-pic3c.png)

1. K autorizaci SAP CAL pro nasazení do předplatného Azure, kterou jste definovali, klikněte na tlačítko **Authorize**. Na záložce prohlížeče, zobrazí se následující stránka:

   ![Aplikace Internet Explorer cloudových služeb přihlášení](./media/cal-s4h/s4h-pic4c.png)

1. Pokud je uveden více než jednoho uživatele, zvolte účet Microsoft, který je propojen se spolusprávce předplatného Azure, které jste vybrali. Na záložce prohlížeče, zobrazí se následující stránka:

   ![Potvrzení aplikace Internet Explorer cloud services](./media/cal-s4h/s4h-pic5a.png)

1. Klikněte na tlačítko **přijmout**. Pokud je ověřování úspěšné, znovu zobrazí definici účet SAP CAL. Po krátkou dobu zobrazí se zpráva potvrzující, že proces autorizace byla úspěšná.

1. Chcete-li přiřadit nově vytvořený účet SAP CAL pro vaše uživatele, zadejte vaše **ID uživatele** do textového pole vpravo a klikněte na tlačítko **přidat**.

   ![Účet pro přidružení uživatelů](./media/cal-s4h/s4h-pic8a.png)

1. Pokud chcete přidružit k účtu uživatele, který používáte pro přihlášení k SAP CAL, klikněte na tlačítko **revize**. 
 
1. Vytvoření přidružení mezi uživateli a nově vytvořený účet SAP CAL, klikněte na tlačítko **vytvořit**.

   ![Uživatele k přidružení účtu SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Úspěšně jste vytvořili účet SAP CAL, aby bylo možné:

- Pomocí modelu nasazení Resource Manager.
- Nasazení systémů SAP do vašeho předplatného Azure.

Teď můžete začít nasazovat S/4HANA do vašeho předplatného uživatele v Azure.

> [!NOTE]
Než budete pokračovat, zjistěte, zda máte kvóty virtuálních procesorů Azure pro virtuální počítače Azure řady H-Series. V tomto okamžiku SAP CAL využívá řady H-Series virtuálních počítačů Azure k nasazení některá z řešení SAP HANA založené. Vaše předplatné Azure, nemusí mít žádné kvóty virtuálních procesorů řady H-Series pro řady H-Series. Pokud ano, pravděpodobně muset obrátit na podporu Azure se kvóta aspoň 16 virtuálních procesorů řady H-Series.

> [!NOTE]
Když nasadíte řešení ve službě Azure v SAP CAL, můžete zjistit, které můžete zvolit pouze jedné oblasti Azure. Pokud chcete nasadit do oblasti Azure, než na kterém navrhl SAP CAL, budete muset nakupovat SAP CAL předplatného. Můžete také potřebovat otevřít zprávu s řešením SAP CAL uživatelského účtu povoleno k doručování do Azure oblastí, než ty původně navržen.

### <a name="deploy-a-solution"></a>Nasazení řešení

Můžeme nasadit řešení z **řešení** stránky SAP CAL. SAP CAL má dvě sekvence nasazení:

- Základní sekvenci, která používá jednu stránku k definování nasazení systému
- Pokročilé sekvence, které vám některé volby o velikostech virtuálních počítačů 

Ukazujeme základní cesta k nasazení tady.

1. Na **Podrobnosti účtu** stránce, budete muset:

    a. Vyberte účet SAP CAL. (Použít účet, který je přidružený k nasazení pomocí modelu nasazení Resource Manager).

    b. Zadejte instanci **název**.

    c. Vyberte Azure **oblasti**. SAP CAL navrhuje oblast. Pokud nemáte předplatné SAP CAL budete potřebovat jiné oblasti Azure, budete muset objednat předplatné s řešením SAP CAL.

    d. Zadejte hlavní **heslo** řešení osmi nebo devíti znaků. Heslo se používá pro správce různé součásti.

   ![SAP CAL Basic režim: Vytvoření Instance](./media/cal-s4h/s4h-pic10a.png)

1. Klikněte na tlačítko **vytvořit**a v okně se zprávou, která se zobrazí, klikněte na tlačítko **OK**.

   ![SAP CAL podporované velikosti virtuálních počítačů](./media/cal-s4h/s4h-pic10b.png)

1. V **privátní klíč** dialogové okno, klikněte na tlačítko **Store** uložení privátního klíče v SAP CAL. Pro účely ochrany heslem privátního klíče, klikněte na tlačítko **Stáhnout**. 

   ![SAP CAL privátní klíč](./media/cal-s4h/s4h-pic10c.png)

1. Přečtěte si SAP CAL **upozornění** zprávu a klikněte na tlačítko **OK**.

   ![Upozornění SAP CAL](./media/cal-s4h/s4h-pic10d.png)

    Nyní probíhá nasazení. Zopakovat později v závislosti na velikosti a složitosti řešení (SAP CAL poskytuje odhad), stav se zobrazí jako aktivní a připravena k použití.

1. Virtuální počítače, které shromáždila, se přidružené prostředky v jedné skupině prostředků najdete na webu Azure portal: 

   ![Nasazení na novém portálu objekty SAP CAL](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. Na portálu SAP CAL stav zobrazuje jako **aktivní**. Připojení k řešení, klikněte na tlačítko **připojit**. Různé možnosti pro připojení k jiné komponenty jsou nasazené v rámci tohoto řešení.

   ![Instance SAP CAL](./media/cal-s4h/active_solution.png)

1. Než použijete jednu z možností pro připojení k nasazené systémy, klikněte na tlačítko **– Příručka Začínáme**. 

   ![Připojte se k instanci](./media/cal-s4h/connect_to_solution.png)

    V dokumentaci k názvy uživatelů pro každou z metod připojení. Hesla pro uživatele, jsou nastaveny na hlavní heslo, které jste definovali na začátku procesu nasazení. V dokumentaci jsou uvedeny ostatním uživatelům více funkcí pomocí hesla, které můžete použít k přihlášení k systému nasazené. 

    Například pokud použijete grafické uživatelské rozhraní SAP, který je předinstalován na počítači pro vzdálenou plochu Windows, s/4 systému může vypadat například takto:

   ![SM50 v grafickém Uživatelském rozhraní předinstalovaných SAP](./media/cal-s4h/gui_sm50.png)

    Nebo pokud používáte DBACockpit, instanci může vypadat třeba takto:

   ![SM50 v grafickém Uživatelském rozhraní DBACockpit SAP](./media/cal-s4h/dbacockpit.png)

Během pár hodin v pořádku zařízení SAP s/4 nasazené v Azure.

Pokud jste si zakoupili předplatné SAP CAL, SAP v Azure plně podporuje nasazení SAP CAL. Podpora fronta je BC. VCM CAL.







