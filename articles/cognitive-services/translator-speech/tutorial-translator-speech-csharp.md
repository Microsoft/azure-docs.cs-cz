---
title: Překladač kurzu rozpoznávání řeči (C#) | Microsoft Docs
titleSuffix: Cognitive Services
description: Naučte se používat službu řeči překladač k převodu textu v reálném čase.
services: cognitive-services
author: v-jerkin
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.devlang: csharp
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jerkin
ms.openlocfilehash: e82c5c5ccfa6b7de8a9ec111140dad1a40ad44f6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342666"
---
# <a name="tutorial-microsoft-translator-wpf-application-in-c"></a>Kurz: Microsoft Translator WPF aplikace v jazyce C#

Tento kurz je určen prohlídka nástroje interaktivní řeči překlad, která používá službu Microsoft překladač řeči překlad, součástí kognitivní služby společnosti Microsoft v Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Žádost o seznam jazyků podporovaných službou
> * Zaznamenání zvuk a přenést do služby
> * Přijímat a zobrazit překlady řeč jako text.
> * Volitelně můžete přehrát oznamována (převod textu na řeč) verzi překlad

Soubor řešení sady Visual Studio pro tuto aplikaci je [dostupná na Githubu](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu potřebujete libovolná edice Visual Studio 2017, včetně edice Community. 

Řešení nástroje Visual Studio také vytvoří instalační program pro aplikaci. Je nutné [sadu nástrojů WiX Toolset](http://wixtoolset.org/) a [rozšíření sady Visual Studio WiX Toolset](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) pro tuto funkci podporují.

Budete také potřebovat klíč předplatného pro službu překladač řeči, který můžete získat na řídicím panelu Microsoft Azure. Volná cenová úroveň je k dispozici, které umožňuje překládat až 10 hodin řeči za měsíc zdarma. Tato úroveň je dostačující pro účely tohoto kurzu.

Třetí strany [JSON.Net knihovny](https://www.newtonsoft.com/json) (z Newtonsoft) je také nutný. Toto sestavení se automaticky nainstaluje balíčkem NuGet, pokud obě zaškrtávací políčka obnovení balíčků jsou povolené v možnostech nástroje Visual Studio.

## <a name="trying-the-translation-app"></a>Při překladu aplikace

Po otevření řešení Microsoft řeči překladač (`SpeechTranslator.sln`) ve Visual Studiu stisknutím klávesy F5 sestavení a spuštění aplikace.  Zobrazí se hlavní okno aplikace.

![[Hlavní okno překladač řeči]](media/speech-translator-main-window.png)

Při prvním spuštění, zvolte **nastavení účtu** z **nastavení** nabídky a otevřete okno zobrazeny zde.

![[Hlavní okno překladač řeči]](media/speech-translator-settings-window.png)

Vložte svůj klíč předplatného Microsoft překladač řeči v tomto okně a potom klikněte na tlačítko **uložit.** Klíč je uložit mezi spustí.

Zpět v hlavním okně vyberte zvuk vstupní a výstupní zařízení, které chcete k použití a od a pro jiné jazyky. Pokud chcete k přehrávání zvuku překladu, ujistěte se, zda **převod textu na ŘEČ** (převod textu na řeč) zaškrtnete políčko. Pokud chcete zobrazit spekulativní překlady částečné, jako je Předčítání, povolte **částečné výsledky** možnost.

Nakonec klikněte na **spustit** zahájíte překlad. Řekněme něco, co chcete mít přeložit a sledovat rozpoznaný text a překlad se zobrazí v okně. Pokud jste povolili možnost převod textu na ŘEČ, uslyšíte také překlad.

## <a name="obtaining-supported-languages"></a>Získání podporovaných jazyků

Při psaní tohoto textu služby Microsoft Translator podporuje více než pět tucet jazyky pro překlad textu. Menší počet jazyky jsou podporovány pro rozpoznávání řeči překlad. Tyto jazyky vyžadují podporu pro obě přepis (rozpoznávání řeči) a pro převod textu na řeč výstupu souhrnnou.

Jinými slovy pro překlad řeči jazyka zdroje musí mít jednu pro přepis podporována. Jazyk, ve výstupu může být jedno z jazyky podporované pro překlad textu, za předpokladu, že chcete výsledku text. Pokud chcete řečovém výstupu, lze pouze převede na podporován pro převod textu na řeč.

Microsoft může občas přidat podporu pro nové jazyky. Z tohoto důvodu byste měli pevné kódování znalostí podporované jazyky ve vaší aplikaci. Namísto toho poskytuje rozhraní API pro rozpoznávání řeči překladač jazyky koncový bod, který vám umožní načíst na podporované jazyky za běhu. Můžete získat jeden nebo více seznam jazyků: 

| | |
|-|-|
|`speech`|Jazyky podporované pro přepis řeči. Jazyky zdrojové pro překlad řeči může být.|
|`text`|Jazyky podporované pro překlad text na text. Může být cílový jazyků pro překlad rozpoznávání řeči, když textový výstup se používá.|
|`tts`|Hlasy pro rozpoznávání řeči souhrnnou podporována, každý přidružený konkrétního jazyka. Může být cílový jazyků pro překlad rozpoznávání řeči, když se používá převod textu na řeč. Daný jazyk může podporovat více než jeden hlas.|

Koncový bod jazyky nevyžaduje klíč předplatného a jeho využití nepočítá proti vaší kvóty. Jeho identifikátoru URI je `https://dev.microsofttranslator.com/languages` a vrátí její výsledky ve formátu JSON.

Metoda `UpdateLanguageSettingsAsync()` v `MainWindow.xaml.cs`, uvedené v tomto zavolá koncový bod jazyky získat seznam podporovaných jazyků. 

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

Tato metoda vytvoří první požadavek HTTP na koncový bod jazyky, požaduje všechny tři seznamy jazyků (`text`, `speech`, a `tts`).

Koncový bod jazyky používá žádosti `Accept-Languages` záhlaví Určuje jazyk, ve kterém jsou reprezentované názvy jazyků. Například jazyka na angličtinu mluvčí známé jako "Němčině", se říká "Němčina" v němčině a "Alemán" ve španělštině a seznam jazyků odráží tyto rozdíly. Výchozí jazyk systému se používá pro tuto hlavičku.

Po odeslání žádosti a odpovědi JSON přijetí odpovědi je analyzován do interních datových strukturách. Tyto struktury pak lze vytvořit nabídky jazyk z a do jazyka. 

Vzhledem k tomu, že k dispozici hlasy závisí na jazyku k volená uživatelem, není možné nastavit v nabídce hlasové ještě. Místo toho jsou uloženy dostupné hlasy pro každý jazyk pro pozdější použití. `ToLanguage_SelectionChanged` Obslužné rutiny (ve stejném zdrojového souboru) později aktualizací v nabídce hlasové voláním `UpdateVoiceComboBox()` když uživatel vybere pro jazyk. 

Jenom pro zábavu jazyk pro náhodně vybrány Pokud uživatel nebyl spuštění aplikace před. (V nabídce nastavení jsou uloženy mezi relacemi.)

## <a name="authenticating-requests"></a>Ověřování požadavků

K ověření služby Microsoft překladač řeči, budete muset odeslat svůj klíč předplatného Azure v hlavičce jako hodnota `Ocp-Apim-Subscription-Key` v požadavku na připojení.

## <a name="translation-overview"></a>Přehled posunutí

Rozhraní API převede (koncový bod pro objekty WebSockets `wss://dev.microsofttranslator.com/speech/translate`) přijímá zvuk přeložit v monophonic, 16 kHz, 16 bitů podepsané WAVE formátu. Služba vrátí jeden nebo více odpovědí JSON obsahující jak text rozpoznaný a přeložený. Pokud bylo vyzváno převod textu na řeč, se budou odesílat zvukový soubor.

Uživatel vybere zdroj zvuku pomocí nabídky vstup mikrofon nebo k souboru. Zvuk mohou pocházet z zvukového zařízení (například mikrofon) nebo `.WAV` souboru.

Metoda `StartListening_Click` je volána, když uživatel klikne na tlačítko Start. Tuto obslužnou rutinu události, volá `Connect()` zahájíte proces odesílání zvuk ke koncovému bodu rozhraní API služby. `Connect()` Metoda provádí následující úlohy:


> [!div class="checklist"]
> * Načtení nastavení uživatele z hlavního okna a jejich ověření
> * Inicializace zvuk vstupní a výstupní datové proudy
> * Volání metody `ConnectAsync()` pro zpracování zbývající práce

`ConnectAsync()`, pak, zpracovává chores následující:

> [!div class="checklist"]
> * Ověřování pomocí klíč předplatného Azure v záhlaví `Ocp-Apim-Subscription-Key`
> * Vytváření `SpeechClient` instance (v nalezen `SpeechClient.cs`) ke komunikaci se službou
> * Inicializace `TextMessageDecoder` a `BinaryMessageDecoder` instance (viz `SpeechResponseDecoder.cs`) pro zpracování odpovědi
> * Odesílání zvuk prostřednictvím `SpeechClient` instance ke službě překladač řeči
> * Přijímání a zpracování výsledků překladu

Odpovědnosti `SpeechClient` jsou méně:

> [!div class="checklist"]
> * Vytvoření připojení protokolu WebSocket ke službě překladač řeči
> * Zvuk data odesílání a příjem odpovědí prostřednictvím soket

## <a name="a-closer-look"></a>Bližší pohled

Mělo by být jasnější teď jak částí aplikace spolupracují k provedení této žádosti překlad. Podívejme se na nějaký kód zaměřené na příslušné části.

Tady je částečná verze `Connect()` zvuk datové proudy, objeví nastavení:

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

Podstatnou část `Connect()` zahrnuje vytvoření `SpeechClientOptions` instance (viz `SpeechClientOptions.cs`) pro uložení možnosti pro překlad. Možnosti zahrnují informace potřebné pro připojení ke službě (například ověřovací klíč a název hostitele) a funkce, které používá pro překlad. Pole zde mapování na pole hlavičky a parametry protokolu HTTP, které jsou vystavené [rozhraní API pro rozpoznávání řeči překladač](http://docs.microsofttranslator.com/speech-translate.html).

`Connect()` také vytvoří a inicializuje vstupní zvuková zařízení (proměnná `sampleProvider`), slouží jako zdroj řeči k převodu. Toto zařízení je vstupní zařízení hardwaru, například mikrofon nebo soubor obsahující data zvuk WAVE.

Tady je `ConnectAsync()` metoda, která vytvoří instanci `speechClient` třídy a háky až anonymní funkce pro zpracování textové a binární odpovědi ze služby.

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

Po ověření vytvoří metodu `SpeechClient` instance. `SpeechClient` – Třída (v `SpeechClient.cs`) vyvolá obslužné rutiny událostí při příjmu dat binární a text. Další obslužné rutiny jsou vyvolány, dojde k selhání připojení nebo odpojení.

Binární data jsou odeslaných službou, pokud je povoleno převod textu na ŘEČ zvuk (je výstup). Textová data je částečné nebo úplné překlad mluvené textu. Proto po vytvoření instance, metoda zachytí až funkce pro zpracování těchto zpráv: zvuk uloží pro pozdější přehrávání a text zobrazením v okně.

## <a name="next-steps"></a>Další postup

Tato ukázka kódu je bohaté aplikace znázorňující používání rozhraní API pro rozpoznávání řeči překladač. Existuje jako takový správného několik částí pochopit. Jste si projít nejdůležitější bits. Pro zbývající může být významné nastavit několik zarážky v sadě Visual Studio a provede proces překladu. Až porozumíte ukázkovou aplikaci, jste vybavený používat službu překladač řeči v aplikaci.

> [!div class="nextstepaction"]
> [Referenční dokumentace rozhraní API řeči překladač Microsoft](http://docs.microsofttranslator.com/speech-translate.html)
