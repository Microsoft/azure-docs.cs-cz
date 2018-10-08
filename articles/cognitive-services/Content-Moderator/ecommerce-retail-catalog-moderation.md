---
title: 'Kurz: Moderování katalogu elektronického obchodování – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Automatické moderování katalogů elektronického obchodování pomocí strojového učení a AI
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 2405ba9087e63b57c7bd6bc6f290cdafacaf7a49
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227346"
---
# <a name="tutorial-ecommerce-catalog-moderation-with-machine-learning"></a>Kurz: Moderování katalogu elektronického obchodování pomocí strojového učení

V tomto kurzu se naučíme implementovat inteligentní moderování katalogu elektronického obchodování založené na strojovém učení. Využijeme k tomu kombinaci technologií strojově podporované umělé inteligence s moderováním člověkem a vytvoříme tak inteligentní systém katalogu.

![Klasifikované obrázky produktů](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>Obchodní scénář

Pomocí strojově podporovaných technologií můžete klasifikovat a moderovat obrázky produktů v těchto kategoriích:

1. Obsah pro dospělé (nahota)
2. Nevhodné (sugestivní)
3. Celebrity
4. Vlajky USA
5. Hračky
6. Pera

## <a name="tutorial-steps"></a>Kroky v kurzu

Tento kurz vás provede následujícími kroky:

1. Registrace a vytvoření týmu Content Moderatoru
2. Konfigurace značek (popisků) moderování pro případný obsah s celebritami a vlajkami
3. Vyhledání případného nevhodného obsahu a obsahu pro dospělé pomocí rozhraní API Content Moderatoru pro obrázky
4. Vyhledání případných celebrit pomocí rozhraní API pro počítačové zpracování obrazu
5. Vyhledání případných vlajek pomocí Custom Vision Service
6. Předání nerozhodných výsledků hledání ke kontrole člověkem a konečné rozhodnutí

## <a name="create-a-team"></a>Vytvoření týmu

Zaregistrovat se do Content Moderatoru a vytvořit tým můžete na stránce [Rychlý start](quick-start.md). Poznamenejte si **ID týmu** ze stránky **Přihlašovací údaje**.


## <a name="define-custom-tags"></a>Definice vlastních značek

Pokud chcete přidat vlastní značky, přečtěte si článek o [značkách](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags). Kromě integrovaných značek **adult** a **racy** umožňují nové značky nástroji pro kontrolu zobrazovat popisné názvy značek.

V našem případě definujeme tyto vlastní značky (**celebrity**, **flag**, **us**, **toy**, **pen**):

![Konfigurace vlastních značek](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>Vypsání klíčů a koncových bodů rozhraní API

1. Tento kurz používá tři rozhraní API, odpovídající klíče a koncové body rozhraní API.
2. Vaše koncové body rozhraní API se budou lišit v závislosti na oblastech předplatného a ID posuzovacího týmu Content Moderatoru.

> [!NOTE]
> Tento kurz je navržený tak, aby používal klíče předplatných v oblastech, které se zobrazují v následujících koncových bodech. Ujistěte se, že klíče rozhraní API odpovídají identifikátorům URI oblastí, jinak vaše klíče nemusí fungovat s následujícími koncovými body:

         // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>Hledání nevhodného obsahu a obsahu pro dospělé

1. Funkce jako své parametry přijímá adresu URL obrázku a pole párů klíč-hodnota.
2. Zavolá rozhraní API Content Moderatoru pro obrázky a získá skóre v kategoriích obsahu pro dospělé a nevhodného obsahu.
3. Pokud je skóre větší než 0,4 (z rozsahu 0 až 1), nastaví hodnotu v poli **ReviewTags** na **True**.
4. Pole **ReviewTags** slouží ke zvýraznění odpovídajících značek v nástroji pro kontrolu.

        public static bool EvaluateAdultRacy(string ImageUrl, ref KeyValuePair[] ReviewTags)
        {
            float AdultScore = 0;
            float RacyScore = 0;

            var File = ImageUrl;
            string Body = $"{{\"DataRepresentation\":\"URL\",\"Value\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(ImageUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // {“answers”:[{“answer”:“Hello”,“questions”:[“Hi”],“score”:100.0}]}
                // Parse the response body. Blocking!
                GetAdultRacyScores(response.Content.ReadAsStringAsync().Result, out AdultScore, out RacyScore);
            }

            ReviewTags[0] = new KeyValuePair();
            ReviewTags[0].Key = "a";
            ReviewTags[0].Value = "false";
            if (AdultScore > 0.4)
            {
                ReviewTags[0].Value = "true";
            }

            ReviewTags[1] = new KeyValuePair();
            ReviewTags[1].Key = "r";
            ReviewTags[1].Value = "false";
            if (RacyScore > 0.3)
            {
                ReviewTags[1].Value = "true";
            }
            return response.IsSuccessStatusCode;
        }

## <a name="scan-for-celebrities"></a>Vyhledání celebrit

1. Zaregistrujte si [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) [rozhraní API pro počítačové zpracování obrazu](https://azure.microsoft.com/services/cognitive-services/computer-vision/).
2. Klikněte na tlačítko **Získat klíč rozhraní API**.
3. Přijměte podmínky.
4. Přihlaste se tak, že si zvolíte nějakou hodnotu ze seznamu dostupných účtů v internetu.
5. Poznamenejte si klíče rozhraní API, které se zobrazují na stránce služby.
    
   ![Klíče rozhraní API pro počítačové zpracování obrazu](images/tutorial-computer-vision-keys.PNG)
    
6. Funkci, která prohledá obrázky pomocí rozhraní API pro počítačové zpracování obrazu, najdete ve zdrojovém kódu projektu.

         public static bool EvaluateComputerVisionTags(string ImageUrl, string ComputerVisionUri, string ComputerVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage Response = CallAPI(ComputerVisionUri, ComputerVisionKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (Response.IsSuccessStatusCode)
            {
                ReviewTags[2] = new KeyValuePair();
                ReviewTags[2].Key = "cb";
                ReviewTags[2].Value = "false";

                ComputerVisionPrediction CVObject = JsonConvert.DeserializeObject<ComputerVisionPrediction>(Response.Content.ReadAsStringAsync().Result);

                if ((CVObject.categories[0].detail != null) && (CVObject.categories[0].detail.celebrities.Count() > 0))
                {                 
                    ReviewTags[2].Value = "true";
                }
            }

            return Response.IsSuccessStatusCode;
        }

## <a name="classify-into-flags-toys-and-pens"></a>Klasifikace na vlajky, hračky a pera

1. [Přihlaste se](https://azure.microsoft.com/en-us/services/cognitive-services/custom-vision-service/) k [rozhraní Custom Vision API Preview](https://www.customvision.ai/).
2. Pomocí [Rychlého startu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) vytvořte vlastní klasifikátor, který zjistí případnou přítomnost značek, hraček a per.
   ![Trénovací obrázky pro Custom Vision](images/tutorial-ecommerce-custom-vision.PNG)
3. [Získejte adresu URL koncového bodu pro predikce](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api) pro váš vlastní klasifikátor.
4. Funkci, která volá koncový bod pro predikci vlastního klasifikátoru, aby se prohledal obrázek, najdete ve zdrojovém kódu projektu.

        public static bool EvaluateCustomVisionTags(string ImageUrl, string CustomVisionUri, string CustomVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(CustomVisionUri, CustomVisionKey, CallType.POST,
                                                   "Prediction-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // Parse the response body. Blocking!
                SaveCustomVisionTags(response.Content.ReadAsStringAsync().Result, ref ReviewTags);
            }
            return response.IsSuccessStatusCode;
        }       
 
## <a name="reviews-for-human-in-the-loop"></a>Kontroly člověkem zapojeným do procesu

1. V předchozích částech jste vyhledali v příchozích obrázcích obsah pro dospělé a nevhodný obsah (Content Moderator), celebrity (Počítačové zpracování obrazu) a vlajky (Custom Vision).
2. Nerozhodné případy můžete na základě našich prahových hodnot pro jednotlivá hledání předat ke kontrole člověkem v nástroji pro kontrolu.
        public static bool CreateReview(string ImageUrl, KeyValuePair[] Metadata) {

            ReviewCreationRequest Review = new ReviewCreationRequest();
            Review.Item[0] = new ReviewItem();
            Review.Item[0].Content = ImageUrl;
            Review.Item[0].Metadata = new KeyValuePair[MAXTAGSCOUNT];
            Metadata.CopyTo(Review.Item[0].Metadata, 0);

            //SortReviewItems(ref Review);

            string Body = JsonConvert.SerializeObject(Review.Item);

            HttpResponseMessage response = CallAPI(ReviewUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            return response.IsSuccessStatusCode;
        }

## <a name="submit-batch-of-images"></a>Odeslání dávky obrázků

1. V tomto kurzu se předpokládá existence adresáře C:Test s textovým souborem, který obsahuje seznam adres URL obrázků.
2. Následující kód zkontroluje existenci souboru a načte všechny adresy URL do paměti.
            // Kontrola textového souboru se seznamem adres URL obrázků, které se prohledají, v testovacím adresáři var topdir = @"C:\test\"; var Urlsfile = topdir + "Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>Zahájení všech kontrol

1. Tato funkce nejvyšší úrovně prochází všechny adresy URL obrázků v textovém souboru, který jsme zmínili výše.
2. Prohledá je jednotlivými rozhraními API, a pokud skóre spolehlivosti shody spadá do našich kritérií, vytvoří se kontrola pro lidské moderátory.
             // pro každou adresu URL obrázku v souboru... foreach (var Url in Urls) { // Inicializace nového pole ReviewTags array ReviewTags = new KeyValuePair[MAXTAGSCOUNT];

                // Evaluate for potential adult and racy content with Content Moderator API
                EvaluateAdultRacy(Url, ref ReviewTags);

                // Evaluate for potential presence of celebrity (ies) in images with Computer Vision API
                EvaluateComputerVisionTags(Url, ComputerVisionUri, ComputerVisionKey, ref ReviewTags);

                // Evaluate for potential presence of custom categories other than Marijuana
                EvaluateCustomVisionTags(Url, CustomVisionUri, CustomVisionKey, ref ReviewTags);

                // Create review in the Content Moderator review tool
                CreateReview(Url, ReviewTags);
            }

## <a name="license"></a>Licence

Všechny sady SDK služeb Microsoft Cognitive Services a ukázky jsou licencovány licencí MIT. Další podrobnosti najdete v [LICENCI](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Pravidla chování vývojáře

Vývojáři, kteří používají Cognitive Services, včetně této klientské knihovny a ukázky, by měli postupovat podle pravidel chování vývojářů pro Microsoft Cognitive Services, která se nacházejí na adrese http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Další kroky

Sestavte a rozšiřte kurz pomocí [zdrojových souborů projektu](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) na Githubu.
