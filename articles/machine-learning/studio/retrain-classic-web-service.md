---
title: Přeškolit klasickou webovou službu
titleSuffix: ML Studio (classic) - Azure
description: Zjistěte, jak přeškolit model a aktualizovat klasickou webovou službu tak, aby používala nově trénovaný model v Azure Machine Learning Studio (klasika).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterclu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: 8094d64eab1a4b25a76554bf9eb6848c2e4d3493
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204235"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Přeškolit a nasadit klasickou webovou službu Studio (classic)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Rekvalifikace modelů strojového učení je jedním ze způsobů, jak zajistit, aby zůstaly přesné a založené na nejrelevantnějších dostupných údajích. Tento článek vám ukáže, jak přeškolit klasickou studiovou (klasickou) webovou službu. Průvodce přeškolit novou webovou službu Studio (klasické) [natomto článku s návody.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že již máte rekvalifikační experiment a prediktivní experiment. Tyto kroky jsou vysvětleny v [retrain a nasadit model strojového učení.](/azure/machine-learning/studio/retrain-machine-learning-model) Namísto nasazení modelu strojového učení jako nové webové služby však nasadíte prediktivní experiment jako klasickou webovou službu.
     
## <a name="add-a-new-endpoint"></a>Přidání nového koncového bodu

Prediktivní webová služba, kterou jste nasadili obsahuje výchozí bod bodování koncový bod, který je udržován v synchronizaci s původní školení a hodnocení experimenty trénovaný model. Chcete-li aktualizovat webovou službu na nový trénovaný model, musíte vytvořit nový koncový bod vyhodnocování.

Existují dva způsoby, jak přidat nový koncový bod do webové služby:

* Prostřednictvím kódu programu
* Použití portálu Webových služeb Azure

> [!NOTE]
> Ujistěte se, že přidáváte koncový bod do prediktivní webové služby, nikoli školicí webové služby. Pokud jste správně nasadili školení a prediktivní webové služby, měli byste vidět dvě samostatné webové služby uvedené. Prediktivní webová služba by měla končit "[prediktivní exp.]".
>

### <a name="programmatically-add-an-endpoint"></a>Programově přidání koncového bodu

Koncové body vyhodnocování můžete přidat pomocí ukázkového kódu uvedeného v tomto [úložišti GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Přidání koncového bodu pomocí portálu Azure Web Services

1. V Machine Learning Studio (klasické) v levém navigačním sloupci klikněte na Webové služby.
1. V dolní části řídicího panelu webové služby klikněte na **Spravovat náhled koncových bodů**.
1. Klikněte na **Přidat**.
1. Zadejte název a popis nového koncového bodu. Vyberte úroveň protokolování a zda jsou povolena ukázková data. Další informace o protokolování naleznete v [tématu Enable logging for Machine Learning web services](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Aktualizace trénovaného modelu přidaného koncového bodu

### <a name="retrieve-patch-url"></a>Načíst adresu URL PATCH

Chcete-li pomocí webového portálu získat správnou adresu URL opravy, postupujte takto:

1. Přihlaste se k portálu [Azure Machine Learning Web Services.](https://services.azureml.net/)
1. Nahoře klikněte na **Webové služby** nebo **Klasické webové služby.**
1. Klikněte na webovou službu hodnocení, se kterou pracujete (pokud jste neupravili výchozí název webové služby, skončí na "[Vyhodnocování exp.]").
1. Klikněte na **tlačítko +NOVÝ**.
1. Po přidání koncového bodu klikněte na název koncového bodu.
1. V části Adresa URL **opravy** kliknutím na **nápovědu rozhraní API** otevřete stránku nápovědy pro opravy.

> [!NOTE]
> Pokud jste přidali koncový bod do školicí webové služby namísto prediktivní webové služby, zobrazí se po klepnutí na odkaz **Aktualizovat prostředek** následující chyba: "Omlouváme se, ale tato funkce není v tomto kontextu podporována nebo k dispozici. Tato webová služba nemá žádné aktualizovatelné prostředky. Omlouváme se za nepříjemnosti a pracujeme na zlepšení tohoto pracovního postupu."
>

Stránka nápovědy PATCH obsahuje adresu URL PATCH, kterou musíte použít, a poskytuje ukázkový kód, který můžete použít k jeho volání.

![Adresa URL opravy.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Aktualizace koncového bodu

Nyní můžete použít trénovaný model k aktualizaci koncového bodu vyhodnocování, který jste vytvořili dříve.

Následující ukázkový kód ukazuje, jak použít adresu URL *BaseLocation*, *RelativeLocation*, *SasBlobToken*a PATCH k aktualizaci koncového bodu.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: "experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner"
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: "?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl"
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

*ApiKey* a *endpointUrl* pro volání lze získat z řídicího panelu koncového bodu.

Hodnota *Name* parametr v *resources* by měla odpovídat název prostředku uloženétovaný model v prediktivní experiment. Získání názvu prostředku:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levé nabídce klikněte na **Machine Learning**.
1. V části Název klikněte na pracovní prostor a potom klikněte na **Webové služby**.
1. V části Název klikněte na **Model sčítání lidu [prediktivní exp.]**.
1. Klikněte na nový koncový bod, který jste přidali.
1. Na řídicím panelu koncového bodu klikněte na **aktualizovat prostředek**.
1. Na stránce Dokumentace k aktualizaci rozhraní RESOURCE API pro webovou službu najdete **název prostředku** v části **Aktualizovatelné prostředky**.

Pokud platnost tokenu SAS vyprší před dokončením aktualizace koncového bodu, musíte provést GET s ID úlohy, abyste získali nový token.

Po úspěšném spuštění kódu by měl nový koncový bod začít používat retrained model přibližně za 30 sekund.

## <a name="next-steps"></a>Další kroky

Další informace o správě webových služeb nebo sledování spuštění více experimentů naleznete v následujících článcích:

* [Prozkoumání portálu webových služeb](manage-new-webservice.md)
* [Správa iterací experimentu](manage-experiment-iterations.md)