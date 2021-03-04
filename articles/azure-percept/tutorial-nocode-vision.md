---
title: Vytvoření řešení pro vize bez kódu v Azure Percept studiu
description: Naučte se, jak vytvořit řešení vize bez kódu ve službě Azure Percept Studio a nasadit ho do Azure Percept DK.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 6de86cbc065b5352b3b643708dd55c6856b37dd7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097903"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>Vytvoření řešení pro vize bez kódu v Azure Percept studiu

Azure Percept Studio umožňuje vytvářet a nasazovat vlastní řešení pro počítačové zpracování obrazu bez nutnosti psaní kódu. V tomto článku provedete následující:

- Vytvoření projektu vize v [Azure Percept studiu](https://go.microsoft.com/fwlink/?linkid=2135819)
- Shromažďování školicích imagí pomocí DevKit
- Označení školicích imagí v [Custom Vision](https://www.customvision.ai/)
- Výuka vlastního modelu detekce nebo klasifikace objektů
- Nasazení modelu do DevKit
- Vylepšení modelu nastavením přeškolení

Tento kurz je vhodný pro vývojáře s malým množstvím bez použití AI a stejně jako Začínáme se službou Azure Percept.

## <a name="prerequisites"></a>Požadavky

- Azure Percept DK (DevKit)
- [Předplatné Azure](https://azure.microsoft.com/free/)
- Prostředí pro instalaci Azure Percept DK: připojili jste DevKit k síti Wi-Fi, vytvořili IoT Hub a připojili jste DevKit k IoT Hub

## <a name="create-a-vision-prototype"></a>Vytvoření prototypu vize

1. Spusťte prohlížeč a navštivte [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Na stránce Přehled klikněte na kartu **ukázky & kurzy** .  :::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Obrazovka s přehledem Azure Percept Studio" lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. V části **kurzy a ukázky pro vize** klikněte na **vytvořit prototyp vize**.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Obrazovky s ukázkami a kurzy pro Azure Percept Studio" lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. Na stránce **nový prototyp Custom Vision Azure Percept** postupujte takto:

    1. Do pole **název projektu** zadejte název prototypu vize.

    1. Do pole **Popis projektu** zadejte popis prototypu vize.

    1. V rozevírací nabídce **typ zařízení** vyberte **Azure Percept DK** .

    1. V rozevírací nabídce **prostředek** vyberte prostředek nebo klikněte na **vytvořit nový prostředek**. Pokud se rozhodnete vytvořit nový prostředek, v okně **vytvořit** proveďte následující:
        1. Zadejte název nového prostředku.
        1. Vyberte své předplatné Azure.
        1. Vyberte skupinu prostředků nebo vytvořte novou.
        1. Vyberte preferovanou oblast.
        1. Vyberte si cenovou úroveň (doporučujeme S0).
        1. V dolní části okna klikněte na **vytvořit** .

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="Vytvořit okno prostředků":::

    1. V případě **typu projektu** vyberte, zda projekt vize provede detekci objektu nebo klasifikaci obrázku. Další informace o typech projektů získáte kliknutím na tlačítko **pomoc při výběru**.

    1. V případě **optimalizace** vyberte, zda chcete optimalizovat projekt pro přesnost, nízkou latenci sítě nebo rovnováhu obou.

    1. Klikněte na tlačítko **Vytvořit**.

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="Vytvoření vlastní obrazovky prototypu vize":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>Připojení zařízení k projektu a zachycení imagí

Po vytvoření řešení Vision musíte do něj přidat své DevKit a odpovídající IoT Hub.

1. Zapněte si DevKit.

1. V rozevírací nabídce **IoT Hub** vyberte Centrum IoT, ke kterému se vaše DevKit připojilo během počátečního nastavení.

1. V rozevírací nabídce **zařízení** vyberte své DevKit.

Dále musíte načíst obrázky nebo zachytit image pro školení modelu AI. U každého typu značek doporučujeme nahrávat aspoň 30 imagí. Například pokud chcete sestavit detektor psa a Cat, je nutné nahrát nejméně 30 imagí psi a 30 imagí koček. Pokud chcete zachytit obrázky s modelem DevKit, udělejte toto:

1. V okně **zachycení bitové kopie** vyberte možnost **Zobrazit datový proud zařízení** a zobrazte datový proud videa Vision SOM.

1. Zkontrolujte Stream videa, abyste měli jistotu, že máte správně zarovnaný fotoaparát služby Vision SoM, abyste mohli pořizovat školicí snímky. Proveďte úpravy podle potřeby.

1. V okně **zachycení obrázku** klikněte na možnost **pořídit fotografii**.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="Obrazovka zachycení obrázku":::

1. Případně můžete nastavit automatické zachycení obrázků pro shromažďování velkého množství imagí v čase zaškrtnutím políčka **Automatické zachycení obrázku** . Vyberte preferovanou rychlost zpracování obrazu v části **rychlost zachycení** a celkový počet imagí, které chcete shromažďovat v rámci **cíle**. Kliknutím na **nastavit automatické zachytávání** zahajte proces automatického zachycení obrázku.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="Rozevírací nabídka automatického zachycení obrázku":::

Máte-li dostatek fotografií, klikněte na tlačítko **Další: označit obrázky a školení modelů** v dolní části obrazovky. Všechny obrázky budou uloženy v [Custom Vision](https://www.customvision.ai/).

> [!NOTE]
> Pokud se rozhodnete odeslat školicí snímky přímo do Custom Vision, počítejte s tím, že velikost souboru obrázku nemůže být větší než 6MB.

## <a name="tag-images-and-train-your-model"></a>Označení obrázků a výuka modelu

Před školením modelu přidejte k obrázkům popisky.

1. Na stránce **obrázky značek a školení modelů** klikněte na **otevřít projekt v Custom Vision**.

1. Na levé straně stránky **Custom Vision** klikněte na **neoznačeno** pod **značkou** . zobrazí se obrázky, které jste právě shromáždili v předchozím kroku. Vyberte jednu nebo více netagovaných imagí.

1. V okně **Podrobnosti o imagi** klikněte na obrázek a začněte označovat značky. Pokud jste vybrali možnost detekce objektu jako typ projektu, musíte také nakreslit [ohraničující rámeček](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#upload-and-tag-images) kolem konkrétních objektů, které chcete označit. Podle potřeby upravte ohraničovací rámeček. Zadejte značku objektu a kliknutím **+** použijte značku. Například pokud jste vytvořili řešení Vision, které vás upozorní, když je potřeba skladovat úložiště, přidejte značku "prázdná police" do imagí prázdné police a přidejte značku "plná poli" do imagí pro plně skladovaná police. Opakujte u všech netagovaných imagí.

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="Obrazovka označování obrázku v Custom Vision.":::

1. Po označení obrázků klikněte na ikonu **X** v pravém horním rohu okna. Kliknutím na **tagované** v části **značky** zobrazíte všechny nově označené obrázky.

1. Po označení obrázků jste připraveni ke výukě svého modelu AI. Provedete to tak, že kliknete na možnost **vlak** v horní části stránky. Pro výuku modelu musíte mít aspoň 15 imagí na typ značek. doporučujeme použít aspoň 30. Školení obvykle trvá přibližně 30 minut, ale může trvat déle, pokud je sada imagí velmi velká.

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="Školicí výběr obrazu s zvýrazněným výukovým tlačítkem":::

1. Po dokončení školení se na obrazovce zobrazí výkon vašeho modelu. Další informace o vyhodnocení těchto výsledků najdete v [dokumentaci k vyhodnocení modelu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#evaluate-the-detector). Po školení si můžete také [vyzkoušet svůj model](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/test-your-model) na dalších obrázcích a v případě potřeby ho přeškolit. Pokaždé, když vytvoříte model, uloží se jako nová iterace. Další informace o tom, jak zvýšit výkon modelu, najdete v [dokumentaci k Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier) .

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="Výsledky školení modelu.":::

    > [!NOTE]
    > Pokud se rozhodnete Testovat model na dalších obrázcích v Custom Vision, počítejte s tím, že velikost testovacího souboru bitové kopie nemůže být větší než 4 MB.

Až budete s výkonem svého modelu spokojeni, zavřete ho Custom Vision zavřením karty prohlížeče.

## <a name="deploy-your-ai-model"></a>Nasazení modelu AI

1. Vraťte se na kartu Azure Percept Studio a klikněte na **Další: vyhodnotit a nasadit** v dolní části obrazovky.

1. V okně **vyhodnocení a nasazení** se zobrazí výkon vybrané iterace modelu. V rozevírací nabídce **model iterace** vyberte iteraci, kterou chcete nasadit do svého devkitu, a v dolní části obrazovky klikněte na **nasadit model** .

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="Obrazovka nasazení modelu." lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. Po nasazení modelu si zobrazte datový proud videa vašeho zařízení a podívejte se, jaký model Inferencing v akci.

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="Datový proud zařízení znázorňující detektor sluchátek v akci.":::

Po zavření tohoto okna se můžete kdykoli vrátit a upravit projekt vize kliknutím na možnost **vize** v části **projekty AI** na domovské stránce Azure Percept Studio a výběrem názvu projektu vize.

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="Stránka projektu vize" lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="improve-your-model-by-setting-up-retraining"></a>Vylepšení modelu nastavením přeškolení

Jakmile provedete svůj model a nasadíte ho do zařízení, můžete zvýšit výkon modelu nastavením parametrů přeškolení pro zachycení více školicích dat. Tato funkce se používá ke zlepšení výkonu vycvičeného modelu tím, že poskytuje možnost zachycení imagí na základě rozsahu pravděpodobnosti. Můžete například nastavit, aby zařízení zachytávání pouze školicích snímků, když je pravděpodobnost nízká. Tady je několik [dalších pokynů](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier) pro přidání dalších imagí a vyvážení školicích dat.

1. Pokud chcete nastavit opětovné školení, vraťte se do **projektu** a pak na **Souhrn projektu** .
1. Na kartě **zachycení obrázku** vyberte **Automatické zachycení obrázků** a **nastavte rekurzi**.
1. Zaškrtnutím políčka **Automatické zachycení obrázku** nastavte automatický záznam obrázků pro shromažďování velkého množství imagí najednou.
1. Vyberte preferovanou rychlost zpracování obrazu v části **rychlost zachycení** a celkový počet imagí, které chcete shromažďovat v rámci **cíle**.
1. V části **Nastavení rekurze** vyberte iteraci, pro kterou chcete zachytit více školicích dat, a pak vyberte rozsah pravděpodobnosti. Do projektu se nahrají jenom obrázky, které splňují míru pravděpodobnosti.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-image-capture.png" alt-text="zachycení bitové kopie.":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste pro tento kurz vytvořili nový prostředek Azure a už nechcete vyvíjet ani používat řešení Vision, provedete následující kroky a odstraníte svůj prostředek:

1. Přejděte na [Azure Portal](https://ms.portal.azure.com/).
1. Klikněte na **všechny prostředky**.
1. Klikněte na zaškrtávací políčko vedle prostředku vytvořeného v tomto kurzu. Typ prostředku bude uveden jako **Cognitive Services**.
1. Klikněte na ikonu **Odstranit** v horní části obrazovky.

## <a name="next-steps"></a>Další kroky

V dalším kroku se podívejte na články s návody, kde najdete informace o dalších funkcích řešení Vision v Azure Percept studiu.

<!--
Add links to how-to articles and oobe article.
-->
