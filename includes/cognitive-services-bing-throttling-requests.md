Služba a typ vašeho předplatného určuje počet dotazů, které můžete provést za sekundu (QPS). Ujistěte se, že vaše aplikace obsahuje logiku potřebnou k nepřekročení vaší kvóty. Pokud je limit QPS dosažen nebo překročen, požadavek selže a bude vrácen stavový kód HTTP 429. Odpověď zahrnuje hlavičku `Retry-After`, která označuje, jak dlouho je nutné počkat před odesláním dalšího požadavku.

## <a name="denial-of-service-versus-throttling"></a>Odepření služby a omezování

Služba rozlišuje mezi útokem s cílem odepření služby (DoS) a porušením QPS. Pokud služba má podezření na útok DoS, požadavek bude úspěšný (stavový kód HTTP je 200 OK); text odpovědi však bude prázdný.