---
title: 'Kurz: K analýze faktur pomocí aplikací Azure Logic Apps použijte nástroj Pro rozpoznávání formulářů – nástroj pro rozpoznávání formulářů .'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu použijete nástroj pro rozpoznávání formulářů s aplikacemi Azure Logic Apps k vytvoření pracovního postupu, který automatizuje proces trénovaní modelu a testování pomocí ukázkových dat.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: nitinme
ms.openlocfilehash: d71d9c7e6570e562fe4c692ede1d07b70c923cb6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118271"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Kurz: Analýza faktur pomocí nástroje Pro rozpoznávání formulářů s aplikacemi Azure Logic Apps

V tomto kurzu vytvoříte pracovní postup v Azure Logic Apps, který používá nástroj pro rozpoznávání formulářů, službu, která je součástí sady Azure Cognitive Services, k extrahování dat z faktur. Nejprve tádáte model nástroje pro rozpoznávání formulářů pomocí ukázkové datové sady a potom model otestujete na jiné datové sadě.

Zde je to, co tento výukový program pokrývá:

> [!div class="checklist"]
> * Požádat o přístup k rozpoznávání formulářů
> * Vytvoření kontejneru objektů blob úložiště Azure
> * Nahrání ukázkových dat do kontejneru objektů blob Azure
> * Vytvoření aplikace azure logiky
> * Konfigurace aplikace logiky pro použití prostředku nástroje pro rozpoznávání formulářů
> * Testování pracovního postupu spuštěním aplikace logiky

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/).

## <a name="understand-the-invoice-to-be-analyzed"></a>Principy faktury, která má být analyzována

Ukázková sada dat, kterou použijete k trénování a testování modelu, je k dispozici jako soubor ZIP z [GitHubu](https://go.microsoft.com/fwlink/?linkid=2090451). Stáhněte a extrahujte soubor ZIP a otevřete soubor PDF faktury ve složce **/Train.** Všimněte si, že má tabulku s číslem faktury, datem faktury a tak dále. 

> [!div class="mx-imgBorder"]
> ![Ukázková faktura](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

V tomto kurzu se dozvíte, jak pomocí pracovního postupu Azure Logic Apps extrahovat informace z tabulek, jako jsou tyto do formátu JSON.

## <a name="create-an-azure-storage-blob-container"></a>Vytvoření kontejneru objektů blob úložiště Azure

Tento kontejner slouží k nahrání ukázkových dat, která jsou vyžadována k trénování modelu.

1. Podle pokynů v [části Vytvoření účtu Úložiště Azure](../../storage/common/storage-account-create.md) vytvořte účet úložiště. Jako název účtu úložiště použijte **formrecostorage.**
1. Postupujte podle pokynů v [části Vytvoření kontejneru objektů blob Azure](../../storage/blobs/storage-quickstart-blobs-portal.md) k vytvoření kontejneru v rámci účtu Azure Storage. Jako název kontejneru použijte **kontejner formreco.** Ujistěte se, že jste nastavili úroveň veřejného přístupu na **Kontejner (anonymní přístup pro čtení pro kontejnery a objekty BLOB).**

    > [!div class="mx-imgBorder"]
    > ![Vytvoření kontejneru objektů blob](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Nahrání ukázkových dat do kontejneru objektů blob Azure

Stáhněte si ukázková data dostupná na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2090451). Extrahujte data do místní složky a nahrajte obsah složky **/Train** do **kontejneru formrecocontainer,** který jste vytvořili dříve. Podle pokynů na [nahrát objekt blob bloku](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) nahrát data do kontejneru.

Zkopírujte adresu URL kontejneru. Tuto adresu URL budete potřebovat později v kurzu. Pokud jste vytvořili účet úložiště a kontejner se stejnými názvy, jak je uvedeno v tomto kurzu, adresa URL bude *\/https: /formrecostorage.blob.core.windows.net/formrecocontainer/*.

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku nástroje pro rozpoznávání formulářů

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

Azure Logic Apps můžete použít k automatizaci a orchestraci úloh a pracovních postupů. V tomto kurzu vytvoříte aplikaci logiky, která se aktivuje přijetím faktury, kterou chcete analyzovat jako přílohu e-mailu. V tomto pracovním postupu provádíte následující úkoly:
* Nakonfigurujte aplikaci logiky tak, aby se automaticky aktivovala, když obdržíte e-mail s připojenou fakturou.
* Nakonfigurujte aplikaci logiky tak, aby používala operaci **modelu trénování** modelu nástroje Pro rozpoznávání formulářů pomocí ukázkových dat, která jste nahráli do úložiště objektů blob Azure.
* Nakonfigurujte aplikaci logiky tak, aby používala operaci **analyzátoru rozpoznávání formulářů** k použití modelu, který jste již trénovaní. Tato součást bude analyzovat fakturu, kterou poskytnete do této aplikace logiky na základě modelu, který trénoval dříve.

Postupujte podle následujících kroků a nastavte pracovní postup.

1. V hlavní nabídce Azure vyberte > Vytvořit > **aplikaci logiky****integrace** **prostředků**.

1. V části **Vytvořit aplikaci logiky** zadejte podrobnosti o vaší aplikaci logiky, jak je vidět zde. Až budete hotovi, vyberte **Vytvořit**.

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | <*název aplikace logika*> | Název aplikace logiky, který může obsahovat pouze`-`písmena,`_`čísla, pomlčky`(`( `)`), podtržítka ( ), závorky ( , ) a tečky (`.`). Tento příklad používá "My-First-Logic-App". |
   | **Předplatné** | <*Název předplatného Azure*> | Název předplatného Azure |
   | **Skupina prostředků** | <*Název skupiny Azure-resource-group*> | Název [skupiny prostředků Azure,](./../../azure-resource-manager/management/overview.md) která slouží k uspořádání souvisejících prostředků. Tento příklad používá "My-First-LA-RG". |
   | **Umístění** | <*Oblast Azure*> | Oblast, kde chcete uložit informace o aplikaci logiky. Tento příklad používá "Západní USA". |
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. |
   ||||

1. Po nasazení aplikace Azure na panelu nástrojů Azure vyberte **Oznámení** > **Přejít na prostředek** pro nasazenou aplikaci logiky. Nebo můžete najít a vybrat aplikaci logiky zadáním názvu do vyhledávacího pole.

   Otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem a běžně používanými triggery. V části **Šablony** vyberte **Prázdná aplikace logiky**.

   > [!div class="mx-imgBorder"]
   > ![Výběr prázdné šablony pro aplikaci logiky](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>Konfigurace aplikace logiky pro aktivaci pracovního postupu při příchodu e-mailu

V tomto kurzu aktivujete pracovní postup při přijetí e-mailu s připojenou fakturou. Tento kurz používá Office 365 jako e-mailovou službu, ale můžete použít libovolného jiného poskytovatele e-mailu, který chcete použít.

1. Na kartách vyberte Vše, vyberte **Office 365 Outlook**a potom v části **Triggers**vyberte **When a new email arrives**.

    ![Aktivace aplikace logiky prostřednictvím příchozího e-mailu](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. V poli **Office 365 Outlook** klikněte na **Přihlásit**se a zadejte podrobnosti pro přihlášení k účtu Office 365.

1. V dalším dialogovém okně proveďte následující kroky.
    1. Vyberte složku, která má být sledována pro všechny nové e-maily.
    1. V **pole Má přílohy**vyberte **Ano**. Tím je zajištěno, že pracovní postup spustí pouze e-maily s přílohami.
    1. V **případě zahrnout přílohy**vyberte **ano**. Tím je zajištěno, že obsah přílohy se používají v následnézpracování.

        > [!div class="mx-imgBorder"]
        > ![Konfigurace aktivační události e-mailu aplikace logiky](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. Nahoře klikněte na **Uložit** z panelu nástrojů.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>Konfigurace aplikace logiky pro použití operace modelu vlaku nástroje pro rozpoznávání formulářů

Před použitím služby Rozpoznávání formulářů k analýze faktur je třeba trénovat model tím, že mu poskytnete data ukázkových faktur, která může model analyzovat a učit se.

1. Vyberte **Nový krok**a v části **Zvolit akci**vyhledejte nástroj pro **rozpoznávání formulářů**. Ve výsledcích, které se zobrazí, vyberte **Nástroj pro rozpoznávání formulářů**a potom v části akce, které jsou k dispozici pro rozpoznávání formulářů, vyberte **Model vlaku**.

    > [!div class="mx-imgBorder"]
    > ![Trénování modelu nástroje pro rozpoznávání formulářů](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. V dialogovém okně Nástroj pro rozpoznávání formulářů zadejte název připojení a zadejte adresu URL koncového bodu a klíč, který jste načetli pro prostředek nástroje pro rozpoznávání formulářů.

    > [!div class="mx-imgBorder"]
    > ![Název připojení pro nástroj pro rozpoznávání formulářů](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Klikněte na **Vytvořit**.

1. V dialogovém okně **Model vlaku** zadejte do pole **Zdroj**adresu URL kontejneru, do kterého jste nahráli ukázková data.

    > [!div class="mx-imgBorder"]
    > ![Skladovací kontejner pro ukázkové faktury](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. Nahoře klikněte na **Uložit** z panelu nástrojů.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>Konfigurace aplikace logiky pro použití operace Analyzátor uvytvoření formuláře

V této části přidáte operaci **Analyzovat formulář** do pracovního postupu. Tato operace používá již trénovaný model k analýze nové faktury, která je k dispozici do aplikace logiky.

1. Vyberte **Nový krok**a v části **Zvolit akci**vyhledejte nástroj pro **rozpoznávání formulářů**. Ve výsledcích, které se zobrazí, vyberte **Nástroj pro rozpoznávání formulářů**a potom v části akce, které jsou k dispozici pro rozpoznávání formulářů, vyberte **Analyzovat formulář**.

    > [!div class="mx-imgBorder"]
    > ![Analýza modelu nástroje pro rozpoznávání formulářů](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. V dialogovém okně **Analyzovat formulář** proveďte následující kroky:

    1. Klepněte na textové pole **ID modelu** a v dialogovém okně, které se otevře, vyberte v části Karta **Dynamický obsah** **položku Id modelu**. Tímto způsobem poskytnete aplikaci toku s ID modelu modelu, který jste trénovali v poslední části.

        > [!div class="mx-imgBorder"]
        > ![Použití modelové id pro rozpoznávání formulářů](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Klepněte na textové pole **Dokument** a v dialogovém okně, které se otevře, v části Karta **Dynamický obsah** vyberte **Položku Obsah příloh**. Tím nakonfigurujete tok pro použití ukázkového souboru faktury, který je připojen v e-mailu, který aktivuje pracovní postup.

        > [!div class="mx-imgBorder"]
        > ![Analýza faktur pomocí přílohy e-mailu](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. Nahoře klikněte na **Uložit** z panelu nástrojů.

### <a name="extract-the-table-information-from-the-invoice"></a>Extrahovat informace o tabulce z faktury

V této části nakonfigurujete aplikaci logiky extrahovat informace z tabulky v rámci faktury.

1. Vyberte **Přidat akci**a v části **Zvolit akci**vyhledejte **compose** a v části akce, které jsou k dispozici, vyberte **Znovu vytvořit.**
    ![Extrahovat informace o tabulce z faktury](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. V dialogovém okně **Napsat** klepněte na textové pole **Vstupy** a v dialogovém okně, které se objeví, vyberte **tabulky**.

    > [!div class="mx-imgBorder"]
    > ![Extrahovat informace o tabulce z faktury](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Klikněte na **Uložit**.

## <a name="test-your-logic-app"></a>Testování aplikace logiky

Chcete-li otestovat aplikaci logiky, použijte ukázkové faktury ve složce **/Test** ukázkové datové sady, kterou jste stáhli z [GitHubu](https://go.microsoft.com/fwlink/?linkid=2090451). Postupujte následovně:

1. V návrháři Azure Logic Apps pro vaši aplikaci vyberte **Spustit** z panelu nástrojů nahoře. Pracovní postup je nyní aktivní a čeká na přijetí e-mailu s přiloženou fakturou.
1. Odešlete e-mail s ukázkovou fakturou připojenou k e-mailové adrese, kterou jste zadali při vytváření aplikace logiky. Ujistěte se, že e-mail je doručena do složky, které jste zadali při konfiguraci aplikace logiky.
1. Jakmile je e-mail doručen do složky, Návrhář logických aplikací zobrazí obrazovku s průběhem každé fáze. Na následujícím snímku obrazovky zjistíte, že je přijat e-mail s přílohou a probíhá pracovní postup.

    > [!div class="mx-imgBorder"]
    > ![Spuštění pracovního postupu odesláním e-mailu](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. Po dokončení spuštění všech fází pracovního postupu zobrazí Návrhář logických aplikací zelené zaškrtávací políčko pro každou fázi. V okně návrháře vyberte **Pro každý 2**a pak vyberte **Compose**.

    > [!div class="mx-imgBorder"]
    > ![Pracovní postup dokončen](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    Z pole **VÝSTUPY** zkopírujte výstup a vložte jej do libovolného textového editoru.

1. Porovnejte výstup JSON s ukázkovou fakturou, kterou jste odeslali jako přílohu v e-mailu. Ověřte, zda data JSON odpovídají datům v tabulce na faktuře.

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```
    Úspěšně jste dokončili tento kurz!

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte pracovní postup Azure Logic Apps pro použití nástroje pro rozpoznávání formulářů k trénování modelu a extrahování obsahu faktury. Dále se dozvíte, jak vytvořit trénovací sadu dat, abyste mohli vytvořit podobný scénář s vlastními formuláři.

> [!div class="nextstepaction"]
> [Vytvoření trénovací sady dat](build-training-data-set.md)