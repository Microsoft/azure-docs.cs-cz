---
title: Upgrade na Azure Defender – Azure Security Center
description: V tomto rychlém startu se dozvíte, jak upgradovat na Azure Defender pro další zabezpečení Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 5e39093e0472705111907e72b70446db53770012
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634480"
---
# <a name="quickstart-set-up-azure-security-center"></a>Rychlý Start: nastavení Azure Security Center

Azure Security Center zajišťuje jednotnou správu zabezpečení a ochranu před hrozbami napříč vašimi hybridními cloudovými úlohami. I když bezplatné funkce nabízí jenom omezené zabezpečení pro vaše prostředky Azure, takže Azure Defender tyto možnosti rozšiřuje na místní a jiné cloudy. Azure Defender vám pomůže najít a opravit chyby zabezpečení, použít řízení přístupu a aplikací k blokování škodlivých aktivit, zjišťovat hrozby pomocí analýz a inteligentních funkcí a rychle reagovat při útoku. Azure Defender si můžete vyzkoušet bez jakýchkoli nákladů. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

Tento rychlý Start vás provede povolením Azure Defenderu pro zvýšení zabezpečení a instalaci agenta Log Analytics do počítačů za účelem monitorování ohrožení zabezpečení a hrozeb.

Proveďte následující kroky:

> [!div class="checklist"]
> * Povolení Security Center v předplatném Azure
> * Povolení Azure Defenderu v předplatném Azure
> * Povolit automatické shromažďování dat

## <a name="prerequisites"></a>Požadavky
Pokud chcete začít využívat Security Center, musíte mít předplatné pro Microsoft Azure. Pokud nemáte předplatné, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

Pokud chcete povolit Azure Defender v rámci předplatného, musíte mít přiřazenou roli Vlastník předplatného, přispěvatele předplatného nebo správce zabezpečení.


## <a name="enable-security-center-on-your-azure-subscription"></a>Povolení Security Center v předplatném Azure

1. Přihlaste se k webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

1. V nabídce portálu vyberte **Security Center**. 

    Otevře se stránka s přehledem Security Center.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Řídicí panel s přehledem Security Center" lightbox="./media/security-center-get-started/overview.png":::

Stránka **Security Center – Přehled** poskytuje jednotný přehled o stavu hybridních cloudových úloh a umožňuje tak zkoumat a posuzovat zabezpečení úloh a identifikaci a omezení rizika. Security Center automaticky, a to bez nákladů, povolí žádné z vašich předplatných Azure, které jste předtím nepřipojili vy nebo jiný uživatel předplatného.

Seznam předplatných můžete zobrazit a filtrovat tak, že vyberete položku nabídky **předplatná** . Security Center upraví displej tak, aby odrážel stav zabezpečení vybraných předplatných. 

Během několika minut od prvního spuštění Security Center se může zobrazit:

- **Doporučení** , jak zvýšit zabezpečení připojených prostředků.
- Inventarizace vašich prostředků, které se teď Security Center společně s stav zabezpečení každého z nich.

Abyste mohli plně využít výhod Security Center, musíte provést následující postup, abyste mohli Azure Defender povolit a nainstalovat agenta Log Analytics.

> [!TIP]
> Pokud chcete povolit Security Center u všech předplatných v rámci skupiny pro správu, přečtěte si téma [povolení Security Center ve více předplatných Azure](onboard-management-group.md).

## <a name="enable-azure-defender"></a>Povolení Azure Defenderu

Pro účely Security Center rychlých startů a kurzů musíte povolit Azure Defender. K dispozici je bezplatná 30denní zkušební verze. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). 

1. Z bočního panelu Security Center vyberte **Začínáme**.

    :::image type="content" source="./media/security-center-get-started/get-started-upgrade-tab.png" alt-text="Karta upgrade stránky Začínáme"::: 

    Karta **upgrade** obsahuje seznam předplatných a pracovních prostorů, které mají nárok na registraci.

1. V seznamu **Vyberte pracovní prostory, pro které chcete povolit Azure Defender v** seznamu vyberte pracovní prostory, které chcete upgradovat.
   - Pokud vyberete předplatná a pracovní prostory, které nemají nárok na zkušební verzi, další krok se upgraduje a začnou platit poplatky.
   - Pokud vyberete pracovní prostor, který má nárok na bezplatnou zkušební verzi, bude další krok začínat zkušební verzí.
1. Pokud chcete povolit Azure Defender, vyberte **upgradovat** .

## <a name="enable-automatic-data-collection"></a>Povolit automatické shromažďování dat
Security Center shromažďuje data z vašich počítačů, aby je bylo možné monitorovat kvůli ohrožení zabezpečení a hrozbám. Data se shromažďují pomocí Log Analytics agenta, který čte různé konfigurace a protokoly událostí související se zabezpečením z počítače a kopíruje data do pracovního prostoru pro účely analýzy. Ve výchozím nastavení pro vás Security Center vytvoří nový pracovní prostor.

Pokud je povoleno automatické zřizování, Security Center nainstaluje agenta Log Analytics na všech podporovaných počítačích a všech nově vytvořených. Automatické zřizování se důrazně doporučuje.

Povolení automatického zřizování agenta Log Analytics:

1. V nabídce Security Center vyberte **cenové & nastavení**.
1. Vyberte příslušné předplatné.
1. V případě **automatického zřizování** na stránce **Agent Log Analytics pro virtuální počítače Azure** nastavte stav na **zapnuto**.
1. Vyberte **Uložit**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Povolení automatického zřizování agenta Log Analytics":::

>[!TIP]
> Pokud je potřeba zřídit pracovní prostor, může instalace agenta trvat až 25 minut.

S agentem nasazeným pro vaše počítače může Security Center poskytovat další doporučení týkající se stavu aktualizace systému, konfigurace zabezpečení operačního systému, ochrany koncových bodů a také generování dalších výstrah zabezpečení.

>[!NOTE]
> Nastavení automatického zřizování na **vypnuto** neodebere agenta Log Analytics z virtuálních počítačů Azure, ve kterých se agent už zřídil. Vypnutí automatického zřizování omezí sledování zabezpečení pro vaše prostředky.



## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste povolili Azure Defender a zřídili Log Analytics agenta pro jednotnou správu zabezpečení a ochranu před hrozbami napříč vašimi úlohami hybridního cloudu. Další informace o používání služby Security Center najdete v rychlém startu pro připojení počítačů s Windows v místním prostředí a jiných cloudech.

> [!div class="nextstepaction"]
> [Rychlý Start: zprovoznění počítačů mimo Azure](quickstart-onboard-machines.md)

Chcete optimalizovat a uložit své útraty do cloudu?

> [!div class="nextstepaction"]
> [Zahájení analýzy nákladů pomocí Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png