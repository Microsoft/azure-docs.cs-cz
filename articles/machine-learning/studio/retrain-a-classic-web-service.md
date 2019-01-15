---
Název: Přeučování titleSuffix klasické webové služby: Azure Machine Learning Studio Popis: Zjistěte, jak programově přeučit modelu a aktualizovat webovou službu, která používá nově trénovaného modelu ve službě Azure Machine Learning.
Services: machine learningu ms.service: ms.component strojového učení: studio ms.topic: článku

Autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18, předchozí ms.author=yahajiza, předchozí Autor = YasinMSFT ms.date: 04/19/2017
---
# <a name="retrain-a-classic-azure-machine-learning-studio-web-service"></a>Přeučování webové služby Classic Azure Machine Learning Studio
Prediktivní webové služby, kterou jste nasadili je výchozí bodovací koncový bod. Výchozí koncové body se udržovat synchronizované s původní trénování a vyhodnocování experimenty, a proto nejde nahradit trénovaný model pro výchozí koncový bod. Chcete-li přeučování webové služby, je nutné přidat nový koncový bod webové služby.

## <a name="prerequisites"></a>Požadavky
Je nutné nastavit výukového experimentu a prediktivní experiment jak je znázorněno v [modelů Machine Learning Přeučování](retrain-models-programmatically.md).

> [!IMPORTANT]
> Prediktivní experiment musí být nasazený jako klasickém webovou službu machine learning.
>
>

Další informace o nasazování webových služeb najdete v tématu [nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Přidat nový koncový bod
Prediktivní webové služby, které jste nasadili obsahuje výchozí bodovací koncový bod, který je udržovat synchronizované s původní trénování a vyhodnocování trénovaného modelu experimentů. Pokud chcete aktualizovat webovou službu, která s novou trénovaného modelu, musíte vytvořit nový bodovací koncový bod.

Chcete-li vytvořit nový bodovací koncový bod, na prediktivní webové služby, které se dají aktualizovat trénovaného modelu:

> [!NOTE]
> Ujistěte se, že chcete přidat koncový bod prediktivní webové služby, ne webová služba školení. Pokud jste nasadili správně Trénovací a prediktivní webové služby, měli byste vidět uvedené dvě samostatné webové služby. Prediktivní webová služba by měla končit "[prediktivní exp.]".
>
>

Existují dva způsoby, ve kterých můžete přidat nový koncový bod webové služby:

1. Prostřednictvím kódu programu
2. Použití portálu webových služeb Microsoft Azure

### <a name="programmatically-add-an-endpoint"></a>Přidání koncového bodu prostřednictvím kódu programu
Můžete přidat bodovací koncové body pomocí ukázkového kódu, poskytnutý v tomto [úložiště GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Použití portálu webových služeb Microsoft Azure k přidání koncového bodu
1. V nástroji Machine Learning Studio v levém navigačním sloupci klikněte na webové služby.
2. V dolní části řídicího panelu webové služby, klikněte na tlačítko **spravovat koncové body ve verzi preview**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Zadejte název a popis pro nový koncový bod. Vyberte úroveň protokolování a povolení ukázková data. Další informace o protokolování naleznete v tématu [povolení protokolování pro webové služby Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Aktualizovat trénovaného modelu přidání koncového bodu
K dokončení procesu retraining, je nutné aktualizovat trénovaného modelu, který jste přidali nový koncový bod.

Pokud jste přidali na koncový bod pomocí ukázkového kódu, jedná se o umístění adresa URL nápovědy, který je identifikován *HelpLocationURL* hodnota ve výstupu.

Načíst cestu adresy URL:

1. Zkopírujte a vložte adresu URL do prohlížeče.
2. Klikněte na odkaz aktualizace prostředku.
3. Zkopírujte adresu URL příspěvku požadavku PATCH. Příklad:
   
     OPRAVA ADRESY URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

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
2. V nabídce vlevo klikněte na tlačítko **Machine Learning**.
3. Pod názvem, klikněte na váš pracovní prostor a pak klikněte na **webových služeb**.
4. Pod názvem, klikněte na **modelu sčítání [prediktivní exp.]** .
5. Klikněte na tlačítko Nový koncový bod, který jste přidali.
6. Na řídicím panelu endpoint, klikněte na tlačítko **aktualizace prostředku**.
7. Na stránce dokumentace k rozhraní API aktualizace prostředků pro webovou službu můžete najít **název prostředku** pod **aktualizovat prostředky**.

Pokud váš token SAS vyprší před dokončením aktualizuje se koncový bod, je nutné provést GET s Id úlohy získat nový token.

Při úspěšném spuštění kódu by nový koncový bod začít používat retrained modelu během přibližně 30 sekund.

## <a name="summary"></a>Souhrn
Pomocí rozhraní Retraining API, můžete aktualizovat trénovaného modelu umožňuje scénáře, jako prediktivní webové služby:

* Pravidelné model přetrénování s novými daty.
* Distribuce modelu pro zákazníky s cílem aby programovém přeučení modelů pomocí svá vlastní data.

## <a name="next-steps"></a>Další postup
[Řešení potíží s přeučováním klasické webové služby Azure Machine Learning](troubleshooting-retraining-models.md)

