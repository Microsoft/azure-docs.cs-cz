---
title: Správa prostředí integračních služeb v Azure Logic Apps
description: Projděte si stav sítě a spravujte aplikace logiky, připojení, vlastní konektory a účty pro integraci v prostředí ISE (Integration Service Environment) pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: 4df9543e5e747de640562b7e5be224e257e0cfd1
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676103"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Správa prostředí integrační služby (ISE) v Azure Logic Apps

Tento článek ukazuje, jak provádět úlohy správy pro [prostředí integračních služeb (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), například:

* Spravujte prostředky, jako jsou Logic Apps, připojení, účty pro integraci a konektory v ISE.

* Ověřte stav sítě v ISE.

* Přidejte kapacitu, restartujte ISE nebo odstraňte své ISE, postupujte podle kroků v tomto tématu. Pokud chcete přidat tyto artefakty do svého ISEu, přečtěte si téma [Přidání artefaktů do prostředí integrační služby](../logic-apps/add-artifacts-integration-service-environment-ise.md).

## <a name="view-your-ise"></a>Zobrazení ISE

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Do vyhledávacího pole na portálu zadejte "prostředí integračních služeb" a pak vyberte **prostředí integrační služby** .

   ![Hledání prostředí integrační služby](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. V seznamu výsledků vyberte prostředí integrační služby.

   ![Vybrat prostředí integrační služby](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Přejděte k dalším oddílům, kde najdete Logic Apps, připojení, konektory nebo účty pro integraci v ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Ověřit stav sítě

V nabídce ISE v části **Nastavení** vyberte **stav sítě** . V tomto podokně se zobrazuje stav podsítí a odchozí závislosti v jiných službách.

![Ověřit stav sítě](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

> [!CAUTION]
> Pokud se vaše síť ISE stane ve špatném stavu, může se stát, že se interní App Service Environment (pomocného mechanismu), který používá váš ISE, stane také špatným. Pokud není stav pomocného mechanismu v pořádku po dobu delší než sedm dnů, pozastaví se pomocného mechanismu. Chcete-li tento stav vyřešit, ověřte nastavení virtuální sítě. Vyřešte všechny problémy, které najdete, a pak restartujte ISE. V opačném případě se po 90 dnech odstraní pozastavený pomocného mechanismu řízení a vaše ISE se stane nepoužitelnou. Ujistěte se, že udržujete ISE v dobrém stavu, abyste umožnili potřebný provoz.
> 
> Další informace najdete v těchto tématech:
>
> * [Přehled diagnostiky Azure App Service](../app-service/overview-diagnostics.md)
> * [Protokolování zpráv pro Azure App Service Environment](../app-service/environment/using-an-ase.md#logging)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Správa aplikací logiky

Můžete zobrazit a spravovat aplikace logiky, které jsou ve vaší ISE.

1. V nabídce ISE v části **Nastavení** vyberte **Logic Apps** .

   ![Zobrazit Logic Apps](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Pokud chcete odebrat Logic Apps, které už v ISE nepotřebujete, vyberte tyto aplikace logiky a pak vyberte **Odstranit** . Pokud chcete potvrdit, že chcete odstranit, vyberte **Ano** .

> [!NOTE]
> Pokud odstraníte a znovu vytvoříte podřízenou aplikaci logiky, musíte znovu uložit nadřazenou aplikaci logiky. Znovu vytvořená podřízená aplikace bude mít odlišná metadata.
> Pokud neuložíte znovu nadřazenou aplikaci logiky po opětovném vytvoření podřízené aplikace, vaše volání podřízené aplikace logiky se nezdaří s chybou "Neautorizováno". Toto chování se vztahuje na aplikace logiky nadřazeného a podřízeného objektu, například na ty, které používají artefakty v integračních účtech nebo volají službu Azure Functions.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Správa připojení rozhraní API

Můžete zobrazit a spravovat připojení, která vytvořila aplikace logiky spuštěné v ISE.

1. V nabídce ISE v části **Nastavení** vyberte **připojení rozhraní API** .

   ![Zobrazit připojení rozhraní API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Pokud chcete odebrat připojení, která už nepotřebujete v ISE, vyberte Tato připojení a pak vyberte **Odstranit** . Pokud chcete potvrdit, že chcete odstranit, vyberte **Ano** .

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Správa konektorů ISE

Můžete zobrazit a spravovat konektory rozhraní API, které jsou nasazené do vaší ISE.

1. V nabídce ISE v části **Nastavení** vyberte **spravované konektory** .

   ![Zobrazit spravované konektory](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Pokud chcete odebrat konektory, které nechcete mít k dispozici ve vašem ISE, vyberte tyto konektory a pak vyberte **Odstranit** . Pokud chcete potvrdit, že chcete odstranit, vyberte **Ano** .

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Správa vlastních konektorů

Můžete zobrazit a spravovat vlastní konektory, které jste nasadili do ISE.

1. V nabídce ISE v části **Nastavení** vyberte **vlastní konektory** .

   ![Vyhledání vlastních konektorů](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Pokud chcete z ISE odebrat vlastní konektory, které už nepotřebujete, vyberte tyto konektory a pak vyberte **Odstranit** . Pokud chcete potvrdit, že chcete odstranit, vyberte **Ano** .

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Správa účtů pro integraci

1. V nabídce ISE v části **Nastavení** vyberte účty pro **integraci** .

   ![Najít účty pro integraci](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Chcete-li odebrat účty pro integraci z ISE, pokud už je nepotřebujete, vyberte tyto účty pro integraci a pak vyberte **Odstranit** .

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Přidání kapacity prostředí integrační služby (ISE)

Základní jednotka ISE úrovně Premium má pevnou kapacitu, takže pokud potřebujete větší propustnost, můžete přidat další jednotky škálování, a to buď během vytváření, nebo později. SKU vývojáře neobsahuje možnost přidávat jednotky škálování.

1. V [Azure Portal](https://portal.azure.com)přejdete do svého ISEu.

1. Pokud chcete zkontrolovat metriky využití a výkonu pro váš ISE, v nabídce ISE vyberte **Přehled** .

   ![Zobrazit využití pro ISE](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. V části **Nastavení** vyberte **horizontální** navýšení kapacity. V podokně **Konfigurace** vyberte z těchto možností:

   * [**Ruční škálování**](#manual-scale): škálování na základě počtu zpracovávaných jednotek, které chcete použít.
   * [**Vlastní automatické škálování**](#custom-autoscale): škálování na základě metrik výkonu výběrem z různých kritérií a určením mezních podmínek pro splnění těchto kritérií.

   ![Snímek obrazovky zobrazující stránku horizontálního navýšení kapacity s vybraným ručním škálováním](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Ruční škálování

1. Po výběru **ručního škálování** pro **Další kapacitu** vyberte počet jednotek škálování, které chcete použít.

   ![Vyberte typ škálování, který chcete.](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. Jakmile budete mít hotovo, vyberte **Uložit** .

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Vlastní automatické škálování

1. Po výběru možností **vlastní automatické škálování** , pro **název nastavení automatického škálování** zadejte název vašeho nastavení a volitelně vyberte skupinu prostředků Azure, do které nastavení patří.

   ![Zadejte název pro nastavení automatického škálování a vyberte skupinu prostředků.](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. Pro **výchozí** podmínku vyberte možnost škálování na **základě metriky** nebo **škály na konkrétní počet instancí** .

   * Pokud zvolíte možnost založený na instanci, zadejte číslo pro jednotky zpracování, což je hodnota od 0 do 10.

   * Zvolíte-li možnost založenou na metrikách, postupujte následovně:

     1. V části **pravidla** vyberte **Přidat pravidlo** .

     1. V podokně **pravidlo škálování** nastavte kritéria a akci, která se má provést při triggeru pravidla.

     1. V případě **omezení instancí** zadejte tyto hodnoty:

        * **Minimum** : minimální počet jednotek zpracování, které se mají použít
        * **Maximum** : maximální počet jednotek zpracování, které se mají použít
        * **Výchozí** : Pokud dojde k potížím při čtení metriky prostředků a aktuální kapacita je pod výchozí kapacitou, automatické škálování škáluje na výchozí počet zpracovaných jednotek. Pokud ale aktuální kapacita překročí výchozí kapacitu, automatické škálování se neškáluje.

1. Pokud chcete přidat další podmínku, vyberte **Přidat podmínku škálování** .

1. Až skončíte s nastavením automatického škálování, uložte změny.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>Restartovat ISE

Pokud změníte nastavení serveru DNS nebo serveru DNS, musíte restartovat ISE, aby ISE mohl tyto změny vyzvednout. Restartování ISE SKU úrovně Premium nevede k výpadkům kvůli redundanci a komponentám, které během recyklace restartují jeden po druhém. Nicméně SKU vývojáře ISE výpadky, protože neexistuje žádná redundance. Další informace najdete v tématu [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

1. V [Azure Portal](https://portal.azure.com)přejdete do svého ISEu.

1. V nabídce ISE vyberte **Přehled** . Na panelu nástrojů přehled **restartujte** .

   ![Restartovat prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>Odstranění prostředí integrační služby (ISE)

Než odstraníte ISE, který už nepotřebujete, nebo skupinu prostředků Azure obsahující ISE, ověřte, že nemáte žádné zásady ani zámky ve skupině prostředků Azure, která obsahuje tyto prostředky nebo ve službě Azure Virtual Network, protože tyto položky můžou blokovat odstranění.

Po odstranění ISE možná budete muset počkat až 9 hodin, než se pokusíte odstranit virtuální síť Azure nebo podsítě.

## <a name="next-steps"></a>Další kroky

* [Přidání prostředků do prostředí integrační služby](../logic-apps/add-artifacts-integration-service-environment-ise.md)
