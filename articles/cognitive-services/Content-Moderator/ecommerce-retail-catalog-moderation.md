---
title: přerušování katalogu elektronické obchodování s machine learning a AI s Azure obsahu moderátora | Microsoft Docs
description: Automaticky střední katalogů elektronické obchodování s machine learning a AI
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 5cb93941751c8744a19a5473d13f1e135a589eaa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342489"
---
# <a name="ecommerce-catalog-moderation-with-machine-learning"></a>přerušování katalogu elektronické obchodování s machine learning

V tomto kurzu jsme zjistěte, jak implementovat na základě machine learning inteligentního elektronické obchodování katalogu přerušování kombinací s asistencí počítač AI technologie s lidským přerušování k zajištění inteligentního katalogu systému.

![Obrázky klasifikovaný produktů](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>Podnikový scénář

Počítače s asistencí technologie používají ke klasifikaci a střední obrázky produktů v těchto kategoriích:

1. Pro dospělé (nahota)
2. Zájem (sugestivní)
3. Celebrit
4. Příznaky USA
5. Toys
6. Pera

## <a name="tutorial-steps"></a>Kurz kroky

Tento kurz vás provede tyto kroky:

1. Registrace a vytvoření týmu obsahu moderátora.
2. Nakonfigurujte přerušování značky (popisky) pro potenciální celebrit a příznak obsahu.
3. Pomocí rozhraní API moderátora obsah image vyhledat potenciální obsah pro dospělé a zájem.
4. Použijte rozhraní API vize počítače můžete spustit kontrolu potenciálních celebrit.
5. Použijte službu vlastní vize kontrolovala možné přítomnosti příznaky.
6. K dispozici výsledky nuanced kontroly pro lidské zkontrolujte a provedení konečné rozhodnutí.

## <a name="create-a-team"></a>Vytvoření týmu

Odkazovat [rychlý Start](quick-start.md) stránky zaregistrovat obsahu moderátora a vytvoření týmu. Poznámka: **ID týmu** z **pověření** stránky.


## <a name="define-custom-tags"></a>Zadejte vlastní značky

Odkazovat [značky](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) článek a přidat vlastní značky. Kromě integrovaných **pro dospělé** a **zájem** značky, nové značky povolit nástroj zkontrolujte zobrazíte popisný název pro značek.

V našem případě jsme definovali tyto vlastní značky (**celebrit**, **příznak**, **nám**, **hračka**, **pera**):

![Konfigurace vlastní značky](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>Seznam koncové body a klíče rozhraní API

1. Tento kurz používá tři rozhraní API a odpovídající klíče a rozhraní API koncové body.
2. Váš koncový bod rozhraní API se liší podle toho oblasti vašeho předplatného a ID obsahu moderátora zkontrolujte Team.

> [!NOTE]
> Kurz je určena k použití předplatného klíče v oblastech viditelné v následujících koncových bodů. Ujistěte se, že odpovídají klíče rozhraní API s oblasti identifikátory URI jinak klíče nemusí fungovat s vytvoření následujících koncových bodů:

         // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>Vyhledat obsah pro dospělé a zájem

1. Funkce přijímá jako parametry adresu URL obrázku a pole páry klíč hodnota.
2. Zavolá rozhraní API bitové kopie obsahu moderátora získat Dospělý a Racy skóre.
3. Pokud skóre je vyšší než 0.4 (rozsah je od 0 do 1), nastaví hodnotu **ReviewTags** pole na **True**.
4. **ReviewTags** pole se používá ke zvýraznění odpovídající značky v nástroji revize.

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

## <a name="scan-for-celebrities"></a>Vyhledat celebrit

1. Zaregistrujte si [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) z [počítače vize API](https://azure.microsoft.com/services/cognitive-services/computer-vision/).
2. Klikněte **získat klíč rozhraní API** tlačítko.
3. Podmínky přijměte.
4. Chcete-li přihlášení, zvolte ze seznamu Internetové účty, které jsou k dispozici.
5. Poznámka: zobrazené na stránku služby klíče rozhraní API.
    
   ![Rozhraní API vize klíče počítače](images/tutorial-computer-vision-keys.PNG)
    
6. Naleznete projektu zdrojového kódu pro funkce, která kontroluje bitovou kopii s rozhraním API vize počítače.

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

## <a name="classify-into-flags-toys-and-pens"></a>Klasifikovat do příznaky, toys a pera

1. [Přihlaste se](https://www.customvision.ai/account/signin) k [preview rozhraní API vize vlastní](https://www.customvision.ai/).
2. Použití [rychlý Start](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) vytvořit vaše vlastní třídění k detekci přítomnosti potenciální příznaky, toys a pera.
   ![Vlastní vize školení obrázků](images/tutorial-ecommerce-custom-vision.PNG)
3. [Získat adresu URL koncového bodu předpovědi](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api) pro vaše vlastní třídění.
4. Prohlédněte si kód zdroj projektu zobrazíte funkce, která volá váš koncový bod předpovědi vlastní třídění ke kontrole bitové kopie.

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
 
## <a name="reviews-for-human-in-the-loop"></a>Recenze pro lidské v the smyčky

1. V předchozích částech zkontrolovat pro příchozí obrázky pro dospělé a zájem (obsahu moderátora), celebrit (vize počítače) a příznaky (vlastní vize).
2. Podle našich shodu prahové hodnoty pro každý kontroly, zpřístupníte nuanced případů pro lidské zkontrolujte v nástroji Kontrola.
        Veřejné statické bool {CreateReview (řetězec ImageUrl, KeyValuePair [] Metadata)

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

## <a name="submit-batch-of-images"></a>Odeslání dávky bitových kopií

1. Tento kurz předpokládá adresář "C:Test" s textový soubor, který obsahuje seznam adres URL bitové kopie.
2. Následující kód zkontroluje existenci souboru a načte všechny adresy URL do paměti.
            Vyhledejte adresář testu pro textový soubor s seznam adres URL bitové kopie ke kontrole var topdir = @"C:\test\"; var Urlsfile = topdir +"Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>Spuštění všech kontrol

1. Tato funkce nejvyšší úrovně projde všechny adresy URL bitové kopie v textovém souboru, který jsme už zmínili dřív.
2. Prohledá je s každé rozhraní API a pokud skóre spolehlivosti shodu spadá do našich kritéria pro lidské moderátorů vytvoří kontrolu.
             pro každou adresa URL obrázku v souboru... příkazu foreach (var Url v adresách URL) {/ / Initiatize nové pole značky zkontrolujte ReviewTags = nové KeyValuePair [MAXTAGSCOUNT];

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

Všechny sady Microsoft kognitivní služby SDK a ukázky licenci s licencí MIT. Další podrobnosti najdete v tématu [licence](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Pravidla chování vývojáře

Vývojáře, kteří používají kognitivní služeb, včetně Tato klientská knihovna pro & ukázce se očekává, postupujte podle "Vývojáře kód z chování pro Microsoft kognitivní služby", který naleznete na http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Další postup

Sestavení a rozšíření kurz pomocí [zdrojové soubory projektu](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) na Githubu.
