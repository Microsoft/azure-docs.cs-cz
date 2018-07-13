Služba a typ vašeho předplatného určuje počet dotazů, které můžete provést za sekundu (QPS). Ujistěte se, že vaše aplikace obsahuje logiku potřebnou k nepřekročení vaší kvóty. Pokud překročíte vaše QPS, požadavek selže se stavovým kódem HTTP 429. Odpověď obsahuje také hlavičku Retry-After, která obsahuje počet sekund, po které se má vyčkat před odesláním dalšího požadavku.  
  
### <a name="denial-of-service-dos-versus-throttling"></a>Odepření služby (DOS) a omezení

Služba rozlišuje útoky DOS a porušení QPS. Pokud služba má podezření na útok DOS, požadavek bude úspěšný (stavový kód HTTP je 200 OK); text odpovědi však bude prázdný.