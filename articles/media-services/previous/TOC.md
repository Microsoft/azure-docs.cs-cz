# [Dokumentace ke službě Media Services](index.md)

# [Přehled](media-services-overview.md)
## [Scénáře a dostupnost](scenarios-and-availability.md)
## [Koncepty](media-services-concepts.md)

# Začínáme
## [Vytvoření a správa účtu](media-services-portal-create-account.md)
## [Vytvoření a nastavení vývojového prostředí](media-services-set-up-computer.md)
### [.NET](media-services-dotnet-how-to-use.md)
### [REST](media-services-rest-how-to-use.md)  
## [Přístup k rozhraní API pomocí ověřování AAD](media-services-use-aad-auth-to-access-ams-api.md)
### [Správa ověřování AAD pomocí portálu](media-services-portal-get-started-with-aad.md)
### [Přístup k rozhraní API pomocí .NET](media-services-dotnet-get-started-with-aad.md)
### [Přístup k rozhraní API pomocí REST](media-services-rest-connect-with-aad.md)
### [Vytvoření a konfigurace aplikace AAD pomocí rozhraní příkazového řádku Azure](media-services-cli-create-and-configure-aad-app.md)
### [Vytvoření a konfigurace aplikace AAD pomocí Azure PowerShellu](media-services-powershell-create-and-configure-aad-app.md)

## Doručování videa na vyžádání
### [Azure Portal](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## Zajištění živého streamování
### [Azure Portal](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# Postup
## Spravovat
### Entity
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [Koncové body streamování](media-services-streaming-endpoints-overview.md)
#### [Azure Portal](media-services-portal-manage-streaming-endpoints.md)
#### [.NET](media-services-dotnet-manage-streaming-endpoints.md)
### Úložiště
#### [Aktualizace Media Services po postupném zavedení přístupových klíčů k úložišti](media-services-roll-storage-access-keys.md)
#### [Správa prostředků napříč několika účty úložiště](meda-services-managing-multiple-storage-accounts.md)
### [Kvóty a omezení](media-services-quotas-and-limitations.md)
## [Konfigurace nástroje Postman](media-rest-apis-with-postman.md)
### [Kolekce streamingu na vyžádání](postman-collection.md)
### [Kolekce živého streamování](postman-live-streaming-collection.md)
### [Prostředí](postman-environment.md)
## Nahrání obsahu
### Nahrání souborů do účtu
#### [Azure Portal](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Nahrávání velkých souborů pomocí Aspery](media-services-upload-files-with-aspera.md)
### [Nahrávání souborů pomocí StorSimple](media-services-upload-files-from-storsimple.md)
### [Kopírování existujících objektů blob](media-services-copying-existing-blob.md)

## [Kódování obsahu](media-services-encode-asset.md)
### [Porovnání kodérů](media-services-compare-encoders.md)
### [Správa rychlosti a souběžného zpracování vašeho kódování](media-services-manage-encoding-speed.md)
### Media Encoder Standard (MES)
#### [Kodeky a formáty Media Encoderu Standard](media-services-media-encoder-standard-formats.md)
#### [Použití MES k automatickému vygenerování žebříčku přenosových rychlostí](media-services-autogen-bitrate-ladder-with-mes.md)
#### Kódování pomocí Media Encoderu Standard
##### [Azure Portal](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST](media-services-rest-encode-asset.md)
#### [Pokročilé kódování pomocí MES](media-services-advanced-encoding-with-mes.md)
##### [Přizpůsobení předvoleb Media Encoderu Standard](media-services-custom-mes-presets-with-dotnet.md)
##### [Postup generování miniatur pomocí kodéru Media Encoder Standard a .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [Oříznutí videa pomocí kodéru Media Encoder Standard](media-services-crop-video.md)
#### Schémata MES
##### [Schéma Media Encoderu Standard](media-services-mes-schema.md)
##### [Vstupní metadata](media-services-input-metadata-schema.md)
##### [Výstupní metadata](media-services-output-metadata-schema.md)
#### [Přednastavení MES](media-services-mes-presets-overview.md) 
##### [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [H264 Multiple Bitrate 16x9 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [H264 Multiple Bitrate 4x3 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [H264 Single Bitrate 720p for Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [H264 Single Bitrate High Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [H264 Single Bitrate Low Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### Pracovní postup kodéru Media Encoder Premium
#### [Formáty a kodeky pracovního postupu Media Encoderu Premium](media-services-premium-workflow-encoder-formats.md)
#### Kódování pomocí pracovního postupu kodéru Media Encoder Premium
##### [Pracovní postup kodéru Media Encoder Premium](media-services-encode-with-premium-workflow.md)
##### [Výukové kurzy k pracovním postupům kodéru Media Encoder Premium](media-services-media-encoder-premium-workflow-tutorials.md)
##### [Vytváření pokročilých pracovních postupů kódování pomocí Návrháře postupu provádění](media-services-workflow-designer.md)
##### [Pracovní postup Premium s více vstupy](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [Vytvoření úlohy, která generuje bloky fMP4](media-services-generate-fmp4-chunks.md)
### Procesory médií
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)
### [Kódy chyb](media-services-encoding-error-codes.md)
### Zastaralé
#### [Statické balení a šifrování](media-services-static-packaging.md)

## [Živé streamování](media-services-manage-channels-overview.md)
### [Místní kodéry](media-services-live-streaming-with-onprem-encoders.md)
#### [Doporučené místní kodéry](media-services-recommended-encoders.md)
#### [Azure Portal](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
### [Živé streamování s použitím cloudového kodéru](media-services-manage-live-encoder-enabled-channels.md)
#### [Azure Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [Konfigurace místních kodérů pro použití s cloudovým kodérem](media-services-live-encoders-overview.md)
#### [Kodér Elemental Live](media-services-configure-elemental-live-encoder.md)
#### [Kodér FMLE](media-services-configure-fmle-live-encoder.md)
#### [Kodér Haivision KB](media-services-configure-kb-live-encoder.md)
#### [Kodér NewTek TriCaster](media-services-configure-tricaster-live-encoder.md)
#### [Kodér Wirecast](media-services-configure-wirecast-live-encoder.md)
### [Zpracování dlouhotrvajících operací](media-services-dotnet-long-operations.md)
### [Specifikace živého ingestování fragmentovaného MP4](media-services-fmp4-live-ingest-overview.md)

## [Oříznutí obsahu](media-services-azure-media-clipper-overview.md)
### [Začínáme](media-services-azure-media-clipper-getting-started.md)
### [Načtení videa](media-services-azure-media-clipper-load-assets.md)
### [Konfigurace klávesových zkratek](media-services-azure-media-clipper-keyboard-shortcuts.md)
### [Konfigurace lokalizace](media-services-azure-media-clipper-localization.md)
### [Odesílání úloh oříznutí](media-services-azure-media-clipper-submit-job.md)
### [Azure Portal](media-services-azure-media-clipper-portal.md)

## [Ochrana obsahu](media-services-content-protection-overview.md)
### [Šifrování úložiště](media-services-rest-storage-encryption.md)
### [Šifrování AES-128](media-services-protect-with-aes128.md)
### [PlayReady/Widevine pro streamování](media-services-protect-with-playready-widevine.md)
### [FairPlay pro streamování](media-services-protect-hls-with-fairplay.md)
### [PlayReady pro Windows 10 v offline režimu](https://blogs.msdn.microsoft.com/playready4/2016/10/26/does-azure-media-services-support-offline-mode/)
### [Fairplay pro iOS v offline režimu](media-services-protect-hls-with-offline-fairplay.md)
### [Widevine pro Android v offline režimu](offline-widevine-for-android.md)
### [Konfigurace na webu Azure Portal](media-services-portal-protect-content.md)
### [Doručování licencí DRM](media-services-deliver-keys-and-licenses.md)
### Vytvoření klíčů ContentKeys
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### Přehledy šablon licencování
#### [Šablona licencování PlayReady](media-services-playready-license-template-overview.md)
#### [Šablona licencování Widevine](media-services-widevine-license-template-overview.md)
### Konfigurace zásad doručování prostředků
#### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
#### [REST](media-services-rest-configure-asset-delivery-policy.md)
### Konfigurace zásad autorizace klíčů obsahu
#### [Azure Portal](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)
### [Předání ověřovacích tokenů do AMS](media-services-pass-authentication-tokens.md)
### Referenční návrhy
#### [Hybridní návrh systému DRM](hybrid-design-drm-sybsystem.md)
#### [Referenční návrh s více variantami DRM](media-services-cenc-with-multidrm-access-control.md)

## [Analýza](media-services-analytics-overview.md)
### [Analýza médií s využitím webu Azure Portal](media-services-portal-analyze.md)
### [Zpracování pomocí Indexeru 2](media-services-process-content-with-indexer2.md)
### [Zpracování pomocí Indexeru](media-services-index-content.md)
#### [Předvolby úloh](indexer-task-preset.md)
### [Zpracování pomocí technologie Hyperlapse](media-services-hyperlapse-content.md)
### [Zpracování pomocí detektoru obličejů](media-services-face-and-emotion-detection.md)
### [Zpracování pomocí detektoru pohybu](media-services-motion-detection.md)
### [Zpracování pomocí Face Redactoru](media-services-face-redaction.md)
#### [Názorný postup práce s Face Redactorem](media-services-redactor-walkthrough.md)
### [Zpracování pomocí miniatur videí](media-services-video-summarization.md)
### [Zpracování pomocí technologie OCR](media-services-video-optical-character-recognition.md)
### [Zpracování pomocí Content Moderatoru](media-services-content-moderation.md)

## [Konfigurace telemetrie](media-services-telemetry-overview.md)
###[.NET](media-services-dotnet-telemetry.md)
###[REST](media-services-rest-telemetry.md)

## Měřítko
### [Zpracování médií](media-services-scale-media-processing-overview.md)
#### [Azure Portal](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
### Koncové body streamování
#### [Azure Portal](media-services-portal-scale-streaming-endpoints.md)

## [Doručování obsahu](media-services-deliver-content-overview.md)
### [Dynamické balení](media-services-dynamic-packaging-overview.md)
### [Přehled filtrů a dynamických manifestů](media-services-dynamic-manifest-overview.md)
#### [Vytváření filtrů pomocí .NET](media-services-dotnet-dynamic-manifest.md)
#### [Vytváření filtrů pomocí REST](media-services-rest-dynamic-manifest.md)
### [Zásady ukládání do mezipaměti CDN v rozšíření Media Services Extension](../../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### Publikování obsahu
#### [Azure Portal](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [Doručení materiálu stažením](media-services-deliver-asset-download.md)
### [Scénář streamování při selhání](media-services-implement-failover.md)

## Konzumace
### [Přehrávání multimédií pomocí stávajících přehrávačů](media-services-playback-content-with-existing-players.md)
### [Přehrávání multimédií pomocí Media Playeru](media-services-develop-video-players.md)
### Další možnosti přehrávání
#### [Aplikace pro Windows Store využívající Smooth Streaming](media-services-build-smooth-streaming-apps.md)
#### [Aplikace HTML5 využívající DASH.js](media-services-embed-mpeg-dash-in-html5.md)
#### [Přehrávače Adobe Open Source Media Framework](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [Vkládání reklam na straně klienta](media-services-inserting-ads-on-client-side.md)
### [Licencování sady pro portování klienta Microsoft Smooth Streaming](media-services-sspk.md)

## Integrace
### [Použití Azure Functions se službou Media Services](media-services-dotnet-how-to-use-azure-functions.md)
### [Příklady Azure Functions se službou Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## Monitorování
### Kontrola průběhu úlohy
#### [REST](media-services-rest-check-job-progress.md)
#### [Azure Portal](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Monitorování oznámení úloh pomocí Queue Storage](media-services-dotnet-check-job-progress-with-queues.md)
### [Monitorování oznámení úloh pomocí webhooků](media-services-dotnet-check-job-progress-with-webhooks.md)

## Řešení potíží
### [Nejčastější dotazy](media-services-frequently-asked-questions.md)
### [Průvodce řešením problémů s živým streamováním](media-services-troubleshooting-live-streaming.md)
### [Kódy chyb](media-services-error-codes.md)
### [Logika opakování](media-services-retry-logic-in-dotnet-sdk.md)

# Referenční informace
## [Ukázky kódu](https://azure.microsoft.com/resources/samples/?service=media-services)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.media)
## [Azure PowerShell (správa služeb)](/powershell/module/azure/?view=azuresmps-3.7.0)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST](/rest/api/media/mediaservice)
## Specifikace
### [Živé ingestování – specifikace živého ingestování fragmentovaného MP4](media-services-fmp4-live-ingest-overview.md)
### [Živé ingestování – signalizace časovaných metadat v živém streamování](media-services-specifications-live-timed-metadata.md)
### [Smooth Streaming HEVC](media-services-specifications-ms-sstr-amendment-hevc.md)

# Zdroje a prostředky
## [Komunita Azure Media Services](media-services-community.md)
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/?category=web-mobile)
## [Ceny](https://azure.microsoft.com/pricing/details/media-services/)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Poznámky k verzi](media-services-release-notes.md)
## [Videa](https://azure.microsoft.com/resources/videos/index/?services=media-services)
