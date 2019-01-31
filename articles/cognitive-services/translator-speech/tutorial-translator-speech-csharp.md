---
title: 'Kurz: Translator Speech APIC#'
titleSuffix: Azure Cognitive Services
description: Rozhraní Translator Speech API můžete použít k překladu textu v reálném čase.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: tutorial
ms.date: 3/5/2018
ms.author: v-jerkin
ms.openlocfilehash: 01e56366fc8ae0e711664d35e95ac1aacc89730f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458495"
---
# <a name="tutorial-translator-speech-application-in-c"></a>Kurz: Translator Speech aplikace vC#

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Tento kurz pojednává o nástroji pro interaktivní překlad řeči využívajícím rozhraní Translator Speech API, které je součástí služby Azure Cognitive Services. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vyžádat seznam jazyků, které tato služba podporuje
> * Zaznamenat zvuk a přenést ho do této služby
> * Přijmout a zobrazit překlady řeči ve formě textu
> * Volitelně přehrát mluvenou verzi překladu (pomocí převodu textu na řeč)

Soubor řešení sady Visual Studio pro tuto aplikaci je [dostupný na GitHubu](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu potřebujete libovolnou edici sady Visual Studio 2017 včetně Community Edition. 

Toto řešení sady Visual Studio také vytvoří instalační program pro aplikaci. Kvůli podpoře této funkce potřebujete [WiX Toolset](http://wixtoolset.org/) a rozšíření [WiX Toolset Visual Studio Extension](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension).

Potřebujete rovněž klíč předplatného pro službu Translator Speech, který si můžete opatřit na řídicím panelu Microsoft Azure. K dispozici je cenová úroveň Free, která umožňuje bezplatný překlad až 10 hodin řeči za měsíc. Tato úroveň je pro účely tohoto kurzu dostačující.

Dále se vyžaduje [knihovna JSON.Net](https://www.newtonsoft.com/json) (od společnosti Newtonsoft). NuGet toto sestavení nainstaluje automaticky, pokud jsou v možnostech sady Visual Studio zaškrtnutá obě políčka pro obnovení balíčku.

## <a name="trying-the-translation-app"></a>Vyzkoušení aplikace pro překlad

Po otevření řešení Speech Translator (`SpeechTranslator.sln`) v sadě Visual Studio tuto aplikaci sestavte a spusťte stisknutím klávesy F5.  Zobrazí se hlavní okno programu.

![[Hlavní okno aplikace Speech Translator]](media/speech-translator-main-window.png)

Při prvním spuštění zvolte **Nastavení účtu** v nabídce **Nastavení**. Otevře se zde zobrazené okno.

![[Hlavní okno aplikace Speech Translator]](media/speech-translator-settings-window.png)

Vložte do tohoto okna klíč předplatného služby Translator Speech a klikněte na **Uložit**. Klíč se uloží pro následná spuštění.

Zpět v hlavním okně zvolte požadovaná zařízení pro vstup a výstup zvuku a jazyky From (Zdrojový) a To (Cílový). Pokud chcete tento překlad přehrát, zaškrtněte políčko **TTS** (převod textu na řeč). Pokud chcete během mluvení vidět spekulativní částečné překlady, povolte možnost **Partial Results** (Částečné výsledky).

Nakonec kliknutím na **Start** zahajte překlad. Řekněte něco, co chcete přeložit, a sledujte, jak se v okně zobrazuje rozpoznaný text a jeho překlad. Pokud jste zaškrtli políčko TTS, překlad navíc uslyšíte.

## <a name="obtaining-supported-languages"></a>Získání podporovaných jazyků

V době psaní tohoto článku podporuje služba Translator Speech více než 60 jazyků pro překlad textu. Pro překlad řeči je podporovaný menší počet jazyků. Tyto jazyky vyžadují podporu jak pro přepis (rozpoznávání řeči), tak pro výstup převodu textu na řeč, neboli syntézu.

Jinými slovy, kvůli překladu řeči musí zdrojový jazyk podporovat přepis. Jazyk výstupu může být libovolný z podporovaných jazyků pro překlad textu, pokud chcete výsledek v podobě textu. Pokud chcete řečový výstup, můžete překládat jen do jazyka s podporou převodu textu na řeč.

Microsoft může čas od času přidat podporu nových jazyků. Z tohoto důvodu byste neměli znalost podporovaných jazyků v aplikaci pevně zakódovat. Rozhraní Translator Speech API místo toho poskytuje koncový bod Jazyky, který umožňuje získat podporované jazyky za běhu. Můžete přijmout jeden nebo více seznamů jazyků: 

| | |
|-|-|
|`speech`|Jazyky podporované pro přepis řeči Mohou být zdrojovými jazyky pro překlad řeči.|
|`text`|Jazyky podporované pro překlad textu na text Mohou být cílovými jazyky pro překlad řeči, pokud se používá textový výstup.|
|`tts`|Hlasy podporované pro syntézu řeči, každý přidružený ke konkrétnímu jazyku Mohou být cílovými jazyky pro překlad řeči, pokud se používá převod textu na řeč. Daný jazyk může být podporován více než jedním hlasem.|

Koncový bod Jazyky nevyžaduje klíč předplatného a jeho použití se nezapočítává do vaší kvóty. Jeho identifikátor URI je `https://dev.microsofttranslator.com/languages` a vrací výsledky ve formátu JSON.

Zde uvedená metoda `UpdateLanguageSettingsAsync()` v souboru `MainWindow.xaml.cs` získává voláním koncového bodu Jazyky seznam podporovaných jazyků. 

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

Tato metoda nejprve sestaví žádost HTTP na koncový bod Jazyky a vyžádá si všechny tři seznamy jazyků (`text`, `speech` a `tts`).

Koncový bod Jazyky pomocí hlavičky žádosti `Accept-Languages` určí jazyk, ve kterém jsou uvedeny názvy jazyků. Například jazyk, který je anglicky mluvícím lidem známý jako „German“, se v němčině řekne „Deutsch“ a ve španělštině „Alemán“, přičemž do seznamu jazyků jsou tyto rozdíly promítnuty. Pro tuto hlavičku se používá výchozí jazyk systému.

Potom, co byla odeslána žádost a přijata odpověď JSON, se tato odpověď parsuje do interních datových struktur. Tyto struktury se následně použijí k sestavení nabídek zdrojového a cílového jazyka. 

Protože dostupné hlasy závisejí na cílovém jazyku zvoleném uživatelem, není zatím možné nastavit nabídku hlasů. Místo toho se dostupné hlasy pro jednotlivé jazyky uloží pro pozdější použití. Obslužná rutina `ToLanguage_SelectionChanged` (ve stejném zdrojovém souboru) později aktualizuje nabídku hlasů voláním metody `UpdateVoiceComboBox()`, když uživatel zvolí cílový jazyk. 

Čistě pro zábavu se cílový jazyk vybere náhodně, pokud uživatel tuto aplikaci dříve nespustil. (Nastavení nabídek se uloží pro následné relace.)

## <a name="authenticating-requests"></a>Ověřování požadavků

Kvůli ověření vůči službě Microsoft Translator Speech musíte v žádosti o připojení odeslat svůj klíč předplatného Azure v hlavičce jako hodnotu `Ocp-Apim-Subscription-Key`.

## <a name="translation-overview"></a>Přehled překladu

Rozhraní Translate API (koncový bod `wss://dev.microsofttranslator.com/speech/translate` protokolu WebSocket) akceptuje překládaný zvuk v 16kHz, 16bitovém monofonním formátu WAVE. Tato služba vrací jednu nebo několik odpovědí JSON obsahující jak rozpoznaný, tak přeložený text. Pokud se požaduje převod textu na řeč, odešle se zvukový soubor.

Uživatel zvolí zdroj zvuku pomocí nabídky vstupu přes mikrofon nebo soubor. Zvuk může pocházet ze zvukového zařízení (například mikrofonu) nebo souboru `.WAV`.

Metoda `StartListening_Click` se volá, když uživatel klikne na tlačítko Start. Tato obslužná rutina události následně voláním metody `Connect()` zahájí proces odeslání zvuku do koncového bodu rozhraní API této služby. Metoda `Connect()` provádí následující úlohy:


> [!div class="checklist"]
> * Získání uživatelských nastavení z hlavního okna a jejich ověření
> * Inicializace zvukového vstupu a výstupních streamů
> * Volání metody `ConnectAsync()`, která zařídí zbytek

Metoda `ConnectAsync()` se následně postará o:

> [!div class="checklist"]
> * Ověření pomocí klíče předplatného Azure v hlavičce `Ocp-Apim-Subscription-Key`
> * Vytvoření instance `SpeechClient` (v souboru `SpeechClient.cs`) ke komunikaci se službou
> * Inicializaci instancí `TextMessageDecoder` a `BinaryMessageDecoder` (viz `SpeechResponseDecoder.cs`) pro zpracování odpovědí
> * Odeslání zvuku prostřednictvím instance `SpeechClient`do služby Translator Speech
> * Příjem a zpracování výsledků překladu

`SpeechClient` toho má na starosti méně:

> [!div class="checklist"]
> * Navázání připojení ke službě Translator Speech přes protokol WebSocket
> * Odeslání zvukových dat a příjem odpovědí přes tento soket

## <a name="a-closer-look"></a>Bližší pohled

Nyní by mělo být jasnější, jak spolu součásti aplikace spolupracují při zpracování žádosti o překlad. Podívejme se na kód se zaměřením na relevantní části.

Zde je částečná verze metody `Connect()`, která ukazuje nastavení zvukových streamů:

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };
    
    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

Podstatná část metody `Connect()` se stará o vytvoření instance `SpeechClientOptions` (viz `SpeechClientOptions.cs`) pro uložení možností překladu. Mezi tyto možnosti patří informace potřebné pro připojení ke službě (například ověřovací klíč a název hostitele) a funkce použité pro překlad. Zde uvedená pole se mapují na pole hlavičky a parametry HTTP zpřístupněné [rozhraním Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference).

Metoda `Connect()` také vytváří a inicializuje vstupní zvukové zařízení (proměnná `sampleProvider`), které slouží jako zdroj překládané řeči. Tímto zařízením je buď hardwarové vstupní zařízení (například mikrofon), nebo soubor obsahující zvuková data WAVE.

Zde je metoda `ConnectAsync()`, která vytváří instanci třídy `speechClient` a připojuje anonymní funkce pro zpracování textu a binárních odpovědí ze služby.

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);
    
    TextMessageDecoder textDecoder;
    
    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);
    
    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected. 
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

Po ověření vytvoří tato metoda instanci `SpeechClient`. Třída `SpeechClient` (v `SpeechClient.cs`) vyvolává obslužné rutiny událostí při příjmu binárních a textových dat. Další obslužné rutiny se vyvolají, když připojení selže nebo se odpojí.

Binární data představují zvuk (výstup převodu textu na řeč) odesílaný službou, pokud je zaškrtnuté políčko TTS. Textová data představují částečný nebo úplný překlad vysloveného textu. Po vytvoření instance proto tato metoda připojí funkce pro zpracování těchto zpráv: zvuk jeho uložením pro pozdější přehrání a text jeho zobrazením v okně.

## <a name="next-steps"></a>Další postup

Tato ukázka kódu je plnohodnotná aplikace, která demonstruje použití rozhraní Translator Speech API. Vyžaduje pochopení celé řady součástí. Prošli jste si ty nejdůležitější prvky. U zbytku může být poučné, když si nastavíte v sadě Visual Studio pár zarážek a celý proces překladu si projdete. Až této ukázkové aplikaci porozumíte, budete mít dostatek vědomostí k použití služby Translator Speech ve svých vlastních aplikacích.

> [!div class="nextstepaction"]
> [Referenční informace k rozhraní Microsoft Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
