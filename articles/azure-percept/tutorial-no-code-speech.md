---
title: Vytvoření hlasového asistenta pomocí Azure Percept DK a Azure Percept audio
description: Naučte se vytvářet a nasazovat řešení pro rozpoznávání řeči bez kódu do Azure Percept DK.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 76333e11916641be71c72ce6142cd59b496a17e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023159"
---
# <a name="create-a-voice-assistant-with-azure-percept-dk-and-azure-percept-audio"></a>Vytvoření hlasového asistenta pomocí Azure Percept DK a Azure Percept audio

V tomto kurzu vytvoříte hlasového asistenta ze šablony, která se bude používat pro Azure Percept DK a Azure Percept audio. Ukázka hlasového asistenta se spouští v rámci [Azure Percept studia](https://go.microsoft.com/fwlink/?linkid=2135819) a obsahuje výběr hlasových virtuálních objektů řízených hlasem. Chcete-li řídit objekt, řekněte klíčové slovo, což je slovo nebo krátká fráze, která probudit vaše zařízení a za ním následuje příkaz. Každá šablona reaguje na sadu konkrétních příkazů.

Tato příručka vás provede procesem nastavení vašich zařízení, vytváření hlasového asistenta a nezbytných prostředků [služby Speech Services](../cognitive-services/speech-service/overview.md) , testování hlasového asistenta, konfigurace klíčového slova a vytváření vlastních klíčových slov.

## <a name="prerequisites"></a>Požadavky

- Azure Percept DK (DevKit)
- Zvuk Azure Percept
- Mluvčí nebo sluchátka, které se můžou připojit ke zvukové zdířkě 3,5 mm (volitelné)
- [Předplatné Azure](https://azure.microsoft.com/free/)
- [Prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): připojili jste DevKit k síti Wi-Fi, vytvořili IoT Hub a připojili jste devkit k IoT Hub
- [Nastavení služby Azure Percept audio](./quickstart-percept-audio-setup.md)


## <a name="create-a-voice-assistant-using-an-available-template"></a>Vytvoření hlasového asistenta pomocí dostupné šablony

1. Přejděte do [Azure Percept studia](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Otevřete kartu **ukázky & kurzy** .

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Snímek obrazovky Azure Portal domovské stránky":::

1. Klikněte na tlačítko **vyzkoušet šablony hlasového asistenta** v části **kurzy a ukázky řeči**. Otevře se okno na pravé straně obrazovky.

1. V okně proveďte následující:

    1. V rozevírací nabídce **IoT Hub** vyberte Centrum IoT, ke kterému je připojená vaše DevKit.

    1. V rozevírací nabídce **zařízení** vyberte své DevKit.

    1. Vyberte jednu z dostupných šablon hlasového asistenta.

    1. Zaškrtněte políčko **Souhlasím s podmínkami & podmínky pro tento projekt** .

    1. Klikněte na **Vytvořit**.

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="Snímek obrazovky s vytvořením šablony hlasového asistenta":::

1. Po kliknutí na **vytvořit** se portál otevře další okno, ve kterém se vytvoří prostředek motivu řeči. V okně proveďte následující:

    1. V poli **předplatné** vyberte své předplatné Azure.

    1. V rozevírací nabídce **skupiny prostředků** vyberte upřednostňovanou skupinu prostředků. Pokud chcete vytvořit novou skupinu prostředků pro používání hlasového asistenta, klikněte v rozevírací nabídce na **vytvořit** a postupujte podle pokynů.

    1. Jako **předpona aplikace** zadejte název. Toto bude předpona pro váš projekt a název vlastního příkazu.

    1. V části **oblast** vyberte oblast, do které chcete prostředky nasadit.

    1. V části **cenová úroveň předpovědi Luis** vyberte **Standard** (úroveň Free nepodporuje požadavky na řeč).

    1. Klikněte na tlačítko **Vytvořit**. Prostředky pro aplikaci hlasového asistenta se nasadí do vašeho předplatného.

        > [!WARNING]
        > Nezavírejte **okno** , dokud portál nedokončí nasazení prostředku. Předčasné zavření okna může vést k neočekávanému chování hlasového asistenta. Po nasazení prostředku se zobrazí ukázka.

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="Snímek obrazovky s oknem výběru předplatného a skupiny prostředků":::

## <a name="test-out-your-voice-assistant"></a>Test hlasového asistenta

Pokud chcete s hlasovým asistentem pracovat, řekněme klíčové slovo následované příkazem. Když ušní SoM rozpozná vaše klíčové slovo, zařízení pošle CHIME (což vám může slyšet, pokud jsou připojené mluvčí nebo sluchátka), a diody LED budou blikat modrou. Diody LED budou při zpracování příkazu přepnuty na dostihy modro. Reakce hlasového asistenta na váš příkaz se vytiskne v okně ukázka a vygeneruje audiblyy prostřednictvím mluvčího nebo sluchátek. Výchozí klíčové slovo (uvedené vedle možnosti **vlastní klíčové slovo**) je nastavené na "počítač" a Každá šablona obsahuje sadu kompatibilních příkazů, které vám umožní pracovat s virtuálními objekty v ukázkovém okně. Pokud například používáte ukázku pohostinství nebo zdravotní péče, vyslovte si "počítač, zapněte televizi" a zapněte virtuální televizní program.

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="Snímek obrazovky s ukázkovým oknem pohostinství":::

### <a name="hospitality-and-healthcare-demo-commands"></a>Pohostinství a ukázkové příkazy pro zdravotní péči

Ukázky zdravotnictví i pohostinství mají virtuální televizory, světla, rolety a termostaty, se kterými můžete pracovat. Podporují se následující příkazy (a další variace):

* "Zapněte nebo vypněte světla."
* "Zapnout/vypnout televizor."
* "Zapnout/vypnout AC."
* "Otevřít/zavřít rolety."
* "Nastavit teplotu na X stupňů." (X je požadovaná teplota, třeba 75.)

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="Snímek obrazovky s oknem ukázek zdravotní péče":::

### <a name="automotive-demo-commands"></a>Ukázkové příkazy pro automobil

Ukázka automobilu má teplého, odmrazení a termostat, se kterým můžete pracovat. Podporují se následující příkazy (a další variace):

* "Zapněte nebo vypněte odmrazení."
* "Zapněte nebo vypněte kapesní stanici."
* "Nastavit teplotu na X stupňů." (X je požadovaná teplota, třeba 75.)
* "Zvětšit/zmenšit teplotu o Y stupňů."


:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="Snímek obrazovky s podoknem ukázky automobilu":::

### <a name="inventory-demo-commands"></a>Ukázkové příkazy inventáře

Ukázka inventarizace obsahuje výběr Virtual modrých, žlutých a zelených polí pro interakci s aplikací virtuálního inventáře společně s aplikací. Podporují se následující příkazy (a další variace):

* Přidat/odebrat pole X (X je počet polí, třeba 4.)
* Pole "Order/expedice X".
* "Kolik krabic je na skladě?"
* "Počet polí Y". (Y je barva polí, například žlutá.)
* "Dodat všechno na skladě."


:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="Snímek obrazovky s ukázkovým oknem inventáře":::

## <a name="configure-your-keyword"></a>Konfigurace klíčového slova

Můžete přizpůsobit klíčové slovo pro aplikaci hlasového asistenta.

1. V ukázkovém okně klikněte na **změnit** u **klíčového slova Custom** .

1. Vyberte jedno z dostupných klíčových slov. Budete si moct vybrat z výběru ukázkových klíčových slov a všech vlastních klíčových slov, která jste vytvořili.

1. Klikněte na **Uložit**.

### <a name="create-a-custom-keyword"></a>Vytvoření vlastního klíčového slova

Pro hlasovou aplikaci můžete vytvořit vlastní klíčové slovo. Školení vlastního klíčového slova může být dokončeno během několika minut.

1. Klikněte na **+ vytvořit vlastní klíčové slovo** v horní části okna ukázka. 

1. Zadejte požadované klíčové slovo, které může být jedno slovo nebo krátká fráze.

1. Vyberte **prostředek řeči** (Tento příkaz se zobrazí v poli Ukázka vedle **vlastního příkazu** a obsahuje předponu vaší aplikace).

1. Klikněte na **Uložit**. 

## <a name="create-a-custom-command"></a>Vytvoření vlastního příkazu

Portál také nabízí funkce pro vytváření vlastních příkazů se stávajícími prostředky pro rozpoznávání řeči. "Vlastní příkaz" odkazuje na samotnou aplikaci hlasového asistenta, nikoli na konkrétní příkaz v rámci existující aplikace. Vytvořením vlastního příkazu vytvoříte nový projekt řeči, který budete muset dále vyvíjet v [studiu řeči](https://speech.microsoft.com/).

Pokud chcete vytvořit nový vlastní příkaz v okně demo, klikněte na **+ vytvořit vlastní příkaz** v horní části stránky a postupujte takto:

1. Zadejte název vlastního příkazu.

1. Zadejte popis projektu (volitelné).

1. Vyberte preferovaný jazyk.

1. Vyberte prostředek pro rozpoznávání řeči.

1. Vyberte prostředek LUIS.

1. Vyberte prostředek pro vytváření LUIS nebo vytvořte nový.

1. Klikněte na **Vytvořit**.

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="Snímek obrazovky s oknem pro vytváření vlastních příkazů":::

Když vytvoříte vlastní příkaz, musíte přejít na [řeč Studio](https://speech.microsoft.com/) pro další vývoj. Pokud otevřete Speech Studio a v seznamu nevidíte svůj vlastní příkaz, postupujte takto:

1. Na panelu nabídky na levé straně v Azure Percept studiu klikněte na **řeč** v části **projekty AI**.

1. Vyberte kartu **příkazy** .

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="Snímek obrazovky se seznamem vlastních příkazů, které lze upravit.":::

1. Vyberte vlastní příkaz, který chcete vyvíjet. Tím otevřete projekt v aplikaci Speech Studio.

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Obrazovka domovské obrazovky sady Speech Studio":::

Další informace o vývoji vlastních příkazů najdete v [dokumentaci ke službě Speech Service](../cognitive-services/speech-service/custom-commands.md).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>Hlasový asistent se vytvořil, ale nereaguje na příkazy.

Ověřte indikátory LED na vývěsce:

* Tři plná modrá světla označují, že hlasový asistent je připravený a čeká na klíčové slovo.
* Pokud je INDIKÁTORem středu (L02) bílá, dokončila se inicializace DevKit a musí být nakonfigurovaná pomocí klíčového slova.
* Pokud je na středovém LED (L02) nastavené blikání, zvuk SoM ještě nedokončil inicializaci. Dokončení inicializace může trvat několik minut.

Další informace o indikátorech LED naleznete v [článku indikátoru LED](./audio-button-led-behavior.md).

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>Hlasový asistent nereaguje na vlastní klíčové slovo vytvořené v studiu řeči.

Tato situace může nastat, pokud je modul řeči zastaralý. Pomocí těchto kroků aktualizujte modul řeči na nejnovější verzi:

1. V levém panelu nabídky na domovské stránce Azure Percept studia klikněte na **zařízení** .

1. Vyhledejte a vyberte své zařízení.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Snímek obrazovky se seznamem zařízení v Azure Percept studiu":::

1. V okně zařízení vyberte kartu **řeč** .

1. Podívejte se na verzi modulu Speech. Pokud je k dispozici aktualizace, zobrazí se vedle čísla verze tlačítko **aktualizace** .

1. Kliknutím na **aktualizovat** nasadíte aktualizaci modulu Speech. Dokončení procesu aktualizace obvykle trvá 2-3 minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení práce s aplikací hlasového asistenta pomocí těchto kroků vyčistěte prostředky pro rozpoznávání řeči, které jste nasadili během tohoto kurzu:

1. Z [Azure Portal](https://portal.azure.com)v levém panelu nabídky vyberte **skupiny prostředků** nebo je zadejte do panelu hledání.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Snímek obrazovky domovské stránky Azure Portal zobrazující levou nabídku a skupiny prostředků.":::

1. Vyberte skupinu prostředků.

1. Vyberte všechny 6 prostředků obsahující předponu aplikace a klikněte na ikonu **Odstranit** v horním panelu nabídky.
\
    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="Snímek obrazovky s prostředky řeči vybranými k odstranění":::

1. Odstranění potvrďte tak, že v potvrzovacím poli zadáte **Ano** , ověříte, že jste vybrali správné prostředky a kliknete na **Odstranit**.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Snímek obrazovky s potvrzovacím oknem pro odstranění":::

> [!WARNING]
> Tím se odeberou všechna vlastní klíčová slova vytvořená pomocí prostředků řeči, které odstraňujete, a ukázka hlasového asistenta už nebude fungovat.

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili řešení pro rozpoznávání řeči bez kódu, zkuste pro Azure Percept DK vytvořit [řešení pro vize bez kódu](./tutorial-nocode-vision.md) .