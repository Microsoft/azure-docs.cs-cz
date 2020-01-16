---
title: 'Kurz: použití nástroje pro rozpoznávání formulářů s Azure Logic Apps k analýze faktur – Nástroj pro rozpoznávání formulářů'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu použijete nástroj pro rozpoznávání formulářů s Azure Logic Apps k vytvoření pracovního postupu, který automatizuje proces školení modelu a testování pomocí ukázkových dat.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: nitinme
ms.openlocfilehash: 14affb2c2aa53fc7a2b1a5946e81ad124800f678
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981269"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Kurz: použití nástroje pro rozpoznávání formulářů s Azure Logic Apps k analýze faktur

V tomto kurzu vytvoříte pracovní postup v Azure Logic Apps, který používá nástroj pro rozpoznávání formulářů, službu, která je součástí sady Azure Cognitive Services Suite, k extrakci dat z faktur. Pomocí nástroje pro rozpoznávání formulářů nejprve provedete model pomocí ukázkové sady dat a potom otestujete model pomocí jiné sady dat. Ukázková data použitá v tomto kurzu se ukládají do Azure Storage kontejnerů objektů BLOB.

V tomto kurzu se dozvíte, co tento kurz popisuje:

> [!div class="checklist"]
> * Požádat o přístup pro rozpoznávání formulářů
> * Vytvoření Azure Storage kontejneru objektů BLOB
> * Nahrání ukázkových dat do kontejneru objektů blob Azure
> * Vytvoření aplikace logiky Azure
> * Konfigurace aplikace logiky, aby používala prostředek pro rozpoznávání formulářů
> * Testování pracovního postupu spuštěním aplikace logiky

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).

## <a name="request-access-for-form-recognizer"></a>Požádat o přístup pro rozpoznávání formulářů

Nástroj pro rozpoznávání formulářů je k dispozici ve verzi Preview s omezeným přístupem. Chcete-li získat přístup k verzi Preview, vyplňte a odešlete formulář [žádosti o přístup pro rozpoznávání formulářů](https://aka.ms/FormRecognizerRequestAccess) . Po schválení žádosti týmem týmu Azure Cognitive Services obdržíte e-mail s pokyny pro přístup ke službě.

## <a name="understand-the-invoice-to-be-analyzed"></a>Pochopení faktury k analýze

Ukázková datová sada, kterou používáme pro výuku modelu a testování modelu, je k dispozici jako soubor. zip z [GitHubu](https://go.microsoft.com/fwlink/?linkid=2090451). Stáhněte a rozbalte soubor. zip a otevřete soubor PDF faktury ve složce **/Train** . Všimněte si, jak má tabulka s číslem faktury, datem faktury atd. 

> [!div class="mx-imgBorder"]
> ](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png) ukázkové faktury ![

V tomto kurzu se naučíme extrahovat informace z těchto tabulek do formátu JSON pomocí pracovního postupu vytvořeného pomocí Azure Logic Apps a nástroje pro rozpoznávání formulářů.

## <a name="create-an-azure-storage-blob-container"></a>Vytvoření Azure Storage kontejneru objektů BLOB

Tento kontejner použijete k nahrání ukázkových dat, která jsou nutná pro výuku modelu.

1. Při vytváření účtu úložiště postupujte podle pokynů v části [Vytvoření účtu Azure Storage](../../storage/common/storage-account-create.md) . Jako název účtu úložiště použijte **formrecostorage** .
1. Podle pokynů v části [vytvoření kontejneru objektů BLOB v Azure](../../storage/blobs/storage-quickstart-blobs-portal.md) vytvořte kontejner v rámci Azure Storage účtu. Jako název kontejneru použijte **formrecocontainer** . Ujistěte se, že jste nastavili úroveň veřejného přístupu na **kontejner (anonymní přístup pro čtení kontejnerů a objektů BLOB)** .

    > [!div class="mx-imgBorder"]
    > ![vytvořit kontejner objektů BLOB](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Nahrání ukázkových dat do kontejneru objektů blob Azure

Stáhněte si ukázková data dostupná na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2090451). Extrahujte data do místní složky a nahrajte obsah složky **/Train** do **formrecocontainer** , který jste vytvořili dříve. Podle pokynů v části [nahrání objektu blob bloku](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) nahrajte data do kontejneru.

Zkopírujte adresu URL kontejneru. Tento kurz budete potřebovat později v tomto kurzu. Pokud jste vytvořili účet úložiště a kontejner se stejnými názvy, jako jsou uvedené v tomto kurzu, adresa URL bude *https:\//formrecostorage.blob.Core.Windows.NET/formrecocontainer/* .

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání formulářů

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

Azure Logic Apps můžete použít k automatizaci a orchestraci úloh a pracovních postupů. V tomto kurzu vytvoříte aplikaci logiky, která se aktivuje přijetím faktury, kterou chcete analyzovat jako přílohu e-mailu. V tomto pracovním postupu provedete následující úlohy:
* Nastavte automatickou aktivaci aplikace logiky při obdržení e-mailu s připojenou fakturou.
* Nakonfigurujte aplikaci logiky tak, aby používala funkci rozpoznávání formulářů, která **umožňuje výuku** modelu pomocí ukázkových dat, která jste nahráli do úložiště objektů BLOB v Azure.
* Nakonfigurujte aplikaci logiky tak, aby používala operaci **analyzovat** formulář nástroje pro rozpoznávání formulářů k použití modelu, který už máte vyškolený. Tato součást provede analýzu faktury, kterou zadáte do této aplikace logiky, na základě modelu, který dříve vyškole.

Pojďme začít! Pomocí těchto kroků nastavte pracovní postup.

1. V hlavní nabídce Azure vyberte **vytvořit prostředek** > **integraci** > **Aplikace logiky**.

1. V části **Vytvořit aplikaci logiky** zadejte podrobnosti o vaší aplikaci logiky, jak je vidět zde. Až budete hotovi, vyberte **vytvořit**.

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | <*Logic-App-name*> | Název aplikace logiky, který může obsahovat jenom písmena, číslice, spojovníky (`-`), podtržítka (`_`), kulaté závorky (`(`, `)`) a tečky (`.`). Tento příklad používá "moji-First-Logic-App". |
   | **Předplatné** | <*název_předplatného_Azure*> | Název vašeho předplatného Azure |
   | **Skupina prostředků** | <*Azure-resource-group-name*> | Název [skupiny prostředků Azure](./../../azure-resource-manager/management/overview.md) , která slouží k uspořádání souvisejících prostředků. V tomto příkladu se používá "My-First-LA-RG". |
   | **Umístění** | <*Azure – oblast*> | Oblast, kam se mají ukládat informace o aplikaci logiky V tomto příkladu se používá "Západní USA". |
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. |
   ||||

1. Až Azure nasadí vaši aplikaci, vyberte na panelu nástrojů Azure možnost **oznámení** > pro vaši nasazenou aplikaci logiky **Přejít na prostředek** . Nebo můžete vyhledat a vybrat aplikaci logiky zadáním názvu do vyhledávacího pole.

   Otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem a běžně používanými triggery. V části **Šablony** vyberte **Prázdná aplikace logiky**.

   > [!div class="mx-imgBorder"]
   > ![pro aplikaci logiky vybrat prázdnou šablonu](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>Konfigurace aplikace logiky, která aktivuje pracovní postup při přijetí e-mailu

V tomto kurzu aktivujete pracovní postup při přijetí e-mailu s připojenou fakturou. V tomto kurzu zvolíme jako e-mailovou službu Office 365, ale můžete použít libovolného jiného poskytovatele e-mailu, kterého chcete použít.

1. Na kartách vyberte vše, vyberte **Office 365 Outlook**a potom v části **triggery**vyberte, **kdy přijde nový e-mail**.

    ![Aktivace aplikace logiky prostřednictvím příchozího e-mailu](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. V poli **Office 365 Outlook** klikněte na **Přihlásit**se a zadejte podrobnosti pro přihlášení k účtu Office 365.

1. V dalším dialogovém okně proveďte následující kroky.
    1. Vyberte složku, která se má monitorovat pro všechny nové e-maily.
    1. V případě **příloh** vyberte **Ano**. Tím se zajistí, že pracovní postup aktivuje jenom e-maily s přílohami.
    1. V případě **zahrnutí příloh** vyberte **Ano**. Tím se zajistí, že se obsah přílohy použije při zpracování pro příjem dat.

        > [!div class="mx-imgBorder"]
        > ![nakonfigurovat aktivační proceduru e-mailu aplikace logiky](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. V horní části panelu nástrojů klikněte na **Uložit** .

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>Konfigurace aplikace logiky na použití funkce pro rozpoznávání formulářů

Předtím, než budete moci použít službu pro rozpoznávání formulářů k analýze faktur, je nutné vytvořit model tak, že mu poskytnete některá ukázková data faktury, která model dokáže analyzovat a dozvědět se od nich.

1. Vyberte **Nový krok**a v části **Zvolte akci**vyhledejte možnost **rozpoznávání formulářů**. Z zobrazených výsledků vyberte možnost **Nástroj pro rozpoznávání formulářů**a potom v části akce, které jsou k dispozici pro nástroj pro rozpoznávání formulářů vyberte možnost **model výuky**.

    > [!div class="mx-imgBorder"]
    > ![výukového modelu pro rozpoznávání formulářů](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. V dialogovém okně pro rozpoznávání formulářů zadejte název připojení a zadejte adresu URL koncového bodu a klíč, který jste získali pro prostředek pro rozpoznávání formuláře.

    > [!div class="mx-imgBorder"]
    > ![název připojení pro nástroj pro rozpoznávání formulářů](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Klikněte na **Vytvořit**.

1. V dialogovém okně **vlakový model** pro **zdroj**zadejte adresu URL kontejneru, do kterého jste nahráli ukázková data.

    > [!div class="mx-imgBorder"]
    > ![kontejner úložiště pro ukázkové faktury](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. V horní části panelu nástrojů klikněte na **Uložit** .

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>Konfigurace aplikace logiky, aby používala operaci analyzovat formulář v nástroji pro rozpoznávání formulářů

V této části přidáte do pracovního postupu operaci **analyzovat formulář** . Tato operace používá již vyškolený model k analýze nové faktury, která je k dispozici v aplikaci logiky.

1. Vyberte **Nový krok**a v části **Zvolte akci**vyhledejte možnost **rozpoznávání formulářů**. Z zobrazených výsledků vyberte možnost **Nástroj pro rozpoznávání formulářů**a potom v části akce, které jsou k dispozici pro nástroj pro rozpoznávání formulářů vyberte možnost **analyzovat formulář**.

    > [!div class="mx-imgBorder"]
    > ![analyzovat model pro rozpoznávání formulářů](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. V dialogovém okně **Analýza formuláře** proveďte následující akce:

    1. Klikněte na textové pole **ID modelu** a v dialogovém okně, které se otevře, vyberte na kartě **dynamický obsah** možnost **modelId**. Provedete to tak, že zadáte aplikaci Flow s ID modelu modelu, který jste si vyškolei v poslední části.

        > [!div class="mx-imgBorder"]
        > ![použít ModelID pro rozpoznávání formulářů](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Klikněte na textové pole **dokumentu** a v dialogovém okně, které se otevře, v části karta **dynamického obsahu** vyberte **obsah příloh**. Tím se nakonfiguruje tok pro použití ukázkového souboru faktury, který je připojený k aktivaci pracovního postupu.

        > [!div class="mx-imgBorder"]
        > ![k analýze faktur použít přílohu e-mailu](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. V horní části panelu nástrojů klikněte na **Uložit** .

### <a name="extract-the-table-information-from-the-invoice"></a>Extrahuje informace z tabulky z faktury.

V této části nakonfigurujeme aplikaci logiky pro extrakci informací z tabulky v rámci faktur.

1. Vyberte možnost **přidat akci**a v části **Zvolte akci**vyhledejte položku **vytvořit** a v části akce, které jsou k dispozici, vyberte možnost znovu **vytvořit** .
    ![extrahovat informace o tabulce z faktury](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. V dialogovém okně **psaní** klikněte na textové pole **vstupy** a v dialogovém okně, které se zobrazí, vyberte **tabulky**.

    > [!div class="mx-imgBorder"]
    > ![extrahovat informace o tabulce z faktury](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Klikněte na možnost **Uložit**.

## <a name="test-your-logic-app"></a>Testování aplikace logiky

K otestování aplikace logiky použijte ukázkové faktury ve složce **/test** ukázkové sady dat, kterou jste si stáhli z [GitHubu](https://go.microsoft.com/fwlink/?linkid=2090451). Proveďte následující kroky:

1. Z návrháře Azure Logic Apps pro vaši aplikaci vyberte **Spustit** z panelu nástrojů v horní části. Pracovní postup je teď aktivní a čeká na příjem e-mailu s připojenou fakturou.
1. Odešlete e-mail s ukázkovou fakturou připojenou k e-mailové adrese, kterou jste zadali při vytváření aplikace logiky. Zajistěte, aby byl e-mail doručen do složky, kterou jste zadali při konfiguraci aplikace logiky.
1. Jakmile se e-mail doručí do složky, Návrhář Logic Apps zobrazí obrazovku s průběhem jednotlivých fází. Na snímku obrazovky níže vidíte, že byl přijat e-mail s přílohou a probíhá pracovní postup.

    > [!div class="mx-imgBorder"]
    > ![spustit pracovní postup odesláním e-mailu](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. Po dokončení všech fází pracovního postupu se v Návrháři Logic Apps zobrazí zelené zaškrtávací políčko proti každé fázi. V okně návrháře vyberte **pro každou možnost 2**a pak vyberte **vytvořit**.

    > [!div class="mx-imgBorder"]
    > Pracovní postup ![dokončen](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    Z pole **výstupy** zkopírujte výstup a vložte ho do libovolného textového editoru.

1. Porovnejte výstup JSON s ukázkovou fakturou, kterou jste poslali jako přílohu v e-mailu. Ověřte, že data JSON odpovídají datům v tabulce v rámci faktury.

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

V tomto kurzu nastavíte pracovní postup Azure Logic Apps pro použití rozpoznávání formulářů k učení modelu a extrakci obsahu faktury. Dále se naučíte, jak vytvořit školicí sadu dat, abyste mohli vytvořit podobný scénář s vlastními formuláři.

> [!div class="nextstepaction"]
> [Sestavení sady školicích dat](build-training-data-set.md)