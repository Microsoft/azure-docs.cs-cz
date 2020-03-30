---
title: Správa prostředí integračních služeb v Aplikacích Azure Logic Apps
description: Kontrola stavu sítě a správa aplikací logiky, připojení, vlastních konektorů a účtů integrace v prostředí integračních služeb (ISE) pro Aplikace Logika Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: f48106be67763c093a183be01098cab74391752e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284197"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Správa prostředí integračních služeb (ISE) v Aplikacích Azure Logic Apps

Tento článek ukazuje, jak provádět úlohy správy pro [prostředí integračních služeb (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), například:

* Spravujte prostředky, jako jsou aplikace logiky, připojení, účty integrace a konektory ve vaší službě ISE.
* Zkontrolujte stav sítě ise.
* Přidejte kapacitu, restartujte službu ISE nebo odstraňte službu ISE podle pokynů v tomto tématu. Pokud chcete tyto artefakty přidat do služby ISE, přečtěte si informace o [přidání artefaktů do prostředí integrační služby](../logic-apps/add-artifacts-integration-service-environment-ise.md).

## <a name="view-your-ise"></a>Zobrazit ise

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Do vyhledávacího pole portálu zadejte "prostředí integračních služeb" a vyberte **možnost Prostředí integračních služeb**.

   ![Najít prostředí integračních služeb](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. V seznamu výsledků vyberte prostředí integrační služby.

   ![Vybrat prostředí integrační služby](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Pokračujte k dalším částem a najděte ve službě ISE aplikace logiky, připojení, konektory nebo účty integrace.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Kontrola stavu sítě

V nabídce ISE vyberte v části **Nastavení** **položku Stav sítě**. Toto podokno zobrazuje stav podsítí a odchozí závislosti na jiných službách.

![Kontrola stavu sítě](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Správa aplikací logiky

Můžete zobrazit a spravovat aplikace logiky, které jsou ve vašem ISE.

1. V nabídce ISE vyberte v části **Nastavení** **položku Logické aplikace**.

   ![Zobrazení aplikací logiky](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Pokud chcete odebrat aplikace logiky, které už ve službě ISE nepotřebujete, vyberte tyto aplikace logiky a pak vyberte **Odstranit**. Chcete-li potvrdit, že chcete odstranit, vyberte **možnost Ano**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Správa připojení rozhraní API

Můžete zobrazit a spravovat připojení, které byly vytvořeny aplikace logiky spuštěné ve vašem ISE.

1. V nabídce ISE vyberte v části **Nastavení** **připojení rozhraní API**.

   ![Zobrazení připojení rozhraní API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Pokud chcete odebrat připojení, která ve své službu ISE již nepotřebujete, vyberte tato připojení a pak vyberte **Odstranit**. Chcete-li potvrdit, že chcete odstranit, vyberte **možnost Ano**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Správa konektorů ISE

Můžete zobrazit a spravovat konektory rozhraní API, které jsou nasazeny do vašeho ISE.

1. V nabídce ISE vyberte v části **Nastavení** **spravované konektory**.

   ![Zobrazit spravované konektory](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Pokud chcete odebrat konektory, které ve své ise nechcete dostupné, vyberte tyto konektory a pak vyberte **Odstranit**. Chcete-li potvrdit, že chcete odstranit, vyberte **možnost Ano**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Správa vlastních konektorů

Můžete zobrazit a spravovat vlastní konektory, které jste nasadili do služby ISE.

1. V nabídce ISE vyberte v části **Nastavení** **položku Vlastní konektory**.

   ![Vyhledání vlastních konektorů](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Pokud chcete odebrat vlastní konektory, které ve své ise už nepotřebujete, vyberte tyto konektory a pak vyberte **Odstranit**. Chcete-li potvrdit, že chcete odstranit, vyberte **možnost Ano**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Správa účtů pro integraci

1. V nabídce ISE vyberte v části **Nastavení** **položku Účty integrace**.

   ![Najít účty integrace](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Pokud chcete odebrat účty integrace ze své ise, když už není potřeba, vyberte tyto účty integrace a pak vyberte **Odstranit**.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Přidání kapacity prostředí integrační služby (ISE)

Základní jednotka Premium ISE má pevnou kapacitu, takže pokud potřebujete větší propustnost, můžete přidat další jednotky škálování, a to buď během vytváření, nebo později. Skladová položka vývojáře neobsahuje možnost přidat jednotky škálování.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na ise.

1. Chcete-li zkontrolovat metriky využití a výkonu služby ISE, vyberte v nabídce Služby ISE **možnost Přehled**.

   ![Zobrazit využití pro ISE](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. V části **Nastavení**vyberte **Horizontální navýšení kapacity**. V podokně **Konfigurovat** vyberte z těchto možností:

   * [**Ruční škálování**](#manual-scale): Měřítko na základě počtu jednotek zpracování, které chcete použít.
   * [**Vlastní automatické škálování**](#custom-autoscale): Měřítko na základě metrik výkonu výběrem z různých kritérií a určením prahových podmínek pro splnění těchto kritérií.

   ![Vyberte požadovaný typ měřítka.](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Ruční měřítko

1. Po výběru **možnosti Ruční škálování**vyberte v části **Další kapacita**počet jednotek škálování, které chcete použít.

   ![Vyberte požadovaný typ měřítka.](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. Jakmile budete mít hotovo, vyberte **Uložit**.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Vlastní automatické škálování

1. Po **výběru vlastní automatické škálování**, pro **název nastavení automatického škálování**, zadejte název pro vaše nastavení a volitelně vyberte skupinu prostředků Azure, kam nastavení patří.

   ![Zadejte název pro nastavení automatického škálování a vyberte skupinu prostředků.](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. Pro **výchozí** podmínku vyberte **měřítko na základě metriky** nebo **Měřítko pro konkrétní počet instancí**.

   * Pokud zvolíte instanci, zadejte číslo pro jednotky zpracování, což je hodnota od 0 do 10.

   * Pokud zvolíte metriky založené, postupujte takto:

     1. V části **Pravidla** vyberte **Přidat pravidlo**.

     1. V podokně **pravidla škálování** nastavte kritéria a akce, které se mají provést při aktivaci pravidla.

     1. U **limitů instancí**zadejte tyto hodnoty:

        * **Minimální**: Minimální počet zpracovatelských jednotek, které mají být
        * **Maximum**: Maximální počet zpracovatelských jednotek, které mají být
        * **Výchozí**: Pokud při čtení metrik prostředků dojde k problémům a aktuální kapacita je pod výchozí kapacitou, automatické škálování se vyšplhá na výchozí počet jednotek zpracování. Pokud však aktuální kapacita překročí výchozí kapacitu, automatické škálování se neškáluje.

1. Chcete-li přidat další podmínku, vyberte **Přidat podmínku měřítka**.

1. Až skončíte s nastavením automatického škálování, uložte změny.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>Restartování služby ISE

Pokud změníte nastavení serveru DNS nebo serveru DNS, je nutné restartovat službu ISE, aby služba ISE mohla tyto změny vyzvednout. Restartování služby ISE s prémiovou skladovou položkou nevede k prostojům z důvodu redundance a součástí, které se během recyklace restartují po jednom. Však vývojářská skladová položka ISE dochází k prostojům, protože neexistuje žádná redundance. Další informace naleznete v tématu [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na ise.

1. V nabídce ISE vyberte **Přehled**. Na panelu nástrojů Přehled **restartujte**program .

   ![Restartování prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>Odstranění prostředí integrační služby (ISE)

Než odstraníte ise, kterou už nepotřebujete, nebo skupinu prostředků Azure, která obsahuje službu ISE, zkontrolujte, zda nemáte žádné zásady nebo zámky ve skupině prostředků Azure, která obsahuje tyto prostředky, nebo ve vaší virtuální síti Azure, protože tyto položky můžou blokovat odstranění.

Po odstranění ise, budete muset počkat až 9 hodin, než se pokusíte odstranit virtuální síť Azure nebo podsítě.

## <a name="next-steps"></a>Další kroky

* [Přidání prostředků do prostředí integračních služeb](../logic-apps/add-artifacts-integration-service-environment-ise.md)