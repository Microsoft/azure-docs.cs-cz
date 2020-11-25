---
title: Nasazení SAP S/4HANA nebo ČERNOBÍLé/4HANA na virtuálním počítači Azure | Microsoft Docs
description: Nasazení SAP S/4HANA nebo ČERNOBÍLé/4HANA na virtuálním počítači Azure
author: hermanndms
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 09/15/2016
ms.author: hermannd
ms.reviewer: cynthn
ms.openlocfilehash: 6492e770479042a5a1b4da6f61917832adad4873
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021414"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Nasazení SAP S/4HANA nebo ČERNOBÍLé/4HANA v Azure
Tento článek popisuje, jak nasadit S/4HANA v Azure pomocí knihovny cloudových zařízení SAP (SAP CAL) 3,0. Pokud chcete nasadit další řešení založená na SAP HANA, jako je například ČERNOBÍLé/4HANA, postupujte podle stejných kroků.

> [!NOTE]
> Další informace o licencích CAL pro SAP najdete na webu [Knihovna cloudových zařízení SAP](https://cal.sap.com/) . SAP má také blog o [knihovně cloudových zařízení SAP 3,0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> Od 29. května 2017 můžete k nasazení SAP CAL použít i model nasazení Azure Resource Manager společně s méně upřednostňovaným modelem nasazení Classic. Doporučujeme použít nový model nasazení Správce prostředků a ignorovat model nasazení Classic.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Krok za krokem postup nasazení řešení

Následující posloupnost snímků obrazovky ukazuje, jak nasadit S/4HANA v Azure pomocí SAP CAL. Tento proces funguje stejným způsobem jako u jiných řešení, jako je například ČERNOBÍLé/4HANA.

Na stránce **řešení** se zobrazují některá řešení založená na SAP CAL Hana dostupná v Azure. **SAP S/4HANA 1610 FPS01, Fully-Activated zařízení** je v prostředním řádku:

![Řešení SAP CAL](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Vytvoření účtu v rámci SAP CAL
1. K prvnímu přihlášení ke službě SAP CAL použijte svého uživatele SAP S nebo jiného uživatele zaregistrovaného v SAP. Pak definujte účet SAP CAL, který je používán službou SAP CAL k nasazení zařízení v Azure. V definici účtu potřebujete:

    a. Vyberte model nasazení v Azure (Správce prostředků nebo Classic).

    b. Zadejte své předplatné Azure. Účet SAP CAL se dá přiřadit jenom k jednomu předplatnému. Pokud potřebujete více než jedno předplatné, budete muset vytvořit jiný účet SAP CAL.

    c. Udělte oprávnění SAP CAL k nasazení do vašeho předplatného Azure.

   > [!NOTE]
   >  Další kroky ukazují, jak vytvořit účet SAP CAL pro nasazení Správce prostředků. Pokud už máte účet SAP CAL, který je propojený s modelem nasazení Classic, je *potřeba* pomocí těchto kroků vytvořit nový účet SAP CAL. Nový účet SAP CAL musí být nasazený v modelu Správce prostředků.

1. Vytvořte nový účet SAP CAL. Na stránce **účty** se zobrazují tři možnosti pro Azure: 

    a. **Microsoft Azure (Classic)** je model nasazení Classic a již není upřednostňovaný.

    b. **Microsoft Azure** je nový model nasazení Správce prostředků.

    c. **Windows Azure provozovaný společností 21Vianet** je možnost v Číně, která používá model nasazení Classic.

    Chcete-li nasadit v modelu Správce prostředků, vyberte možnost **Microsoft Azure**.

    ![Podrobnosti účtu SAP CAL](./media/cal-s4h/s4h-pic-2a.png)

1. Zadejte **ID předplatného** Azure, které najdete na Azure Portal.

   ![Účty SAP CAL](./media/cal-s4h/s4h-pic3c.png)

1. Kliknutím na **autorizovat** autorizujete službu SAP CAL k nasazení do vámi definovaného předplatného Azure. Na kartě prohlížeč se zobrazí následující stránka:

   ![Přihlášení ke cloudovým službám v Internet Exploreru](./media/cal-s4h/s4h-pic4c.png)

1. Pokud je v seznamu uvedeno více uživatelů, vyberte účet Microsoft propojených se správcem předplatného Azure, které jste vybrali. Na kartě prohlížeč se zobrazí následující stránka:

   ![Potvrzení Cloud Services v Internet Exploreru](./media/cal-s4h/s4h-pic5a.png)

1. Klikněte na **Přijmout**. Pokud je autorizace úspěšná, znovu se zobrazí definice účtu SAP CAL. Po krátké době zpráva potvrdí, že proces autorizace byl úspěšný.

1. Pokud chcete uživateli přiřadit nově vytvořený účet SAP CAL, zadejte své **ID uživatele** do textového pole vpravo a klikněte na **Přidat**.

   ![Účet pro přidružení uživatele](./media/cal-s4h/s4h-pic8a.png)

1. Chcete-li přidružit svůj účet k uživateli, který používáte k přihlášení k SAP CAL, klikněte na tlačítko **zkontrolovat**. 
 
1. Pokud chcete vytvořit přidružení mezi vaším uživatelem a nově vytvořeným účtem SAP CAL, klikněte na **vytvořit**.

   ![Přidružení uživatele k účtu SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Úspěšně jste vytvořili účet SAP CAL, který může:

- Použijte model nasazení Resource Manager.
- Nasaďte systémy SAP do svého předplatného Azure.

Teď můžete začít nasazovat S/4HANA do svého uživatelského předplatného v Azure.

> [!NOTE]
> Než budete pokračovat, určete, jestli máte vCPU kvóty Azure pro virtuální počítače Azure H-Series. V tuto chvíli k nasazení některých řešení založených na SAP HANA využívá Azure CAL virtuální počítače H-Series v Azure. Vaše předplatné Azure možná nemá pro řady H-Series žádnou vCPU kvótu H-Series. Pokud ano, možná budete muset kontaktovat podporu Azure, abyste získali kvótu minimálně 16 vCPU H-Series.
> 
> [!NOTE]
> Když do Azure nasadíte řešení v rámci SAP CAL, může se stát, že si vyberete jenom jednu oblast Azure. K nasazení do jiných oblastí Azure, než je navržena prostřednictvím služby SAP CAL, je třeba si koupit předplatné CAL z SAP. Je také možné, že budete muset otevřít zprávu s protokolem SAP, aby byl váš účet CAL povolený pro doručování do oblastí Azure kromě těch, které jsou původně navržené.

### <a name="deploy-a-solution"></a>Nasazení řešení

Pojďme nasadit řešení na stránce **řešení** v rámci SAP CAL. Rozhraní SAP CAL má dvě sekvence nasazení:

- Základní sekvence, která používá jednu stránku k definování systému, který se má nasadit
- Pokročilá sekvence, která poskytuje určité možnosti pro velikosti virtuálních počítačů 

Tady Ukážeme základní cestu k nasazení.

1. Na stránce **Podrobnosti o účtu** potřebujete:

    a. Vyberte účet SAP CAL. (Použijte účet, který je přidružený k nasazení pomocí modelu nasazení Správce prostředků.)

    b. Zadejte **název** instance.

    c. Vyberte **oblast** Azure. Rozhraní SAP CAL navrhuje oblast. Pokud potřebujete jinou oblast Azure a nemáte předplatné SAP CAL, musíte si objednat předplatné CAL pomocí SAP.

    d. Zadejte hlavní **heslo** pro řešení o osmi nebo devíti znacích. Heslo se používá pro správce různých součástí.

   ![Základní režim SAP CAL: vytvořit instanci](./media/cal-s4h/s4h-pic10a.png)

1. Klikněte na tlačítko **vytvořit** a v zobrazeném okně se zprávou klikněte na tlačítko **OK**.

   ![Podporované velikosti virtuálních počítačů SAP CAL](./media/cal-s4h/s4h-pic10b.png)

1. V dialogovém okně **privátní klíč** klikněte na **Uložit** a uložte privátní klíč do SAP CAL. Chcete-li použít ochranu heslem pro privátní klíč, klikněte na tlačítko **Stáhnout**. 

   ![Privátní klíč SAP CAL](./media/cal-s4h/s4h-pic10c.png)

1. Přečtěte si **varovnou** zprávu SAP CAL a klikněte na **OK**.

   ![Upozornění SAP CAL](./media/cal-s4h/s4h-pic10d.png)

    Nyní proběhne nasazení. Po určité době se v závislosti na velikosti a složitosti řešení (odhad licence SAP CAL) zobrazí stav aktivní a připravený k použití.

1. Pokud chcete najít virtuální počítače shromážděné s ostatními přidruženými prostředky v jedné skupině prostředků, navštivte Azure Portal: 

   ![Objekty SAP CAL nasazené na novém portálu](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. V portálu SAP CAL se stav zobrazuje jako **aktivní**. Pokud se chcete připojit k řešení, klikněte na **připojit**. V rámci tohoto řešení se nasazují různé možnosti připojení k různým součástem.

   ![Instance SAP CAL](./media/cal-s4h/active_solution.png)

1. Než budete moct použít jednu z možností pro připojení k nasazeným systémům, klikněte na **Začínáme Průvodce**. 

   ![Připojit k instanci](./media/cal-s4h/connect_to_solution.png)

    Dokumentace pro každou metodu připojení pojmenuje uživatele. Hesla pro tyto uživatele jsou nastavena na heslo hlavního serveru, které jste definovali na začátku procesu nasazení. V dokumentaci jsou další funkční uživatelé uvedeni s hesly, které můžete použít k přihlášení do nasazeného systému. 

    Pokud například používáte grafické uživatelské rozhraní SAP, které je předinstalováno na počítači se vzdálenou plochou systému Windows, může to vypadat jako v systému S/4:

   ![SM50 v předinstalovaném grafickém uživatelském rozhraní SAP](./media/cal-s4h/gui_sm50.png)

    Nebo pokud používáte DBACockpit, může tato instance vypadat takto:

   ![SM50 v grafickém uživatelském rozhraní SAP DBACockpit](./media/cal-s4h/dbacockpit.png)

Během několika hodin se v Azure nasadí dobré zařízení SAP S/4.

Pokud jste si koupili předplatné SAP CAL, SAP plně podporuje nasazení prostřednictvím SAP CAL v Azure. Fronta podpory je BC-VCM-CAL.







