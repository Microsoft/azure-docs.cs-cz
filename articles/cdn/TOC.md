# [Dokumentace ke službě CDN](index.md)

# [Přehled](cdn-overview.md)
## [Co je Azure CDN?](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json)

# Začínáme
## [Povolení platformy Azure CDN](cdn-create-new-endpoint.md)

# Postup
## Integrace
### [Web Apps](../app-service/app-service-web-tutorial-content-delivery-network.md?toc=%2fazure%2fcdn%2ftoc.json)
### [Cloud Services](cdn-cloud-service-with-cdn.md)
### Úložiště
#### [Integrace účtu úložiště](cdn-create-a-storage-account-with-cdn.md)
#### [Podpora úložiště SAS](cdn-sas-storage-support.md)
### [Sdílení prostředků různého původu](cdn-cors.md)
### [Přidání vlastní domény do koncového bodu CDN](cdn-map-content-to-custom-domain.md)
### [Konfigurace HTTPS pro vlastní doménu](cdn-custom-ssl.md)
## Optimalizace obsahu
### [Přehled optimalizace](cdn-optimization-overview.md)
####[Optimalizace velkých souborů](cdn-large-file-optimization.md)
####[Optimalizace streamování médií](cdn-media-streaming-optimization.md)
####[Akcelerace dynamického webu](cdn-dynamic-site-acceleration.md)
 
## Spravovat
### [Správa pomocí Azure PowerShellu](cdn-manage-powershell.md)
### [Omezení přístupu podle země](cdn-restrict-access-by-country.md)
### [Vylepšení výkonu prostřednictvím komprimace souborů](cdn-improve-performance.md)
### Řízení chování při ukládání do mezipaměti
#### [Jak funguje ukládání do mezipaměti](cdn-how-caching-works.md)
#### [Řízení chování při ukládání do mezipaměti s využitím pravidel ukládání do mezipaměti](cdn-caching-rules.md)
#### Ukládání obsahu do mezipaměti podle řetězců dotazu
##### [Úroveň Standard](cdn-query-string.md)
##### [Úroveň Premium](cdn-query-string-premium.md)
#### [Vyprázdnění prostředků z mezipaměti](cdn-purge-endpoint.md)
#### [Předběžné načtení prostředků do mezipaměti](cdn-preload-endpoint.md)
### Konfigurace hodnoty TTL (time-to-live)
#### [Webový obsah Azure](cdn-manage-expiration-of-cloud-service-content.md)
#### [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
### [Ověření tokenu](cdn-token-auth.md)
### [Sledování prostředků](cdn-resource-health.md)
### [Potlačení chování pomocí pravidel](cdn-rules-engine.md)
### [Získávání výstrah v reálném čase](cdn-real-time-alerts.md)
### [Podpora HTTP/2](cdn-http2.md)

## Analýza
### [Analýza vzorů využití Azure CDN](cdn-log-analysis.md)
#### [Protokoly diagnostiky Azure](cdn-azure-diagnostic-logs.md)
#### Analytické nástroje pro Azure CDN od Verizonu
##### [Základní sestavy od Verizonu](cdn-analyze-usage-patterns.md)
##### [Vlastní sestavy od Verizonu](cdn-verizon-custom-reports.md)
#### Analytické nástroje pro Azure CDN Premium od Verizonu
##### [Generování pokročilých sestav HTTP](cdn-advanced-http-reports.md)
##### [Zobrazení statistik v reálném čase](cdn-real-time-stats.md)
##### [Analýza výkonu hraničních uzlů](cdn-edge-performance.md)

## Vývoj
### [.NET](cdn-app-dev-net.md)
### [Node.js](cdn-app-dev-node.md)

## Řešení potíží
### [Stav 404](cdn-troubleshoot-endpoint.md)
### [Komprese](cdn-troubleshoot-compression.md)

# Referenční informace
##  [Referenční informace ke stroji pravidel](cdn-rules-engine-reference.md)
### [Podmíněné výrazy stroje pravidel](cdn-rules-engine-reference-conditional-expressions.md)
### [Podmínky shody stroje pravidel](cdn-rules-engine-reference-match-conditions.md)
### [Funkce stroje pravidel](cdn-rules-engine-reference-features.md)
## [Ukázky kódu](https://azure.microsoft.com/en-us/resources/samples/?service=cdn)
## [Azure PowerShell](/powershell/module/azurerm.cdn)
## [.NET](/dotnet/api/microsoft.azure.management.cdn)
## [Java](/java/api/com.microsoft.azure.management.cdn)
## [REST](/rest/api/cdn/)

# Zdroje a prostředky
## Lokality POP v Azure CDN
### [Lokality POP podle oblastí](cdn-pop-locations.md)
### [Lokality POP podle zkratek](cdn-pop-abbreviations.md)
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/)
## [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecdn)
## [Ceny](https://azure.microsoft.com/pricing/details/cdn/)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Aktualizace služeb](https://azure.microsoft.com/updates/?product=cdn)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cdn)
## [Videa](https://azure.microsoft.com/documentation/videos/index/?services=cdn)

