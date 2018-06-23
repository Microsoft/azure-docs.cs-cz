---
title: Předkompilované aplikace Cortana odkazovat | Microsoft Docs
description: Referenční dokumentace pro něho osobní Cortana, předkompilované aplikace z jazyka Principy inteligentního služby (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: cd808c30a6781fc0252992c13ba247486e35ad44
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342545"
---
# <a name="cortana-prebuilt-app-reference"></a>Předkompilované aplikace Cortana odkaz
Tento odkaz seznam tříd Intent a entit, které aplikace předem Cortana rozpozná.

## <a name="cortana-prebuilt-intents"></a>Cortana předem záměry
Předkompilované pomocníka osobní aplikace můžete identifikovat záměry následující:

| Záměr | Příklad utterances |
|--------| ------------------|
| Builtin.Intent.alarm.alarm_other|Aktualizovat Moje 7:30 alarmů na osm hodin <br/>Změňte Moje alarmů od 8:9:00 |
| Builtin.Intent.alarm.delete_alarm|Odstranit alarm <br/>Odstranit mé alarmů "probuzení"|
| Builtin.Intent.alarm.find_alarm|pro jaké čas nastavena Moje funkce wake-up alarmů? <br/> je Moje funkce wake-up alarmů na? |
| Builtin.Intent.alarm.set_alarm|zapnout Moje funkce wake-up výstrahy<br/>můžete nastavit alarm 12 názvem trvat antibiotik?|
| Builtin.Intent.alarm.Snooze|připomenutí výstrahy 5 minut<br/>připomenutí výstrahy|
| Builtin.Intent.alarm.time_remaining| jak dlouho je nutné provést až "funkce wake-up"? <br/> jak dlouho dokud Moje další výstrahy?|
| Builtin.Intent.alarm.turn_off_alarm | vypnout Moje alarmů 7: 00 <br/> vypnout Moje funkce wake-up výstrahy |
| Builtin.Intent.Calendar.change_calendar_entry| Změna události<br/>Přesunout Moje schůzku od dnešního dne na zítra|
|Builtin.Intent.Calendar.check_availability|je zaneprázdněn tim na pátek?<br/>je brian volné na sobotu|
|Builtin.Intent.Calendar.connect_to_meeting|připojení k schůzky<br/>připojení k online schůzky|
|Builtin.Intent.Calendar.create_calendar_entry|Je nutné naplánovat událost s tony pro pátek<br/>vytvořit událost s lisa na 14: 00 v neděli|
|Builtin.Intent.Calendar.delete_calendar_entry|Odstranit mé událost<br/>Odeberte meeting v 15: 00 zítra z kalendáře|
|Builtin.Intent.Calendar.find_calendar_entry|Zobrazit kalendář<br/>Zobrazí týdenní kalendáře|
|Builtin.Intent.Calendar.find_calendar_when|Když můj další splňuje s jon?<br/>jaké čas je můj schůzku s larry v pondělí?|
|Builtin.Intent.Calendar.find_calendar_where|Zobrazit mi umístění Moje schůzku 6 hodin<br/>kde je tento schůzku s jon?|
|Builtin.Intent.Calendar.find_calendar_who|kdo je tuto schůzku s?<br/>uživatele, kteří se vyzývá?|
|Builtin.Intent.Calendar.find_calendar_why|jaké jsou podrobnosti o schůzku Moje 11 hodin?<br/>Co je tento schůzku s jon o?|
|Builtin.Intent.Calendar.find_duration|jak dlouho je můj další schůzku<br/>kolik minut dlouhé je můj schůzku tento odpoledne|
|Builtin.Intent.Calendar.time_remaining|jak dlouho dokud Moje další událost?<br/>kolik víc času je nutné provést před zahájením schůzky?|
|Builtin.Intent.Communication.add_contact|Uložte tento počet a uveďte název jako jose<br/>Uveďte jason ze seznamu kontaktů|
|Builtin.Intent.Communication.answer_phone|Odpověď<br/>odpověď volání|
|Builtin.Intent.Communication.assign_nickname|Upravit přezdívku pro nickolas<br/>Přidání přezdívku pro john doe|
|Builtin.Intent.Communication.call_voice_mail|Hlasová pošta<br/>volání hlasová pošta|
|Builtin.Intent.Communication.find_contact|Zobrazit kontakty<br/>Najít kontakty|
|Builtin.Intent.Communication.forwarding_off|Zastaví předávání Moje volání<br/>vypnout přesměrování volání|
|Builtin.Intent.Communication.forwarding_on|nastavení předávání volání odeslat volání na telefon domů<br/>předat dál volání na telefon domů|
|Builtin.Intent.Communication.ignore_incoming|Neodpovídejte na tuto volání<br/>teď ne jsem zaneprázdněn|
|Builtin.Intent.Communication.ignore_with_message|nechcete odpovězte toto volání, ale místo toho odeslat zprávu<br/>Ignorovat a odeslat jako text zpět|
|Builtin.Intent.Communication.make_call|bob volání a Jan<br/>volání mom a dad|
|Builtin.Intent.Communication.press_key|Vytočit hvězdičkou<br/>stiskněte 1 2 3|
|Builtin.Intent.Communication.read_aloud|čtení textu<br/>co uživatel říká ve zprávě|
|Builtin.Intent.Communication.redial|Opakovat vytáčení Moje posledním volání<br/>Opakovat vytáčení|
|Builtin.Intent.Communication.send_email|e-mailu Michal obsaženému vod Jan, že byl splendid večeři minulého týdne<br/>e-mailovou zprávu Bob|
|Builtin.Intent.Communication.send_text|text poslat bob a Jan<br/>zpráva|
|Builtin.Intent.Communication.speakerphone_off|Přejít na požadované vypnout mluvčího<br/>vypnout sluchátek s mikrofonem|
|Builtin.Intent.Communication.speakerphone_on|režim sluchátek s mikrofonem<br/>umístí sluchátek s mikrofonem|
|Builtin.Intent.mystuff.find_attachment|Najít že Jan dokumentu e-mailem poslat mi včera <br/>najít na dokumentů z Jan|
|Builtin.Intent.mystuff.find_my_stuff|Je potřeba upravit Moje nákupní seznamu od včerejška<br/>Vyhledání poznámky chemie ze září
|Builtin.Intent.mystuff.search_messages|otevření zprávy <br/>cloud-zařízení|
|Builtin.Intent.mystuff.transform_my_stuff|sdílet Moje nákupní seznam s Moje manžela<br/>Odstranit mé nákupní seznamu|
|Builtin.Intent.ondevice.open_setting|Otevřete nastavení cortany <br/>Přejít na oznámení|
|Builtin.Intent.ondevice.pause|vypnout Hudba<br/>Hudba vypnuto|
|Builtin.Intent.ondevice.play_music|Chcete nás vlastník osamocené vysílat<br/>přehrávání některé gospel hudby|
|Builtin.Intent.ondevice.recognize_song|Chci vědět, co je tento skladbu<br/>analyzovat a načíst název skladbu|
|Builtin.Intent.ondevice.REPEAT|Opakujte tento stopy<br/>přehrát toto skladbu znovu|
|Builtin.Intent.ondevice.Resume|Restartujte Hudba<br/>znovu spustit Hudba|
|Builtin.Intent.ondevice.skip_back|zálohování sledování<br/>předchozí skladbu|
|Builtin.Intent.ondevice.skip_forward|další skladbu<br/>Přeskočit sledování|
|Builtin.Intent.ondevice.turn_off_setting|Wi-Fi vypnuto<br/>zakázat režim v letadle|
|Builtin.Intent.ondevice.turn_on_setting|Wi-Fi na<br/>zapnout bluetooth|
|Builtin.Intent.Places.add_favorite_place|Přidat tuto adresu do složky Oblíbené položky<br/>Toto umístění pro uložení do složky Oblíbené položky|
|Builtin.Intent.Places.book_public_transportation|Kupte si lístek train<br/>sešit tramvají průchodu|
|Builtin.Intent.Places.book_taxi|můžete je najít mi pravé v této hodinu?<br/>Najít taxíkem|
|Builtin.Intent.Places.check_area_traffic|Co je provoz jako na 520<br/>jak je provoz na i-5|
|Builtin.Intent.Places.check_into_place|vrácení se změnami do joya<br/>Zde se změnami|
|Builtin.Intent.Places.check_route_traffic|Zobrazit provoz na způsob, jak kirkland<br/>jak je provoz k webu seattle?|
|Builtin.Intent.Places.find_place|kde jak živě <br/>mě japonské restaurace horní 3|
|Builtin.Intent.Places.get_address|Zobrazte adresu guu v robson ulice<br/>Co je adresa nejbližší starbucks?|
|Builtin.Intent.Places.get_coupon|Najít zpracovává na libovolné televizi v mé oblasti<br/>slevy v Horská zobrazení|
|Builtin.Intent.Places.get_distance|jak daleko je svátek DIČ?<br/>Co je vzdálenost k tahoe|
|Builtin.Intent.Places.get_hours|jaké jsou panelu del corso hodin na pondělí?<br/>Když je otevřený knihovny?|
|Builtin.Intent.Places.get_menu|Zobrazit mi na applebee nabídky<br/>Co je v nabídce v sizzler|
|Builtin.Intent.Places.get_phone_number|Zadejte číslo pro domácí skladu<br/>Co je telefonní číslo nejbližší starbucks?|
|Builtin.Intent.Places.get_price_range|kolik nemá večeři red severského náklady<br/>jak nákladné je fialové kavárně|
|Builtin.Intent.Places.get_reviews|Zobrazit mi recenze pro místní úložiště hardwaru<br/>Chcete vidět, že zkontroluje restaurace|
|Builtin.Intent.Places.get_route|mě pokynů k|je možné, abyste se dostali na pizza budka na stopy|
|Builtin.Intent.Places.get_star_rating|kolik hvězdiček má francouzštině prací?<br/>je vhodné akvárium v monterrey?|
|Builtin.Intent.Places.get_transportation_schedule|jaké čas san franciscu ferry ponechejte?<br/>Pokud je nejnovější train k webu seattle?|
|Builtin.Intent.Places.get_travel_time|Co je doba řízení k Ostravy z SF<br/>jak dlouho bude trvat zobrazíte Brno z tohoto umístění|
|Builtin.Intent.Places.make_call|volání Dr. smith v bellevue<br/>volání domácí skladu v ulici 1.|
|Builtin.Intent.Places.rate_place|poskytnout hodnocení pro tento restaurace<br/>míra il fornaio 5 hvězdiček na yelp|
|Builtin.Intent.Places.show_map|Co je aktuální umístění <br/>Co je můj umístění|
|Builtin.Intent.Places.takes_reservations|je možné provádět rezervace v oliv zahrada<br/>galerii obrázky přijímat rezervace|
|Builtin.Intent.Reminder.change_reminder|změnit připomenutí<br/>Přesunout nahoru Moje obrázek den připomenutí 30 minut|
|Builtin.Intent.Reminder.create_single_reminder|Připomenout probudit v 7: 00<br/>Připomeňte přejdete efektu nebo práce s luca ve 4:40|
|Builtin.Intent.Reminder.delete_reminder|odstranit toto připomenutí<br/>Odstranit připomenutí Moje obrázek|
|Builtin.Intent.Reminder.find_reminder|mají všechny připomenutí nastavil pro ještě dnes<br/>je nutné o na luca strany|
|Builtin.Intent.Reminder.read_aloud|Číst připomenutí nahlas<br/>čtení tohoto upozornění|
|Builtin.Intent.Reminder.Snooze|Připomenout znovu tuto připomenutí dokud zítra<br/>Připomenout znovu toto připomenutí|
|Builtin.Intent.Reminder.turn_off_reminder|vypnout připomenutí<br/>Zavřít výstupní připomenutí letišti|
|Builtin.Intent.weather.change_temperature_unit|Změňte z Fahrenheita kelvin<br/>Změňte z Fahrenheita c|
|Builtin.Intent.weather.check_weather|Zobrazit předpovědi pro moje nadcházející cesta k webu seattle<br/>jak bude počasí tento víkendu|
|Builtin.Intent.weather.check_weather_facts|Co je počasí jako v Havaj v prosinci?<br/>jaká byla teplota této doby poslední rok?|
|Builtin.Intent.weather.compare_weather|mě porovnání mezi teploty vysoké a nejnižších hodnot dallas a houston, tx<br/>jak počasí porovnat slc a nyc|
|Builtin.Intent.weather.get_frequent_locations|mě Moje nejčastěji se vyskytující umístění<br/>Zobrazit nejčastěji zastaví.|
|Builtin.Intent.weather.get_weather_advisory|upozornění počasí<br/>Zobrazit informační zpravodaj pro sacramento počasí|
|Builtin.Intent.weather.get_weather_maps|Zobrazit mapu počasí<br/>Zobrazit mi počasí mapy pro Afrika|
|Builtin.Intent.weather.question_weather|bude se zamlženého ráno zítra?<br/>bude potřeba shovel sněhové tento víkendu?|
|Builtin.Intent.weather.show_weather_progression|Zobrazit paprskového místní počasí<br/>Začněte paprskového|
|Builtin.Intent.None|Kolik ti je<br/>Otevřete fotoaparát|

## <a name="prebuilt-entities"></a>Předkompilované entity 

Tady jsou některé příklady entity, které můžete identifikovat aplikace předem pomocníka osobní:

|Entita |Příklad entity v utterance |
|-------|------------------|
|Builtin.alarm.alarm_state | zapnout `off` Moje funkce wake-up výstrahy    <br/> je Moje funkce wake-up výstrahy `on`  | 
|Builtin.alarm.Duration |připomenutí pro `10 minutes`    <br/> připomenutí výstrahy `5 minutes`  |
|Builtin.alarm.start_date | nastavit alarm pro `monday` na 7: 00   <br/> nastavit alarm pro `tomorrow` v poledne   |
|Builtin.alarm.start_time | Vytvoření alarm pro `30 minutes`    <br/> Nastavení upozornění vypnutí `in 20 minutes`   |
|Builtin.alarm.Title | je můj `wake up` připomenutí zapnuto <br/> můžete nastavit pro čtvrtletí do 12 pondělí až pátek názvem alarm `take antibiotics` |
|Builtin.Calendar.absolute_location | Vytvoření události pro zítra v `123 main street`   <br/> Schůzka bude probíhat `cincinnati` na 5. z června    |
|Builtin.Calendar.contact_name|Vložit marketing schůzky na můj kalendář a ujistěte se, který `joe` je k dispozici <br/>Chcete nastavit oběd v il fornaio a pozvat `paul` |   
|Builtin.Calendar.destination_calendar|Přidat k mé `work` plán   <br/>to uvedli Moje `personal` kalendáře |
|Builtin.Calendar.Duration| nastavit událost pro `an hour` na dnešní večer 6 <br/>  adresáře `2 hour` schůzka s Jan |  
|Builtin.Calendar.end_date | Vytvoření položky kalendáře volat dovolenou z zítra až `next monday` <br/>    blokovat Moje dobu jako zaneprázdněn až `monday, october 5th` | 
|Builtin.Calendar.end_time | schůzku končí na `5:30 PM` <br/> naplánovat od 11 `noon`  |   
|Builtin.Calendar.implicit_location | Zrušit schůzku na dmv <br/> Změňte umístění narozeniny paliva. `poppy restaurant` |    
|Builtin.Calendar.move_earlier_time | předat dál push schůzky `an hour` <br/> Přesunout nahoru k zubaři schůzku `30 minutes`       |
|Builtin.Calendar.move_later_time | Přesunout Moje zubní lékař událost `30 minutes`    <br/> Vysunout schůzky `an hour` |  
|Builtin.Calendar.original_start_date | změnit plán naplánovaných Moje schůzku v barber z "Úterý" do středy <br/> Přesunout Moje schůzku s ken z `monday` na úterý |
|Builtin.Calendar.original_start_time | změnit plán naplánovaných Moje schůzku z `2:00` na 3  <br/> změnit Moje zubní lékař schůzku z `3:30` 4 |  
|Builtin.Calendar.start_date | jaké čas Moje strany spustit na `flag day` <br/> naplánovat oběd `friday after next` v poledne 
|Builtin.Calendar.start_time | Chcete ho naplánovat na `this morning` <br/> Chcete ho naplánovat `morning` |  
|Builtin.Calendar.Title | `vet appointment`  <br/> `dentist` Úterý |
|Builtin.Communication.audio_device_type | Ujistěte se pomocí volání `bluetooth`  <br/> volání pomocí Moje `headset` | 
|Builtin.Communication.contact_name | text `bob jones`  <br/> | volání `sarah`|
|Builtin.Communication.destination_platform | volání dave v `london` <br/> volání svůj `work line` |    
|Builtin.Communication.from_relationship_name | Zobrazit volání z mé `daughter` <br/> čtení e-mailu z `mom`   |   
|Builtin.Communication.Key | telefonické připojení `star` <br/>  stiskněte `hash` klíč    |
|Builtin.Communication.Message | e-mailu carly. Tím vyjádříte `i'm running late` <br/> text angus smith `good luck on your exam` |    
|Builtin.Communication.message_category | nový e-mailu označen pro `follow up`  <br/> Označit nových e-mailů `high priority` |    
|Builtin.Communication.message_type | Odeslat `email`   <br/> čtení my `text` nahlas zprávy |
|Builtin.Communication.phone_number | Chci vytočit `1-800-328-9459` <br/>     volání `555-555-5555` |   
|Builtin.Communication.relationship_name | text Moje `husband` <br/>  E-mailu `family`| 
|Builtin.Communication.slot_attribute | změnit `recipient` <br/>    změnit `text` | 
|Builtin.comminication.source_platform | volání mu z `skype` <br/> volání mu z mé `personal line` |
|Builtin.mystuff.Attachment| s dokumenty `attached` <br/> najít e-mailu `attachment` bob odeslána |
|Builtin.mystuff.contact_name| najít tabulku, kterou posílá lisa `me` <br/> kdy je dokument i odesílají na `susan` poslední noci |
|Builtin.mystuff.data_source | `c:\dev\` <br/> Moje `desktop` <br/> |`"resolution": { "resolution_type": "metadataItems", "metadataType": CanonicalEntity", "value": "desktop" }`|
|Builtin.mystuff.data_type | Vyhledejte `document` i pracovali poslední noci <br/> |`"resolution": {"resolution_type": "metadataItems", "metadataType": "CanonicalEntity", "value":Document" }` <br/> zprovoznit uživatele `visio diagram`  |
|Builtin.mystuff.end_date| Zobrazit mi dokumenty, které fungovaly i na `between yesterday and today`   <br/> najít, co doc i pracoval `before thursday the 31st` |
|Builtin.mystuff.end_time| hledání souborů i uložit `before noon` <br/> najít, co doc i pracoval `before 4pm`|      
|Builtin.mystuff.file_action| Otevřít tabulku i `saved` včera <br/> najít tabulku kevina `created`| 
|Builtin.mystuff.from_contact_name | Najít návrh `jason` Odeslat mi <br/> Otevřete `isaac` na poslední e-mailu |
|Builtin.mystuff.Keyword | Zobrazit mi `french conjugation` soubory <br/> Najít `marketing plan` i sepsány včera |
|Builtin.mystuff.Location | dokument i upravit v `work` <br/> fotografie i trvala `paris` |
|Builtin.mystuff.message_category | Vyhledejte Moje `new` e-mailů <br/> Vyhledejte Moje `high priority` e-mailu |
|Builtin.mystuff.message_type | Zkontrolujte Moje `email` <br/>  Zobrazit mi Moje `text` zprávy  |
|Builtin.mystuff.source_platform | hledání Moje `hotmail` e-mailu pro e-mailu z Jan    <br/> Najít že odeslaný dokumentu i `work` |
|Builtin.mystuff.start_date | najít poznámky z `january` <br/> Najít ukrást odesílání e-mailu i `after january 1st` |
|Builtin.mystuff.start_time | Najít že e-mailové i odeslané ukrást chvíli před 14: 00, ale `after noon`? <br/> Najít listu kristin Odeslat mi, který lze upravovat `last night` | 
|Builtin.mystuff.Title | `c:\dev\mystuff.txt` <br/> `*.txt` |
|Builtin.mystuff.transform_action | `download` Jan soubor odeslat mi <br/> `open` Moje doc pokyny poznámky |
|Builtin.note.note_text | vytvořit, včetně seznamu supermarketu `pork chops, applesauce and milk` <br/> Poznamenejte si do `buy milk` |
|Builtin.note.Title | Poznamenejte si názvem `grocery list` <br/> Poznamenejte si názvem `people to call` |
|Builtin.ondevice.music_artist_name | přehrání všechno podle `rufus wainwright` <br/> přehrání `garth brooks` Hudba |   
|Builtin.ondevice.music_genre | Zobrazit `classic rock` skladeb <br/> přehrání Moje `classical` Hudba z baroque období. |
|Builtin.ondevice.music_playlist | náhodný výběr všech britney spears z `workout` seznam stop <br/> přehrání `breakup` seznam stop
|Builtin.ondevice.music_song_name | přehrávání `summertime` <br/> přehrávání `me and bobby mcgee` | 
|Builtin.ondevice.setting_type | `quiet hours` <br/> `airplane mode` |
|Builtin.Places.absolute_location | Přechod k průniku `5th and pike` <br/> Ne, nechci pokynů k `1 microsoft way redmond wa 98052` |
|Builtin.Places.atmosphere | Vyhledejte `interesting` místa a přejděte    <br/> kde lze najít `casual` restaurace |  
|Builtin.Places.audio_device_type |volání post office na `hands free` <br/> volání papa Jan s `speakerphone` |    
|Builtin.Places.avoid_route | Vyhněte se `toll road` <br/> získání mi k zamezení Brno `construction on 101` |  
|Builtin.Places.cuisine | `halal` Oddělovač téměř Horská zobrazení   <br/> `kosher` přesně jídlo na na poloostrově |
|Builtin.Places.Date | vytvořit rezervaci `next friday the 12th` <br/>  je otevřený v mashiko `mondays` ? |
|Builtin.Places.discount_type | Najít `coupon` pro společnosti macy <br/> Poslat mi najít `coupon` |
|Builtin.Places.Distance | je dobré diner `within 5 miles` z tohoto umístění <br/> Najít těch, které jsou `within 15 miles` |   
|Builtin.Places.from_absolute_location | pokyny od `45 elm street` na domovskou stránku <br/> získat mi pokyny od `san francisco` k palo alto  |
|Builtin.Places.from_place_name | řídí z `post office` 56 center ulice <br/> získat mi pokyny od `home depot` k lowes |
|Builtin.Places.from_place_type | Pokyny k downtown z `work` <br/>  získat mi pokyny od `drug store` na domovskou stránku |
|Builtin.Places.meal_type | blízkým míst pro `dinner` <br/> najít vhodná pro podnik `lunch` | 
|Builtin.Places.Nearby | Zobrazit mi některé nástrojů služby `near` mi  <br/> Existují jakékoli dobrý Libanonská restaurace `around` tady? |
|Builtin.Places.open_status | Pokud je Centrum `closed` <br/> získat mi `opening` hodin úložiště | 
|Builtin.Places.place_name | Přejít na `central park` <br/> vyhledat `eiffel tower` |   
|Builtin.Places.place_type | `atms` <br/> `post office` |
|Builtin.Places.prefer_route | Zobrazit podle pokynů `shortest` trasy <br/> proveďte `fastest` trasy | 
|Builtin.Places.price_range| mě míst, na které jsou `moderately affordable` <br/>  Chci, aby `expensive` jeden   |
|Builtin.Places.Product | kde lze získat `fresh fish` kolem sem  <br/> kde kolem sem prodává `bare minerals` |
|Builtin.Places.public_transportation_route | plán sběrnice `m2` sběrnice <br/> sběrnice trasy `3x` |  
|Builtin.Places.Rating | Zobrazit `3 star` restaurace <br/> Zobrazit výsledky, které jsou `3 stars or higher`  |
|Builtin.Places.reservation_number | sešit na tabulku pro `seven` osoby <br/>  vytvořit rezervaci `two` v il fornaio |
|Builtin.Places.results_number | Zobrazit mi `10` v kavárnách nejblíže k sem <br/> Zobrazit mi nejvyšší `3` akváriích  
|Builtin.Places.service_provided | kde lze přejít `whale watch` pomocí sběrnice? <br/> Potřebuji mechanik do `fix my brakes` |    
|Builtin.Places.Time | Chci míst, na které jsou otevřené na sobotu ve `8 am`| je mashiko otevřené `now` |
|Builtin.Places.transportation_company | cvičení plány pro `new jersey transit` <br/> můžete získat existuje `bart` | 
|Builtin.Places.transportation_type | kde je úložiště Hudba i můžete získat `on foot`? <br/>  mě `biking` pokynů k mashiko|
|Builtin.Places.travel_time | Chci moct jednotky `less than 15 minutes` <br/>   Chci někde, které se můžete dostat do v `under 15 minutes` |   
|Builtin.Reminder.absolute_location | Připomeňte volat Moje dad při i nebude zobrazovat v `chicago` <br/> Při obdržení zpět do `seattle` připomenout získat plynu |
|Builtin.Reminder.contact_name | Když `bob` volání, připomeňte mi říct mu zábavné <br/> Zobrazí se připomenutí pro sběrnici školní zmínili, když máme obrátit vytvořte `arthur` |
|Builtin.Reminder.leaving_absolute_location | připomenutí se vyzvedávat Craiga při ukončení `1200 main` |
|Builtin.Reminder.leaving_implicit_location | Připomenout získat plynu při opuštění `work`  |
|Builtin.Reminder.original_start_date | připomenutí o trávníku z změnit `saturday` k neděle <br/> Přesunout Moje připomenutí o školní z `monday` na úterý   |
|Builtin.Reminder.relationship_name | Když můj `husband` volání, připomeňte mi mu říct o schůzku pta <br/> Připomenout znovu při `mom` volání|
|Builtin.Reminder.reminder_text | můžete je připomenout k `bring up my small spot of patchy skin` při smith'calls zotavení po havárii  <br/> Připomenout `pick up dry cleaning` na 4:40   |
|Builtin.Reminder.start_date | Připomenout `thursday after next` na 20: 00  <br/> Připomenout `next thursday the 18th` na 20: 00    |
|Builtin.Reminder.start_time | Vytvoření připomenutí `in 30 minutes` <br/> Zobrazí se připomenutí pro vodní asijských výrobních vytvořit `this evening at 7` |  
|Builtin.weather.absolute_location | bude rain v `boston` <br/> Co je předpovědi pro `seattle`?  |
|Builtin.weather.date_range | počasí v nyc `this weekend` <br/>   vyhledat `five day` prognózy v hollywood florida |
|Builtin.weather.suitable_for | můžete přejít `hiking` v šortky tento víkendu?   <br/> bude mít dostatečně dobrý k `walk` hře dnes? | 
|Builtin.weather.temperature_unit | Co je teploty dnes v `kelvin`   <br/> Zobrazit mi temps v `celsius` |  
|Builtin.weather.time_range | nepodporuje se zdát, že bude sněhu `tonight`? <br/> je to fouká vítr vpravo `now`?  |
|Builtin.weather.weather_condition | Zobrazit `precipitation` <br/> jak silné je `snow` v lake tahoe nyní?  |

