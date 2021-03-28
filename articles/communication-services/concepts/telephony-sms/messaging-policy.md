---
title: Zásady zasílání zpráv
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si o zásadách zasílání zpráv SMS.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/19/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bb9765c2620f45d67bf888f8bfe8a4dee450cfd6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646662"
---
# <a name="azure-communication-services-messaging-policy"></a>Zásady zasílání zpráv služby Azure Communication Services

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Komunikační služby Azure transformují způsob, jakým naši zákazníci komunikují se svými klienty, díky tvorbě bohatých a vlastních komunikačních prostředí, která využívají stejné služby na podnikové úrovni jako Microsoft teams a Skype. Integrujte funkce zasílání zpráv SMS do svých komunikačních řešení, abyste svým zákazníkům mohli kdykoli a kdekoli potřebovat podporu. Stačí, abyste měli na začátku několik požadavků na zasílání zpráv, abyste mohli začít.

Víme, že požadavky na zasílání zpráv se můžou zdát těžké, ale jsou stejně jednoduché jako zapamatování "COMS":

- Vyjádření souhlasu s C
- O-Opt-Out
- Obsah zprávy M
- S-falšování identity

Vyvinuli jsme tyto zásady zasílání zpráv, které vám pomůžou splnit zákonné požadavky a sjednotit Doporučené osvědčené postupy. 

[!INCLUDE [Notice](../../includes/messaging-policy-include.md)]

## <a name="consent"></a>Souhlas 

### <a name="what-is-consent"></a>Co je to souhlas?

Souhlas je smlouva mezi vámi a příjemcem zprávy, která vám umožní odesílat do nich automatizované zprávy. Před odesláním první zprávy musíte získat souhlas a měli byste se dát jasným příjemcům, že si budou souhlasit s přijímáním zpráv. Tento postup se označuje jako příjem "předchozího výslovného souhlasu" od jednotlivce, kterému chcete zprávu zastavovat.

Zprávy, které odesíláte, musí být stejného typu zpráv, na které se příjemce dohodl, a měla by se odeslat jenom na číslo, které vám příjemce poskytl. Pokud máte v úmyslu odesílat informační zprávy, jako jsou například připomenutí a upozornění na události, je možné vyjádřit souhlas buď písemně, nebo ústní. Pokud máte v úmyslu odesílat propagační zprávy, jako jsou prodejní nebo marketingové zprávy, které podporují produkt nebo službu, je nutné napsat souhlas.

### <a name="how-do-you-obtain-consent"></a>Jak získáte souhlas?

Souhlas lze získat různými způsoby, například:

- Když uživatel zadá své telefonní číslo do webu, 
- Když uživatel zahájí výměnu textových zpráv, nebo
- Když uživatel pošle do telefonního čísla klíčové slovo pro registraci. 
 
Bez ohledu na to, jak souhlas obdržíte, musíte vy a vaši zákazníci zajistit, aby byl souhlas jednoznačný. Rozsah souhlasu by měl být pro příjemce jasný.


### <a name="consent-requirements"></a>Požadavky na souhlas:

- Před získáním souhlasu zadejte "volání akce". Vy a vaši zákazníci by měli poskytnout potenciálním příjemcům zprávy výzvy k zadání akce, které je pozve k tomu, aby se k vašemu programu zasílání zpráv mohli vyjádřit. Volání akce by mělo zahrnovat minimálně: (1) identitu odesilatele zprávy, (2) Vymazat pokyny pro výslovný souhlas, (3) pokyny pro výslovný souhlas a (4) všechny přidružené poplatky za zasílání zpráv.
- Souhlas nejde přenést ani přiřadit. Jakýkoli souhlas, kterému jednotlivec poskytnete, se nedá přenést ani prodat nepřidruženým třetím stranám. Pokud si pro třetí stranu vyshromáždíte souhlas jednotlivce, je nutné, aby třetí strana jednoznačně identifikovala daného uživatele. Je také nutné uvést, že souhlas, který jste získali, se vztahuje pouze na komunikaci od třetí strany.
- Souhlas je omezený pro účely. Jednotlivec, který poskytuje své číslo pro určitý účel, který přijímá komunikaci pouze za konkrétní účel a od konkrétního odesílatele zprávy. Před získáním souhlasu byste měli jasně informovat zamýšleného příjemce zprávy, jestli budete odesílat opakující se zprávy nebo zprávy z přidruženého uživatele.

### <a name="consent-best-practices"></a>Osvědčené postupy pro vyjádření souhlasu:

Kromě požadavků na zasílání zpráv popsaných výše můžete chtít implementovat několik běžných osvědčených postupů, včetně: 

- Podrobné informace o volání akce Abyste měli jistotu, že získáte odpovídající souhlas, poskytněte
  - název nebo popis programu zasílání zpráv nebo produktu
  - číslo (y), ze kterého příjemci obdrží zprávy, a 
  - jakékoli platné podmínky a ujednání před jednotlivými výslovný pro příjem zpráv.
- Přesné záznamy o souhlasu Měli byste si zachovávat záznamy o všech souhlasech, které vám k tomu poskytne jednotlivec po dobu nejméně čtyř let. Záznamy o souhlasu můžou zahrnovat:
  - časová razítka
  - médium, kterým se získal souhlas
  - konkrétní kampaň, pro kterou byla získána souhlas
  - snímky obrazovky
  - ID relace nebo IP adresa odesílající osoby.
- Zásady ochrany osobních údajů a zabezpečení. Vývojářům doporučujeme poskytovat jasné zásady ochrany osobních údajů, které můžou příjemci zprávy před jejich souhlasem zkontrolovat. Doporučujeme také udržovat proaktivní bezpečnostní prvky zabezpečení, které chrání soukromé informace jednotlivců.


## <a name="double-opt-in-consent"></a>Dvojí výslovný souhlas:

Služba Azure Communication Services doporučuje použít pro všechny kampaně zasílání zpráv dvojí výslovný souhlas. Dvojitá výslovný souhlas je dvoustupňový proces, ve kterém jednotlivec poprvé poskytne souhlas s přijímáním určitých typů zpráv. Pak odešlete následnou zprávu o souhlasu s potvrzením souhlasu. Měli byste poslat více zpráv až po potvrzení souhlasu příjemce zprávy.

Úvodní zpráva s potvrzením, kterou odešlete, by měla zahrnovat vaši identitu, možnost odsouhlasení budoucích zpráv (například použití příkazu STOP), bezplatné telefonní číslo nebo "NÁPOVĚDU" pro další informace, oznámení o tom, že se jednotlivec zaregistroval v programu pro opakovanou zprávu, stručný popis programu, četnost, se kterou chcete odesílat opakované zprávy. a všechny přidružené poplatky. 

### <a name="does-azure-communication-services-ever-require-double-opt-in-consent"></a>Vyžaduje komunikační služby Azure ke stejnému souhlasu výslovný souhlas?
Ano, i když se vždy doporučuje výslovný souhlas s výslovným výslovným souhlasem, služba Azure Communication Services vyžaduje, abyste pro některé typy kampaní zasílání zpráv používali výslovný souhlas se zachováním, a to v důsledku jejich častého použití v podvodných schématech nebo v důsledku jejich nevětšího počtu Mezi tyto kampaně patří:
- Zprávy o automatické záruce
- Krátkodobé plány pojištění stavu
- Refinancování dluhů nebo zprávy o snížení úrokové míry, pokud je finanční instituce neudělaly
- Zprávy generování zájemců
- Účast, soutěže a dárků
- Nabídky pro práci z domova

Kampaně, pro které jsou vyžadovány dvojí výslovný souhlas, se mohou změnit na uvážení služby Azure Communication Services.

### <a name="exceptions-to-traditional-consent-rules"></a>Výjimky pro tradiční pravidla souhlasu:
I když se před odesláním zprávy obvykle vyžaduje předchozí expresní souhlas, jsou k dispozici dvě situace, kdy je odvozena souhlas se zprávou jednotlivce.

- Příjemce iniciuje komunikaci. Pokud jednotlivec zahájí komunikaci odesláním zprávy, můžete poskytnout relevantní informace v reakci na konkrétní dotaz nebo požadavek obsažený ve zprávě. Implicitní souhlas, že poskytnutá osoba je omezená jenom na konverzaci, kterou inicioval jednotlivec, pokud neobdržíte souhlas s další komunikací.
- Výjimky pro konkrétní služby. Existuje několik konkrétních služeb, pro které jste pravděpodobně předpokládali souhlas s zahájením zprávy. Nejběžnější jsou tyto: 
- zprávy o doručení balíčku
- zprávy finančních institucí, které se týkají časově citlivých témat (například potenciálně podvodných transakcí nebo porušení dat)
- zprávy poskytovatele zdravotní péče, které obsahují informace citlivé na čas a účel zpracování (například připomenutí události nebo zkoušky, výsledky testovacího prostředí a oznámení o předpisech). 
 
Žádná z těchto zpráv nesmí zahrnovat oslovování ani reklamy.


## <a name="opt-out"></a>Odhlásit se

Příjemci zprávy můžou odvolat souhlas a vyjádřit příjem budoucích zpráv jakýmkoli rozumným způsobem. Nemůžete určit exkluzivní způsob, jak můžou příjemci zpráv odvolat souhlas. 

### <a name="opt-out-requirements"></a>Požadavky na výslovný souhlas:

Zajistěte, aby příjemci zpráv mohli kdykoli odhlásit budoucí zprávy. Také je nutné nabídnout více možností pro výslovný souhlas. Po výslovný příjemce zprávy byste neměli odesílat další zprávy, pokud jednotlivec neposkytne obnovený souhlas.

Jedním z nejběžnějších mechanismů pro odhlášení je zahrnutí klíčového slova "STOP" v úvodní zprávě každé nové konverzace. Připravte se na odebrání zákazníků, kteří odpoví malým písmenem "Stop" nebo jinými běžnými klíčovými slovy, jako je například "zrušení odběru" nebo "zrušit". Po odsouhlasení jednotlivých odvolání byste je měli odebrat ze všech opakovaných kampaní pro zasílání zpráv, pokud výslovně nerozhodnete, že budou dostávat zprávy z konkrétního programu.

### <a name="opt-out-best-practices"></a>Osvědčené postupy pro výslovný souhlas:

Kromě klíčových slov můžou další běžné mechanismy pro odhlášení zahrnovat zákazníkům s určenou e-mailovou adresou pro výslovný souhlas, telefonní číslo pracovníků zákaznické podpory nebo odkaz na zrušení odběru webové stránky. 


### <a name="how-we-handle-opt-out-requests"></a>Jak zpracováváme žádosti o výslovný souhlas:

Pokud jednotlivé požadavky na výslovný souhlas v budoucích zprávách na komunikačních službách Azure Communications bezplatně zastavují, bude se automaticky zastavit veškerá další přenos z tohoto čísla. Přesto však musíte zajistit, abyste neodesílali další zprávy pro tuto kampaň zasílání zpráv z nových nebo různých čísel. Pokud jste samostatně získali výslovný souhlas s jinou kampaní zasílání zpráv, můžete dál odesílat zprávy z jiného čísla pro danou kampaň. Další informace o [zpracování výslovných odhlašování](https://github.com/Prakulka/azure-docs-pr/blob/master/articles/communication-services/concepts/telephony-sms/sms-faq.md#how-can-i-receive-messages-using-azure-communication-services) najdete v části Nejčastější dotazy.

## <a name="message-content"></a>Obsah zprávy

### <a name="adult-content"></a>Obsah pro dospělé:

Obsah zprávy, který obsahuje prvky pohlaví, nenávist, alkohol, střelné, tabákové, hazardní a spolutesty, mohou aktivovat další požadavky. Tento obsah je v některých předpisech výslovně zakázaný. Pokud odešlete zprávu, která obsahuje tento obsah, je povinností dodržovat všechny platné zákony o předpisech, ve kterých byla komunikace přijata. Na žádost o vynucení zákona nebo na komunikačních službách Azure je nutné zajistit, aby bylo možné poskytnout důkaz souhlasu s místními zákony, které upravují obsah pro dospělé.

I v případě, že takový obsah není v úmyslu nezákonný, byste měli zahrnout ověřovací mechanismus pro věkové zařízení, u kterého se má příjemce zprávy od obsahu pro dospělé odhlásit. V USA platí další zákonné požadavky na marketingová sdělení, která jsou směrována na děti pod stáří 13. 

### <a name="prohibited-content"></a>Zakázaný obsah:

Komunikační služby Azure zakazují určitý obsah zprávy bez ohledu na souhlas. Zakázaný obsah zahrnuje:
- Obsah, který propaguje nezákonné aktivity (např. daňový daňový daňový nebo živočišný Cruelty v USA)
- Nenávist řeč, pomlouvačného rozpoznávání řeči, obtěžování nebo jiné rozpoznávání řeči určené k patentově urážlivému
- Pornografický obsah
- Obsah obscénního nebo vulgaris
- Intimidation a hrozby
- Obsah, který je v úmyslu vycházet z podvodu, v omylu, způsobuje poškození nebo wrongfully získá cokoli o hodnotě 
- Obsah, který uvádí újmu, diskriminaci nebo násilí
- Obsah, který šíří malware
- Obsah, který je v úmyslu obejít požadavky na omezení stáří

Vyhrazujeme si právo kdykoli upravit seznam zakázaných zpráv.

## <a name="spoofing"></a>Falšování identity

Falšování je způsob, jakým se v zařízení příjemce zprávy zobrazí klamavé nebo nepřesné původní číslo. Důrazně vás zabráníte a všem poskytovatelům služeb, které používáte při posílání falešných zpráv. Falšování identity chrání identitu odesilatele zprávy a brání příjemcům zprávy v jednoduchém vypnutí nevyžádaných komunikací. Také vyžadujeme, abyste provedli všechny příslušné zákony k falšování identity.

## <a name="final-thoughts"></a>Konečné myšlenky

### <a name="legal-responsibility"></a>Právní odpovědnost:

Tato zásada zasílání zpráv nepředstavuje zákonné poradenství a vyhrazujeme si právo zásady kdykoli upravit. Komunikační služby Azure nejsou zodpovědné za to, že obsah, časování nebo příjemci zpráv našich zákazníků splňují všechny použitelné zákonné požadavky. 

Naši zákazníci zodpovídají za všechny požadavky na zasílání zpráv. Pokud jste poskytovatelem platformy nebo softwaru, který používá komunikační služby Azure pro účely zasílání zpráv, měli byste vyžadovat, aby vaši zákazníci také dodržovali všechny požadavky popsané v těchto zásadách zasílání zpráv. Pro další doprovodné materiály poskytuje CTIA užitečné [Principy zasílání zpráv a osvědčené postupy](https://api.ctia.org/wp-content/uploads/2019/07/190719-CTIA-Messaging-Principles-and-Best-Practices-FINAL.pdf).

### <a name="penalties"></a>Uloženy

Naši zákazníci doporučujeme vyvíjet a implementovat zásady a postupy navržené pro zajištění souladu se všemi požadavky na zasílání zpráv. Porušení požadavků na zasílání zpráv může mít za následek značnou pokutu, která může rychle docházet k bublinám. Je ve vašem nejlepším zájmu, abyste se seznámili se všemi platnými požadavky na zasílání zpráv a vyhnuli se jejich dodržování a vyhnuli se jejich porušení. Pokud porušíte zásady zasílání zpráv nebo jiné zákonné požadavky, budeme s vámi spolupracovat a zajistit budoucí dodržování předpisů. Vyhrazujeme si ale právo odebrat libovolného zákazníka z platformy Azure Communications Services, který předvádí vzor nedodržení našich zásad zasílání zpráv nebo zákonných požadavků.
