>[!NOTE]
>U prostředků, které nejsou pevné, je možné prostřednictvím lístku podpory požádat o zvýšení kvóty. **Nevytvářejte** další účty služby Azure Media Services ve snaze zajistit vyšší limity.

| Prostředek | Výchozí omezení | 
| --- | --- | 
| Účty Azure Media Services (AMS) v rámci jednoho předplatného | 25 (pevné) |
| Rezervované jednotky médií (RU) na jeden účet AMS |25 (S1)<br/>10 (S2, S3) <sup>(1)</sup> | 
| Úlohy na jeden účet AMS | 50 000<sup>(2)</sup> |
| Zřetězené úkoly na jednu úlohu | 30 (pevné) |
| Prostředky na jeden účet AMS | 1 000 000|
| Prostředky na jeden úkol | 50 |
| Prostředky na jednu úlohu | 100 |
| Jedinečné lokátory přidružené k prostředku najednou | 5<sup>(4)</sup> |
| Živé kanály na jeden účet AMS |5|
| Programy v zastaveném stavu na jeden kanál |50|
| Programy ve spuštěném stavu na jeden kanál |3|
| Koncové body streamování ve spuštěném stavu na jeden účet AMS|2|
| Jednotky streamování na jeden koncový bod streamování |10 |
| Účty úložiště | 1 000<sup>(5)</sup> (pevné) |
| Zásady | 1 000 000<sup>(6)</sup> |
| Velikost souboru| V některých případech je limit na maximální velikost souboru pro zpracování ve službě Media Services podporována. <sup>7</sup> |
  
<sup>1</sup> Pokud změníte typ (například z S2 k S1), se obnoví maximální RU omezení.

<sup>2</sup> Tato hodnota zahrnuje úlohy zařazené do fronty a dokončené, aktivní a zrušené úlohy. Nezahrnuje odstraněné úlohy. K odstranění starých úloh můžete použít **IJob.Delete** nebo požadavek HTTP **DELETE**.

Od 1. dubna 2017 záznam všechny úlohy ve vašem účtu, který je starší než 90 dní se automaticky odstraní, společně s jeho přidružené záznamy úloh i v případě, že celkový počet záznamů je nižší než maximální kvótu. Pokud potřebujete informace o úlohách/úkolech archivovat, můžete použít kód popsaný [tady](../articles/media-services/media-services-dotnet-manage-entities.md).

<sup>3</sup> při vytváření požadavku na seznam úloh entity, se maximálně 1000 úloh vrátí na žádost. Pokud potřebujete sledovat všechny odeslané úlohy, můžete použít parametry top/skip popsané v tématu věnovaném [možnostem dotazů pro systém OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Lokátory nejsou určené ke správě řízení přístupu pro jednotlivé uživatele. Pokud chcete konkrétním uživatelům poskytnout různá přístupová práva, použijte řešení pro správu digitálních práv (DRM). Další informace najdete v [tomto](../articles/media-services/media-services-content-protection-overview.md) oddílu.

<sup>5</sup> Účty úložiště musí být ze stejného předplatného Azure.

<sup>6</sup> Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Pokud vždycky používáte stejné dny, přístupová oprávnění atd., měli byste používat stejné ID zásad. Informace a příklad najdete v [tomto](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies) oddílu.

<sup>7</sup>Pokud nahráváte obsah pro prostředek služby Azure Media Services zpracovat s jedním z procesory médií v rámci služby (to znamená, kodéry jako Media Encoder Standard a Media Encoder Premium pracovního postupu nebo analysis motorů třeba vzhled detektor) pak musí být vědomi omezení na maximální velikosti podporované. 

Maximální velikost podporovaná pro jeden objekt blob je aktuálně až 5 TB ve službě Azure Blob Storage. Další omezení však použít v závislosti na velikosti virtuálních počítačů, které používají službu Azure Media Services. Následující tabulka uvádí omezení na jednotlivých jednotek rezervovaných média (S1 S2, S3.) Pokud váš zdrojový soubor je větší než omezení definovaná v tabulce, se nezdaří vaše úlohy kódování. Pokud jsou kódování 4K rozlišení zdroje dlouhou dobu, je nutné použít k dosažení výkonu potřebné jednotky rezervované pro média S3. Pokud máte 4K obsahu, který je větší než limit 260 GB na jednotky rezervované pro média S3, kontaktujte nás na adrese amshelp@microsoft.com pro potenciální jejich zmírnění pro podporu váš scénář.

| Typ rezervovaných jednotek médií | Maximální velikost vstupní (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
