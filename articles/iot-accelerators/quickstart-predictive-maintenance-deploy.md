---
title: Vyzkoušení cloudového řešení prediktivní údržby IoT v Azure | Microsoft Docs
description: V tomto rychlém startu nasadíte akcelerátor řešení prediktivní údržby Azure IoT, přihlásíte se k řídicímu panelu řešení a budete s ním pracovat.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/12/2018
ms.author: dobett
ms.openlocfilehash: 10ff6565ed8997a5cb87394aa0d743a0d94b67e0
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144308"
---
# <a name="quickstart-try-a-cloud-based-solution-to-run-a-predictive-maintenance-analysis-on-my-connected-devices"></a>Rychlý start: Vyzkoušení cloudového řešení za účelem spuštění analýzy prediktivní údržby na připojených zařízeních

V tomto rychlém startu se dozvíte, jak nasadit akcelerátor řešení prediktivní údržby Azure IoT, abyste mohli spustit cloudovou simulaci prediktivní údržby. Po nasazení akcelerátoru řešení spustíte na stránce **řídicího panelu** řešení analýzu prediktivní údržby u dat ze simulace leteckého motoru. Tento akcelerátor řešení můžete využít jako výchozí bod pro vlastní implementaci nebo jako učební nástroj.

V simulaci Fabrikam představuje regionální leteckou společnost, která se zaměřuje na pohodlí zákazníků za konkurenční ceny. Jednou z příčin zpoždění letů jsou problémy s údržbou, protože údržba leteckých motorů je velmi náročná. Společnost Fabrikam musí za každou cenu zabránit poruchám motorů během letu, a proto své motory pravidelně kontroluje a plánuje údržbu v souladu s plánem. Letecké motory se ale neopotřebovávají všechny stejně. Některou údržbu motorů není vždy nutné provádět. Naproti tomu se můžou vyskytnout problémy, kvůli kterým musí letadlo zůstat na zemi, dokud není opravené. Pokud je letadlo v místě, kde nejsou k dispozici vhodní technici nebo náhradní díly, pak tyto problémy můžou být zvláště nákladné.

Motory letadel společnosti Fabrikam jsou vybaveny snímači, které monitorují stav motoru během letu. Z údajů o provozu a selháních motorů nashromážděných za mnoho let analytici společnosti Fabrikam vytvořili model, který předpovídá zbývající dobu životnosti (RUL) leteckého motoru. Tento model využívá korelaci mezi daty ze čtyř snímačů v motoru a opotřebením motoru, které vede k jeho případnému selhání. I když společnost Fabrikam stále provádí pravidelné kontroly k zajištění bezpečnosti, může pomocí modelu vypočítat zbývající životnost jednotlivých motorů po každém letu. Společnost Fabrikam nyní může předvídat budoucí riziková místa a tím minimalizovat prostoje letadel a snižovat provozní náklady a současně zajistit bezpečnost cestujících i posádek.

K dokončení tohoto rychlého startu potřebujete aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="deploy-the-solution"></a>Nasazení řešení

Při nasazování akcelerátoru řešení do předplatného Azure je potřeba nastavit několik možností konfigurace.

Přihlaste se k webu [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) pomocí přihlašovacích údajů svého účtu Azure.

Klikněte na **Vyzkoušejte** na dlaždici **Prediktivní údržba**.

![Volba dlaždice Prediktivní údržba](./media/quickstart-predictive-maintenance-deploy/predictivemaintenance.png)

Na stránce **vytvoření řešení prediktivní údržby** zadejte jedinečný **název řešení** pro akcelerátor řešení prediktivní údržby. Pro účely tohoto rychlého startu používáme název **MyPredictiveMaintenance**.

Vyberte **Předplatné** a **Oblast**, které chcete při nasazování akcelerátoru řešení použít. Obvykle byste měli zvolit oblast, která je vám nejblíže. V tomto rychlém zprovoznění používáme možnosti **Visual Studio Enterprise** a **Východní USA**. V předplatném musíte být [globálním správcem nebo uživatelem](iot-accelerators-permissions.md).

Kliknutím na **Vytvořit řešení** zahájíte nasazování. Dokončení tohoto procesu trvá minimálně pět minut:

![Podrobnosti řešení prediktivní údržby](./media/quickstart-predictive-maintenance-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Přihlášení k řešení

Po dokončení nasazení předplatného Azure se na dlaždici řešení zobrazí zelené zaškrtnutí a **Připraveno**. Nyní se můžete přihlásit k řídicímu panelu akcelerátoru řešení prediktivní údržby.

Na stránce **Zřízená řešení** klikněte na nový akcelerátor řešení prediktivní údržby. Na panelu, který se zobrazí, si můžete prohlédnout informace o akcelerátoru řešení. Zvolte **Řídicí panel řešení** a zobrazte akcelerátor řešení prediktivní údržby:

![Panel řešení](./media/quickstart-predictive-maintenance-deploy/solutionpanel.png)

Kliknutím na **Přijmout** přijměte žádost o oprávnění. Ve vašem prohlížeči se zobrazí řídicí panel řešení prediktivní údržby:

![Řídicí panel řešení](./media/quickstart-predictive-maintenance-deploy/solutiondashboard.png)

Kliknutím na **Spustit simulaci** zahajte simulaci. Na řídicím panelu se zobrazí historie hodnot snímačů, zbývající doba životnosti (RUL), počet cyklů a historie hodnot RUL:

![Spuštěná simulace](./media/quickstart-predictive-maintenance-deploy/simulationrunning.png)

Když je zbývající doba životnosti (RUL) nižší než 160 (libovolná prahová hodnota zvolená pro demonstrační účely), portál řešení zobrazí vedle hodnoty RUL symbol upozornění. Portál řešení také zvýrazní daný motor letadla žlutou barvou. Můžete si všimnout, že hodnoty zbývající doby životnosti (RUL) mají obecné klesající trend, ale kolísají nahoru a dolů. Toto chování vyplývá z různých délek cyklu a přesnosti modelu.

![Upozornění simulace](./media/quickstart-predictive-maintenance-deploy/simulationwarning.png)

Při úplné simulaci trvá dokončení 148 cyklů asi 35 minut. Prahová hodnota 160 pro zbývající dobu životnosti (RUL) je poprvé dosažena přibližně po 5 minutách simulace a oba motory se na tuto hodnotu dostanou přibližně po 8 minutách.

Simulace zpracuje úplnou datovou sadu s údaji o 148 cyklech a vytvoří konečnou hodnotu RUL a cyklů.

Simulaci lze zastavit v libovolný okamžik, ale kliknutím na tlačítko **Start simulace** spustíte simulaci znovu od začátku datové sady.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v prozkoumávání, ponechte akcelerátor řešení prediktivní údržby nasazený.

Pokud už akcelerátor řešení nepotřebujete, odstraňte ho na stránce [Zřízená řešení](https://www.azureiotsolutions.com/Accelerators#dashboard) tak, že ho vyberete a pak kliknete na **Odstranit řešení**:

![Odstranění řešení](media/quickstart-predictive-maintenance-deploy/deletesolution.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili akcelerátor řešení prediktivní údržby a spustili jste simulaci.

Další informace o akcelerátoru řešení a simulovaných leteckých motorech najdete v následujícím článku.

> [!div class="nextstepaction"]
> [Přehled akcelerátorů řešení prediktivní údržby](iot-accelerators-predictive-walkthrough.md)
