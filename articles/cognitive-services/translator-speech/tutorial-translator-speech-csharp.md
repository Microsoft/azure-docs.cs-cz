---
title: Translator Speech kurzu (C#) | Dokumentace Microsoftu
titleSuffix: Cognitive Services
description: Naučte se používat službu Translator speech můžete přeložit text v reálném čase.
services: cognitive-services
author: v-jerkin
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.devlang: csharp
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jerkin
ms.openlocfilehash: 010ad8b5ceeaf046c8d361ff352e6058154a482d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "41987452"
---
# <a name="tutorial-microsoft-translator-wpf-application-in-c"></a>Kurz: Microsoft Translatoru WPF aplikace v jazyce C#

Tento kurz je si nástroj pro překladatele interaktivní řeči, používající překladatelské služby Microsoft Translator Speech, součást služeb Microsoft Cognitive Services v Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Žádost o seznam jazyků podporovaných službou
> * Zaznamenat zvuk a předá je do služby
> * Přijímat a zobrazovat překlady řeči jako text
> * Volitelně můžete přehrát mluvené řeči (převod textu na řeč) verze překladu

Soubor řešení sady Visual Studio pro tuto aplikaci je [k dispozici na Githubu](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu potřebujete libovolná edice sady Visual Studio 2017, včetně Community Edition. 

Řešení sady Visual Studio také vytvoří instalační program pro aplikaci. Je nutné [sadu nástrojů WiX Toolset](http://wixtoolset.org/) a [rozšíření sady Visual Studio WiX Toolset](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) pro podporu této funkce.

Budete také potřebovat klíč předplatného pro službu Translator Speech, kterou lze získat z řídicího panelu Microsoft Azure. Volná cenová úroveň je k dispozici, který umožňuje překlad řeči za měsíc zdarma až 10 hodin. Tato úroveň je dostačující pro účely tohoto kurzu.

Třetí strany [JSON.Net knihovny](https://www.newtonsoft.com/json) (z Newtonsoft) je také nutný. Toto sestavení se automaticky nainstaluje balíčkem NuGet, pokud obě zaškrtávací políčka obnovení balíčků jsou povolené v možnostech sady Visual Studio.

## <a name="trying-the-translation-app"></a>Při operaci posunutí aplikace

Po otevření řešení Microsoft Speech Translator (`SpeechTranslator.sln`) v sadě Visual STudio, stiskněte klávesu F5 sestavte a spusťte aplikaci.  Zobrazí se hlavní okno programu.

![[Hlavní okno Translator speech]](media/speech-translator-main-window.png)

Při prvním spuštění, zvolte **nastavení účtu** z **nastavení** nabídce otevřete okno je vidět tady.

![[Hlavní okno Translator speech]](media/speech-translator-settings-window.png)

V tomto okně vložte váš klíč předplatného Microsoft Translator Speech a potom klikněte na tlačítko **uložit.** Mezi spuštění se uloží váš klíč.

Zpět v hlavním okně zvolte zvukový vstup a výstup zařízení, které chcete použít a jaké z a do jazyků. Pokud chcete k přehrávání zvuku překladu, ujistěte se, **převod textu na ŘEČ** (převod textu na řeč) zaškrtnete políčko. Pokud chcete zobrazit spekulativního částečné překlady při mluvit, povolte **částečných výsledků** možnost.

Nakonec klikněte na tlačítko **Start** zahájíte překladu. Dejme tomu, že něco, co chcete mít přeložit a prohlédněte si rozpoznaný text a překladu se zobrazí v okně. Pokud jste povolili možnost převodu textu na ŘEČ, také slyšet překlad.

## <a name="obtaining-supported-languages"></a>Získání podporovaných jazyků

Při psaní tohoto návodu služba Microsoft Translator podporuje více než pět deseti jazycích pro překlad textu. Menší počet jazyky jsou podporovány pro překlad řeči. Tyto jazyky vyžadují podporu pro obě přepis (rozpoznávání řeči) a pro převod textu na řeč výstup, syntézy.

Jinými slovy pro překlad řeči, jazyka zdroje musí mít jednu podporované pro přepis. Jazyk výstup může být libovolná z jazyků pro překlady textů, za předpokladu, že chcete, aby výsledkem text nepodporuje. Pokud chcete řečovém výstupu, lze pouze přeloží do jazyka pro převod textu na řeč nepodporuje.

Microsoft může čas od času přidat podporu nových jazyků. Z tohoto důvodu byste měli není pevně zakódovat žádnou znalost podporované jazyky ve vaší aplikaci. Místo toho rozhraní Translator Speech API poskytuje koncový bod jazyky, které vám umožní načíst podporované jazyky v době běhu. Je možné přijímat jeden nebo více seznamů jazyky: 

| | |
|-|-|
|`speech`|Jazyky podporované pro určené k transkripci řeči. Může být source jazyky pro překlad řeči.|
|`text`|Jazyky podporované pro překlad textu do textu. Může být cílový jazyk pro překlad řeči, pokud se používá textový výstup.|
|`tts`|Hlasy podporované pro syntézu řeči, každý přidružený konkrétní jazyk. Může být cílový jazyk pro překlad řeči při převodu textu na řeč se používá. Daný jazyk může být podporován více než jeden hlas.|

Koncový bod jazyky nevyžaduje klíč předplatného a jeho využití se nepočítají proti vaší kvóty. Jeho identifikátoru URI je `https://dev.microsofttranslator.com/languages` a vrátí výsledky ve formátu JSON.

Metoda `UpdateLanguageSettingsAsync()` v `MainWindow.xaml.cs`, jak je znázorněno zde, zavolá koncový bod jazyky zobrazíte seznam podporovaných jazyků. 

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

Tato metoda nejprve vytvoří požadavek HTTP na koncový bod jazyky, požadování všechny tři seznamy jazyků (`text`, `speech`, a `tts`).

Koncový bod jazyky používá žádosti `Accept-Languages` hlavičky určuje jazyk, ve kterém jsou reprezentovány názvy jazyků. Například jazyk říká "Německé" se nazývá "Deutsch" v němčině a "Alemán" ve španělštině a seznamu jazyků k anglické přednášející odráží tyto rozdíly. Výchozí jazyk v systému se používá pro toto záhlaví.

Poté, co byl odeslán požadavek a odpověď JSON přijetí odpovědi je analyzován do interních datových struktur. Tyto struktury se následně použijí k vytvoření nabídky v jazyce a pro jazyk. 

Protože k dispozici hlasy závisí na jazyku k uživatelem, není možné nastavit v nabídce hlasové ještě. Místo toho k dispozici hlasy pro jednotlivé jazyky se ukládají pro pozdější použití. `ToLanguage_SelectionChanged` Obslužné rutině (ve stejném zdrojovém souboru) později aktualizace v nabídce hlasové voláním `UpdateVoiceComboBox()` když uživatel klikne na jazyk. 

Jen pro zábavu jazyk na náhodně vybrané Pokud uživatel nebyla spuštěna před aplikaci. (Nastavení nabídky jsou uloženy mezi relacemi.)

## <a name="authenticating-requests"></a>Ověřování požadavků

K ověření služby Microsoft Translator Speech, je nutné odeslat svůj klíč předplatného Azure v hlavičce hodnotu `Ocp-Apim-Subscription-Key` v žádosti o připojení.

## <a name="translation-overview"></a>Přehled nástroje Translation

Rozhraní API pro překlad (koncový bod WebSockets `wss://dev.microsofttranslator.com/speech/translate`) přijímá zvuku přeložit v monophonic, 16 kHz, 16bitové podepsané formátu WAVE. Tato služba vrátí jeden nebo více odpověďmi ve formátu JSON obsahující jak známé a přeložený text. Pokud se požaduje převod textu na řeč, odešle se zvukový soubor.

Uživatel vybere zdroje zvuku pomocí nabídky vstupního mikrofon nebo souboru. Zvuk může pocházet ze zvukové zařízení (například mikrofon) nebo `.WAV` souboru.

Metoda `StartListening_Click` je voláno, když uživatel klikne na tlačítko Start. Tato obslužná rutina události, volá `Connect()` zahájíte proces odeslat zvuk na koncový bod rozhraní API služby. `Connect()` Metoda provádí následující úlohy:


> [!div class="checklist"]
> * Nastavení uživatele z hlavního okna a ověřit je
> * Inicializace zvukového vstupu a výstupu datové proudy
> * Volání `ConnectAsync()` zpracovat zbývající práce

`ConnectAsync()`, však zpracovává následující úkoly:

> [!div class="checklist"]
> * Ověřování pomocí klíče předplatného Azure v záhlaví `Ocp-Apim-Subscription-Key`
> * Vytváření `SpeechClient` instance (součástí `SpeechClient.cs`) ke komunikaci se službou
> * Inicializace `TextMessageDecoder` a `BinaryMessageDecoder` instance (viz `SpeechResponseDecoder.cs`) pro zpracování odpovědi
> * Posílání zvuku prostřednictvím `SpeechClient` instance Translator Speech service
> * Příjem a zpracování výsledků překladu

Odpovědnosti `SpeechClient` jsou méně:

> [!div class="checklist"]
> * Vytvoření objektu websocket na straně připojení ke službě Translator Speech
> * Zvuková data odesílání a příjem odpovědí prostřednictvím soketu.

## <a name="a-closer-look"></a>Bližší pohled

Měla by být jasnější nyní jak částí aplikace spolupracují při zpracování požadavku překlad. Pojďme se podívat na nějaký kód, zaměřuje se na relevantní části.

Tady je částečný verze `Connect()` zvukové datové proudy, které zobrazí nastavení:

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

Podstatná část `Connect()` zahrnuje vytvoření `SpeechClientOptions` instance (naleznete v tématu `SpeechClientOptions.cs`) pro uložení možnosti pro překlad. Mezi možnosti patří informace potřebné pro připojení ke službě (třeba ověřovací klíč a název hostitele) a funkce používá pro překlad. Pole tady mapují na pole hlavičky a parametry protokolu HTTP vystavené [rozhraní Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference).

`Connect()` také vytvoří a inicializuje vstupní zvuková zařízení (proměnná `sampleProvider`), který slouží jako zdroj řeč k převodu. Toto zařízení je vstupní zařízení hardwaru, jako například mikrofon nebo soubor, který obsahuje WAVE zvuková data.

Tady je `ConnectAsync()` metodu, která vytvoří instanci `speechClient` třídy a připojuje anonymní funkce pro zpracování textu a binárních odpovědí ze služby.

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

Po ověření, kterou metoda vytvoří `SpeechClient` instance. `SpeechClient` Třídy (v `SpeechClient.cs`) vyvolá obslužné rutiny událostí při příjmu binární a textová data. Další obslužné rutiny jsou vyvolány, dojde k selhání připojení nebo odpojení.

Binární data jsou zvuku (řeč výstup) odeslaných službou, pokud je povolena převod textu na ŘEČ. Textová data se částečné nebo úplné překladu textu mluvené slovo. Proto po vytvoření instance, metoda zavěšení funkcí pro zpracování těchto zpráv: zvuku uložením později přehrát a text zobrazením v okně.

## <a name="next-steps"></a>Další postup

Tato ukázka kódu je plně funkční aplikace pro používání rozhraní Translator Speech API. V důsledku toho jsou veletrh řadu pohyblivých částí pochopit. Jste prošli nejdůležitějších částí. Pro ostatní může být poučné nastavit několik zarážky v sadě Visual Studio a provede procesem překladu. Až porozumíte ukázkovou aplikaci, že umožňuje použít službu Translator Speech ve svých vlastních aplikacích.

> [!div class="nextstepaction"]
> [Odkaz na rozhraní Microsoft Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
