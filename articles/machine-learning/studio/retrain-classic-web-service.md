---
title: Přeučování a nasadit klasickou webovou službou
titleSuffix: Azure Machine Learning Studio
description: Zjistěte, jak model přetrénujete a aktualizovat klasické webové služby, která používá nově trénovaný model v Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: peterlu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: e8fd3511eb7b718374bfbc608a35fb84740ed03c
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331167"
---
# <a name="retrain-and-deploy-a-classic-studio-web-service"></a>Přeučování a nasazení webové služby classic Studio

Přetrénování modelů strojového učení je jedním ze způsobů zajistilo že jejich setrvání přesné a založená na nejdůležitější data dostupná. Tento článek vám ukáže postup přeučování webové služby classic Studio. Informace o tom, jak přeučování nové Studio webové služby [zobrazit tento článek.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že již máte retraining experiment a prediktivní experiment. Tyto kroky jsou vysvětlené v [Přetrénujete a nasadit model strojového učení.](retrain-models-programmatically.md) Namísto nasazení svůj model strojového učení jako nové webové služby, ale bude nasazení prediktivní experiment jako klasickou webovou službou.
     
## <a name="add-a-new-endpoint"></a>Přidat nový koncový bod

Prediktivní webové služby, které jste nasadili obsahuje výchozí bodovací koncový bod, který je udržovat synchronizované s původní trénování a vyhodnocování trénovaného modelu experimentů. Pokud chcete aktualizovat webovou službu, která s novou trénovaného modelu, musíte vytvořit nový bodovací koncový bod.

Existují dva způsoby, ve kterých můžete přidat nový koncový bod webové služby:

* Prostřednictvím kódu programu
* Použití portálu webových služeb Azure

> [!NOTE]
> Ujistěte se, že chcete přidat koncový bod prediktivní webové služby, ne webová služba školení. Pokud jste nasadili správně Trénovací a prediktivní webové služby, měli byste vidět uvedené dvě samostatné webové služby. Prediktivní webová služba by měla končit "[prediktivní exp.]".
>

### <a name="programmatically-add-an-endpoint"></a>Přidání koncového bodu prostřednictvím kódu programu

Můžete přidat bodovací koncové body pomocí ukázkového kódu, poskytnutý v tomto [úložiště GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Použití portálu webových služeb Azure k přidání koncového bodu

1. V nástroji Machine Learning Studio v levém navigačním sloupci klikněte na webové služby.
1. V dolní části řídicího panelu webové služby, klikněte na tlačítko **spravovat koncové body ve verzi preview**.
1. Klikněte na tlačítko **Add** (Přidat).
1. Zadejte název a popis pro nový koncový bod. Vyberte úroveň protokolování a povolení ukázková data. Další informace o protokolování naleznete v tématu [povolení protokolování pro webové služby Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Aktualizovat trénovaného modelu přidání koncového bodu

### <a name="retrieve-patch-url"></a>Načíst oprava adresy URL

### <a name="option-1-programmatically"></a>Option 1: Prostřednictvím kódu programu

Chcete-li získat správnou adresu URL oprava prostřednictvím kódu programu, postupujte takto:

1. Spustit [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) ukázkový kód.
1. Z výstupu AddEndpoint najít *HelpLocation* hodnotu a zkopírujte adresu URL.

   ![HelpLocation ve výstupu příkazu addEndpoint vzorku.][image2]
1. Vložte adresu URL do prohlížeče přejděte na stránku, která obsahuje odkazy na nápovědu pro webovou službu.
1. Klikněte na tlačítko **aktualizace prostředku** odkaz k otevření stránky s nápovědou opravy.

### <a name="option-2-use-the-azure-machine-learning-web-services-portal"></a>Option 2: Použití portálu Azure Machine Learning Web Services

Postupujte podle těchto kroků můžete získat správnou opravu URL prostřednictvím webového portálu:

1. Přihlaste se k [Azure Machine Learning Web Services](https://services.azureml.net/) portálu.
1. Klikněte na tlačítko **webových služeb** nebo **klasické webové služby** v horní části.
1. Klikněte na tlačítko pracujete s hodnoticí webové služby (pokud nebyl změnit výchozí název webové služby, skončí v "[bodování Exp.]").
1. Klikněte na tlačítko **+ nová**.
1. Po přidání koncového bodu, klikněte na název koncového bodu.
1. V části **opravy** adresu URL, klikněte na tlačítko **API nápovědy** otevřete oprav stránky s nápovědou.

> [!NOTE]
> Pokud jste přidali na koncový bod webové služby školení místo prediktivní webové služby, zobrazí se následující chyba po kliknutí **aktualizace prostředku** odkaz: "Je nám líto, ale tato funkce není podporována nebo v tomhle kontextu dostupné. Tato webová služba nemá žádné prostředky aktualizovat. Omlouváme se za nepříjemnosti a pracujeme na vylepšení tohoto pracovního postupu."
>

Stránky s nápovědou PATCH obsahuje adresu URL OPRAVIT, je nutné použít a poskytuje ukázkový kód, který slouží k jeho volání.

![Adresa URL Patch.][image5]

### <a name="update-the-endpoint"></a>Aktualizace koncového bodu

Nyní můžete trénovaného modelu aktualizujete bodovací koncový bod, který jste vytvořili dříve.

Následující ukázkový kód ukazuje, jak používat *BaseLocation*, *RelativeLocation*, *SasBlobToken*a OPRAVIT URL aktualizujte koncový bod.

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
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
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

Hodnota *název* parametr *prostředky* by měl odpovídat názvu prostředku z uložené Trénovaného modelu v prediktivní experiment. Pokud chcete získat název prostředku:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo klikněte na tlačítko **Machine Learning**.
1. Pod názvem, klikněte na váš pracovní prostor a pak klikněte na **webových služeb**.
1. Pod názvem, klikněte na **modelu sčítání [prediktivní exp.]** .
1. Klikněte na tlačítko Nový koncový bod, který jste přidali.
1. Na řídicím panelu endpoint, klikněte na tlačítko **aktualizace prostředku**.
1. Na stránce dokumentace k rozhraní API aktualizace prostředků pro webovou službu můžete najít **název prostředku** pod **aktualizovat prostředky**.

Pokud váš token SAS vyprší před dokončením aktualizuje se koncový bod, je nutné provést GET s ID úlohy získat nový token.

Při úspěšném spuštění kódu by nový koncový bod začít používat retrained modelu během přibližně 30 sekund.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak spravovat webové služby nebo udržovat přehled o různých spuštění experimentů, naleznete v následujících článcích:

* [Prozkoumejte portálu webových služeb](manage-new-webservice.md)
* [Správa iterací experimentu](manage-experiment-iterations.md)

[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png