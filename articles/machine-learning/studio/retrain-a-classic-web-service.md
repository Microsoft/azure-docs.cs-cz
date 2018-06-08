---
title: Přeučování Classic webové služby | Microsoft Docs
description: Zjistěte, jak programově přeučit modelu a aktualizovat webovou službu, která používá nově trénovaného modelu v Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: e36e1961-9e8b-4801-80ef-46d80b140452
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 6fc03865185b97fb1f34028239f647f97d5bd315
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836870"
---
# <a name="retrain-a-classic-web-service"></a>Přeučování webové služby Classic
Prediktivní webové služby, které jste nasadili je výchozí vyhodnocovací koncový bod. Výchozí koncové body jsou synchronizovány s původní školení a vyhodnocování experimenty, a proto nelze nahradit pro cvičný model pro výchozí koncový bod. Chcete-li přeučování webovou službu, je nutné přidat nový koncový bod k webové službě. 

## <a name="prerequisites"></a>Požadavky
Musí mít nastavíte výukový experiment a prediktivní experiment jak je znázorněno v [Machine Learning Přeučování modelů prostřednictvím kódu programu](retrain-models-programmatically.md). 

> [!IMPORTANT]
> Prediktivní experiment musí být nasazený jako Classic strojového učení webové služby. 
> 
> 

Další informace o nasazení webové služby, najdete v části [nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Přidat nový koncový bod
Prediktivní webové služby, která jste nasadili obsahuje výchozí vyhodnocovací koncový bod, který je uložen synchronizována s původní školení a vyhodnocování experimenty trained model. Pokud chcete aktualizovat webovou službu, která se nový trained model, musíte vytvořit nový koncový bod vyhodnocování. 

Chcete-li vytvořit nový koncový bod vyhodnocování, na prediktivní webové služby, která mohou být aktualizovány s trénovaného modelu:

> [!NOTE]
> Ujistěte se, že přidáváte koncový bod do prediktivní webové služby není školení webovou službu. Pokud jste nasadili správně školení a prediktivní webové služby, měli byste vidět dvě samostatné webové služby, které jsou uvedené. Prediktivní webové služby musí končit "[prediktivní exp.]".
> 
> 

Existují dva způsoby, ve které můžete přidat nový koncový bod webové služby:

1. Prostřednictvím kódu programu
2. Použití portálu Microsoft Azure webové služby

### <a name="programmatically-add-an-endpoint"></a>Přidání koncového bodu prostřednictvím kódu programu
Můžete přidat vyhodnocování koncové body pomocí ukázkový kód zadaný v tomto [úložiště github](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Přidání koncového bodu pomocí portálu Microsoft Azure webové služby
1. Machine Learning Studio v levém navigačním sloupec, klikněte na možnost webové služby.
2. V dolní části řídicího panelu webové služby, klikněte na tlačítko **spravovat koncové body preview**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Zadejte název a popis pro nový koncový bod. Vyberte úroveň protokolování a určuje, jestli je povolená ukázková data. Další informace o protokolování naleznete v tématu [povolení protokolování pro webové služby Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Aktualizovat trained model přidání koncového bodu
Dokončete proces retraining, je třeba aktualizovat pro cvičný model nový koncový bod, který jste přidali.

Pokud jste přidali koncový bod pomocí ukázkový kód, jedná se o umístění adresa URL nápovědy identifikovaný *HelpLocationURL* hodnotu ve výstupu.

Načíst cestu adresy URL:

1. Zkopírujte a vložte adresu URL do prohlížeče.
2. Klikněte na odkaz aktualizace prostředků.
3. Zkopírujte adresu URL POST požadavku PATCH. Příklad:
   
     ADRESA URL OPRAVY: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Nyní můžete trénovaného modelu k aktualizaci vyhodnocování koncového bodu, který jste předtím vytvořili.

Následující vzorový kód ukazuje, jak používat *BaseLocation*, *RelativeLocation*, *SasBlobToken*a oprava adresu URL pro aktualizaci koncového bodu.

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

*ApiKey* a *endpointUrl* pro volání můžete získat z řídicího panelu koncový bod.

Hodnota *název* parametr v *prostředky* by měl odpovídat názvu prostředku uložené Trained Model prediktivní experiment. Pokud chcete získat název prostředku:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo klikněte na **Machine Learning**.
3. Pod názvem, klikněte na pracovní prostor a potom klikněte na **webové služby**.
4. Pod názvem, klikněte na **modelu sčítání [prediktivní exp.]** .
5. Klikněte na tlačítko Nový koncový bod, který jste přidali.
6. Na řídicím panelu endpoint, klikněte na tlačítko **aktualizace prostředků**.
7. Na stránce dokumentace rozhraní API aktualizace prostředků pro webovou službu, můžete najít **název prostředku** pod **aktualizovat prostředky**.

Pokud váš token SAS vyprší dokončení aktualizace koncový bod, je třeba provést GET s Id úlohy získat nový token.

Při kód byl úspěšně spuštěn, začněte nový koncový bod pomocí retrained modelu v přibližně 30 sekund.

## <a name="summary"></a>Souhrn
Pomocí rozhraní Retraining API, můžete aktualizovat pro cvičný model prediktivní povolení scénáře, jako webové služby:

* Pravidelné model retraining s nová data.
* Distribuce modelu pro zákazníky s cílem aby přeučování modelu s použitím svá vlastní data.

## <a name="next-steps"></a>Další postup
[Řešení potíží s retraining classic webové služby Azure Machine Learning](troubleshooting-retraining-models.md)

